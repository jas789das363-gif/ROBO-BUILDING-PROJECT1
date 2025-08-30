# 1) Update + install dependencies (only need to do this once)
sudo apt-get update
sudo apt-get install -y espeak-ng python3-opencv

# 2) Create a new Python file
nano robot_pipeline.py


import cv2
import os
import subprocess

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

def reasoning_step(prompt, model="llama3.2:3b"):
    """Send prompt to local Ollama LLaMA model and return response."""
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

if __name__ == "__main__":
    frame = capture_frame()
    if frame is not None:
        # For now, we’re not analyzing the image — just sending a text prompt
        prompt = "You are a robot looking through your camera. Say one short sentence about what you might see."
        response = reasoning_step(prompt, model="llama3.2:3b")
        print("LLM Response:", response)
        speak(response)



# Then:
# Press CTRL+O → Enter (save)
# Press CTRL+X (exit)


# 3) Run your program
python3 robot_pipeline.py


