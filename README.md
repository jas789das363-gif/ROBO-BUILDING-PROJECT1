# 1) Update + install dependencies (only need to do this once)
sudo apt-get update
sudo apt-get install -y espeak-ng python3-opencv

# 2) Create a new Python file
nano robot_pipeline.py


import cv2
import os

def capture_frame():
    cap = cv2.VideoCapture(0)  # 0 = default camera
    if not cap.isOpened():
        print("Camera not found!")
        return None

    ret, frame = cap.read()
    cap.release()

    if ret:
        return frame
    else:
        print("Failed to grab frame.")
        return None
        
def reasoning_step(frame):
    # Placeholder for your local LLM call later
    return "I see something in front of me."

def speak(text):
    os.system(f'espeak-ng "{text}"')

if __name__ == "__main__":
    frame = capture_frame()
    if frame is not None:
        response = reasoning_step(frame)
        print("LLM Response:", response)
        speak(response)


# Then:
# Press CTRL+O → Enter (save)
# Press CTRL+X (exit)


# 3) Run your program
python3 robot_pipeline.py


