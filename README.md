import cv2

cap = cv2.VideoCapture(0)  # try 0, 1, or 2 if 0 fails

if not cap.isOpened():
    print("Camera not found!")
else:
    ret, frame = cap.read()
    if ret:
        print("Camera works! Frame captured.")
        print("Frame size:", frame.shape)
    else:
        print("Failed to grab frame.")
    cap.release()



