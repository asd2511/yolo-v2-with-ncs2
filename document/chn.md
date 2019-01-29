# 如何从零开始使用YOLO V3 with ncs2



### 步骤 1：在树莓派中安装raspbian图像
跳过具体过程<br>
运行测试版本为[2018-11-13-raspbian-stretch.img](https://downloads.raspberrypi.org/raspbian/images/raspbian-2018-11-15/2018-11-13-raspbian-stretch.zip.torrent)<br>
### 步骤2：克隆yolo-v3-with-ncs2
 ```shell

cd 

git clone https://github.com/asd2511/yolo-v3-with-ncs2


```

### Step 3: 运行以下shell指令完成OpenVINO toolkit设置
#### Step 3.1：运行 
```shell
sed -i “ s | <INSTALLDIR> | $（ pwd ） / inference_engine_vpu_arm | ” inference_engine_vpu_arm / bin / setupvars.sh

source inference_engine_vpu_arm/bin/setupvars.sh
```

#### Step3.2设置默认的`path`：
 ```shell
vi ~/.bashrc
```
#### 步骤3.3将以下行添加到`bashrc`文件中

```shell
source ~/inference_engine_vpu_arm/bin/setupvars.sh
```
Save and exit, open a new terminal, you should see the following text.

![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/terminal.png)  

#### 步骤3.4：添加USB规则
```shell
sudo usermod -a -G users "$(whoami)"

sh yolo-v3-with-ncs2/inference_engine_vpu_arm/install_dependencies/install_NCS_udev_rules.sh

```
#### 步骤4：运行对象检测示例（包括`OpenCV`安装）

```shell
cd yolo-v3-with-ncs2/inference_engine_vpu_arm/deployment_tools/inference_engine/samples

mkdir build && cd build

cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-march=armv7-a"

make -j2 object_detection_sample_ssd

./armv7l/Release/object_detection_sample_ssd -m face-detection-adas-0001.xml -d MYRIAD -i ~/yolo-v3-with-ncs2/test/hp.jpeg
```

#### 步骤5：使用OpenCV * API运行人脸检测模型（在Python中运行Opencv示例）
#### 检查OpenCV是否安装正确

```shell

cd 

python3 yolo-v3-with-ncs2/test/test.py
```


在`/test`目录中，找到新创建的out.png <br>
我的例子：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/out.png)  



### OpenVINO-YoloV3
Code by [PINTO0309](https://github.com/PINTO0309)

#### 运行python文件
```shell
cd ~/yolo-v3-with-ncs2/OpenVINO-YoloV3/

python3 openvino_yolov3_MultiStick_test.py -numncs 1
```
结果如下：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/person.png)  
