# ROBO-BUILDING-PROJECT1
sudo apt-get update
sudo apt-get install espeak-ng python3-opencv


# 2
import cv2
import os

def capture_frame():
    # Open camera (0 is default, adjust if needed)
    cap = cv2.VideoCapture(0)

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
    # Placeholder for your LLM call
    # Here you can replace with actual AI reasoning
    return "I see something in front of me."

def speak(text):
    # Lightweight TTS
    os.system(f'espeak-ng "{text}"')

# === Main loop ===
if __name__ == "__main__":
    frame = capture_frame()
    if frame is not None:
        response = reasoning_step(frame)
        print("LLM Response:", response)
        speak(response)



