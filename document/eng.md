# Run YOLO V3 with ncs2





# Install the openVINO toolkit
## Step 1: Install the raspbian image in the Raspberry Pi
Skip the specific process<br>
Runing test version：[2018-11-13-raspbian-stretch.img](https://downloads.raspberrypi.org/raspbian/images/raspbian-2018-11-15/2018-11-13-raspbian-stretch.zip.torrent)

## Step 2: Download and extract the openVINO toolkit
 ```shell

cd 

wget https://download.01.org/openvinotoolkit/2018_R5/packages/l_openvino_toolkit_ie_p_2018.5.445.tgz

tar -xf l_openvino_toolkit_ie_p_2018.5.445.tgz

sed -i "s|<INSTALLDIR>|$(pwd)/inference_engine_vpu_arm|" inference_engine_vpu_arm/bin/setupvars.sh

```

## Step 3: Set the environment variable and set it as the default path

```shell
source inference_engine_vpu_arm/bin/setupvars.sh
```
Set the default path:
 ```shell
vi ~/.bashrc
```
Add the following line to the bashrc file <br>
```shell
source ~/inference_engine_vpu_arm/bin/setupvars.sh
```
Save and exit, open a new terminal, you should see the following text.


![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/terminal.png)  

## Step 4: Add the usb rule
```shell
sudo usermod -a -G users "$(whoami)"

sh inference_engine_vpu_arm/install_dependencies/install_NCS_udev_rules.sh

```
## Step 5: Run object detection example (including OpenCV installation)

```shell
cd inference_engine_vpu_arm/deployment_tools/inference_engine/samples

mkdir build && cd build

cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-march=armv7-a"

make -j2 object_detection_sample_ssd

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.bin

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.xml

./armv7l/Release/object_detection_sample_ssd -m face-detection-adas-0001.xml -d MYRIAD -i <path_to_image>
```
Where <path_to_image> needs to be replaced. The selected image should be a portrait photo (at least one face)

## Step 6: Run the face detection model using the OpenCV * API (Run the Opencv example in Python)
### Check if OpenCV is installed correctly
### 6.1 Create a new test folder and download the weight bin, xml file
```shell
cd

mkdir test

cd test

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.bin

wget --no-check-certificate https://download.01.org/openvinotoolkit/2018_R4/open_model_zoo/face-detection-adas-0001/FP16/face-detection-adas-0001.xml

```
### 6.2 Import the measured photos and create a python program
Import portrait photos into this directory（/test）<br>
run
```shell
    nano test.py
```

And paste the python program below
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
Note: ('/path/to/image') path needs to be replaced

### 6.4 Running python program
```shell
python3 test.py
```
In the root directory, find the newly created 'out.png' <br>
My example：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/out.png)  

# OpenVINO-YoloV3
Code from[PINTO0309](https://github.com/PINTO0309)

## Step 1: Clone the git repository
```shell
cd 
git clone https://github.com/PINTO0309/OpenVINO-YoloV3
```
## Step 2: Download the preparation file for the corresponding YOLOV3
### Bin and xml file
```shell
cd /home/pi/OpenVINO-YoloV3/lrmodels/YoloV3/FP16 
sh /home/pi/OpenVINO-YoloV3/lrmodels/YoloV3/FP16/download_yolov3lrFP16.sh

```
### pbmodels file
```shell
cd /home/pi/OpenVINO-YoloV3/pbmodels
sh /home/pi/OpenVINO-YoloV3/pbmodels/download_yolov3.sh

```
### weights file
```shell
cd /home/pi/OpenVINO-YoloV3/weights
sh /home/pi/OpenVINO-YoloV3/weights/download_yolov3_weights.sh

```
## Step 3: Run the python file
```shell
cd /home/pi/OpenVINO-YoloV3/

python3 openvino_yolov3_MultiStick_test.py -numncs 1
```
The result is shown：
![](https://github.com/asd2511/yolo-v2-with-ncs2/blob/master/img/person.png)  
