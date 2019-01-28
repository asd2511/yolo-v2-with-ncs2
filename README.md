# 如何在树莓派上使用Movidius（NCS V2）运行Tiny-YOLO-V2（使用MS-COCO训练的）


## 步骤 1： 在树莓派中安装raspbian的镜像
测试运行版本：2018-11-13-raspbian-stretch.img

## 步骤 2 ：在克隆github中 ncsdk2仓库
 ```shell

cd 

git clone -b ncsdk2 https://github.com/movidius/ncsdk.git

 ```
 
## 步骤 3 ：安装ncsdk2
 ```shell

cd ncsdk/

make install

 ```
 提示：在我使用的设备（raspberry 3B）中，该过程耗时大约30min
 
 ## 步骤 4 ：运行示例文件
 
 ```shell

make examples


 ```
 该步骤的作用是为了下载安装一些使用的数据库，比如OpenCV
 与上一步相同，该步骤耗时非常长
