# z轴偏移量校准
G1 x150 y150
SET_HEATER_TEMPERATURE HEATER=extruder TARGET=200
SET_HEATER_TEMPERATURE HEATER=heater_bed TARGET=80
Z_ENDSTOP_CALIBRATE
TESTZ Z=-1
accept
SAVE_CONFIG 

# 加热头PID校准


# 调试参考
https://github.com/crazy-luke/Voron/blob/main/%E8%B0%83%E8%AF%95%E6%89%8B%E5%86%8CV2.txt