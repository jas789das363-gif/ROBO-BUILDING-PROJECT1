import cv2

cap = cv2.VideoCapture("/dev/video0")  # your CSI camera

if not cap.isOpened():
    print("🚨 Camera not found!")
    exit()
else:
    print("✅ Camera opened successfully!")

while True:
    ret, frame = cap.read()
    if not ret:
        print("⚠️ Frame not received!")
        break

    cv2.imshow("CSI Camera Test", frame)

    # Press 'q' to quit
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
