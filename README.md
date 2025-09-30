# Cyclone V SoC ALSA WM8978G

# Project Descriptions

This project is making use of previous ALSA I2S driver.

Previous driver have two driving methods, splited capture playback, and this mixed codec.

# Issues

Although this sound trivial on 2-wires communication of the WM8978, such 2-wires communcation introduced all kinds of issues.

During development of this codec, the kernel never recognize the codec on all possible IIC bus.

However, the same IIC buses are checked with other I2C devices w/o issue.

As such, the Wolfson codec is really "pain in the ass" where the NACK behavior is well found.

To make use of such obsolete codec, GPIO bitband IIC must be used !!!

# Kernel Control

```
brian@brian:~$ aplay -l
brian@brian:~$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: WM8978 [Wolfson WM8978], device 0: opencores-i2s-wm8978-hifi wm8978-hifi-0 [opencores-i2s-wm8978-hifi wm8978-hifi-0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
brian@brian:~$ arecord -l
**** List of CAPTURE Hardware Devices ****
card 0: WM8978 [Wolfson WM8978], device 0: opencores-i2s-wm8978-hifi wm8978-hifi-0 [opencores-i2s-wm8978-hifi wm8978-hifi-0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
brian@brian:~$
```

<img width="678" height="381" alt="image" src="https://github.com/user-attachments/assets/b5b87207-0813-461c-a02e-80213b95fd07" />


# Device Tree

```
sound-wm8978 {
  compatible = "simple-audio-card";
  simple-audio-card,name = "Wolfson WM8978";
  simple-audio-card,format = "i2s";
  simple-audio-card,widgets =
    "Microphone", "Built-in Microphone",
    "Line", "Line-in Jack",
    "Line", "Aux-in Jack",
    "Headphone", "Headphone Jack",
    "Speaker", "Built-in Speaker";
  simple-audio-card,routing =
    "LMICP", "Built-in Microphone",
    "RMICP", "Built-in Microphone",
    "L2", "Line-in Jack",
    "R2", "Line-in Jack",
    "LAUX", "Aux-in Jack",
    "RAUX", "Aux-in Jack",
    "Headphone Jack", "LHP",
    "Headphone Jack", "RHP";
  status = "okay";

  simple-audio-card,cpu {
    sound-dai = <&i2s 0>;
    bitclock-master;
    frame-master;
  };
  
  simple-audio-card,codec {
    sound-dai = <&wm8978 0>;
    bitclock-slave;
    frame-slave;
  };
};
```

