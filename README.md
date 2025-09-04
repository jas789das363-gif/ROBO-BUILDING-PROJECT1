import cv2
import numpy as np

# Full GStreamer pipeline for Jetson Orin Nano IMX219
gst_pipeline = (
    "nvarguscamerasrc ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, framerate=30/1, format=NV12 ! "
    "nvvidconv ! "
    "video/x-raw, format=BGRx ! "
    "videoconvert ! "
    "video/x-raw, format=BGR ! appsink drop=True"
)

print("🔍 Opening camera with pipeline:")
print(gst_pipeline)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)

if not cap.isOpened():
    print("❌ Could not open camera with GStreamer pipeline.")
    exit()

ret, frame = cap.read()
if not ret or frame is None:
    print("❌ Could not read frame from camera.")
    cap.release()
    exit()

print(f"✅ Frame captured, shape: {frame.shape}")

# Save the captured frame for inspection
cv2.imwrite("frame_bgr.jpg", frame)
print("💾 Saved: frame_bgr.jpg")

# Display the frame
cv2.imshow("Camera Frame (BGR)", frame)
cv2.waitKey(3000)  # show for 3 seconds

cap.release()
cv2.destroyAllWindows()
