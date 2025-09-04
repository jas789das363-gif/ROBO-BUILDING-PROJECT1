import cv2
import numpy as np

# Open camera (V4L2)
cap = cv2.VideoCapture(0, cv2.CAP_V4L2)

if not cap.isOpened():
    print("❌ Failed to open camera.")
    exit()

# Set resolution (optional)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1280)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 720)

# Capture one frame
ret, frame = cap.read()
if not ret:
    print("❌ Failed to capture frame.")
    cap.release()
    exit()

# Convert from NV12/YUV to BGR
# On Jetson, frames from /dev/video0 are often NV12 stored in a single plane
# OpenCV sometimes reads them as a 2D array, so we reconstruct manually

# If frame is already 2D, treat as NV12
if len(frame.shape) == 2:
    height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
    width = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
    # frame is a single plane NV12 -> convert
    frame_bgr = cv2.cvtColor(frame, cv2.COLOR_YUV2BGR_NV12)
else:
    # Already 3 channels? Just pass
    frame_bgr = frame

cv2.imshow("Camera Frame (BGR)", frame_bgr)
cv2.imwrite("frame_bgr_fixed.jpg", frame_bgr)
print("💾 Saved frame_bgr_fixed.jpg")

cv2.waitKey(3000)
cap.release()
cv2.destroyAllWindows()
