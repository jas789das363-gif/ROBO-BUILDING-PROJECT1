v4l2-ctl -d /dev/video0 --stream-mmap --stream-count=1 --stream-to=frame.jpg


xdg-open frame.jpg



gst-launch-1.0 nvarguscamerasrc ! nvoverlaysink



sudo systemctl restart nvargus-daemon


gst-launch-1.0 nvarguscamerasrc ! nvoverlaysink
