import cv2
from ultralytics import YOLO

# Load YOLOv8 model
model = YOLO("yolov8n.pt")  # make sure the .pt file exists

# GStreamer pipeline for live feed
gst_pipeline = (
    "nvarguscamerasrc sensor-id=0 ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! "
    "videoconvert ! video/x-raw, format=BGR ! appsink"
)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)
if not cap.isOpened():
    print("🚨 Camera not opened")
    exit()

while True:
    ret, frame = cap.read()
    if not ret:
        print("🚨 Frame capture failed")
        break

    # Run YOLOv8 inference
    results = model(frame)

    # Render results on frame
    annotated_frame = results[0].plot()

    cv2.imshow("YOLOv8 Live Feed", annotated_frame)

    key = cv2.waitKey(1) & 0xFF
    if key == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
