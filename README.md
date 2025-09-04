import jetson.utils
from ultralytics import YOLO
import cv2
import numpy as np

# -------------------------
# Load YOLOv8 model
# -------------------------
# Make sure you have a YOLOv8 model in PyTorch/TensorRT format
model = YOLO("yolov8n.pt")  # replace with your model path

# -------------------------
# Open IMX219 camera using nvarguscamerasrc
# -------------------------
camera = jetson.utils.gstCamera(
    1280, 720,
    "nvarguscamerasrc ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=RGBA ! appsink"
)

# -------------------------
# Helper: Convert Jetson RGBA -> OpenCV BGR
# -------------------------
def jetson_to_cv2(img_rgba, width, height):
    # Converts CUDA memory to numpy array
    array = jetson.utils.cudaToNumpy(img_rgba, width, height, 4)
    return cv2.cvtColor(array, cv2.COLOR_RGBA2BGR)

# -------------------------
# Main loop
# -------------------------
while True:
    # Capture RGBA frame from camera
    img, width, height = camera.CaptureRGBA()

    # Convert to OpenCV BGR
    frame_bgr = jetson_to_cv2(img, width, height)

    # Run YOLOv8 inference
    results = model(frame_bgr)

    # Overlay YOLO detections
    annotated_frame = results[0].plot()

    # Display frame
    cv2.imshow("YOLOv8 Detection", annotated_frame)
    if cv2.waitKey(1) & 0xFF == 27:  # ESC to quit
        break

cv2.destroyAllWindows()
