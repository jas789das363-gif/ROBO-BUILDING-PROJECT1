import jetson.inference
import jetson.utils
import numpy as np
import cv2

# -------------------------
# Load YOLOv8 model (Jetson-friendly version)
# -------------------------
# You can export a YOLOv8 model to ONNX / TensorRT first and put path here
# For demonstration, we'll use Jetson's built-in SSD-Mobilenet for detection
net = jetson.inference.detectNet("ssd-mobilenet-v2", threshold=0.5)

# -------------------------
# Open IMX219 camera
# -------------------------
camera = jetson.utils.gstCamera(1280, 720, "/dev/video0")

# -------------------------
# Create display window
# -------------------------
display = jetson.utils.glDisplay()

# -------------------------
# Optional: function to convert Jetson RGBA to OpenCV BGR
# -------------------------
def jetson_to_cv2(image_rgba, width, height):
    # Convert Jetson RGBA to numpy array
    array = jetson.utils.cudaToNumpy(image_rgba, width, height, 4)  # 4 channels RGBA
    # Convert RGBA -> BGR for OpenCV
    return cv2.cvtColor(array, cv2.COLOR_RGBA2BGR)

# -------------------------
# Main loop
# -------------------------
while display.IsOpen():
    # Capture RGBA frame from camera
    img, width, height = camera.CaptureRGBA()
    
    # Run detection
    detections = net.Detect(img, width, height)
    
    # Convert to OpenCV BGR for optional overlay or saving
    frame_bgr = jetson_to_cv2(img, width, height)
    
    # Draw detections using OpenCV (optional)
    for det in detections:
        left = int(det.Left)
        top = int(det.Top)
        right = int(det.Right)
        bottom = int(det.Bottom)
        label = net.GetClassDesc(det.ClassID)
        cv2.rectangle(frame_bgr, (left, top), (right, bottom), (0,255,0), 2)
        cv2.putText(frame_bgr, label, (left, top-5), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0,255,0), 2)
    
    # Show OpenCV window (optional)
    cv2.imshow("YOLOv8 Detection", frame_bgr)
    if cv2.waitKey(1) & 0xFF == 27:  # ESC to quit
        break
    
    # Render display via Jetson OpenGL
    display.RenderOnce(img, width, height)
    display.SetTitle("Jetson Camera YOLO")

# Cleanup
cv2.destroyAllWindows()
display.Close()
