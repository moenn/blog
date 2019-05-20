---
title: 树莓派 + HC SR04 测距模块
date:   2018-12-03 21:26:43 +0800
---
超声波测距模块 HC-SR04 非常易于使用，这篇文章就介绍 HC-SR04 的测距原理、树莓派如何与 HC-SR04 接线，以及使用 python 程序控制测距的方法。

### HC-SR04 测距过程
下图说明了超声波的测距原理:
![ultrasonic.png](https://i.loli.net/2018/12/03/5c054a956fd67.png)
HC-SR04 引脚图:
![HC-SR04_pin.png](https://i.loli.net/2018/12/03/5c054aa6385ab.png)
HC-SR04 模块包含了超声波发射器、超声波接收器和控制电路，可以提供 3cm - 4m 的距离测量。引脚图如上图所示有4个引脚：电源引脚(5v Vcc),触发脉冲引脚(Trig),返回脉冲引脚(Echo),地引脚(Gnd)。 
如何使用呢？下面是 HC-SR04 的工作过程：
1. 树莓派向 Trig 脚发送一个持续时间至少为 10us 的高电平信号。
2. HC-SR04 接收到树莓派的触发信号，自动发送 40 Khz 的超声波，并把 Echo 脚置为高电平。然后开始检测返回的声波。
3. 当检测到返回的声波时，Echo 脚被置为低电平。

从上述过程可以看出，Echo 脚的高电平持续时间就是超声波从发射到返回所经过的时间间隔 T。那么传感器和物体间的距离就能用下面的公式计算：
			距离(Distance) = 超声波速度(340m/s) * 时间 T / 2

### HC-SR04 与树莓派的接线
树莓派 GPIO 引脚图
![树莓派GPIO 引脚图](https://pinout.xyz/resources/raspberry-pi-pinout.png)
接线图
![raspiberry_HCSR04_bb.png](https://i.loli.net/2018/12/03/5c054ab4c5960.png)

- 电源引脚 Vcc 与树莓派的 5v 脚（物理引脚2）相连，地引脚 Gnd 与树莓派的 Gnd 脚（物理引脚6）相连。  
- 触发脉冲引脚 Trig 用来接收来自树莓派的控制信号，接任意 GPIO 口。图中连接了 GPIO_23（物理引脚16)。  
- 返回脉冲引脚 Echo 用来返回测距结果，需要注意的是 Echo 输出的是 5v 信号，而树莓派的 GPIO 最高只能承受3.3v 信号，所以 Echo 引脚需要经**分压电路**连接到树莓派的 GPIO。图中连接了 GPIO_24（物理引脚18).

*为什么 GPIO_23 又是物理引脚16呢，这跟 GPIO 的两种编号方式有关，这两种编号方式分别是 BCM 编号和物理编号。*

分压电路使用了 330 欧姆和 470 欧姆的电阻，这样 GPIO_24 分到的电压为: (470) / (330+470) * 5v = 2.9v < 3.3 v 。当然你可以选用任意阻值的两只电阻来组成分压电路，只要经过计算后 GPIO_24 分到的电压合适即可。
### python 程序控制测距
如果你理解了 HC-SR04 的工作过程，那么下面包含注释的程序对你应该很简单，就不一一解释了。
```python
#导入 GPIO库
import RPi.GPIO as GPIO
import time
 
#设置 GPIO 模式为 BCM
GPIO.setmode(GPIO.BCM)
 
#定义 GPIO 引脚
GPIO_TRIGGER = 23
GPIO_ECHO = 24
 
#设置 GPIO 的工作方式 (IN / OUT)
GPIO.setup(GPIO_TRIGGER, GPIO.OUT)
GPIO.setup(GPIO_ECHO, GPIO.IN)
 
def distance():
    # 发送高电平信号到 Trig 引脚
    GPIO.output(GPIO_TRIGGER, True)
 
    # 持续 10 us 
    time.sleep(0.00001)
    GPIO.output(GPIO_TRIGGER, False)
 
    start_time = time.time()
    stop_time = time.time()
 
    # 记录发送超声波的时刻1
    while GPIO.input(GPIO_ECHO) == 0:
        start_time = time.time()
 
    # 记录接收到返回超声波的时刻2
    while GPIO.input(GPIO_ECHO) == 1:
        stop_time = time.time()
 
    # 计算超声波的往返时间 = 时刻2 - 时刻1
    time_elapsed = stop_time - start_time
    # 声波的速度为 343m/s， 转化为 34300cm/s。
    distance = (time_elapsed * 34300) / 2
 
    return distance
 
if __name__ == '__main__':
    try:
        while True:
            dist = distance()
            print("Measured Distance = {:.2f} cm".format(dist))
            time.sleep(1)
 
        # Reset by pressing CTRL + C
    except KeyboardInterrupt:
        print("Measurement stopped by User")
        GPIO.cleanup()

```
运行结果:

![Snipaste_2018-12-04_00-24-20.png](https://i.loli.net/2018/12/04/5c05597c85566.png)

这个程序放在了 [github](https://github.com/moenn/my_raspi/blob/master/ultrasonic_test.py)

### 参考文章
[Using a Raspberry Pi distance sensor (ultrasonic sensor HC-SR04)](https://tutorials-raspberrypi.com/raspberry-pi-ultrasonic-sensor-hc-sr04/)