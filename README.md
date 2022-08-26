# Nano-Yolov7-ESP32
How to setup and perform Yolov7 inference object detection on Jetson Nano, using ESP32 wifi camera.

______

## Notes
- Current versions of both Yolov5, and Yolov7 state that Python 3.7 or higher are required
- Jetson Nano struggles in using any Python version that is different than the installed ones (Python 3.6.9)
- In this project we are showing the lengthy setup required to run Yolov7 on the Nano, without changing from Python 3.6.9
- For this project you need a MicroSD card, Jetson Nano, and an <a href='https://www.amazon.com/ESP32-CAM-MB-Aideepen-ESP32-CAM-Bluetooth-Arduino/dp/B0948ZFTQZ/ref=sr_1_3?crid=2JZECRKP6T310&keywords=esp32+cam&qid=1661528295&sprefix=esp32+cam%2Caps%2C273&sr=8-3'>ESP-32 Camera Board</a>

____

# Installation
____

## First: Download and flash Jetpack 4.6 OS on 64+gb micro SD card

- Go to <a href = 'https://developer.nvidia.com/embedded/jetpack-sdk-46'>this page</a> and download the correct version of Jetpack for your nano
- Unzip the file
- Now download <a href='https://www.balena.io/etcher/'> Balena Etcher</a> which will allow you to flash the unzipped file onto your MicroSD card.
- Plug the MicroSD card into your Nano's MicroSD slot, and run the OS setup 

## Now: Create addition virtual swap memory to help with performance by running the following shell commands

``` 
# Disable ZRAM:
sudo systemctl disable nvzramconfig

# Create 4GB swap file
sudo fallocate -l 4G /mnt/4GB.swap
sudo chmod 600 /mnt/4GB.swap
sudo mkswap /mnt/4GB.swap

# Append the following line to /etc/fstab
sudo su
echo "/mnt/4GB.swap swap swap defaults 0 0" >> /etc/fstab
exit

# REBOOT!

```
---

After rebooting run the following shell commands:
```
sudo apt-get update
sudo apt-get upgrade
```

## Now we need to install the pip Python package manager by running the following in the shell 
```
wget https://bootstrap.pypa.io/get-pip.py

python3 get-pip.py
```
---

## Pull Pytorch for Jetson Nano from Nvidia, and install some dependencies before installing PyTorch
```
wget https://nvidia.box.com/shared/static/p57jwntv436lfrd78inwl7iml6p13fzh.whl -O torch-1.8.0-cp36-cp36m-linux_aarch64.whl

sudo apt-get install libopenblas-base libopenmpi-dev libomp-dev
```
## Now, in the same directory, pip install Cython and PyTorch

``` 
    python3 -m pip install Cython
    python3 -m pip install torch-1.7.0-cp36-cp36m-linux_aarch64.whl

```
<i>Note: Installing PyTorch can take between 15-30 minutes</i>
---

## Now check to see if PyTorch is installed, with GPU enabled

```
python3
>>> import torch
>>>torch.cuda.is_available()
```
## If we get True, we can move on to the TorchVision Installation
<i> Before, we need to install some more linux dependencies</i>
```
sudo apt-get install libjpeg-dev zlib1g-dev libpython3-dev libavcodec-dev libavformat-dev libswscale-dev
```
## Now we need to clone the torchvision repo, using v0.9.0 because it is compatible with PyTorch v1.8, which we just installed

``` 
git clone --branch v0.9.0 https://github.com/pytorch/vision torchvision

cd torchvision

python3 setup.py install
```
<i>Note: Torchvision can take up to 40 minutes to install</i>

---
## Now, we install some linux and python packages, and set the an environment variable:


```
sudo apt install libfreetype6-dev
export OPENBLAS_CORETYPE=ARMV8 python3
sudo apt-get install python3-seaborn

sudo apt-get install python3-pil
python3 -m pip install future
pip3 install 'pillow<7'
pip3 install tdqm

```

## Now that we have setup and installed all dependencies required to run Yolov7, clone the repo

```
git clone https://github.com/WongKinYiu/yolov7

cd yolov7
```
---

# Before running Yolov7's inference, we must setup the ESP32 camera:

- On a different machine, install <a href='https://www.arduino.cc/en/software'>Arduino IDE</a>
- Now clone this repository, that has the code we need to flash the ESP32
``` 
git clone https://github.com/arkhipenko/esp32-cam-mjpeg.git
cd esp32-cam-mjpeg
```
## Before flashing, open and setup ArduinoIDE so that it is compatible with our board
- Under the file dropdown menu, to preferences, and paste this link in the field that says 'Additional Board Manager URLs'
https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json
- Now click on tools, Board, and then Board Manager
- Search for esp32 in the search bar, and install the first package that appears
- Plug in your ESP32 cam via microUSB to your computer
- Click on Tools, the click on Board, and select 'AI Thinker ESP32-CAM'
- Click on Tools, then choose any available Ports
- Open a terminal and change directories to the esp32-cam-mjpeg repo we just downloaded 
- Open esp32_camera_mjpeg.ino file with Arduino IDE
- In the file, comment out the line that says 
> #define CAMERA_MODEL_ESP_EYE
- And uncomment the line that says 
> //#define CAMERA_MODEL_AI_THINKER
- In the same directory make a file called "home_wifi_multi.h", drop in the following code, and save. Replace the strings with your Wifi station name, and password

```
#define SSID1 "replace with your wifi ssid"
#define PWD1 "replace your wifi password"
```
- Now on ArduinoIDE upload the code, and open the Serial Monitor, setting it to 115200 baud
- Press the reset button on the ESP32 cam, and you should see an output similar to the following:
```
192.168.1.42
Stream Link: http://192.168.1.42/mjpeg/1
```
- This is the stream link we are going to use on the Nano, with Yolov7
---

## On the Jetson Nano, change directories to the yolov7 repo we cloned earlier

- Run the following command

```
python3 detect.py --source http://192.168.1.42/mjpeg/1
```
<i>Obviously, replace this stream link with the one you obtained from ArduinoIDE</i>

## Now, we have run Yolov7 inference object detection on the Jetson Nano, using the ESP32 wifi camera.

- The following is a video capture of me running Yolov7 on my Jetson Nano
https://www.youtube.com/watch?v=IZY73M8Pzbk







