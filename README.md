1 week
======
# jetson-nano setting
## SD Card prepare
1. Download the Jetson Nano Developer Kit SD Card Image. [SD Card Image](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit#write, "sd card image link")
2. Format microSD card using SD Memory Card Formatter from the SD Association.
   - Download SD Memory Card Formatter. [Formatter](https://www.sdcard.org/downloads/formatter/sd-memory-card-formatter-for-windows-download/, "formatter link")
   - Select card drive
   - Select “Quick format”
   - Leave “Volume label” blank
   - Click “Format” to start formatting, and “Yes” on the warning dialog
3. Write the image to microSD card 
   - Download [Etcher](https://www.balena.io/etcher, "etcher link")
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
- Right wing keyboard icon -> Configure
- Input Method Configuration -  bottom '+'
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
## Camera motion
1. Image capture
```
$ nvgstcapture-1.0 --camsrc=0 --cap-dev-node=/dev/video0
#Camera running
j   #j Enter
image Captured
```
![KakaoTalk_20241114_214419656_02](https://github.com/user-attachments/assets/c3d1d9fe-dd9b-4add-b2e8-9e5ae608b37e)
2. Face recognition

3week
=====
# Image Classification
## Docker
Docker streamlines the development lifecycle by allowing developers to work in standardized environments using local containers which provide your applications and services. [Docker](https://docs.docker.com/get-started/docker-overview/, "docker link")   
*->Docker allows you to run one coding from Jupiter on jetson*

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
![KakaoTalk_20241126_000218340](https://github.com/user-attachments/assets/71e7bf1e-93aa-4ac2-a4b0-4811feb0fe17)
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
   ### CSI camera error
CSI camera execution error in code cell
-> Replaced with USB camera, solved by rebooting.

3. Collect Initial Data
![KakaoTalk_20241126_000218340_01](https://github.com/user-attachments/assets/6e867213-992a-406c-b4bb-c605780bc64d)
+ Collecting images that fit the category (add)
+ epochs = train after setting the number of learning times
+ Classified by prediction value


Next week
----
# Breadboard
![image](https://github.com/user-attachments/assets/f3e61c03-0169-4026-92e8-f36ecb902e5b)
1. GND Connection
   - Ensure proper grounding for your circuit by connecting GND appropriately.
2. Power Rails
   - Red and Blue Lines: These represent the power rails (typically 5V).
      - Power flows horizontally along these lines.
   - Mini Breadboards: Mini versions do not have dedicated red and blue power rails.
3. Signal Rows
   - The center rows are isolated until connected by jumper wires.
   - When a jumper wire is connected to a signal row, the entire horizontal row becomes electrically connected.
4. Jumper Wires
   - Exposed Ends: Use male jumper wires for connections where pins are visible.
   - No Exposed Ends: Use female jumper wires for connecting components without exposed pins.

# Arduino Uno
![image](https://github.com/user-attachments/assets/7e0adcea-5b2c-4e76-8d54-3e161bdf2747)
### Jetson Nano
- Provides voltage and current for components.
- Used to process analog values with Arduino Uno.
### Arduino Uno Rev3
- Official board from Arduino.cc.
- Key features:
   - Analog Input: Reads analog values.
   - Communication Pins:
      - Pins 4, 5: General communication.
      - Pins 0, 1: Serial communication.
   - Digital Pins: 0–13 for various uses.
   - Power Pins: VIN (external power), GND (x2), 5V, 3.3V.
 
## Arduino down on Jetson Nano sd card
```
sudo apt-get update
sudo apt-get install arduino
```

4 week
======

# Arduino Setup via Terminal

## **Light Sensor and LED Control**  
- **LED_BUILTIN**: Defined as an output (commonly connected to pin **13**).  
- **void loop**:  
  - The computer processes binary (0s and 1s), so the LED state is defined as **HIGH** or **LOW**.  
- **Delay Function**:  
  - `delay(100)` controls the blink speed. Increasing the value slows down the blinking.
 ![KakaoTalk_20241130_222648357](https://github.com/user-attachments/assets/19430c6d-ddc3-4c61-9fd2-e9b4e340a673)
Here’s an updated summary incorporating the LED pin connection:

## **LED Pin Connection**  
- Connect the **long leg** of the LED to **pin 12** and the **short leg** to **GND**.  

## **Code Setup**  
- Define the LED pin:  
  ```cpp
  int led = 12;
  ```  
- Set it as an output:  
  ```cpp
  pinMode(led, OUTPUT);
  ```
# Arduino Setup and Dust Sensor Output
## **Setup**   
- Set port: **COM1** or **COM2**.  
## **Wiring**  
- Black: **GND**  
- Red: **5V**  
- Yellow: Data (e.g., pin **2**)  
![KakaoTalk_20241130_222648357_01](https://github.com/user-attachments/assets/b54b6693-fa36-4522-a86f-008717a1dff6)
## **Dust Sensor Output**  
1. **a: LPO Time** - Total low pulse signal time during measurement.  
2. **b: Ratio** - Proportion of LPO time to total sampling time.  
3. **c: Concentration** - Calculated particle concentration (**μg/m³**).
```
int pin = 8;
unsigned long duration;
unsigned long starttime;
unsigned long sampletime_ms = 30000;  // 30초 동안 샘플링
unsigned long lowpulseoccupancy = 0;
float ratio = 0;
float concentration = 0;

void setup()
{
    Serial.begin(9600);
    pinMode(pin, INPUT);
    starttime = millis();
    Serial.println("미세먼지 측정을 시작합니다...");
    Serial.println("==============================");
}

void loop()
{
    duration = pulseIn(pin, LOW);
    lowpulseoccupancy = lowpulseoccupancy + duration;

    if ((millis()-starttime) > sampletime_ms)  // 30초마다 측정
    {
        ratio = lowpulseoccupancy/(sampletime_ms*10.0);
        concentration = 1.1*pow(ratio,3)-3.8*pow(ratio,2)+520*ratio+0.62; // ug/m3 단위

        Serial.println("==============================");
        Serial.print("미세먼지 농도: ");
        Serial.print(concentration);
        Serial.println(" ug/m3");

        // 대기질 상태 표시
        Serial.print("대기질 상태: ");
        if(concentration <= 30) {
            Serial.println("좋음");
        }
        else if(concentration <= 80) {
            Serial.println("보통");
        }
        else if(concentration <= 150) {
            Serial.println("나쁨");
        }
        else {
            Serial.println("매우 나쁨");
        }

        Serial.println("------------------------------");
        Serial.print("측정 시간: ");
        Serial.print(millis()/1000);
        Serial.println("초");
        Serial.println("==============================\n");

        // 다음 측정을 위한 초기화
        lowpulseoccupancy = 0;
        starttime = millis();
    }
}
```
![KakaoTalk_20241130_222648357_02](https://github.com/user-attachments/assets/a898ed96-d3b1-40bc-b6ea-ae7c0b64653e)

