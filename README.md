import cv2
from ultralytics import YOLO

# Load YOLOv8 model
model = YOLO("yolov8n.pt")

# Minimal working GStreamer pipeline for Jetson + IMX219
gst_pipeline = (
    "nvarguscamerasrc ! "
    "video/x-raw(memory:NVMM), width=640, height=480, framerate=30/1, format=NV12 ! "
    "nvvidconv ! "
    "video/x-raw, format=BGRx ! "
    "appsink"
)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)

if not cap.isOpened():
    print("❌ Failed to open camera. Double-check connection and JetPack version.")
    exit()

while True:
    ret, frame = cap.read()
    if not ret:
        print("❌ Frame capture failed.")
        break

    # Convert BGRx -> BGR for YOLO
    frame_bgr = cv2.cvtColor(frame, cv2.COLOR_BGRA2BGR)

    # Run YOLOv8
    results = model(frame_bgr)
    annotated_frame = results[0].plot()

    cv2.imshow("YOLOv8 Detection", annotated_frame)
    if cv2.waitKey(1) & 0xFF == 27:  # ESC to quit
        break

cap.release()
cv2.destroyAllWindows()
