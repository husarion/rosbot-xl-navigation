# rosbot-navigation

Setting target point in RViz (running on your PC) for ROSbot XL driving autonomously thanks to Nav2 stack. Works both in the LAN network and [over the Internet](https://husarion.com/manuals/rosbot-xl/remote-access/). 

## Quick Start

## PC

Clone this repository:

```
git clone https://github.com/husarion/rosbot-xl-navigation.git
```

Create a map of the environment using the[rosbot-mapping](https://github.com/husarion/rosbot-xl-mapping) project template.

Copy `rosbot-mapping/maps` folder to `rosbot-navigation/maps` (the same directory as this README).

Check your configs in `.env` file

```
LIDAR_SERIAL=/dev/ttyRPLIDAR

# for RPLIDAR A2M8:
# LIDAR_BAUDRATE=115200
# for RPLIDAR A2M12 and A3:
LIDAR_BAUDRATE=256000

# DDS_CONFIG=DEFAULT
DDS_CONFIG=HUSARNET_SIMPLE_AUTO

# RMW_IMPLEMENTATION=rmw_fastrtps_cpp
RMW_IMPLEMENTATION=rmw_cyclonedds_cpp

MECANUM=True
# MECANUM=False

# AMCL_PARAMS=amcl_params.yaml
AMCL_PARAMS=amcl_params_mecanum.yaml

# NAV2_PARAMS=nav2_params.yaml
# NAV2_PARAMS=nav2_params_mecanum.yaml
NAV2_PARAMS=nav2_params_pure_pursuit.yaml
```

**Notes:**
- Usually RPLIDAR is listed under `/dev/ttyUSB0`, but verify it with `ls -la /dev/ttyUSB*` command.
- If you have RPLIDAR A3 or A2M12 (with violet border around the lenses) set: `LIDAR_BAUDRATE=256000`. Otherwise (for older A2 LIDARs): `LIDAR_BAUDRATE=115200`.
- With `DDS_CONFIG=DEFAULT` your robot and laptop need to be in the same LAN network. If you want to use this demo over the Internet, set `DDS_CONFIG=HUSARNET_SIMPLE_AUTO` and [enable Husarnet on ROSbot and you PC](https://husarion.com/manuals/rosbot/remote-access/).


Sync workspace with ROSbot

```bash
./sync_with_rosbot.sh <ROSbot_ip>
```

Run Rviz and set initial `2D Pose Estimate` by using the button in RViz UI:

```bash
xhost +local:docker && \
docker compose -f compose.pc.yaml up
```

## ROSbot

> **Firmware version**
>
> Before running the project, make sure you have the correct version of a firmware flashed on your robot.
>
> Firmware flashing command (run in the ROSbot's terminal)
>
> ```
> docker run --rm -it --privileged \
> husarion/rosbot-xl:humble \
> flash-firmware.py /firmware.bin /dev/ttyUSB0
> ```

In the ROSbot's shell execute (in the `/home/husarion/rosbot-xl-navigation` directory)

```bash
docker compose -f compose.rosbot.yaml up
```

## Alternative configurations

As you probably noticed, there are a few `amcl` and `nav2` parameter config files. You can switch them by modifying the `.env` file.

### Mecanum

Although mecanum wheels will work as well on default configs, you can use these ones to take advantage of an additional degree of freedom. It is necessary to change two env variables - `AMCL_PARAMS` (`robot_model_type` switched to omnidirectional) and `NAV2_PARAMS`. In this demo `DWBLocalPlanner` is configured to move only in the x and y position ignoring orientation.

### Pure pursuit

This config uses a different controller - `RegulatedPurePursuitController` instead of `DWBLocalPlanner` used in the default config. When compared to DWB, movement should be smoother. To use it you have to set `NAV2_PARAMS` to `nav2_params_pure_pursuit.yaml`. It will work on both regular and mecanum wheels (`amcl` parameters can be set to either version).
