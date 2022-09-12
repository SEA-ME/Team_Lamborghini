<details markdown="1">
<summary>9/7</summary>

## Team  _'Lamborghini'_ made it!_

First, Assemble our piracer with wiki guide.

<img src="https://user-images.githubusercontent.com/81483791/188978196-0c487b01-6736-46ed-85b9-2523e3f3639a.png"  width="800" height="600"/>

Second, Install Raspberry pi imager to program in to the SD card.

Insert SD card in the laptop, and execute Raspberry pi imager.
   
<img src="https://user-images.githubusercontent.com/81483791/188979960-53b5cfd6-bd87-42ee-a382-2fe20cc5a659.png"  width="400" height="300"/>
- Choose operating system.
<img src="https://user-images.githubusercontent.com/81483791/188980652-91443fb2-d10d-45b6-9a14-1ca0fe84e210.png"  width="400" height="300"/>
- Choose storage (our SD card)
<img src="https://user-images.githubusercontent.com/81483791/188980909-5347dffc-f018-4355-adef-6498aea1b3d0.png"  width="400" height="300"/>
- Choose advanced options to use SSH, Wifi
<img src="https://user-images.githubusercontent.com/81483791/188980846-23f2c5a4-dada-4551-a28d-3f662efbe534.png"  width="400" height="300"/>

(we can use VNC viewer to SSH)

- Set id, passward for using SSH
<img src="https://user-images.githubusercontent.com/81483791/188980846-23f2c5a4-dada-4551-a28d-3f662efbe534.png"  width="400" height="300"/>
- Connect Wifi
<img src="https://user-images.githubusercontent.com/81483791/188984127-fff097f3-f565-4592-a638-717c0cce14e0.png"  width="400" height="300"/>
- Choose Wireless LAN country : DE (Germany)
<img src="https://user-images.githubusercontent.com/81483791/188981256-8ebe1f13-6f3c-4c61-bbc0-2515abd12a57.png"  width="400" height="300"/>
- Write

Third, setup workspace with wiki guide.

Follow guide of Donkeycar.

1. Setup Raspberry pi

We have problem installing libraries for OpenCV 

We can’t install `libqtgui4 libqt4-test`

Because we got this error message.

**E: Package 'libqtgui4' has no installation candidate**

**E: Unable to locate package libqt4-test**

Also, we have problem installing tensorflow.

For this reason, We did install the other version. (tensorflow-1.9.0)

`pip3 install --upgrade [https://storage.googleapis.com/tensorflow/mac/cpu/tensorflow-1.9.0-py3-none-any.whl](https://storage.googleapis.com/tensorflow/mac/cpu/tensorflow-1.9.0-py3-none-any.whl)`

1. VNC viewer

If we want to use raspberry pi, we needed a moniter.

Each team can’t get one moniter so we connect VNC viewer.

- Download VNC viewer.
- Install putty

`sudo apt install putty`

`putty —version`
- Execute putty client.
<img src="https://user-images.githubusercontent.com/81483791/188981372-11d40a39-0d69-45a7-a3dd-e10887918754.png"  width="400" height="200"/>   
- Fill IP address & choose SSH.
<img src="https://user-images.githubusercontent.com/81483791/188981476-e44bca19-8750-4200-a799-ccce8a42eb4d.png"  width="400" height="300"/>
- Change fonts server:fixed → Ubuntu mono.
<img src="https://user-images.githubusercontent.com/81483791/188981617-56ff8db4-d28e-4f6d-96ef-1bae3741f3e8.png"  width="400" height="300"/>
- Login raspberry pi (we set id, passward for using SSH before).
<img src="https://user-images.githubusercontent.com/81483791/188982070-ff022595-18c6-4a7a-8090-12c4f5ec2d60.png"  width="400" height="300"/>
- Execute VNC viewer.

We got this error "The connection was refused by the host computer”

It can be solved VNC → Enable (for using VNC viewer).

1. WEB control

For WEB control to Donkeycar, we have to change interface options.

`sudo raspi-config`         

<img src="https://user-images.githubusercontent.com/81483791/188982658-c4d246f0-c232-4fcd-b4d5-585ee1f20e8a.png"  width="400" height="300"/>
<img src="https://user-images.githubusercontent.com/81483791/188982723-51f834f4-b2db-4736-a63a-034f4f7b6e4e.png"  width="400" height="300"/>
- Legacy Camera  → Enable
    
- SSH → Enable
   
- VNC → Enable (for using VNC viewer)
   

If you have picamera error, change Legacy Camera → enable .

