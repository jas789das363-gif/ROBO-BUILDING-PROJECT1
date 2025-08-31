import cv2

gst_pipeline = (
    "nvarguscamerasrc sensor-id=0 ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGR ! appsink"
)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)

if not cap.isOpened():
    print("🚨 Camera not opened")
    exit()

while True:
    ret, frame = cap.read()
    if not ret:
        print("🚨 Frame capture failed")
        break

    cv2.imshow("Camera Feed", frame)
    
    key = cv2.waitKey(1) & 0xFF
    if key == ord('s'):
        cv2.imwrite("snapshot.jpg", frame)
        print("✅ Snapshot saved as snapshot.jpg")
    elif key == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
