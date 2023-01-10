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

## Quick Start (Webots simulation)

> **Prerequisites**
>
> The `compose.sim.webots.yaml` file uses NVIDIA Container Runtime. Make sure you have NVIDIA GPU and the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) installed.
Start the containers in a new terminal:

```bash
xhost +local:docker && \
docker compose -f compose.sim.webots.yaml up
```

### Run without Nvidia acceleration
Comment or delete line inside the compose file `compose.sim.webots.yaml`.
```bash
# runtime: nvidia
...
# - NVIDIA_VISIBLE_DEVICES=all
# - NVIDIA_DRIVER_CAPABILITIES=all
```