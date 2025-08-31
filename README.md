import cv2
import os
import subprocess
from ultralytics import YOLO

# ===== Load YOLOv8 Nano TensorRT Model =====
# Make sure yolov8n.engine exists in the container folder
if not os.path.exists("yolov8n.engine"):
    # If engine doesn't exist, create it from PyTorch model
    print("Converting PyTorch model to TensorRT engine...")
    model = YOLO("yolov8n.pt")  # PyTorch model
    model.export(format="engine")  # creates 'yolov8n.engine'

trt_model = YOLO("yolov8n.engine")  # Load TensorRT engine

# ===== Camera Capture Function =====
def capture_frame(camera_index=0, width=640, height=480):
    cap = cv2.VideoCapture(camera_index)
    if not cap.isOpened():
        print("Camera not found!")
        return None

    ret, frame = cap.read()
    cap.release()

    if ret:
        frame_small = cv2.resize(frame, (width, height))
        return frame_small
    else:
        print("Failed to capture frame.")
        return None

# ===== Object Detection =====
def detect_objects(frame):
    """Detect objects in frame using YOLOv8 TensorRT model"""
    results = trt_model(frame)
    detected = []
    for r in results:
        for obj in r.boxes.cls:
            class_name = trt_model.names[int(obj)]
            detected.append(class_name)
    return detected

# ===== LLaMA Reasoning =====
def reasoning_step(object_list, model="llama3.2:3b"):
    """Send detected objects as prompt to LLaMA"""
    if not object_list:
        prompt = "I see nothing."
    else:
        prompt = f"I see the following objects: {', '.join(object_list)}. Describe them in one short sentence."

    try:
        result = subprocess.run(
            ["ollama", "run", model],
            input=prompt.encode(),
            capture_output=True,
            check=True
        )
        return result.stdout.decode().strip()
    except Exception as e:
        return f"Error querying LLM: {e}"

# ===== Lightweight TTS =====
def speak(text):
    os.system(f'espeak-ng "{text}"')

# ===== Main Program =====
if __name__ == "__main__":
    frame = capture_frame()
    if frame is not None:
        objects = detect_objects(frame)
        print("Detected objects:", objects)
        response = reasoning_step(objects)
        print("LLM Response:", response)
        speak(response)
    else:
        print("No frame captured. Exiting.")




___________
____________
yolo_test.py
from ultralytics import YOLO

# Load YOLOv8 Nano model
model = YOLO("yolov8n.pt")  # make sure yolov8n.pt is in the current folder

# Run inference on a sample image from the web
results = model("https://ultralytics.com/images/bus.jpg")

# Print detected class names and confidences
for r in results:
    print("Detected objects:")
    for cls, conf in zip(r.boxes.cls, r.boxes.conf):
        class_name = model.names[int(cls)]
        print(f"  {class_name}: {conf:.2f}")

# Display image with bounding boxes (optional)
results.show()

_______________________________________________

from ultralytics import YOLO

# Load YOLOv8n (tiny version, good for testing)
model = YOLO("yolov8n.pt")

# Export to TensorRT (this will take a few minutes)
model.export(format="engine")

# Load TensorRT model
trt_model = YOLO("yolov8n.engine")

# Run inference on a test image
results = trt_model("https://ultralytics.com/images/bus.jpg")

# Show results
results[0].show()

__________________________
python3 -c "from ultralytics import YOLO; print('YOLOv8 ready ✅')"


