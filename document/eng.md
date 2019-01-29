
# How to use YOLO V3 with ncs2 from scratch



### Step 1: Install the raspbian image in the Raspberry Pi

Skip the specific process<br>
Runing test version：[2018-11-13-raspbian-stretch.img](https://downloads.raspberrypi.org/raspbian/images/raspbian-2018-11-15/2018-11-13-raspbian-stretch.zip.torrent)


### Step 2: Clone yolo-v3-with-ncs2
 ```shell

cd 

git clone https://github.com/asd2511/yolo-v3-with-ncs2


```
### Step 3: Run the following shell command to complete the OpenVINO toolkit setup.
#### Step 3.1： run

```shell
sed -i “ s | <INSTALLDIR> | $（ pwd ） / inference_engine_vpu_arm | ” inference_engine_vpu_arm / bin / setupvars.sh

source inference_engine_vpu_arm/bin/setupvars.sh
```


#### Step3.2 Set the default `path`:
 ```shell
vi ~/.bashrc
```

#### Step 3.3 Add the following line to the `bashrc` file <br>

```shell
source ~/inference_engine_vpu_arm/bin/setupvars.sh
```
Save and exit, open a new terminal, you should see the following text.

![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/terminal.png)  


#### Step 3.4: Add the USB rule
```shell
sudo usermod -a -G users "$(whoami)"

sh yolo-v3-with-ncs2/inference_engine_vpu_arm/install_dependencies/install_NCS_udev_rules.sh

```

#### Step 4: Run object detection example (including `OpenCV` installation)

```shell
cd yolo-v3-with-ncs2/inference_engine_vpu_arm/deployment_tools/inference_engine/samples

mkdir build && cd build

cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-march=armv7-a"

make -j2 object_detection_sample_ssd

./armv7l/Release/object_detection_sample_ssd -m face-detection-adas-0001.xml -d MYRIAD -i ~/yolo-v3-with-ncs2/test/hp.jpeg
```


#### Step 5: Run the face detection model using the OpenCV * API (Run the Opencv example in Python)
#### Check if OpenCV is installed correctly

```shell

cd 

python3 yolo-v3-with-ncs2/test/test.py
```




In `/test`, find the newly created out.png <br>

My example：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/out.png)  



### OpenVINO-YoloV3
Code by [PINTO0309](https://github.com/PINTO0309)


####  Run the python file
```shell
cd ~/yolo-v3-with-ncs2/OpenVINO-YoloV3/

python3 openvino_yolov3_MultiStick_test.py -numncs 1
```

The result is shown：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/person.png)  
