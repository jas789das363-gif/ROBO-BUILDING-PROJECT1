import cv2
from ultralytics import YOLO

# Load YOLOv8 model (nano version is fastest for Jetson)
model = YOLO("yolov8n.pt")

# Use plain OpenCV capture (your working pipeline)
cap = cv2.VideoCapture(0)

if not cap.isOpened():
    print("❌ Failed to open camera with index 0.")
    exit()

while True:
    ret, frame = cap.read()
    if not ret:
        print("❌ Failed to grab frame.")
        break

    # Run YOLOv8 inference
    results = model(frame)

    # Display results
    annotated_frame = results[0].plot()
    cv2.imshow("YOLOv8 Detection", annotated_frame)

    if cv2.waitKey(1) & 0xFF == 27:  # ESC to quit
        break

cap.release()
cv2.destroyAllWindows()
