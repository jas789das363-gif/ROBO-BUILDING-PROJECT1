sudo docker run -it --runtime=nvidia --network host ultralytics/ultralytics:latest-jetson-jetpack6
sudo docker run -it --runtime=nvidia --network host ultralytics/ultralytics:latest-jetson-jetpack6
python3
>>> import jetson.utils   # should work now


# yolo_camera.py
from ultralytics import YOLO
import jetson.utils
import cv2
import numpy as np

# Load YOLOv8 model
model = YOLO("yolov8n.pt")

# Open the IMX219 CSI camera
camera = jetson.utils.videoSource("csi://0")  # 0 = IMX219
window = jetson.utils.videoOutput("YOLOv8 Live Feed")  # creates a live preview

while True:
    # Capture frame from camera
    img_jetson = camera.Capture()

    # Convert Jetson image to OpenCV BGR for YOLO
    frame = jetson.utils.cudaToNumpy(img_jetson)
    frame = cv2.cvtColor(frame, cv2.COLOR_RGBA2BGR)

    # YOLOv8 inference
    results = model(frame)
    annotated_frame = results[0].plot()

    # Display live feed
    cv2.imshow("YOLOv8 Live Feed", annotated_frame)

    key = cv2.waitKey(1) & 0xFF
    if key == ord('q'):
        break
    elif key == ord('s'):
        cv2.imwrite("snapshot.jpg", annotated_frame)
        print("✅ Snapshot saved")

cv2.destroyAllWindows()
