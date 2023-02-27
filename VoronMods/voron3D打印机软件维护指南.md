1. 下载[MainsailOS](https://github.com/mainsail-crew/MainsailOS/releases/),如果使用的是树莓派4B，就下载结尾是`rpi64.img.xz`的文件。
2. 使用balenaEtcher把镜像烧录到内存卡中
3. 安全弹出读卡器，并重新插入读卡器。识别到一个boot分区，打开该分区创建文件`wpa_supplicant.conf`。
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US # US Country code

network={
        scan_ssid=1
        ssid="wifi名,保留双引号"
        psk="wifi密码，保留双引号"
        proto=RSN
        key_mgmt=WPA-PSK
        pairwise=CCMP
        auth_alg=OPEN
}
```
4. 卡插入树莓派，开机，ssh登录树莓派，用户名pi,密码raspberry
5. 换源，参考[清华开源镜像站](https://mirrors.tuna.tsinghua.edu.cn/help/raspbian/)
6. 更新`sudo apt-get update && sudo apt-get upgrade -y`
7. 安装vim，`sudo apt-get install vim`
8. 重启` sudo reboot`
9. 在路由器链接设备列表里找到树莓派，给他设置DHCP绑定，固定一个ip地址。
10. 升级klipper, `cd ~/klipper && git pull`
11. 给驱动板编译固件,`make menuconfig`, Q for exit, Y for save.
```
[*] Enable extra low-level configuration options
    Micro-controller Architecture (STMicroelectronics STM32)  --->
    Processor model (STM32F446)  --->
    Bootloader offset (64KiB bootloader)  --->
    Clock Reference (12 MHz crystal)  --->
    Communication interface (USB (on PA11/PA12))  --->
    USB ids  --->
()  GPIO pins to set at micro-controller startup (NEW)
```
12. make, 在~/klipper/out下会生成klipper.bin
13. 复制klipper.bin 文件到桌面，并将其重命名为firmware.bin
14. 准备一张容量不大于32G的TF卡，使用SDformater工具，将其格式化为Fat32格式，将前面生成的firmware.bin拷入卡中
15.  确保蜘蛛主板没有通电，将TF卡插入蜘蛛主板；给主板通电，等待20S左右后，SD附近的红灯停止闪烁并熄灭，断开电源，取出TF卡插回电脑，如果固件已被重命名为OLD.BIN，说明固件已烧录成功。
16. 查询主板id：`ls /dev/serial/by-id/*`
```bash
# 正常情况下应该形如
/dev/serial/by-id/usb-Klipper_stm32f446xx_32000F001650563046363120-if00
```
17. 编译EBB固件,`cd ~/klipper && make clean && make menuconfig`
```
[*] Enable extra low-level configuration options
    Micro-controller Architecture (STMicroelectronics STM32)  --->
    Processor model (STM32G0B1)  --->
    Bootloader offset (No bootloader)  --->
    Clock Reference (8 MHz crystal)  --->
    Communication interface (CAN bus (on PB0/PB1))  --->
(250000) CAN bus speed
()  GPIO pins to set at micro-controller startup
```
18. make, 在~/klipper/out下会生成klipper.bin
19. 使用STM32CubeProgrammer给EBB刷写固件
20. 拔掉120R跳线，插上拨码开关旁边的跳线，连接USB，按住boot，单击一下RST进入DFU模式。
21. 刷入固件
22. 开机登录树莓派，`sudo vim /etc/network/interfaces.d/can0`，编辑为如下内容，注意波特率要与EBB固件编译时一致。
```
auto can0
iface can0 can static
    bitrate 250000
    up ifconfig $IFACE txqueuelen 1024
```
23. `sudo reboot`
24. 查询can总线id：`~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0`
```
Found canbus_uuid=e317d5a8a66b, Application: Klipper
Total 1 uuids found
```
注意，如果不是第一次安装，即klipper中已经加载了printer.cfg且配置文件中已经填写了正确的canbus_uuid，此处查询会返回空。
25. 浏览器输入树莓派的ip地址，进入控制后台，上传配置文件，注意填写好主控板ID和EBB板的id。
