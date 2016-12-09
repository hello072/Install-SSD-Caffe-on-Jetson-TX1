Install-Tensorflow-and-Caffe-on-Jetson-TX1
========
##JetPack2.3을 설치한 TX1에 SSD(Single Shot Detector) Caffe 설치하기!
--------------
JetPack의 도움으로 ubuntu16.04,cuda8, cudnn5.1 등이 설치되었다고 가정합니다.

편의를 위해 주 작업장 /home/ubuntu/Downloads 를 HOMEPATH로 하겠습니다.


<pre><code>sudo apt-get install build-essential-y # necessary compiler tool dependent
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
sudo apt-get install --no-install-recommends libboost-all-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
</code></pre>
