import jetson.utils
from ultralytics import YOLO
import numpy as np
import cv2

# Load YOLOv8 model
model = YOLO("yolov8n.pt")

# Open camera
camera = jetson.utils.videoSource("csi://0")  # IMX219
window = jetson.utils.videoOutput("YOLOv8 Live Feed")  # window preview

while True:
    img_jetson = camera.Capture()  # Jetson image

    # Convert to OpenCV format for YOLOv8
    frame = jetson.utils.cudaToNumpy(img_jetson)
    frame = cv2.cvtColor(frame, cv2.COLOR_RGBA2BGR)  # convert to BGR

    # Run YOLOv8 inference
    results = model(frame)
    annotated_frame = results[0].plot()  # draw boxes

    # Show in window
    cv2.imshow("YOLOv8 Live Feed", annotated_frame)
    key = cv2.waitKey(1) & 0xFF
    if key == ord('q'):
        break

cv2.destroyAllWindows()