But we also have other problem to using Donkeycar.    
`Traceback (most recent call last):
  File "/home/lambo/mycar/manage.py", line 717, in <module>
    drive(cfg, model_path=args['--model'], use_joystick=args['--js'],
  File "/home/lambo/mycar/manage.py", line 525, in drive
    steering_controller = PCA9685(cfg.STEERING_CHANNEL, cfg.PCA9685_I2C_ADDR, busnum=cfg.PCA9685_I2C_BUSNUM)
  File "/home/lambo/projects/donkeycar/donkeycar/parts/actuator.py", line 30, in __init__
    self.pwm = Adafruit_PCA9685.PCA9685(address=address)
  File "/home/lambo/env/lib/python3.9/site-packages/Adafruit_PCA9685/PCA9685.py", line 74, in __init__
    self._device = i2c.get_i2c_device(address, **kwargs)
  File "/home/lambo/env/lib/python3.9/site-packages/Adafruit_GPIO/I2C.py", line 63, in get_i2c_device
    busnum = get_default_bus()
  File "/home/lambo/env/lib/python3.9/site-packages/Adafruit_GPIO/I2C.py", line 55, in get_default_bus
    raise RuntimeError('Could not determine default I2C bus for platform.')
RuntimeError: Could not determine default I2C bus for platform.`    
For solve this problem, I tried next things.

- Change Adafruit_GPIO script.

one workaround is edit this file:

/usr/local/lib/python3.9/dist-packages/Adafruit_GPIO/Platform.py

Search:

elif match.group(1) == '**BCM2835**':

replace it with:

elif match.group(1) == '**BCM2711**':

- uninstall Adafruit_GPIO & re install Adafruit_GPIO

`sudo pip3 uninstall Adafruit-PureIO`
`sudo pip3 install Adafruit-PureIO`

But we can’t solve this problem.

And also we can't connect web control **https://our raspberry pi ip:8887**


--------------------------------------------------------------------------------------------------------------------------------------------------------------

2022.09.07

![1](https://user-images.githubusercontent.com/81306023/188978782-2fbd44d1-cc72-4eab-a2fb-841dd2d4e82b.png)
Like yesterday, Joypad was well connected with RB Pi, but It didn't work with servo or motor in Jetracer.

we assume that problem was not software, but hardware.
we reassembled the jet racer, but motor still doesn't work at all.

After that, we found code from rasberrypi / config.py
which is 
```
Joystick_mode = 'xbox'
```

I've follow the guidline in jetracer wiki again.
.
![4](https://user-images.githubusercontent.com/81306023/188978874-905d92ec-5740-4c68-bc5b-d38bc846f860.PNG)
.
![5](https://user-images.githubusercontent.com/81306023/188978888-7e889309-eaf2-4043-adce-6f3481513ff4.PNG)
Or, just use the xbox gamepad in the lobby, but there was no special things.

![3](https://user-images.githubusercontent.com/81306023/188978834-728736bd-f2f7-44d2-9051-1e8b0302b4fc.png)

After that, we found that Error about 'PCA9685' which there is no driver for 'servo' 

to solve, we codded below.

![2](https://user-images.githubusercontent.com/81306023/188978818-c4ed193f-ed97-48e4-8539-7bd4284d3abe.png)



</details>


<details markdown="1">
<summary>9/8</summary>

There were so many versions of Rasberry pi,
Today, we planned that rather than down-grade the version of RPI4, watch one more guideline, and install the proper software.
[DONKEY CAR]: <[https://docs.donkeycar.com/]>

```
donkey createcar --path ~/mycar
````

```
cd ~/mycar
nano myconfig.py
```

```
sudo usermod -aG i2c $USER
sudo reboot
```


```
sudo i2cdetect -r -y 1
```


```
sudo apt-get install -y i2c-tools
sudo i2cdetect -y 1
```
--------------------------------------------------------------------------------------------
every thing was going great to this project
but we have a lot of time spending to solve this problem

```
# result must do like this
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: 40 -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: 70 -- -- -- -- -- -- --
```


```
# but our result was this
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --
```

it means that our connection between servo and RPI doesn't match.


</details>



<details markdown="1">
<summary>9/12</summary>

<img src="https://user-images.githubusercontent.com/81306023/189630289-fcf25fd8-0032-4792-8fd9-b3200305baf9.jpg"  width="700" height="700"/>

We assume that connection error was not for driver, but for hardware, so we changed only the board(not the piracer, not the rasberry pi), and solve the problem.
All the process was well running, and we can operate the piracer forward or backward.


Also, we want to customize the key between joypad and Piracer,
So, we have to create joystick creator wizard to type

```
cd ~/mycar
donkey createjs
```

after the process, I completed my settings like this
<img src="https://user-images.githubusercontent.com/81306023/189642084-c931e215-f3a6-4525-af50-b3a9228a5898.png"  width="700" height="400"/>




P.S
There was unexpected issue in cable. so always be careful!

<img src="https://user-images.githubusercontent.com/81306023/189629930-1a6afa75-1e7e-431e-9ef7-b43994ea8365.jpg"  width="400" height="300"/>

</details>
