import cv2

cap = cv2.VideoCapture("/dev/video0")  # your CSI camera

if not cap.isOpened():
    print("🚨 Cannot open camera!")
    exit()
else:
    print("✅ Camera opened successfully!")

while True:
    ret, frame = cap.read()
    if not ret:
        print("⚠️ Frame not received!")
        break

    # FIX: convert YUV/NV12 frame to BGR
    frame = cv2.cvtColor(frame, cv2.COLOR_YUV2BGR_NV12)

    cv2.imshow("Camera Test Fixed", frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
