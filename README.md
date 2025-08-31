import cv2

cap = cv2.VideoCapture("/dev/video0")  # or try 1,2 if 0 fails
ret, frame = cap.read()
if ret:
    cv2.imwrite("frame.jpg", frame)
    print("✅ Frame captured and saved as frame.jpg")
else:
    print("🚨 Failed to capture frame")
cap.release()





______
import cv2

cap = cv2.VideoCapture(0)  # try /dev/video0

if not cap.isOpened():
    print("❌ Camera not opened")
else:
    ret, frame = cap.read()
    if ret:
        cv2.imwrite("frame.jpg", frame)
        print("✅ Saved frame as frame.jpg")
    else:
        print("❌ Failed to grab frame")

cap.release()
_____________________

import cv2

cap = cv2.VideoCapture(0)

if not cap.isOpened():
    print("❌ Camera not opened")
else:
    ret, raw = cap.read()
    if ret:
        # Convert Bayer to BGR
        frame = cv2.cvtColor(raw, cv2.COLOR_BAYER_BG2BGR)
        cv2.imwrite("frame.jpg", frame)
        print("✅ Converted and saved frame.jpg")
    else:
        print("❌ Failed to grab frame")

cap.release()
______________________






___________
v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=1 --stream-to=frame.jpg


xdg-open frame.jpg



gst-launch-1.0 nvarguscamerasrc ! nvoverlaysink



sudo systemctl restart nvargus-daemon


gst-launch-1.0 nvarguscamerasrc ! nvoverlaysink
