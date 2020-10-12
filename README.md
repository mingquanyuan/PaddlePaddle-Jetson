# PaddlePaddle-Jetson
Source compile PaddlePaddle framework for NVIDIA jetson devices


## install dependencies
```
sudo apt-get install gcc g++ make cmake git vim unrar python3 python3-dev python3-pip swig wget patchelf libopencv-dev
sudo pip3 install numpy protobuf wheel setuptools virtualenv
```

## maximum number of files can be opened
```
ulimit -n 2048 #最大的文件打开数量
```

## create virtual environment
```
mkdir my-agx && cd my-agx
python3 -m venv name-of-env 
# opencv is installed with JetPack on jetson devices, you only need to link it to your virtual environment to use it
# path may vary and need to be changed if necessary
ln -s /usr/lib/python3.6/dist-packages/cv2/python-3.6/cv2.cpython-36m-aarch64-linux-gnu.so（本机cv2） path-to-venv/name-of-venv/lib/python3.6/site-packages/cv2.cpython-36m-aarch64-linux-gnu.so 
source name-of-env/bin/activate
pip3 install cython wheel numpy
```

## compile
```
git clone https://github.com/paddlepaddle/paddle
cd paddle
git tag  # find the version to compile, I used v2.0.0-beta0
git checkout v2.0.0-beta0
pip3 install -r python/requirements.txt
mkdir build && cd build
cmake ..  -DCMAKE_BUILD_TYPE=Release \
           -DWITH_CONTRIB=OFF \
           -DWITH_MKL=OFF \
           -DWITH_MKLDNN=OFF \
           -DWITH_TESTING=OFF \
           -DWITH_GPU=OFF \
           -DWITH_PYTHON=ON \
           -DPY_VERSION=3 \
           -DON_INFER=ON \
           -DWITH_XBYAK=OFF \
	            -DWITH_NV_JETSON=ON \
           -DWITH_ARM=ON
 make TARGET=ARMV8 -j8  # agx-xavier has 8 cores, to accelerate compile process we can pass parameter of -j8. Be patient and this process might take a few hours
```
