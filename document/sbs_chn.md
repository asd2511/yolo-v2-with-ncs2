# 使用ncs2 运行YOLO V3





# 安装openVINO toolkit
代码来自[Inter](https://software.intel.com/en-us/articles/OpenVINO-Install-RaspberryPI)
## 步骤 1： 在树莓派中安装raspbian的镜像
具体过程跳过<br>
测试运行版本：[2018-11-13-raspbian-stretch.img](https://downloads.raspberrypi.org/raspbian/images/raspbian-2018-11-15/2018-11-13-raspbian-stretch.zip.torrent)

## 步骤 2 ：下载解压openVINO toolkit
 ```shell

cd 

wget https://download.01.org/openvinotoolkit/2018_R5/packages/l_openvino_toolkit_ie_p_2018.5.445.tgz

tar -xf l_openvino_toolkit_ie_p_2018.5.445.tgz

sed -i "s|<INSTALLDIR>|$(pwd)/inference_engine_vpu_arm|" inference_engine_vpu_arm/bin/setupvars.sh

```

## 步骤 3 ： 设置环境变量，并将其设为默认路径

```shell
source inference_engine_vpu_arm/bin/setupvars.sh
```
设置默认路径：
 ```shell
vi ~/.bashrc
```
将下面一行添加进bashrc文件 <br>
'source ~/inference_engine_vpu_arm/bin/setupvars.sh'

保存并退出，打开新的terminal，应当回看到下面文字。


![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/terminal.png)  

## 步骤 4 ：添加usb规则
```shell
sudo usermod -a -G users "$(whoami)"

sh inference_engine_vpu_arm/install_dependencies/install_NCS_udev_rules.sh

```
## 步骤 5 ：运行对象检测示例（包括 OpenCV安装）

```shell
cd inference_engine_vpu_arm/deployment_tools/inference_engine/samples

mkdir build && cd build

cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-march=armv7-a"

make -j2 object_detection_sample_ssd

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.bin

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.xml

./armv7l/Release/object_detection_sample_ssd -m face-detection-adas-0001.xml -d MYRIAD -i <path_to_image>
```
其中，<path_to_image>需要替换。选择的image应当是一张人像照片（至少有一张脸）

## 步骤  6 ： 使用OpenCV * API运行人脸检测模型（python下运行Opencv示例）
### 检测OpenCV是否正确安装）
### 6.1 新建test文件夹，并下载权重bin，xml文件
```shell
cd

mkdir test

cd test

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.bin

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.xml

```
### 6.2 导入被测照片，创建python程序
将人像照片导入该目录（/test）<br>
运行
```shell
    nano test.py
```

并将下方python程序粘贴
```python
import cv2 as cv

# Load the model 
net = cv.dnn.readNet('face-detection-adas-0001.xml', 'face-detection-adas-0001.bin') 

# Specify target device 
net.setPreferableTarget(cv.dnn.DNN_TARGET_MYRIAD)
      
# Read an image 
frame = cv.imread('/path/to/image')
      
# Prepare input blob and perform an inference 
blob = cv.dnn.blobFromImage(frame, size=(672, 384), ddepth=cv.CV_8U) 
net.setInput(blob) 
out = net.forward()
      
# Draw detected faces on the frame 
for detection in out.reshape(-1, 7): 
    confidence = float(detection[2]) 
    xmin = int(detection[3] * frame.shape[1]) 
    ymin = int(detection[4] * frame.shape[0]) 
    xmax = int(detection[5] * frame.shape[1]) 
    ymax = int(detection[6] * frame.shape[0])

    if confidence > 0.5:
        cv.rectangle(frame, (xmin, ymin), (xmax, ymax), color=(0, 255, 0))

# Save the frame to an image file 
cv.imwrite('out.png', frame) 
```
注意：('/path/to/image')路径需要替换

### 6.4 运行python程序
```shell
python3 test.py
```
在根目录中，找到新创建的out.png <br>
我的例子
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/out.png)  

# OpenVINO-YoloV3
代码来自[PINTO0309](https://github.com/PINTO0309)

## 步骤 1：克隆git仓库
```shell
cd 
git clone https://github.com/PINTO0309/OpenVINO-YoloV3
```
## 步骤 2 ： 下载相应YOLOV3的准备文件
### bin 与 xml文件
```shell
cd /home/pi/OpenVINO-YoloV3/lrmodels/YoloV3/FP16 
sh /home/pi/OpenVINO-YoloV3/lrmodels/YoloV3/FP16/download_yolov3lrFP16.sh

```
### pbmodels 文件
```shell
cd /home/pi/OpenVINO-YoloV3/pbmodels
sh /home/pi/OpenVINO-YoloV3/pbmodels/download_yolov3.sh

```
### weights 文件
```shell
cd /home/pi/OpenVINO-YoloV3/weights
sh /home/pi/OpenVINO-YoloV3/weights/download_yolov3_weights.sh

```
## 步骤 3 ： 运行python文件
```shell
cd /home/pi/OpenVINO-YoloV3/

python3 openvino_yolov3_MultiStick_test.py -numncs 1
```

结果如图所示：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/person.png)  
