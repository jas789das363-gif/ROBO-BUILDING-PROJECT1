import cv2
from ultralytics import YOLO

# Load YOLOv8
model = YOLO("yolov8n.pt")

# Working GStreamer pipeline
gst_pipeline = (
    "nvarguscamerasrc sensor-id=0 ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGR ! appsink"
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

    # YOLO inference
    results = model(frame)
    annotated_frame = results[0].plot()

    cv2.imshow("YOLO Live Feed", annotated_frame)
    key = cv2.waitKey(1) & 0xFF
    if key == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
