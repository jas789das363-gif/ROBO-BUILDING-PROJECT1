dpkg -l | grep nvidia-l4t



# Update apt
sudo apt update
sudo apt install python3-pip
sudo apt install python3-numpy python3-pycuda
sudo apt install libopencv-python libopencv-contrib-python

# Install Jetson Utils / Jetson Inference Python bindings
cd ~
git clone https://github.com/dusty-nv/jetson-inference.git
cd jetson-inference
git submodule update --init
mkdir build
cd build
cmake ../
make -j$(nproc)
sudo make install
sudo ldconfig


____________________________
python3
>>> import jetson.utils
>>> import jetson.inference
