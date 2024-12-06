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

# Setup Hangul

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
카메라
---
# 
csi-camera
```
# 카메라 인식 되는지 확인
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
- 기본 카메라 실행
```
python3 csi-camera-gst.py
```
-카메라 켜진 사진

1.이미지 capture
```
$ nvgstcapture-1.0 --camsrc=0 --cap-dev-node=/dev/video0
#실행
j   
image Captured
```
![KakaoTalk_20241114_214419656_02](https://github.com/user-attachments/assets/c3d1d9fe-dd9b-4add-b2e8-9e5ae608b37e)


3week
=====
Headless Mode
-------
# 
1.Docker 설치
```
#Add a data directory 
mkdir -p ~/nvdli-data
```
```
sudo docker run --runtime nvidia -it --rm --network host \
    --memory=500M --memory-swap=4G \
    --volume ~/nvdli-data:/nvdli-nano/data \
    --volume /tmp/argus_socket:/tmp/argus_socket \
    --device /dev/video0 \
    nvcr.io/nvidia/dli/dli-nano-ai:v2.0.2-r32.7.1kr
```
- 다운로드 사진
```
 ./docker_dli_run.sh
```
```python
Status: Downloaded newer image for nvcr.io/nvidia/dli/dli-nano-ai:v2.0.2-r32.6.1kr allow 10 sec for JupyterLab to start @ http://192.168.137.221:8888 (password dlinano)c932e62bbab: Waiting
JupterLab logging location:  /var/log/jupyter.log  (inside the container)
```

Headless Mode: 메모리가 모자라서 가상 땡겨와,  메모리 연결하면안나오고 검정화면, gui땡겨옴, 
141 를Headless Mode를 gui로 돌아옴
도커설치 136 docker설치 코랩으로 넘어감 (저번시간에 한거임)
```
sudo systemctl disable nvzramconfig

sudo systemctl set-default multi-user.target

sudo fallocate -l 18G /mnt/18GB.swap
sudo chmod 600 /mnt/18GB.swap
sudo mkswap /mnt/18GB.swap

sudo su
echo "/mnt/18GB.swap swap swap defaults 0 0" >> /etc/fstab
exit

sudo reboot
```
+잿슨의 CPU가 4GB라 주피터를 실행할 수 없으므로 18GB로 바꿔줌 

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
![KakaoTalk_20241126_000218340](https://github.com/user-attachments/assets/71e7bf1e-93aa-4ac2-a4b0-4811feb0fe17)
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
+카메라 종류에 따라 usb, csi 카메라 선택
+category에 맞는 이미지 수집(add)
+epochs = 학습횟수 설정한 뒤 train
+prediction 값으로 분류
![KakaoTalk_20241126_000218340_01](https://github.com/user-attachments/assets/6e867213-992a-406c-b4bb-c605780bc64d)

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

# Week 4
====
+ 터미널에서 아두이노에 들어가서 설정하기
1. 불빛 센서
- led_builtin은 output으로 정의함
- 보통 핀 번호 13에 연결되어 있음.
- void loop: 컴퓨터는 0.1로 이진법이므로 'high' or 'low'만으로 인식할 수 있음
- delay(100) : 괄호 안의 숫자를 늘리면 깜빡이는 시간이 느려짐
 ![KakaoTalk_20241130_222648357](https://github.com/user-attachments/assets/19430c6d-ddc3-4c61-9fd2-e9b4e340a673)
- led 핀의 긴 다리를 12번에 짧은 다리를 gnd에 연결하기
- led=12 로 하거나 pinMode(led, OUTPUT); 로 바꾸기
  
2. 설정
- tool을 열면 보드 이름과 코트 이름이 나옴 - 우리는 아두이노 우노
- 포트 : COM1, COM2 꼭 연결이 되어야 함

3. 기타
- 검정색은 그라운드 빨간색은 5볼트, 노란색은 데이터선(아무 번호에 넣아도 됨 2번 아님 3번)
- grove dust sonsor 를 데이터선 2번 아두이노 코드 알려달라고 gpt에 물어보기 또는 제조사에서 올려놓은 코드 찾아보기
- input으로 핀에서 데이터를 받아옴 -> 아두이노에서 센서값을 가져옴
- serial.begin(9600) : 통신속도가 9600이라는 뜻
- 미세먼지 센서 출력값(a,b,c)
![KakaoTalk_20241130_222648357_01](https://github.com/user-attachments/assets/b54b6693-fa36-4522-a86f-008717a1dff6)
- a: Lowpulseoccupancy (LPO time): 측정된 시간 동안 센서가 낮은 펄스 신호를 감지한 총 시간
- b: Ratio : LPO time이 전체 샘플링 시간에서 차지하는 비율
- c: concentration : LPO time을 기반으로 계산된 농도값 일반적으로 (μg/m³)

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

5 week
======

# Function calling
- **Function calling**: The process of calling a defined function (or method) to perform a particular task in programming
- How to use Function calling
- ## 1. Define a function
  input: fruit name, output : fruit's price
```
def get_fruit_price(fruit_name):
    fruit_prices = {
        '사과': '1000',
        '바나나': '500',
        '오렌지': '750',
        '배': '800'
    }
    if fruit_name in fruit_prices:
        return fruit_prices[fruit_name]
