import cv2

# Candidate pipelines (different resolutions, formats, and converters)
pipelines = [
    # 1280x720, NV12
    "nvarguscamerasrc ! video/x-raw(memory:NVMM), width=1280, height=720, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! videoconvert ! video/x-raw, format=BGR ! appsink drop=True",

    # 640x480, NV12
    "nvarguscamerasrc ! video/x-raw(memory:NVMM), width=640, height=480, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! videoconvert ! video/x-raw, format=BGR ! appsink drop=True",

    # 1920x1080, NV12
    "nvarguscamerasrc ! video/x-raw(memory:NVMM), width=1920, height=1080, format=NV12, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! videoconvert ! video/x-raw, format=BGR ! appsink drop=True",

    # Some Jetson setups need explicit I420 instead of NV12
    "nvarguscamerasrc ! video/x-raw(memory:NVMM), width=1280, height=720, format=I420, framerate=30/1 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! videoconvert ! video/x-raw, format=BGR ! appsink drop=True",

    # Fallback: plain VideoCapture(0) — sometimes works if V4L2 is exposed
    0
]

def test_pipeline(pipeline):
    print(f"\n🔍 Testing pipeline: {pipeline}\n")
    if isinstance(pipeline, int):
        cap = cv2.VideoCapture(pipeline)
    else:
        cap = cv2.VideoCapture(pipeline, cv2.CAP_GSTREAMER)

    if not cap.isOpened():
        print("❌ Failed to open with this pipeline.")
        return False

    ret, frame = cap.read()
    if not ret or frame is None:
        print("❌ Opened but could not read a frame.")
        cap.release()
        return False

    print("✅ SUCCESS! This pipeline works.")
    cv2.imshow("Camera Test", frame)
    cv2.waitKey(2000)  # Show for 2 seconds
    cap.release()
    cv2.destroyAllWindows()
    return True

# Try all pipelines until one succeeds
for p in pipelines:
    if test_pipeline(p):
        print(f"\n🎉 Working pipeline: {p}\n")
        break
else:
    print("\n🚨 No pipeline worked. Check camera connection or drivers.\n")
