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


