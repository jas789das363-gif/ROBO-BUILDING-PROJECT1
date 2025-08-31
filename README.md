import cv2

gst_pipeline = (
    "v4l2src device=/dev/video0 ! "
    "video/x-raw, format=(string)BGR ! "
    "videoconvert ! appsink"
)

cap = cv2.VideoCapture(gst_pipeline, cv2.CAP_GSTREAMER)

if not cap.isOpened():
    print("🚨 Camera not found!")
    exit()
else:
    print("✅ Camera feed ready!")

while True:
    ret, frame = cap.read()
    if not ret:
        print("⚠️ Frame not received!")
        break

    cv2.imshow("CSI Camera Feed", frame)

    if cv2.waitKey(1) & 0xFF == ord("q"):
        break

cap.release()
cv2.destroyAllWindows()
