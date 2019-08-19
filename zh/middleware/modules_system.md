# 模块参考：系统

## commander

Source: [modules/commander](https://github.com/PX4/Firmware/tree/master/src/modules/commander)

### 描述

The commander module contains the state machine for mode switching and failsafe behavior.

### Usage {#commander_usage}

    commander <command> [arguments...]
     Commands:
       start
         [-h]        Enable HIL mode
    
       calibrate     Run sensor calibration
         mag|accel|gyro|level|esc|airspeed Calibration type
    
       check         Run preflight checks
    
       arm
         [-f]        Force arming (do not run preflight checks)
    
       disarm
    
       takeoff
    
       land
    
       transition    VTOL transition
    
       mode          Change flight mode
         manual|acro|offboard|stabilized|rattitude|altctl|posctl|auto:mission|auto:l
                     oiter|auto:rtl|auto:takeoff|auto:land|auto:precland Flight mode
    
       lockdown
         [off]       Turn lockdown off
    
       stop
    
       status        print status info
    

## dataman

Source: [modules/dataman](https://github.com/PX4/Firmware/tree/master/src/modules/dataman)

### Description

Module to provide persistent storage for the rest of the system in form of a simple database through a C API. Multiple backends are supported:

- 一个文件 （比如，在 SD 卡上）
- FLASH 内存（如果飞控板支持的话）
- FRAM
- 内存 RAM (显然这种方式不是持续的)

It is used to store structured data of different types: mission waypoints, mission state and geofence polygons. Each type has a specific type and a fixed maximum amount of storage items, so that fast random access is possible.

### Implementation

Reading and writing a single item is always atomic. If multiple items need to be read/modified atomically, there is an additional lock per item type via `dm_lock`.

**DM_KEY_FENCE_POINTS** and **DM_KEY_SAFE_POINTS** items: the first data element is a `mission_stats_entry_s` struct, which stores the number of items for these types. These items are always updated atomically in one transaction (from the mavlink mission manager). During that time, navigator will try to acquire the geofence item lock, fail, and will not check for geofence violations.

### Usage {#dataman_usage}

    dataman <command> [arguments...]
     Commands:
       start
         [-f <val>]  Storage file
                     values: <file>
         [-r]        Use RAM backend (NOT persistent)
         [-i]        Use FLASH backend
    
     The options -f, -r and -i are mutually exclusive. If nothing is specified, a
     file 'dataman' is used
    
       poweronrestart Restart dataman (on power on)
    
       inflightrestart Restart dataman (in flight)
    
       stop
    
       status        print status info
    

## dmesg

Source: [systemcmds/dmesg](https://github.com/PX4/Firmware/tree/master/src/systemcmds/dmesg)

### Description

Command-line tool to show bootup console messages. Note that output from NuttX's work queues and syslog are not captured.

### Examples

Keep printing all messages in the background:

    dmesg -f &
    

### Usage {#dmesg_usage}

    dmesg <command> [arguments...]
     Commands:
         [-f]        Follow: wait for new messages
    

## heater

Source: [drivers/heater](https://github.com/PX4/Firmware/tree/master/src/drivers/heater)

### 描述

Background process running periodically on the LP work queue to regulate IMU temperature at a setpoint.

This task can be started at boot from the startup scripts by setting SENS_EN_THERMAL or via CLI.

### Usage {#heater_usage}

    heater <command> [arguments...]
     Commands:
       controller_period Reports the heater driver cycle period value, (us), and
                     sets it if supplied an argument.
    
       integrator    Sets the integrator gain value if supplied an argument and
                     reports the current value.
    
       proportional  Sets the proportional gain value if supplied an argument and
                     reports the current value.
    
       sensor_id     Reports the current IMU the heater is temperature controlling.
    
       setpoint      Reports the current IMU temperature.
    
       start         Starts the IMU heater driver as a background task
    
       status        Reports the current IMU temperature, temperature setpoint, and
                     heater on/off status.
    
       stop          Stops the IMU heater driver.
    
       temp          Reports the current IMU temperature.
    
       stop
    
       status        print status info
    

## land_detector

Source: [modules/land_detector](https://github.com/PX4/Firmware/tree/master/src/modules/land_detector)

### Description

Module to detect the freefall and landed state of the vehicle, and publishing the `vehicle_land_detected` topic. Each vehicle type (multirotor, fixedwing, vtol, ...) provides its own algorithm, taking into account various states, such as commanded thrust, arming state and vehicle motion.

### Implementation

Every type is implemented in its own class with a common base class. The base class maintains a state (landed, maybe_landed, ground_contact). Each possible state is implemented in the derived classes. A hysteresis and a fixed priority of each internal state determines the actual land_detector state.

#### 多旋翼的 Land Detector

**ground_contact**: thrust setpoint and velocity in z-direction must be below a defined threshold for time GROUND_CONTACT_TRIGGER_TIME_US. When ground_contact is detected, the position controller turns off the thrust setpoint in body x and y.

**maybe_landed**: it requires ground_contact together with a tighter thrust setpoint threshold and no velocity in the horizontal direction. The trigger time is defined by MAYBE_LAND_TRIGGER_TIME. When maybe_landed is detected, the position controller sets the thrust setpoint to zero.

**landed**: it requires maybe_landed to be true for time LAND_DETECTOR_TRIGGER_TIME_US.

The module runs periodically on the HP work queue.

### Usage {#land_detector_usage}

    land_detector <command> [arguments...]
     Commands:
       start         Start the background task
         fixedwing|multicopter|vtol|rover Select vehicle type
    
       stop
    
       status        print status info
    

## load_mon

Source: [modules/load_mon](https://github.com/PX4/Firmware/tree/master/src/modules/load_mon)

### Description

Background process running periodically with 1 Hz on the LP work queue to calculate the CPU load and RAM usage and publish the `cpuload` topic.

On NuttX it also checks the stack usage of each process and if it falls below 300 bytes, a warning is output, which will also appear in the log file.

### Usage {#load_mon_usage}

    load_mon <command> [arguments...]
     Commands:
       start         Start the background task
    
       stop
    
       status        print status info
    

## logger

Source: [modules/logger](https://github.com/PX4/Firmware/tree/master/src/modules/logger)

### Description

System logger which logs a configurable set of uORB topics and system printf messages (`PX4_WARN` and `PX4_ERR`) to ULog files. These can be used for system and flight performance evaluation, tuning, replay and crash analysis.

It supports 2 backends:

- 文件：写入 ULog 文件到文件系统中（SD 卡）
- MAVLink: 通过 MAVLink 将 ULog 数据流传输到客户端上（需要客户端支持此方式）

Both backends can be enabled and used at the same time.

The file backend supports 2 types of log files: full (the normal log) and a mission log. The mission log is a reduced ulog file and can be used for example for geotagging or vehicle management. It can be enabled and configured via SDLOG_MISSION parameter. The normal log is always a superset of the mission log.

### Implementation

The implementation uses two threads:

- 主进程以固定速率运行（如果以 -p 参数启动则会轮询一个主题），并检查数据的更新。
- 写入线程，将数据写入文件中、

In between there is a write buffer with configurable size (and another fixed-size buffer for the mission log). It should be large to avoid dropouts.

### Examples

Typical usage to start logging immediately:

    logger start -e -t
    

Or if already running:

    logger on
    

### Usage {#logger_usage}

    logger <command> [arguments...]
     Commands:
       start
         [-m <val>]  Backend mode
                     values: file|mavlink|all, default: all
         [-x]        Enable/disable logging via Aux1 RC channel
         [-e]        Enable logging right after start until disarm (otherwise only
                     when armed)
         [-f]        Log until shutdown (implies -e)
         [-t]        Use date/time for naming log directories and files
         [-r <val>]  Log rate in Hz, 0 means unlimited rate
                     default: 280
         [-b <val>]  Log buffer size in KiB
                     default: 12
         [-p <val>]  Poll on a topic instead of running with fixed rate (Log rate
                     and topic intervals are ignored if this is set)
                     values: <topic_name>
    
       on            start logging now, override arming (logger must be running)
    
       off           stop logging now, override arming (logger must be running)
    
       stop
    
       status        print status info
    

## replay

Source: [modules/replay](https://github.com/PX4/Firmware/tree/master/src/modules/replay)

### Description

This module is used to replay ULog files.

There are 2 environment variables used for configuration: `replay`, which must be set to an ULog file name - it's the log file to be replayed. The second is the mode, specified via `replay_mode`:

- `replay_mode=ekf2`: 指定 EKF2 回放模式。 该模式只能与 ekf2 模块一起使用，但它可以让回放的运行速度尽可能的快。
- 否则为 Generic ：该模式可用于回放任何模块，但回放速度只能与日志记录的速度相同。

The module is typically used together with uORB publisher rules, to specify which messages should be replayed. The replay module will just publish all messages that are found in the log. It also applies the parameters from the log.

The replay procedure is documented on the [System-wide Replay](https://dev.px4.io/en/debug/system_wide_replay.html) page.

### Usage {#replay_usage}

    replay <command> [arguments...]
     Commands:
       start         Start replay, using log file from ENV variable 'replay'
    
       trystart      Same as 'start', but silently exit if no log file given
    
       tryapplyparams Try to apply the parameters from the log file
    
       stop
    
       status        print status info
    

## send_event

Source: [modules/events](https://github.com/PX4/Firmware/tree/master/src/modules/events)

### 描述

Background process running periodically on the LP work queue to perform housekeeping tasks. It is currently only responsible for temperature calibration and tone alarm on RC Loss.

The tasks can be started via CLI or uORB topics (vehicle_command from MAVLink, etc.).

### Usage {#send_event_usage}

    send_event <command> [arguments...]
     Commands:
       start         Start the background task
    
       temperature_calibration Run temperature calibration process
         [-g]        calibrate the gyro
         [-a]        calibrate the accel
         [-b]        calibrate the baro (if none of these is given, all will be
                     calibrated)
    
       stop
    
       status        print status info
    

## sensors

Source: [modules/sensors](https://github.com/PX4/Firmware/tree/master/src/modules/sensors)

### Description

The sensors module is central to the whole system. It takes low-level output from drivers, turns it into a more usable form, and publishes it for the rest of the system.

The provided functionality includes:

- 读取传感器驱动的输出 (例如，`sensor_gyro` 等)。 如果存在多个同类型传感器，那个模块将进行投票和容错处理。 然后应用飞控板的旋转和温度校正（如果被启用）。 最终发布传感器数据：其中名为 `sensor_combined` 的主题被系统的许多部件所使用。
- 执行 RC 通道映射：读取通道原始输入 (`input_rc`)，应用校正并将 RC 通道映射到配置的通道 & 模式转换开关，低通滤波器，然后发布到 `rc_channels` 和 `manual_control_setpoint` 话题中。
- 从 ADC 驱动中读取输出（通过 ioctl 接口）并发布到 `battery_status` 。
- 当参数发生变化或者启动时，确保传感器驱动获得的矫正参数（缩放因子 & 偏移量）是最新的。 传感器驱动使用 ioctl 接口获取参数更新。 为了使这一功能正常运行，当 `sensors` 模块启动时传感器驱动必须已经处于运行状态。
- 执行起飞前传感器一致性检查并发布到 `sensor_preflight` 主题中。

### Implementation

It runs in its own thread and polls on the currently selected gyro topic.

### Usage {#sensors_usage}

    sensors <command> [arguments...]
     Commands:
       start
         [-h]        Start in HIL mode
    
       stop
    
       status        print status info
    

## tune_control

Source: [systemcmds/tune_control](https://github.com/PX4/Firmware/tree/master/src/systemcmds/tune_control)

### Description

Command-line tool to control & test the (external) tunes.

Tunes are used to provide audible notification and warnings (e.g. when the system arms, gets position lock, etc.). The tool requires that a driver is running that can handle the tune_control uorb topic.

Information about the tune format and predefined system tunes can be found here: https://github.com/PX4/Firmware/blob/master/src/lib/tunes/tune_definition.desc

### Examples

Play system tune #2:

    tune_control play -t 2
    

### Usage {#tune_control_usage}

    tune_control <command> [arguments...]
     Commands:
       play          Play system tune or single note.
         [-t <val>]  Play predefined system tune
                     default: 1
         [-f <val>]  Frequency of note in Hz (0-22kHz)
         [-d <val>]  Duration of note in us
         [-s <val>]  Volume level (loudness) of the note (0-100)
                     default: 40
         [-m <val>]  Melody in string form
                     values: <string> - e.g. "MFT200e8a8a"
    
       libtest       Test library
    
       stop          Stop playback (use for repeated tunes)