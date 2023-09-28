# rosbot-xl-mapping

Create a map of the unknow environment with ROSbot XL controlled in the LAN network or [over the Internet](https://husarion.com/software/os/vpn-access/). 

## Quick Start (real robot)

### PC

Clone this repository:

```
git clone https://github.com/husarion/rosbot-xl-mapping.git
```

**Connect a gamepad to USB port of your PC/laptop** (the steering without the gamepad will also be described as an alternative).

Check your configs in `.env` file:

```
LIDAR_SERIAL=/dev/ttyRPLIDAR

# for RPLIDAR A2M8:
# LIDAR_BAUDRATE=115200
# for RPLIDAR A2M12 and A3:
LIDAR_BAUDRATE=256000

DDS_CONFIG=DEFAULT
# DDS_CONFIG=HUSARNET_SIMPLE_AUTO

RMW_IMPLEMENTATION=rmw_fastrtps_cpp
# RMW_IMPLEMENTATION=rmw_cyclonedds_cpp

MECANUM=True
# MECANUM=False
```

**Notes:**
- Usually RPLIDAR is listed under `/dev/ttyUSB0`, but verify it with `ls -la /dev/ttyUSB*` command.
- If you have RPLIDAR A3 or A2M12 (with violet border around the lenses) set: `LIDAR_BAUDRATE=256000`. Otherwise (for older A2 LIDARs): `LIDAR_BAUDRATE=115200`.
- With `DDS_CONFIG=DEFAULT` your robot and laptop need to be in the same LAN network. If you want to use this demo over the Internet, set `DDS_CONFIG=HUSARNET_SIMPLE_AUTO` and [enable Husarnet on ROSbot XL and you PC](https://husarion.com/software/os/vpn-access/).

Sync a workspace with ROSbot XL:

```bash
./sync_with_rosbot.sh <ROSbot_XL_IP>
```

Open new terminal on PC and run Rviz depending on whether you [have](https://github.com/husarion/rosbot-xl-mapping#with-the-gamepad-connected-to-pc) a gamepad or [not](https://github.com/husarion/rosbot-xl-mapping#without-the-gamepad). Then you will be able to control the ROSbot and create map of the environment. The map os being saved automatically in the `rosbot-xl-mapping/maps` folder.

#### With the gamepad connected to PC

```bash
xhost +local:docker && \
docker compose -f compose.pc.yaml up
```

#### Without the gamepad 

```bash
xhost +local:docker && \
docker compose -f compose.pc.yaml up -d map-saver rviz
```

Then enter the running `rviz` container:

```bash
docker exec -it rviz bash
```

Now, to teleoperate the ROSbot XL with your keyboard, execute:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
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
> flash-firmware.py -p /dev/ttyUSB0
> ```

In the ROSbot's terminal execute (in `/home/husarion/rosbot-xl-mapping` directory):

```bash
docker compose -f compose.rosbot.yaml up
```

## Quick Start (Gazebo simulation)

> **Prerequisites**
>
> The `compose.sim.gazebo.yaml` file uses NVIDIA Container Runtime. Make sure you have NVIDIA GPU and the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) installed.
> This project has been tested on the following setup:
> ```bash
>$ docker compose version
>Docker Compose version 2.17.0
>$ docker --version
>Docker version 23.0.1, build a5ee5b1dfc
>```
> Additionally, the `compose.sim.gazebo.yaml` file in this project uses NVIDIA Container Runtime. So, please make sure that you have an NVIDIA GPU and the [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html) installed on your system before moving forward.
Start the containers in a new terminal:

```bash
xhost +local:docker && \
docker compose -f compose.sim.gazebo.yaml up
```

And in the second terminal start `telop-twist-keyboard` for manual ROSbot XL control:

```bash
docker exec -it rviz bash
```

And inside the running container shell execute:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Map will be automatically saved every 5 seconds.

