gst-launch-1.0 nvarguscamerasrc sensor-id=0 num-buffers=1 ! \
'video/x-raw(memory:NVMM),width=1920,height=1080,format=NV12,framerate=30/1' ! \
nvjpegenc ! filesink location=test.jpg


______
gst-launch-1.0 nvarguscamerasrc sensor-id=0 ! \
'video/x-raw(memory:NVMM),width=1280,height=720,format=NV12,framerate=30/1' ! \
omxh264enc ! qtmux ! filesink location=test.mp4 -e
_________

import cv2

# GStreamer pipeline
gst_pipeline = (
    "nvarguscamerasrc sensor-id=0 ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! "
    "videoconvert ! video/x-raw, format=BGR ! appsink"
)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)

if not cap.isOpened():
    print("Camera not opened")
    exit()

while True:
    ret, frame = cap.read()
    if not ret:
        print("Frame capture failed")
        break

    cv2.imshow("Camera Feed", frame)
    
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()



