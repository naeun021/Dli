1 week
======
jetson-nano setting
-------------------

# SD Card prepare
1. Download the Jetson Nano Developer Kit SD Card Image.
   - https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#write
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

# Boot 

- Connect the USB keyboard and mouse
- Connect Monitor
- Network Dongle
- Insert the microSD card into the slot on the underside of the Jetson Nano module.
- Connect your Micro-USB power supply 

# Setup

- Select system language, keyboard layout
- Connect Wi-Fi Network
- Time zone: Seoul
- Create username, password
     -name: dli , Password: dli
- Click all Continue, Next 

# Setup Hangul

- Terminal
```
sudo apt-get update
sudo apt-get install fcitx-hangul
reboot
```
- Setting -> Region& Language ->  Manage installed Language

- Applying changes 
- In Language Support, Change Keyboard input method system: fcitx
- Reboot the system
- 우상단 keyboard icon -> Configure
- Input Method Configuration - 하단 '+'
- Add input method - check 해제 Only Show Current Language
- search 'hangul' and click
- 



3week
=====
Headless Mode
-------
# 

Headless Mode 메모리가 모자라서 가상 땡겨와,  메모리 연결하면안나오고 검정화면, gui땡겨옴, 
141 를Headless Mode를 gui로 돌아옴
도커설치..? 136 docker설치 코랩으로 넘어가 (저번시간에 한거임)

```
dli@dli-desktop:~$ sudo docker run --runtime nvidia -it --rm --network host \
>     --memory=500M --memory-swap=4G \
>     --volume ~/nvdli-data:/nvdli-nano/data \
>     --volume /tmp/argus_socket:/tmp/argus_socket \
>     --device /dev/video0 \
>     nvcr.io/nvidia/dli/dli-nano-ai:v2.0.2-r32.7.1kr
[sudo] password for dli: 
allow 10 sec for JupyterLab to start @ http://192.168.137.221:8888 (password dlinano)
```
Classification ->Classification -> code cell 실행 
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
+카메라에 따라 usb, csi 카메라 선택
+category에 따라 이미지 수집
+epochs = 학습횟수 설정한 뒤 train
+prediction 값으로 분류


