# 狗头人

> 原文：<https://dev.to/ladvien/kobold-3nj2>

[狗头人文件](///files/Kobold_Working_1.zip)

[https://www.youtube.com/embed/f5g_sLFr69g](https://www.youtube.com/embed/f5g_sLFr69g)

不久前，Sparkfun 发布了一款新产品，他们的 [MiniMoto](https://www.sparkfun.com/products/11890) 分线板。它突破了 [DRV8830](http://www.ti.com/lit/ds/slvsab2f/slvsab2f.pdf) IC，这是一个串行控制(I2C) H 桥。我认为芯片很漂亮。尽管有一些问题，

1.  Sparkfun 的突破是一座桥的 25x25mm 毫米。如果我再加一个，然后再加一个 Arduino Pro Mini，它就没那么小了。
2.  这是 9.95 美元
3.  不在紫色板上:)

所以，我开始用它制作一个比 Sparkfun breakout 还要小的机器人控制器。我最后得到的是一只小母狗，我称之为狗头人。

[![](img/d458113e0e25fc8e431af3a16014f93a.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--wDLDsE9Q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/IMG_0350.jpg)

棋盘很简单。它有一个 ATtiny 85，作为使用 [SoftI2CMaster](http://todbot.com/blog/2010/09/25/softi2cmaster-add-i2c-to-any-arduino-pins/) 库的 I2C 主机。这使得微型 85 只使用两个引脚控制两个电机，留下三个供您使用。

我的最终目标将是为它建造一个防护罩，并连接一个 HM-10，使它成为一个小型无线系留机器人。这将把我带到一个引脚，我相信这将是某种测距仪或感觉传感器。

[![](img/56d07515fc0502530296eef50185ab1f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--6tIeqv3z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Kobold_Board_Close.jpg)

物料清单:

1.  1x attainy 85 soic1.21 美元
2.  2x drv 8830 msop-105.10 美元
3.  2 x .33 欧姆 04020.86 美元
4.  1 x 1uF 0402
5.  2 x 4.7k Res. 0402
6.  [1 块 Kobold PCB](http://www.oshpark.com/shared_projectss/NCVym4Uc)2.50 美元(三块)

**总计:9.76 美元**

该板可以用 Arduino ISP 编程:

[![](img/d11df735708159bebc2ba9f2ecd8d28c.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--Kk36Soy8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://ladvien.cimg/Kobold_ISP_Pinout.jpg)

现在，我只需要用它做一个机器人:)

我目前正在进行第二次迭代，以纠正一些问题。我还会添加一些特性，比如我的 pogo-pin 编程接口。我为它设计的屏蔽还将包括一个充电电路，可能还有一个 SMD 升压电路，可以将 LiPo 转换成漂亮的 5v。

总之，工作正在进行中...只是想分享一下。

**如果有人对这个板感兴趣，请再等几个迭代。我担心我在用别人的钱制造蓝烟。:)**

下面是视频中运行的代码:

```
//Sample Code for the Kobold Board.
#define NO_INTERRUPT 1
#define I2C_TIMEOUT 100 
#define SDA_PORT PORTB
#define SDA_PIN 4
#define SCL_PORT PORTB
#define SCL_PIN 3
#include <SoftI2CMaster.h> #include <avr/io.h>  
void CPUSlowDown(int fac) {
  // slow down processor by a fac
    CLKPR = _BV(CLKPCE);
    CLKPR = _BV(CLKPS1) | _BV(CLKPS0);
}

boolean writeSpeed(byte addr, int speedx)
{
  //This should clear the fault register.
  byte regValue = 0x08;
  //Clear the fault status, in case there is one.
  if (!i2c_start(addr | I2C_WRITE)) {
    return false;
  }
  if (!i2c_write(0x01)) {  //0x01 = We want to write.
    return false;
  }
  if (!i2c_write(regValue)) { //Write the clear bye for fault register.
    return false;
  }
  i2c_stop();  //Stop transmission.

  //Let's convert the integer given us into a byte.
  regValue = (byte)abs(speedx);     //Convert 0-63 to byte value to set speed.
  if (regValue > 63) regValue = 63; // Cap the value at 63.
  regValue = regValue<<2;           // Left shift to make room for bits 1:0
  if (speedx < 0) regValue |= 0x01;  // Set bits 1:0 based on sign of input.
  else           regValue |= 0x02;   //A negative number for reverse and positive for forward.

  //Now, let's move this sucker.
  //Sets the i2c slave address
  if (!i2c_start(addr | I2C_WRITE)) {
    return false;
  }
  //0x00 = We want to write something.
  if (!i2c_write(0x00)) {
    return false;
  }
  //Writes the byte which had been converted from an integer that was passed this function.  Annnnd! The motor moves!
  if (!i2c_write(regValue)) {
    return false;
  }
  i2c_stop();
  return true;
}

boolean writeStop(byte addr)
{
  if (!i2c_start(addr | I2C_WRITE)) {
    return false;
  }
  if (!i2c_write(0x00)) {
    return false;
  }
  if (!i2c_write(0x00)) {
    return false;
  }
  i2c_stop();
  return true;
}

//------------------------------------------------------------------------------
void setup(void) {
#if I2C_CPUFREQ == (F_CPU/8)
  CPUSlowDown();
#endif }

void loop(void){

  for (int i = 0; i <= 3; i++){
  delay(100);
  if (!writeSpeed(0xC0, 10));
  if (!writeSpeed(0xCE, 10));
  delay(1000);
  if (!writeStop(0xC0) );
  if (!writeStop(0xCE) );
  delay(1000);  
  if (!writeSpeed(0xC0, -10));
  if (!writeSpeed(0xCE, -10));
  delay(1000);  
  if (!writeStop(0xC0) );
  if (!writeStop(0xCE) );
  delay(100);  
  }
   for (int i = 0; i <= 3; i++){
       delay(100);
  if (!writeSpeed(0xC0, 14));
  if (!writeSpeed(0xCE, 34));
  delay(1000);
  if (!writeStop(0xC0) );
  if (!writeStop(0xCE) );
  delay(1000);  
  if (!writeSpeed(0xC0, -14));
  if (!writeSpeed(0xCE, -34));
  delay(1000);  
  if (!writeStop(0xC0) );
  if (!writeStop(0xCE) );
  delay(100);  
   }
} 
```

Enter fullscreen mode Exit fullscreen mode