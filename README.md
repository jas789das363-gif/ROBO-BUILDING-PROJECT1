gst-launch-1.0 nvarguscamerasrc ! nvoverlaysink


___________

import cv2

# GStreamer pipeline for IMX219 (CSI camera)
gst_pipeline = (
    "nvarguscamerasrc ! "
    "video/x-raw(memory:NVMM), width=1920, height=1080, framerate=30/1 ! "
    "nvvidconv ! "
    "video/x-raw, format=BGRx ! "
    "videoconvert ! "
    "video/x-raw, format=BGR ! appsink"
)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)

ret, frame = cap.read()
if ret:
    cv2.imwrite("frame.jpg", frame)
    print("✅ Captured and saved frame.jpg")
else:
    print("🚨 Failed to capture frame")

cap.release()
