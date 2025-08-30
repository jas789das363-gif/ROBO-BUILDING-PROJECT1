# Install ultralytics and dependencies
pip install ultralytics opencv-python-headless torch torchvision --extra-index-url https://download.pytorch.org/whl/torch_stable.html


_______

import cv2
import os
import subprocess
from ultralytics import YOLO

# Load YOLOv8 Nano model (pretrained COCO)
model = YOLO('yolov8n.pt')  # small, fast, lightweight

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
        print("Failed to grab frame.")
        return None

def detect_objects(frame):
    """Detect objects in frame using YOLOv8 Nano"""
    results = model(frame)  # returns detection results
    detected = []
    for r in results:
        for obj in r.boxes.cls:  # class indices
            class_name = model.names[int(obj)]
            detected.append(class_name)
    return detected

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

def speak(text):
    os.system(f'espeak-ng "{text}"')

# ===== Main Loop =====
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

___________________________
_________________________

# Example for Jetson Orin Nano + Ubuntu 22.04
sudo apt-get update
sudo apt-get install python3-pip libopenblas-base libopenmpi-dev
pip3 install --upgrade pip

# Install PyTorch & torchvision (Jetson-optimized)
# Use the wheel from NVIDIA site:
# https://developer.nvidia.com/embedded/jetson-reinforcement-learning
# Example:
pip3 install torch-2.2.0+nv22.12-cp38-cp38-linux_aarch64.whl
pip3 install torchvision-0.17.1+nv22.12-cp38-cp38-linux_aarch64.whl

# 2
pip3 install opencv-python-headless


# 3 
pip3 install ultralytics

# 4 
# This will download yolov8n.pt (Nano, small and fast)
from ultralytics import YOLO

model = YOLO('yolov8n.pt')

#from ultralytics import YOLO
import cv2

# Load YOLOv8 Nano
model = YOLO('yolov8n.pt')

# Open camera
cap = cv2.VideoCapture(0)
ret, frame = cap.read()
cap.release()

if ret:
    results = model(frame)
    results.print()  # prints detected objects
else:
    print("Failed to capture frame")

# RUN TEST 
python3 yolo_test.py





