1 week
======
# jetson-nano setting
## SD Card prepare
1. Download the Jetson Nano Developer Kit [SD Card Image]. 
   (https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#write,"sd card image link")
2. Format microSD card using SD Memory Card Formatter from the SD Association.
   - Download SD Memory Card Formatter   
     https://www.sdcard.org/downloads/formatter/sd-memory-card-formatter-for-windows-download/
   - Select card drive
   - Select “Quick format”
   - Leave “Volume label” blank
   - Click “Format” to start formatting, and “Yes” on the warning dialog
3. Write the image to microSD card 
   - Download Etcher   
     https://www.balena.io/etcher
   - Click “Select image” and choose the zipped image file downloaded earlier.
   - Click “Select drive” and choose the correct device.
   - Click “Flash!” It will take Etcher about 10 minutes to write and validate the image

## Boot 

- Connect the USB keyboard and mouse
- Connect Monitor
- Network Dongle
- Insert the microSD card into the slot on the underside of the Jetson Nano module.
- Connect your Micro-USB power supply 

## Setup

- Select system language, keyboard layout
![KakaoTalk_20241107_205552851](https://github.com/user-attachments/assets/9d419246-d2a2-4f26-ac6f-22e1f7110cd8)
- Connect Wi-Fi Network
![KakaoTalk_20241107_205552851_01](https://github.com/user-attachments/assets/82377b0d-8ee7-47fb-8df1-e275c9e09666)
- Time zone: Seoul
![KakaoTalk_20241107_205552851_03](https://github.com/user-attachments/assets/d52b921c-72c5-443a-bf82-cd1c672c9a00)
- Create username, password
     -name: dli , Password: dli
- Click all Continue, Next
![KakaoTalk_20241107_205552851_06](https://github.com/user-attachments/assets/deaa7488-0165-445c-aa28-e9309353a83b)
![KakaoTalk_20241107_205552851_07](https://github.com/user-attachments/assets/11a68cc5-2f1a-43a6-8140-990fa9c0f4c4)

## Setup Hangul

- Terminal
```
$ sudo apt-get update
$ sudo apt-get install fcitx-hangul
$ reboot
```
- Setting -> Region& Language ->  Manage installed Language

- Applying changes 
- In Language Support, Change Keyboard input method system: fcitx
- Reboot the system
- 우상단 keyboard icon -> Configure
- Input Method Configuration - 하단 '+'
- Add input method - check 해제 Only Show Current Language
- search 'hangul' and click


2week
=====
# Camera
## csi-camera
1. Camera setting
```
#Check camera operation
$ ls /dev/vi*
/del/video0

$git clone https://github.com/jetsonhacks/CSI-Camera.git
$ls
Desktop    Downloads         jetson-fan-ctl  Pictures  Templates   Videos
Documents  examples.desktop  Music           Public    CSI-Camera

$ cd CSI-Camera
$ ls
face-detect-csi.py  LICENSE  README.md  csi-camera-gst.py  csi-camera-simple.py
```
2. Open the camera
```
python3 csi-camera-gst.py
```
-카메라 켜진 사진
## 카메라 동작
1. Image capture
```
$ nvgstcapture-1.0 --camsrc=0 --cap-dev-node=/dev/video0
#Camera running
j   #j Enter
image Captured
```
![KakaoTalk_20241114_214419656_02](https://github.com/user-attachments/assets/c3d1d9fe-dd9b-4add-b2e8-9e5ae608b37e)
2. 얼굴 인식

3week
=====
# Image Classification
## Docker
Docker streamlines the development lifecycle by allowing developers to work in standardized environments using local containers which provide your applications and services. [Docker](https://docs.docker.com/get-started/docker-overview/,"docker link")   
*->Docker allows you to run one coding from Jupiter on jetson*
### Docker 주요 기능 (적을지 말지 고민)
1. ppt 126
2. ..

- Docker Install, Run 
```
$ mkdir -p ~/nvdli-data
$ #!/bin/bash
$ sudo docker run --runtime nvidia -it --rm --network host \
    --memory=500M --memory-swap=4G \
    --volume ~/nvdli-data:/nvdli-nano/data \
    --volume /tmp/argus_socket:/tmp/argus_socket \
    --device /dev/video0 \
    nvcr.io/nvidia/dli/dli-nano-ai:v2.0.2-r32.7.1kr
$./docker_dli_run.sh
[sudo]password for dli: #dli
```
![KakaoTalk_20241126_000218340](https://github.com/user-attachments/assets/71e7bf1e-93aa-4ac2-a4b0-4811feb0fe17)
__Out of memory__   
jetson CPU is 4GB and can't run Jupiter -> 18GB swap
```
$sudo systemctl disable nvzramconfig
$sudo systemctl set-default multi-user.target
$sudo fallocate -l 18G /mnt/18GB.swap
$sudo chmod 600 /mnt/18GB.swap
$sudo mkswap /mnt/18GB.swap

$sudo su
echo "/mnt/18GB.swap swap swap defaults 0 0" >> /etc/fstab
exit

$sudo reboot
```
_Return to gui mode_
```
$sudo systemctl set-default graphical.target
$reboot
```
## Headless Mode
No need to connect the monitor directly to the Jetson Nano, save memory resources
```
#Run the Docker container 
sudo docker run --runtime nvidia -it --rm --network host \
    --memory=500M --memory-swap=4G \
    --volume ~/nvdli-data:/nvdli-nano/data \
    --volume /tmp/argus_socket:/tmp/argus_socket \
    --device /dev/video0 \
    nvcr.io/nvidia/dli/dli-nano-ai:v2.0.2-r32.7.1kr
```
- Execution Results 
```
Status: Downloaded newer image for nvcr.io/nvidia/dli/dli-nano-ai:v2.0.2-r32.6.1kr allow 10 sec for JupyterLab to start @ http://192.168.137.221:8888 (password dlinano)c932e62bbab: Waiting
JupterLab logging location:  /var/log/jupyter.log  (inside the container)
```
__http://192.168.137.221:8888__ <- Click link, 'open link' click


## Nupyter lab
### JupyterLab interface
![KakaoTalk_20241126_000218340_01](https://github.com/user-attachments/assets/6e867213-992a-406c-b4bb-c605780bc64d)

## Image Classification Project
Build an image classification project that can determine the meaning of hand signals (thumbs-up or thumbs-down)
1. Open the Notebook
JupyterLab interface: dashboard that provides access to the Jupyter interactive notebooks
- a directory tree in the left sidebar   
__Classification folder in JupyterLab interface -> Open ' classification_interactive.ipynb'__

2. Execute all of the code blocks
__Select USB, CSI camera according to camera type__
If camera type changes -> reboot
```
from jetcam.usb_camera import USBCamera
from jetcam.csi_camera import CSICamera

# for USB Camera (Logitech C270 webcam), uncomment the following line
camera = USBCamera(width=224, height=224, capture_device=0) # confirm the capture_device number

# for CSI Camera (Raspberry Pi Camera Module V2), uncomment the following line
# camera = CSICamera(width=224, height=224, capture_device=0) # confirm the capture_device number

camera.running = True
print("camera created")
```
   ### csi 카메라 오류
CSI camera execution error in next cell
```

```
-> Replaced with USB camera, solved by rebooting.

3. Collect Initial Data
![KakaoTalk_20241126_000218340_01](https://github.com/user-attachments/assets/6e867213-992a-406c-b4bb-c605780bc64d)
+category에 맞는 이미지 수집(add)
+epochs = 학습횟수 설정한 뒤 train
+prediction 값으로 분류


다음주
----
gnd-연결
선이 튀어 나옴 - 수전파선   
선이 없음 - 암전파선   
![image](https://github.com/user-attachments/assets/f3e61c03-0169-4026-92e8-f36ecb902e5b)
![beardbord](https://github.com/kimjome/Dli/issues/1#issue-2679251832.png)
빨간색 선과 파란색 선은 옆으로 흐름 5볼트   
가운데는 전기가 흐르지 않음 전파선 연결하면 가로로 한 줄 다 흐름   
미니판은 빨간색 파란색 선이 없음   

# 아두이노
젯슨나노볼트는 암페어   
Arduino.cc-Arduino Uno Rev3 사용   
젯슨에 선 연결해서 사용   
아날로그 값을 가져옴   
4번 5번이 통신을 주고 받을 때 씀   
0번 1번 시리얼 통신   
0번에서 13번까지 존재   
vin - 외부전원 들어올 수 있음   
gnd 2개 있음 마이너스(-)   
5볼트와 3.3볼트 있음   
sd카드에    
```
sudo apt-get update
sudo apt-get install arduino
```


