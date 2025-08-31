# yolo_csi_camera.py
from ultralytics import YOLO
import jetson.utils
import cv2
import numpy as np

# -------------------------
# Load YOLOv8n model
# -------------------------
model = YOLO("yolov8n.pt")  # Make sure yolov8n.pt is in the same folder

# -------------------------
# Open CSI camera
# -------------------------
camera = jetson.utils.videoSource("csi://0")  # IMX219 camera
window = jetson.utils.videoOutput("YOLOv8 Live Feed")  # live preview

# -------------------------
# Main loop
# -------------------------
while True:
    # Capture frame (Jetson GPU image)
    img_jetson = camera.Capture()

    # Convert to OpenCV BGR for YOLO
    frame = jetson.utils.cudaToNumpy(img_jetson)
    frame = cv2.cvtColor(frame, cv2.COLOR_RGBA2BGR)

    # Run YOLOv8 inference (GPU accelerated)
    results = model(frame)

    # Draw annotations
    annotated_frame = results[0].plot()

    # Display live feed
    cv2.imshow("YOLOv8 Live Feed", annotated_frame)

    key = cv2.waitKey(1) & 0xFF
    if key == ord('q'):
        break
    elif key == ord('s'):
        cv2.imwrite("snapshot.jpg", annotated_frame)
        print("✅ Snapshot saved as snapshot.jpg")

cv2.destroyAllWindows()
