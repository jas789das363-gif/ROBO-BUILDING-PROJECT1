import cv2

def gstreamer_pipeline(
    sensor_id=0,
    capture_width=1280,
    capture_height=720,
    display_width=1280,
    display_height=720,
    framerate=30,
    flip_method=0,
):
    return (
        f"nvarguscamerasrc sensor-id={sensor_id} ! "
        f"video/x-raw(memory:NVMM), width=(int){capture_width}, height=(int){capture_height}, "
        f"format=(string)NV12, framerate=(fraction){framerate}/1 ! "
        f"nvvidconv flip-method={flip_method} ! "
        f"video/x-raw, width=(int){display_width}, height=(int){display_height}, format=(string)BGRx ! "
        f"videoconvert ! video/x-raw, format=(string)BGR ! appsink"
    )

cap = cv2.VideoCapture(gstreamer_pipeline(), cv2.CAP_GSTREAMER)

if not cap.isOpened():
    print("🚨 Camera not found! Check ribbon cable & Jetson connection.")
else:
    print("✅ Camera opened successfully!")
    while True:
        ret, frame = cap.read()
        if not ret:
            print("🚨 Frame not received!")
            break
        cv2.imshow("CSI Camera Test", frame)
        if cv2.waitKey(1) & 0xFF == ord('q'):
            break

cap.release()
cv2.destroyAllWindows()
__________________________________________________________________________________________________________________________
sudo apt-get update
sudo apt-get install -y v4l-utils


# test
v4l2-ctl --list-devices

# 3 liveestream
gst-launch-1.0 v4l2src device=/dev/video0 ! videoconvert ! xvimagesink

#final test
sudo apt-get install -y v4l-utils
v4l2-ctl --list-devices
