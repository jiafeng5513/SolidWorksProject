bittreetech U2C v1.2
bittreetech EBB36 v1.2


树莓派上获取主板id `ls /dev/serial/by-id/*`

/dev/serial/by-id/usb-Klipper_stm32f446xx_cai-if00

sudo vim /etc/network/interfaces.d/can0

~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0
Found canbus_uuid=e317d5a8a66b, Application: Klipper

total 0 可能是没插入120k跳线，也可能是波特率设置未匹配
脚本直接报错，可能是u2c板子与树莓派之间的usb连接有问题

挤出机参数：
https://orbiterprojects.com/orbiter-v2-0/

EBB repo
https://github.com/bigtreetech/EBB/blob/master/README_zh_cn.md

u2c repo
https://github.com/bigtreetech/U2C