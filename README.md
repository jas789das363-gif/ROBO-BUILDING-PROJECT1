import cv2

gst_str = (
    "nvarguscamerasrc ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! "
    "videoconvert ! video/x-raw, format=BGR ! appsink"
)

cap = cv2.VideoCapture(gst_str, cv2.CAP_GSTREAMER)

ret, frame = cap.read()
if ret:
    cv2.imwrite("frame.jpg", frame)
    print("✅ Frame captured and saved as frame.jpg")
else:
    print("🚨 Failed to capture frame")
cap.release()



____

gst-launch-1.0 nvarguscamerasrc ! nvvidconv ! xvimagesink
