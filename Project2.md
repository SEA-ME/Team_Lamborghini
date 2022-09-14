## RPM checker

### 3d model
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
  pinMode(2, INPUT); //핀2를 입력값으로 받는다.
}

static void detect_rotary_change()
{
  static int switch_state = 1;
  
  input = digitalRead(2); //입력으로 설정한 INPUT값(핀2)을 읽어낸다.
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
  Serial.print(F("°C "));
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

If you have “No buffer space available ” ERROR message, Type following

```python
sudo ifconfig can0 txqueuelen 1000
```

- Send data

```python
CAN.sendMsgBuf(INT32U id, INT8U ext, INT8U len, INT8U *buf);
```

**id** represents where the data come from.

**ext** represents the status of the frame. '0' means standard frame. '1' means extended frame.

**len** represents the length of this frame.

**buf** is the content of this message.

- Receive data

```python
CAN.readMsgBuf(INT8U *len, INT8U *buf);
```

**len** represents the data length.

**buf** is where you store the data.

---

## CAN Module

### 1. CAN-BUS Shield → Arduino UNO


[Reference](https://wiki.seeedstudio.com/CAN-BUS_Shield_V2.0/)

![Untitled 2](https://user-images.githubusercontent.com/81483791/190277565-3b7267fc-bd9c-4a44-a5bb-af06bcfe240c.png)
1. **DB9 Interface** - to connect to OBDII Interface via a DBG-OBD Cable.
2. **V_OBD** - It gets power from OBDII Interface (from DB9)
3. **Led Indicator**:
    - **PWR**: power
    - **TX**: blink when the data is sending
    - **RX**: blink when there's data receiving
    - **INT**: data interrupt
4. **Terminal** - CAN_H and CAN_L
5. **Arduino UNO pin out**
6. **Serial Grove connector**
7. **I2C Grove connector**
8. **ICSP pins**
9. **IC** - MCP2551, a high-speed CAN transceiver ([datasheet](https://files.seeedstudio.com/wiki/CAN_BUS_Shield/resource/Mcp2551.pdf))
10. **IC** - MCP2515, stand-alone CAN controller with SPI interface ([datasheet](https://files.seeedstudio.com/wiki/CAN_BUS_Shield/resource/MCP2515.pdf))

### 2. CAN BUS FD Shield → Raspberry PI

reference

[Reference](https://wiki.seeedstudio.com/2-Channel-CAN-BUS-FD-Shield-for-Raspberry-Pi/)
