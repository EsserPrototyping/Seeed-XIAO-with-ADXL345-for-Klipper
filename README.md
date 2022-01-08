# Seeed-XIAO-with-ADXL345-for-Klipper
This will use the Seed XIAO MCU instead of the RPI for Input Shaper measurements, so it can be connected to different PI's via USB.

I had trouble getting my ADXL345 to run directly on the PI with longer cables (SPI does not like long wires).
With the XIAO as MCU, longer USB cables can be used and the connection to the ADXL is way shorter.

<img src="https://github.com/EsserPrototyping/Seeed-XIAO-with-ADXL345-for-Klipper/blob/main/Images/all.JPG">


# Klipper ADXL installation:

Original instructions... https://www.klipper3d.org/Measuring_Resonances.html

If you already configured Input Shaper with your PI, you can skip this part.

- Via Putty:
```
~/klippy-env/bin/pip install -v numpy
sudo apt update
sudo apt install python3-numpy python3-matplotlib
```
..instead of the PI as MCU, we will configure the XIAO
___________________________________________________



# Configure the MCU - Seeeduino XIAO

Original instructions... https://github.com/Tircown/ERCF-easy-brd

- Install bossac (version =1.8):
```
sudo apt install libreadline-dev libwxgtk3.0-*
git clone https://github.com/shumatech/BOSSA.git
cd BOSSA
make
sudo cp bin/bossac /usr/local/bin
```
- Prepare the firmware:
```
cd ~/klipper
make menuconfig
```
___________________________________________________
- uncheck extra low-level options
- Architecture: SAMD21/SAMD51
- Processor: SAMD21G18 (Arduino Zero)
- Clock: Internal clock
- Bottloader offset: 8KiB bootloader (Arduino Zero)
- Communication: USB
___________________________________________________

```
make clean
make
```
___________________________________________________

- Short reset two times on Xiao ( orange led on )

More informations on how to reset for flashing:
https://wiki.seeedstudio.com/Seeeduino-XIAO/#enter-bootloader-mode

- Flash XIAO via Putty:
```
sudo /usr/local/bin/bossac -i -d -p /dev/ttyACM1 -e -w -v -R --offset=0x2000 out/klipper.bin
```
- Reboot PI...
___________________________________________________

- Get serial port via putty:
```
ls /dev/serial/by-id/*
```
Should be something like this: /dev/serial/by-id/usb-Klipper_samd21g18a_96FA56235136575020312E30142D15FF-if00

- Copy it for the printer config
___________________________________________________

# Edit printer.cfg:
- Change the serial to what you´ve received in the previous step.
```
[mcu xiao]
serial: /dev/serial/by-id/usb-Klipper_samd21g18a_96FA56235136575020312E30142D15FF-if00

[samd_sercom my_sercom]
sercom: sercom0
tx_pin: xiao:PA6
rx_pin: xiao:PA5
clk_pin: xiao:PA7

[adxl345]
cs_pin: xiao:PB9

[resonance_tester]
accel_chip: adxl345
probe_points:
   55,55,20
```
___________________________________________________

# XIAO hardware connection:

| XIAO | ADXL |
|---|---|
| GND	| GND |
| 3V | VCC |
| 10 (MOSI) | SDA |
| 9 (MISO) | SDO |
| 8 (SCK) | SCL |
| 7 (CS) | CS | 

<img src="https://github.com/EsserPrototyping/Seeed-XIAO-with-ADXL345-for-Klipper/blob/main/Images/xiao.JPG">
<img src="https://github.com/EsserPrototyping/Seeed-XIAO-with-ADXL345-for-Klipper/blob/main/Images/adxl.JPG">

# Print a case for the XIAO if you want to:
<img src="https://github.com/EsserPrototyping/Seeed-XIAO-with-ADXL345-for-Klipper/blob/main/Images/case.JPG">
