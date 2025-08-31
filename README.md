import cv2

# Try directly opening the device node
cap = cv2.VideoCapture("/dev/video0")  # your camera

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

    cv2.imshow("Camera Test Simple", frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
