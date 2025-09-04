import cv2

def try_pipeline(pipeline, use_gst=True):
    print("\n🔍 Trying:", pipeline)
    if use_gst:
        cap = cv2.VideoCapture(pipeline, cv2.CAP_GSTREAMER)
    else:
        cap = cv2.VideoCapture(pipeline)

    if not cap.isOpened():
        print("❌ Could not open this source.")
        return False

    ret, frame = cap.read()
    if not ret or frame is None:
        print("❌ Opened but could not read frame.")
        cap.release()
        return False

    print(f"✅ Frame captured from {pipeline}, shape={frame.shape}")
    filename = f"test_frame_{'gst' if use_gst else 'v4l2'}.jpg"
    cv2.imwrite(filename, frame)
    print(f"💾 Saved {filename}")

    cv2.imshow("Camera Test", frame)
    cv2.waitKey(3000)  # show 3 sec
    cap.release()
    cv2.destroyAllWindows()
    return True


# Candidate pipelines
gst_pipeline = (
    "nvarguscamerasrc ! "
    "video/x-raw(memory:NVMM), width=1280, height=720, framerate=30/1, format=NV12 ! "
    "nvvidconv ! video/x-raw, format=BGRx ! "
    "videoconvert ! video/x-raw, format=BGR ! appsink drop=True"
)

# Step 1: Try nvarguscamerasrc
if not try_pipeline(gst_pipeline, use_gst=True):
    # Step 2: Fallback to /dev/video0 (plain V4L2)
    try_pipeline(0, use_gst=False)
