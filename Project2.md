# Project 2

[RPM checker](#rpm-checker)

[Temperature sensor](#temperature-sensor)

[Adding CAN to the Raspberry PI](#adding-can-to-the-raspberry-pi)

[CAN communication between Raspberry Pi and Arduino](#can-communication-between-raspberry-pi-and-arduino)

[CAN BUS (FD) Hat](#solution.-CAN-BUS-(FD)-Hat)

[CAN Module](#can-module)

[OLED display](#1-solution.-OLED-display-with-donkeycar)

[How to Calculate battery level](#How-to-Calculate-battery-level)

## RPM checker

1. to check rpm, we need to attach wheels, and arduino nano sensor.

<img src="https://user-images.githubusercontent.com/81306023/190121255-4d449583-0499-4f3f-b1a4-3804c7569b7d.jpg"  width="400" height="500"/>     ------------------>     <img src="https://user-images.githubusercontent.com/81306023/190121265-f4e0e9fd-f370-42f1-b2d1-879d1a3f0ddc.jpg"  width="400" height="500"/> 

so I changed my piracer from left to right

---------------------------------------------------------------------
also, the basic wheels that arduino nano provides doesn't perfectly fit with the axis of piracer. So we designed some wheels, and also some supporter to get attached speed sensor

<img src="https://user-images.githubusercontent.com/81306023/190126805-2f32ab6e-07f9-4922-b62a-000bfaf5e16c.PNG"  width="400" height="350"/>  <img src="https://user-images.githubusercontent.com/81306023/190157058-6eb93043-0d54-41f2-8a70-13e77dff0495.PNG"  width="400" height="350"/>


and the result was like this

<img src="https://user-images.githubusercontent.com/81306023/190191643-16555762-9496-4f62-baa9-5d89a99dcf02.jpg"  width="400" height="500"/>  <img src="https://user-images.githubusercontent.com/81306023/190191666-8080d7f3-5fea-4d21-8f04-f47749ef5e70.jpg"  width="400" height="500"/>



So. Now I could use sensor efficiently!

-----------------------------------------------------------------------------------------------------------------------------

<img src="https://user-images.githubusercontent.com/81306023/190131289-e1cfcb81-44e2-471c-af69-3276ca9825cc.jpg"  width="400" height="500"/> 
I connected Arduino uno with speed sensor like this

| Arduino Uno | Speed Sensor |
| --- | --- |
| Digital Pin | D0 |
| GND | GND |
| Vin | Vcc |

Code to check RPM
```
int input = 0;
int count_change = 0;
unsigned long cur_time = 0;

// detect_rotor_change()
// count_speed()
// count_speed(detect_rotor_change());

void setup() {
  Serial.begin(9600);
  pinMode(2, INPUT); //???2??? ??????????????? ?????????.
}

static void detect_rotary_change()
{
  static int switch_state = 1;
  
  input = digitalRead(2); //???????????? ????????? INPUT???(???2)??? ????????????.
  if (input == HIGH && switch_state){
    //Serial.write("on ");
    switch_state = 0;
    count_change++;
  }
  else if (input == LOW && !switch_state){    
    //Serial.write("off ");
    switch_state = 1;
    count_change++;
  }
}

void loop()
{
  cur_time = millis();
  detect_rotary_change();
  if (cur_time % 1000 == 0){
    Serial.println(count_change);    
    count_change = 0;
  }
}
```

-----------------------------------------------------------------------------------------------
## Temperature sensor


We have dht11 model below

<img src="https://user-images.githubusercontent.com/81306023/190211536-25904ca5-c3f2-4d62-b54c-80ba8c51b2cf.PNG"  width="400" height="400"/> 

so, to use dht11 in arduino, we have to install dht library. type this
```
ctrl + shift + i
```
and install DHT sensor library

after that, we can found the example code of DHT sensor,
and changed it like this

```
#include "DHT.h"

#define DHTPIN 3     // Digital pin connected to the DHT sensor
#define DHTTYPE DHT11   // DHT 22  (AM2302), AM2321

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  Serial.println(F("DHTxx test!"));

  dht.begin();
}

void loop() {
  // Wait a few seconds between measurements.
  delay(2000);

  // Reading temperature or humidity takes about 250 milliseconds!
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  float h = dht.readHumidity();
  // Read temperature as Celsius (the default)
  float t = dht.readTemperature();

  // Check if any reads failed and exit early (to try again).
  if (isnan(h) || isnan(t)) {
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }
  Serial.print(F("Humidity: "));
  Serial.print(h);
  Serial.print(F("%  Temperature: "));
  Serial.print(t);
  Serial.print(F("??C "));
  Serial.print("\n");
}
```

and we done the installation

<img src="https://user-images.githubusercontent.com/81306023/190212444-6015ddff-819b-4497-b27b-7b4b6ab989d3.jpg"  width="400" height="300"/> 




---------------------------------------------------------------------

## ****Adding CAN to the Raspberry PI****

But this way, we can send Raspberry pi to Arduino

![Untitled](https://user-images.githubusercontent.com/81483791/190277626-e53087dd-5832-48ee-a7ab-476482f02af9.png)
### 1. Add the following line to your /boot/config.txt file

- oscillator = 8000000 or 16000000
- interrupt = 25 (We have connected the INT pin to GPIO25)

```python
sudo vi /boot/config.txt
dtoverlay=mcp2515-can0,oscillator=8000000,interrupt=25
```

### 2. Reboot

### 3. Bring up the CAN interface using

```python
sudo /sbin/ip link set can0 up type can bitrate 500000
```

### 4. Edit your /etc/network/interfaces file

To automatically bring up the interface on boot

```python
auto can0
iface can0 inet manual
    pre-up /sbin/ip link set can0 type can bitrate 500000 triple-sampling on restart-ms 100
    up /sbin/ifconfig can0 up
    down /sbin/ifconfig can0 down
```

### 5. Check connecting can

```python
ifconfig
```

![Untitled 1](https://user-images.githubusercontent.com/81483791/190277382-a9fceb8e-f0e4-43a7-8806-39178701589b.png)

### 6. CAN-utils

```python
sudo apt-get install can-utils
```

[Reference](https://www.beyondlogic.org/adding-can-controller-area-network-to-the-raspberry-pi/)

---

## CAN communication between Raspberry Pi and Arduino


[Arduino C CAN Libraries](https://github.com/Seeed-Studio/Seeed_Arduino_CAN/tree/old)



[Raspberry Pi Python CAN](https://python-can.readthedocs.io/en/master/index.html)

[Raspberry Pi Python CAN pdf](https://buildmedia.readthedocs.org/media/pdf/python-can/develop/python-can.pdf)

**IMPORTANT NOTE**

> Bitrate of RPI & UNO is 500000(set in program)
> 
> 
> Clock frequency of RPI & UNO is 8Mhz(set in config file and program)
> 

## Arduino C code

### CAN_RX.ino

```python
#include <SPI.h>
#include "mcp_can.h"

const int spiCSPin = 10;
const int ledPin = 2;
boolean ledON = 1;

MCP_CAN CAN(spiCSPin);

void setup()
{
    Serial.begin(115200);
    pinMode(ledPin,OUTPUT);

    while (CAN_OK != CAN.begin(CAN_500KBPS,MCP_8MHz))
    {
        Serial.println("CAN BUS Init Failed");
        delay(100);
    }
    Serial.println("CAN BUS  Init OK!");
}

void loop()
{
    unsigned char len = 0;
    unsigned char buf[8];

    if(CAN_MSGAVAIL == CAN.checkReceive())
    {
        CAN.readMsgBuf(&len, buf);

        unsigned long canId = CAN.getCanId();

        Serial.println("-----------------------------");
        Serial.print("Data from ID: 0x");
        Serial.println(canId, HEX);

        for(int i = 0; i<len; i++)
        {
            Serial.print(buf[i]);
            Serial.print("\t");
            if(ledON && i==0)
            {

                digitalWrite(ledPin, buf[i]);
                ledON = 0;
                delay(500);
            }
            else if((!(ledON)) && i==4)
            {

                digitalWrite(ledPin, buf[i]);
                ledON = 1;
            }
        }
        Serial.println();
    }
}
```

### CAN_TX.ino

```python
#include <SPI.h>
#include <mcp_can.h>

const int spiCSPin = 10;
int ledHIGH    = 1;
int ledLOW     = 0;

MCP_CAN CAN(spiCSPin);

void setup()
{
    Serial.begin(115200);

    while (CAN_OK != CAN.begin(CAN_500KBPS,MCP_8MHz))
    {
        Serial.println("CAN BUS init Failed");
        delay(100);
    }
    Serial.println("CAN BUS Shield Init OK!");
}

unsigned char stmp[8] = {1, 2, 3, 4, 5, 6, 7, 8};
    
void loop()
{   
  Serial.println("In loop");
  CAN.sendMsgBuf(0x43, 0, 8, stmp);
  delay(1000);
}
```

## Raspberry PI Python code

### can_basic_send.py

```python
import time
import can

bustype = 'socketcan'
channel = 'can0'
bus = can.interface.Bus(channel=channel, bustype=bustype,bitrate=500000)

msg = can.Message(arbitration_id=0xc0ffee, data=[0, 1, 3, 1, 4, 1], is_extended_id=False)
while True:
	bus.send(msg)
	time.sleep(1)
```

### can_basic_recv.py

```python
import can
import time

can_interface = 'can0'
bus = can.interface.Bus(can_interface, bustype='socketcan',bitrate=500000)

while True:
	message = bus.recv()
	print(message)
#for msg in bus:
	#print(msg.data)
```

If you have ???No buffer space available ??? ERROR message, Type following

```python
sudo ifconfig can0 txqueuelen 1000
```
### 7.  Wrong data

OUTPUT :

unsigned char stmp[8] = {1, 2, 3, 4, 5, 6, 7, 8};
![Untitled 1](https://user-images.githubusercontent.com/81483791/191597929-d1bed4a2-b458-45c0-a673-d6bfd3254a4e.png)


I can receive but it is wrong data so I tried to use CAN cus hat

---

## Solution. CAN BUS (FD) Hat

[Reference](https://wiki.seeedstudio.com/2-Channel-CAN-BUS-FD-Shield-for-Raspberry-Pi/)

### 1. Connect CAN BUS(FD) Hat

### 2. Edit config.txt file and Add following

```jsx
sudo vim /boot/config.txt
dtoverlay=seeed-can-fd-hat-v2
```

### 3. Reboot

### 4. Initialized

```jsx
dmesg | grep spi
```

OUTPUT:

```jsx
[    5.848080] spi_master spi0: will run message pump with realtime priority
[    5.861055] mcp251xfd spi0.1 can0: MCP2518FD rev0.0 (-RX_INT -MAB_NO_WARN +CRC_REG +CRC_RX +CRC_TX +ECC -HD c:40.00MHz m:20.00MHz r:17.00MHz e:16.66MHz) successfully initialized.
[    5.873396] mcp251xfd spi0.0 can1: MCP2518FD rev0.0 (-RX_INT -MAB_NO_WARN +CRC_REG +CRC_RX +CRC_TX +ECC -HD c:40.00MHz m:20.00MHz r:17.00MHz e:16.66MHz) successfully initialized.
```

### 5. Check ifconfig list

```jsx
ifconfig -a
```

OUTPUT:


![Untitled 2](https://user-images.githubusercontent.com/81483791/191598262-e86fabbc-7310-4d48-baba-837fd5776912.png)

### 6. Install CAN tools

```jsx
sudo apt-get install can-utils
pip3 install python-can
```

### 7.  Send & Recv test with 2-channel CAN FD

![%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6 32 59](https://user-images.githubusercontent.com/81483791/191598140-cf87a8eb-8dcb-43ac-a327-47b2519413b5.png)

1.  Connect the channels

0_L <===> 1_L

0_H <===> 1_H

2. Set the CAN protocol

```jsx
sudo ip link set can0 up type can bitrate 1000000   dbitrate 8000000 restart-ms 1000 berr-reporting on fd on
sudo ip link set can1 up type can bitrate 1000000   dbitrate 8000000 restart-ms 1000 berr-reporting on fd on
 
sudo ifconfig can0 txqueuelen 65536
sudo ifconfig can1 txqueuelen 65536
```

3. Open two windows

```jsx
#send data
cangen can0 -mv
```

```jsx
#dump data
candump can1
```

OUTPUT : 
![Untitled 3](https://user-images.githubusercontent.com/81483791/191598299-05bf838a-075e-4825-b96d-cfbbbdbaa751.png)
![Untitled 4](https://user-images.githubusercontent.com/81483791/191598322-bddc38d3-ca99-42e3-a1c3-9c453b7a3d9e.png)



### 8. Raspberry pi to Arduino CAN - CAN communication

If you follow step 7, you need to reboot.

![%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-20_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7 03 22](https://user-images.githubusercontent.com/81483791/191598402-d9627002-3ca0-4bba-830e-bde85185d279.png)

1. Set CAN protocol

I connectecd like this picture,

Arduino CAN_L <===> Raspberry pi 0_L

Arduino CAN_H <===> Raspberry pi 0_H

```jsx
sudo ip link set can1 up type can bitrate 500000
```

2. Check details

```jsx
ip -details link show can0
```

### Arduino Code (Send data)

```jsx
// demo: CAN-BUS Shield, send data
// loovee@seeed.cc
 
#include <mcp_can.h>
#include <SPI.h>
 
// the cs pin of the version after v1.1 is default to D9
// v0.9b and v1.0 is default D10
const int SPI_CS_PIN = 9;
 
MCP_CAN CAN(SPI_CS_PIN);                                    // Set CS pin
 
void setup()
{
    Serial.begin(115200);
 
    while (CAN_OK != CAN.begin(CAN_500KBPS))              // init can bus : baudrate = 500k
    {
        Serial.println("CAN BUS Shield init fail");
        Serial.println(" Init CAN BUS Shield again");
        delay(100);
    }
    Serial.println("CAN BUS Shield init ok!");
}
 
unsigned char stmp[8] = {0, 0, 0, 0, 0, 0, 0, 0};
void loop()
{
    //send data:  id = 0x00, standrad frame, data len = 8, stmp: data buf
    stmp[7] = stmp[7]+1;
    if(stmp[7] == 100)
    {
        stmp[7] = 0;
        stmp[6] = stmp[6] + 1;
 
        if(stmp[6] == 100)
        {
            stmp[6] = 0;
            stmp[5] = stmp[6] + 1;
        }
    }
 
    CAN.sendMsgBuf(0x00, 0, 8, stmp);
    delay(100);                       // send data per 100ms
}
// END FILE
```

### Raspberry pi Code (Recv data)

1. Open terminal and following:

```jsx
candump can0
```

or 

2. Make a python file

```jsx
import can
 
can_interface = 'can0'
bus = can.interface.Bus(can_interface, bustype='socketcan')
while True:
    message = bus.recv(1.0) # Timeout in seconds.
    if message is None:
            print('Timeout occurred, no message.')
    print(message)
```

OUTPUT:

unsigned char stmp[8] = {1, 2, 3, 4, 5, 6, 7, 8};

![Untitled 5](https://user-images.githubusercontent.com/81483791/191598481-2ec66e79-b758-436b-b04c-f7278e466990.png)

- Send data

```python
CAN.sendMsgBuf(INT32U id, INT8U ext, INT8U len, INT8U *buf);
```

**id**??represents where the data come from.

**ext**??represents the status of the frame. '0' means standard frame. '1' means extended frame.

**len**??represents the length of this frame.

**buf**??is the content of this message.

- Receive data

```python
CAN.readMsgBuf(INT8U *len, INT8U *buf);
```

**len**??represents the data length.

**buf**??is where you store the data.

---

## CAN Module

### 1. CAN-BUS Shield ??? Arduino UNO


[Reference](https://wiki.seeedstudio.com/CAN-BUS_Shield_V2.0/)

![Untitled 2](https://user-images.githubusercontent.com/81483791/190277565-3b7267fc-bd9c-4a44-a5bb-af06bcfe240c.png)
1. **DB9 Interface** - to connect to OBDII Interface via a DBG-OBD Cable.
2. **V_OBD**??- It gets power from OBDII Interface (from DB9)
3. **Led Indicator**:
    - **PWR**: power
    - **TX**: blink when the data is sending
    - **RX**: blink when there's data receiving
    - **INT**: data interrupt
4. **Terminal**??- CAN_H and CAN_L
5. **Arduino UNO pin out**
6. **Serial Grove connector**
7. **I2C Grove connector**
8. **ICSP pins**
9. **IC**??- MCP2551, a high-speed CAN transceiver ([datasheet](https://files.seeedstudio.com/wiki/CAN_BUS_Shield/resource/Mcp2551.pdf))
10. **IC**??- MCP2515, stand-alone CAN controller with SPI interface ([datasheet](https://files.seeedstudio.com/wiki/CAN_BUS_Shield/resource/MCP2515.pdf))

### 2. CAN BUS FD Shield ??? Raspberry PI

reference

[Reference](https://wiki.seeedstudio.com/2-Channel-CAN-BUS-FD-Shield-for-Raspberry-Pi/)

---

## 1 solution. OLED display with donkeycar

Edit config.py

`USE_SSD1306_128_32 = True`

```jsx
#SSD1306_128_32
USE_SSD1306_128_32 = True    # Enable the SSD_1306 OLED Display
SSD1306_128_32_I2C_ROTATION = 0 # 0 = text is right-side up, 1 = rotated 90 degrees clockwise, 2 = 180 degrees (flipped), 3 = 270 degrees
SSD1306_RESOLUTION = 1 # 1 = 128x32; 2 = 128x64
```

![Untitled](https://user-images.githubusercontent.com/81483791/191599241-a9737d41-02e5-4d1f-a998-6aa1723cdabc.png)

---

## 2 solution. OLED display with pi-display

It is different from the first solution  If you want second solution,  `Please USE_SSD1306_128_32 = False`

### 1. Open terminal and commend following

```jsx
cd ~
git clone https://github.com/waveshare/pi-display
cd pi-display
sudo ./install.sh
```

### 2. Install

```jsx
sudo pip3 install flask
```

*CHECK POINT* 

`Enable ???I2C???`

```jsx
sudo raspi-config
# -> activate 'Interface Options' -> 'I2C'
```

### 3. Execute python code

![%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7 42 17](https://user-images.githubusercontent.com/81483791/191599279-edfef2dc-9720-4af1-9e94-de0e6a6970c8.png)

```jsx
cd pidisplay
python display_server.py
```

If you have a problem ???line 8??? utils,

You should check it has ??? . ??? in front of utils

`from .utils`  ??? `from utils`

---

## How to Calculate battery level

![%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7 48 21](https://user-images.githubusercontent.com/81483791/191600232-0415b88d-431f-4bc5-b968-36e97c4a30ac.png)
8.4V, 18650 battery ?? 4 (two in parallel, two in series)

- series ??? **Voltage rise**
- parallel ???**Ampere rise**


4.2V = full charge

3V = discharge

nominal voltage 3.6~3.7V

### battery level = ( voltage - 6 ) /2.4 *100

If voltage is 8.4V, battery level is 100% 

If voltage is 6V, battery level is 0%
