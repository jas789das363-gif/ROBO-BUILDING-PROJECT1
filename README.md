import cv2
import os
import subprocess

def capture_frame(camera_index=0, width=640, height=480):
    """Capture one frame from the camera and resize it."""
    cap = cv2.VideoCapture(camera_index)
    if not cap.isOpened():
        print("Camera not found!")
        return None

    ret, frame = cap.read()
    cap.release()

    if ret:
        print(f"Frame captured! Original size: {frame.shape}")
        frame_small = cv2.resize(frame, (width, height))
        print(f"Resized frame to: {frame_small.shape}")
        return frame_small
    else:
        print("Failed to grab frame.")
        return None

def reasoning_step(prompt, model="llama3.2:3b"):
    """Send prompt to local Ollama LLaMA model and return response."""
    try:
        print("Sending prompt to LLM...")
        result = subprocess.run(
            ["ollama", "run", model],
            input=prompt.encode(),
            capture_output=True,
            check=True
        )
        response = result.stdout.decode().strip()
        print("LLM Response received.")
        return response
    except subprocess.CalledProcessError as e:
        return f"Error querying LLM: {e}"
    except Exception as e:
        return f"Unexpected error: {e}"

def speak(text):
    """Use eSpeak NG for lightweight TTS."""
    print("Speaking now...")
    os.system(f'espeak-ng "{text}"')
    print("Done speaking.")

if __name__ == "__main__":
    frame = capture_frame()
    if frame is not None:
        prompt = "You are a robot looking through your camera. Say one short sentence about what you see."
        response = reasoning_step(prompt, model="llama3.2:3b")
        print("LLM Response:", response)
        speak(response)
    else:
        print("No frame captured. Exiting.")


