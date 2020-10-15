# PaddlePaddle-Jetson
Source compile PaddlePaddle framework for NVIDIA jetson devices. 
Jetson AGX Xavier is used to create this instruction.

## prerequisite
- JetPack 4.3 (JetPack 4.4 comes with Cuda 10.2 and cuDNN 8, which might not be compatbile for PaddlePaddle.)


## install dependencies
```
sudo apt-get install gcc g++ make cmake git vim unrar python3 python3-dev python3-venv python3-pip swig wget patchelf libopencv-dev 
sudo apt-get install libfreetype6-dev libjpeg-dev zlib1g-dev liblapack-dev  gfortran libpng-dev  

sudo pip3 install virtualenv cython
# sudo pip3 install numpy protobuf wheel setuptools 
```

## maximum number of files can be opened
```
ulimit -n 4096 #最大的文件打开数量
```

## install NCCL
```
git clone https://github.com/NVIDIA/nccl.git
cd nccl
make -j4
sudo make install
```

## 添加虚析构函数
在/usr/include/aarch64-linux-gnu/NvInferRuntime.h中Class IPluginFactory里添加虚析构函数:
```
virtual ~IPluginFactory() {};
```

## create virtual environment
```
mkdir my-agx-project && cd my-agx-project
python3 -m venv name-of-env 
# opencv is installed with JetPack on jetson devices, you only need to link it to your virtual environment to use it
# path may vary and need to be changed if necessary
# ln -s /usr/lib/python3.6/dist-packages/cv2/python-3.6/cv2.cpython-36m-aarch64-linux-gnu.so（本机cv2） path-to-venv/name-of-venv/lib/python3.6/site-packages/cv2.cpython-36m-aarch64-linux-gnu.so 
source name-of-env/bin/activate
pip3 install cython wheel numpy scikit-build
```

## compile
```
git clone https://github.com/paddlepaddle/paddle
cd paddle
git tag  # find the version to compile, I used v2.0.0-beta0
git checkout v2.0.0-beta0
pip3 install -r python/requirements.txt
mkdir build && cd build
cmake .. \
          -DWITH_CONTRIB=OFF \
          -DWITH_MKL=OFF  \
          -DWITH_MKLDNN=OFF \
          -DWITH_TESTING=OFF \
          -DCMAKE_BUILD_TYPE=Release \
          -DON_INFER=ON \
          -DWITH_PYTHON=ON \
          -DWITH_XBYAK=OFF  \
          -DWITH_NV_JETSON=ON \
          -DPY_VERSION=3 \
          -DCUDA_ARCH_NAME=Auto
make TARGET=ARMV8 -j4  # agx-xavier has 8 cores, to accelerate compile process we can pass parameter of -j8. Be patient and this process might take a few hours
make inference_lib_dist
pip3 install python/dist/*.whl  #还是在build文件夹
```

## test demo
- [Object detection with YOLOv3](https://github.com/PaddlePaddle/Paddle-Inference-Demo/tree/master/python/yolov3)
- [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR/blob/develop/doc/doc_ch/models_list.md)

## Reference
- https://my.oschina.net/u/4375893/blog/4369069
