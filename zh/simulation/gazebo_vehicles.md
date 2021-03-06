!REDIRECT "https://docs.px4.io/master/zh/simulation/gazebo_vehicles.html"

# Gazebo 机型

This topic lists/displays the vehicles supported by the PX4 [Gazebo](../simulation/gazebo.md) simulation and the `make` commands required to run them (the commands are run from a terminal in the **PX4-Autopilot** directory).

支持的机型种类包括：多旋翼、VTOL、尾座式 VTOL、固定翼、无人车、潜艇/无人水下航行器。

> **Tip** 使用命令 `make px4_sitl list_vmd_make_targets` 获取构建目标的完整列表（你还可以过滤掉以 `gazebo_` 开头的目标）。

<span></span>
> **Note** [Gazebo](../simulation/gazebo.md) 页面展示了如何安装 Gazebo、如何启用视频并加载自定义地图以及许多其他的配置选项。

## Multicopter

<a id="quadrotor"></a>

### Quadrotor (Default)

```sh
make px4_sitl gazebo
```

<a id="quadrotor_optical_flow"></a>

### Quadrotor with Optical Flow

```sh
make px4_sitl gazebo_iris_opt_flow
```

<a id="3dr_solo"></a>

### 3DR Solo (Quadrotor)

```sh
make px4_sitl gazebo_solo
```

![3DR Solo 的 Gazebo 仿真](../../assets/simulation/gazebo/vehicles/solo.png)

<a id="typhoon_h480"></a>

### Typhoon H480 (Hexrotor)

```
make px4_sitl gazebo_typhoon_h480
```

![Typhoon H480 的 Gazebo 仿真](../../assets/simulation/gazebo/vehicles/typhoon.jpg)

> **Note** 此机型还支持[视频流仿真](#video)。

<a id="fixed_wing"></a>

## Plane/Fixed Wing

<a id="standard_plane"></a>

### Standard Plane

```sh
make px4_sitl gazebo_plane
```

![固定翼的 Gazebo 仿真](../../assets/simulation/gazebo/vehicles/plane.png)

<a id="standard_plane_catapult"></a>

#### Standard Plane with Catapult Launch

```sh
make px4_sitl gazebo_plane_catapult
```

This model simulates hand/catapult launch, which can be used for [fixed wing takeoff](http://docs.px4.io/master/en/flying/fixed_wing_takeoff.html#fixed-wing-takeoff) in position mode, takeoff mode, or missions.

The plane will automatically be launched as soon as the vehicle is armed.


## VTOL

<a id="standard_vtol"></a>

### Standard VTOL

```sh
make px4_sitl gazebo_standard_vtol
```

![Standard VTOL in Gazebo](../../assets/simulation/gazebo/vehicles/standard_vtol.png)

<a id="tailsitter_vtol"></a>

### Tailsitter VTOL

```sh
make px4_sitl gazebo_tailsitter
```

![Tailsitter VTOL in Gazebo](../../assets/simulation/gazebo/vehicles/tailsitter.png)

<a id="ugv"></a>

## Unmmanned Ground Vehicle (UGV/Rover/Car)

<a id="ugv_ackerman"></a>

### Ackerman UGV

```sh
make px4_sitl gazebo_rover
```

![Rover in Gazebo](../../assets/simulation/gazebo/vehicles/rover.png)

<a id="ugv_differential"></a>

### Differential UGV

```sh
make px4_sitl gazebo_r1_rover
```

![Rover in Gazebo](../../assets/simulation/gazebo/vehicles/r1_rover.png)

<a id="uuv"></a>

## Unmanned Underwater Vehicle (UUV/Submarine)

<a id="uuv_hippocampus"></a>

### HippoCampus TUHH UUV

```sh
make px4_sitl gazebo_uuv_hippocampus
```

![Submarine/UUV](../../assets/simulation/gazebo/vehicles/hippocampus.png)

<a id="usv"></a>

## Unmanned Surface Vehicle (USV/Boat)

<a id="usv_boat"></a>

### Boat

```sh
make px4_sitl gazebo_boat
```

![Boat/USV](../../assets/simulation/gazebo/vehicles/boat.png)

<a id="airship"></a>

## Airship

<a id="cloudship"></a>

### Cloudship

```sh
make px4_sitl gazebo_cloudship
```

![Airship](../../assets/simulation/gazebo/vehicles/airship.png)
