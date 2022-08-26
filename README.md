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





