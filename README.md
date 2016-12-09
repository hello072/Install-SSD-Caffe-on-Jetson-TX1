Install-Tensorflow-and-Caffe-on-Jetson-TX1
========
##JetPack2.3을 설치한 TX1에 SSD(Single Shot Detector) Caffe 설치하기!
--------------
JetPack의 도움으로 ubuntu16.04,cuda8, cudnn5.1 등이 설치되었다고 가정합니다.

각 단계마다 시간이 다소 오래 걸릴 수 있으니 인내심을 가지세요!

편의를 위해 Caffe를 받을 경로를 HOMEPATH로 하겠습니다.

다음 명령어를 통해 필요한 패키지를 설치합니다.

이미 설치되어 있는 것들이 있을 수 있으니 뺄 것은 빼세요! (OpenCV나 OpenCV4Tegra 등)
<pre><code>sudo apt-get install build-essential-y # necessary compiler tool dependent
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
</code></pre>
다음은 파이썬 관련 설치 과정입니다. 개인적 판단에 따라 부분 생략하셔도 됩니다.
<pre><code>wget --no-check-certificate https://bootstrap.pypa.io/ez_setup.py 
sudo python ez_setup.py --insecure 
wget https://bootstrap.pypa.io/get-pip.py 
sudo python get -pip.py
sudo apt-get install libblas-dev liblapack-dev libatlas-base-dev gfortran python-numpy
</code></pre>
다음은 SSD Caffe를 받고, pycaffe 관련 패키지를 설치하는 과정입니다.
<pre><code>git clone https://github.com/weiliu89/caffe.git
cd HOMEPATH/caffe/
git checkout ssd
cd python/
sudo su
for req in $(cat "requirements.txt"); do pip install -i https://pypi.tuna.tsinghua.edu.cn/simple $ req; done 
</code></pre>
Ctrl + Shift + d 를 통해 su모드에서 빠져나옵니다.
다음 명령어를 통해 Makefile.config을 복사하고, 에디터로 엽니다.
<pre><code>cd HOMEPATH/caffe
cp Makefile.config.example Makefile.config
vi Makefile.config
</code></pre>
아래 주석을 지웁니다.
<pre><code># USE_CUDNN := 1
</code></pre>
BLAS로 atlas를 사용하도록 수정합니다.
<pre><code>BLAS := atlas
</code></pre>
아래 두 줄에 경로를 각각 추가하여 그 아래 두 줄처럼 수정합니다.
<pre><code>INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib

INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/aarch64-linux-gnu/hdf5/serial/
</code></pre>
다음 명령어를 통해 Makefile을 에디터로 엽니다.
<pre><code>cd HOMEPATH/caffe
vi Makefile
</code></pre>
아래 줄을 그 아래 줄처럼 수정합니다.
<pre><code>NVCCFLAGS += -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS) 

NVCCFLAGS += -D_FORCE_INLINES -ccbin=$(CXX) -Xcompiler -fPIC $(COMMON_FLAGS)
</code></pre>
다음 명령어를 통해 hdf5 관련 링크를 걸어줍니다.
<pre><code>cd /usr/lib/aarch64-linux-gnu/
sudo ln libhdf5_serial.so.10.1.0 libhdf5.so
sudo ln libhdf5_serial_hl.so.10.0.2 libhdf5_hl.so
sudo ldconfig # Enable the connection to take effect
</code></pre>
다음 명령어를 통해 Caffe를 컴파일 합니다.

-j4 옵션을 사용하라는 분들도 있지만 저는 남는 것이 시간이라 그냥 3으로 했습니다.
<pre><code>cd HOMEPATH/caffe
make all -j3
make test -j3
make runtest -j3
make pycaffe -j3
</code></pre>
다음 명령어를 통해 caffe가 import되는지 확인합니다.
<pre><code>cd HOMEPATH/caffe/ python
python
import caffe
</code></pre>