```
- ## 2. Create a description of the function
```
use_functions = [
    {
        "type": "function",
        "function": {
            "name": "get_fruit_price",
            "description": "Returns the current price of the specified fruit.",
            "parameters": {
                "type": "object",
                "properties": {
                    "fruit_name": {
                        "type": "string",
                        "description": "The name of the fruit to retrieve the price for (e.g., '사과', '바나나')."
                    }
                },
                "required": ["fruit_name"]
            }
        }
    }
]
```
- ## 3. Ask GPT to figure out which function to call
   response_message : It tells you which function to use as which factor to call
```
messages = [
{"role": "system", "content": "You are a helpful assistant. Use the supplied tools to retrieve fruit prices for the user."},
{"role": "user", "content": "Hi, can you tell me the price of 3 apples?"}
]

client = OpenAI()

response = client.chat.completions.create(
model="gpt-4o-mini",
messages=messages,
tools=use_functions
)

response_message = response.choices[0].message
tool_calls = response_message.tool_calls
```
- ## 4. Run the function
```
proc_messages = []

if tool_calls:
    available_functions = {
        "get_fruit_price": get_fruit_price
    }
    for tool_call in tool_calls:
        messages.append(response_message)  # extend conversation with assistant's reply
        function_name = tool_call.function.name
        function_to_call = available_functions[function_name]
        function_args = json.loads(tool_call.function.arguments)

        function_response = function_to_call(**function_args)

        proc_messages.append(
                {
                    "tool_call_id": tool_call.id,
                    "role": "tool",
                    "name": function_name,
                    "content": function_response,
                }
            )  # extend conversation with function response
        messages += proc_messages
```
- ## 5. Second call
```
    second_response = client.chat.completions.create(
            model='gpt-4o-mini',
            messages=messages,
        )
```
# Open API
Create a chatbot that informs you of the current weather using the weather API
input: area, putput: Current weather, temperature, humidity
- ## 1. Define a function
```
## 함수 정의

def get_current_weather(city):
    key = '1200913362259bf0f6f6aca9a206894a'
    api = f"http://api.openweathermap.org/geo/1.0/direct?q={city}&limit=5&appid={key}"
    location = requests.get(api)
    location = json.loads(location.text)
    print(location)

    lat = location[0]['lat']
    lon = location[0]['lon']

    location_api = f"https://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&appid={key}"
    result = requests.get(location_api)
    result = json.loads(result.text)

    weather = result['weather'][0]['main']
    temp = result['main']['temp']
    temp = round(temp - 273.15, 2)
    hum = result['main']['humidity']
    return str({'condition': {weather}, 'temperature':{temp}, 'humidity':{hum}})
```
## 2. Add 'get_current_weather'
```
## 함수 묘사

use_functions = [
        {
        "type": "function",
        "function": {
            "name": "get_current_weather",
            "description": "Retrieve the current weather information for a specified city. The function returns the weather description, temperature in Celsius, and humidity percentage. The function type is tuple like (weather description, temperature, humidity)",
            "parameters": {
                "type": "object",
                "properties": {
                    "city": {
                        "type": "string",
                        "description": "The name of the city to get the weather for."
                    }
                },
                "required": ["city"]
            }
        }
    }
]
```



