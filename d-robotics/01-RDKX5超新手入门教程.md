# RDK X5超新手入门教程 ：从系统烧录到 YOLO 物体识别

## 目录

- [RDK X5超新手入门教程 ：从系统烧录到 YOLO 物体识别](#rdk-x5超新手入门教程-从系统烧录到-yolo-物体识别)
  - [目录](#目录)
  - [如何购买](#如何购买)
  - [RDK X5 规格参数](#rdk-x5-规格参数)
  - [系统烧录](#系统烧录)
    - [烧录准备](#烧录准备)
      - [供电](#供电)
      - [存储](#存储)
      - [显示](#显示)
      - [网络连接](#网络连接)
      - [系统烧录](#系统烧录-1)
    - [镜像下载](#镜像下载)
    - [系统烧录](#系统烧录-2)
    - [启动系统](#启动系统)
    - [常见问题](#常见问题)
  - [YOLO算法测试](#yolo算法测试)
    - [运行方法](#运行方法)
  - [摄像头应用](#摄像头应用)
    - [摄像头图像本地保存](#摄像头图像本地保存)
      - [环境准备](#环境准备)
      - [运行方式](#运行方式)
      - [预期效果](#预期效果)
  - [ROS 应用](#ros-应用)
    - [功能介绍](#功能介绍)
    - [实现步骤](#实现步骤)
  - [结语](#结语)


## 如何购买

第一步就是 买板子 啦！大家可以去到 [RDK官网](https://developer.d-robotics.cc/) 进行购买

收到的板子会带一个盒子，非常美观：

![实物图](https://github.com/user-attachments/assets/407ec1fe-da6d-4c58-a1ac-e74c4a5549f2)

## RDK X5 规格参数

| 元器件 | 参数                                                                                                                                                                               |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CPU    | 8x A55 @ 1.5GHz                                                                                                                                                                    |
| RAM    | 4GB / 8GB LPDDR4                                                                                                                                                                   |
| BPU    | 10 TOPS                                                                                                                                                                            |
| GPU    | 32 GFlops                                                                                                                                                                          |
| 储存   | 使用外部Micro SD卡作为存储（自行购买）                                                                                                                                             |
| 多媒体 | H.265 (HEVC) Main Profile @ L5.1, H.264 (AVC) Baseline/Constrained Baseline/Main/High Profiles @ L5.2 with SVC-T encoding, H.265/H.264 encoding and decoding up to 3840x2160@60fps |

RDK X5 提供了网口、USB、摄像头、LCD、HDMI、CANFD、40PIN等功能接口，方便用户进行图像多媒体、深度学习算法等应用的开发和测试。开发板接口布局如下：

![RDK_X5_interface](https://github.com/user-attachments/assets/8e4b6257-7af5-48e7-b5ae-2eac09065598)

| 序号 | 功能                     | 序号 | 功能                     | 序号 | 功能                    |
| ---- | ------------------------ | ---- | ------------------------ | ---- | ----------------------- |
| 1    | 供电接口 （USB Type-C）  | 2    | RTC 电池接口             | 3    | 易连接口 （USB Type-C） |
| 4    | 调试串口（Micro USB）    | 5    | 2 路 MIPI Camera 接口    | 6    | 千兆以太网口，支持 POE  |
| 7    | 4 路 USB 3.0 Type A 接口 | 8    | CAN FD 高速接口          | 9    | 40PIN 接口              |
| 10   | HDMI 显示接口            | 11   | 多标准兼容耳机接口       | 12   | 板载 Wi-Fi 天线         |
| 13   | TF卡接口（底面）         | 14   | LCD 显示接口（MIPI DSI） |      |                         |

机械尺寸：

![RDK_X5_interface](https://github.com/user-attachments/assets/45da5a6f-1fb9-4283-b4c4-58501c019798)

## 系统烧录

### 烧录准备

#### 供电

RDK X5开发板通过 USB Type-C 接口供电，需要使用支持 **5V/3A** 的电源适配器为开发板供电。

> [!NOTE]
> 
> 请不要使用电脑USB接口为开发板供电，否则会因供电不足造成开发板**异常断电、反复重启**等异常情况。
>

#### 存储

RDK X5开发板采用 Micro SD 存储卡作为系统启动介质，推荐至少 `8GB` 容量的存储卡，以便满足 Ubuntu 系统、应用功能软件对存储空间的需求。

> [!IMPORTANT]
> 
> SD 卡选择的型号，参考 1元/1G 的价格去买，我买过便宜的会导致系统启动失败！

#### 显示

RDK X5开发板支持HDMI显示接口，通过HDMI线缆连接开发板和显示器，支持图形化桌面显示。

#### 网络连接

RDK X5开发板支持以太网、Wi-Fi 两种网络接口，用户可通过任意接口实现网络连接功能。

#### 系统烧录

RDK套件目前提供 Ubuntu 22.04 系统镜像，可支持 Desktop 桌面图形化交互。

> [!NOTE]
> 
> **RDK X5 Module**出厂已经烧写测试版本系统镜像，为确保使用最新版本的系统，<font color='Red'>建议参考本文档完成最新版本系统镜像的烧写</font>。
> 

### 镜像下载

点击 [**下载镜像**](https://archive.d-robotics.cc/downloads/os_images/rdk_x5/)，进入版本选择页面，选择对应版本目录，进入3.1.0版本系统下载页。

下载完成后，解压出Ubuntu系统镜像文件，如 `rdk-x5-ubuntu22-preinstalled-desktop-3.1.0-arm64.img`

> [!TIP]
> 
> - `desktop` ：带有桌面的Ubuntu系统，可以外接屏幕、鼠标操作
> - `server` ：无桌面的Ubuntu系统，可以通过串口、网络远程连接操作
> 

### 系统烧录

在烧录Ubuntu系统镜像前，需要做如下准备：

- 准备至少 `8GB` 容量的 Micro SD 卡
- SD 读卡器
- 下载镜像烧录工具[balenaEtcher](https://etcher.balena.io/), [balenaEtcher](https://etcher.balena.io/) 是一款支持 Windows/Mac/Linux 等多平台的 PC 端启动盘制作工具。

制作SD启动卡流程如下：

1. 打开 [balenaEtcher](https://etcher.balena.io/) 工具，点击`Flash from file`按钮，选择解压出来的 `rdk-x5-ubuntu22-preinstalled-desktop-3.1.0-arm64.img` 文件作为烧录镜像 

![image-flash-1](https://github.com/user-attachments/assets/f8fe00d3-341e-4fec-b492-f85d8488e332)

2. 点击`Select target`按钮，选择对应的 Micro SD 存储卡作为目标存储设备  

![image-flash-2](https://developer.d-robotics.cc/rdk_doc/assets/images/image-X3-Update-balena3-7ff1f77425e7b508c81b2d1ceff2f640.png)

3. 点击`Flash`按钮开始烧录，待工具提示`Flash Complete`时，表示镜像烧录完成，可以关闭 balenaEtcher 并取出存储卡

![image-flash-3](https://developer.d-robotics.cc/rdk_doc/assets/images/image-X3-Update-balena4-1741bb9f58f0d1ed04a402111bd75251.png)

### 启动系统

首先保持开发板**断电**，然后将制作好的存储卡插入开发板的 Micro SD 卡槽，并通过 HDMI 线缆连接开发板与显示器，最后给开发板上电。

系统首次启动时会进行默认环境配置，整个过程持续45秒左右，配置结束后会在显示器输出 Ubuntu 系统桌面，如下图：

![image-desktop_display](https://github.com/user-attachments/assets/71b86912-e6c1-4639-89fb-b539775d8a5c)

> [!TIP]
> 开发板指示灯说明
> **<font color='Green'>绿色</font>** 指示灯：点亮代表硬件上电正常
> 
> 如果开发板上电后长时间没有显示输出（2分钟以上），说明开发板启动异常。需要通过串口线进行调试，查看开发板是否正常。
> 
> 如果没有接入显示器同学，可以看下 绿色电源灯 的 附近 是否有 橙色灯 亮起，如果亮起则表明系统起来了，可以开始玩耍了！

### 常见问题

首次使用开发板时的常见问题如下：

- **<font color='Blue'>上电不开机</font>** ：请确保使用[供电](#供电)中推荐的适配器供电；请确保开发板的Micro SD卡已经烧录过Ubuntu系统镜像
- **<font color='Blue'>使用中热插拔存储卡</font>** ：开发板不支持热插拔Micro SD存储卡，如发生误操作请重启开发板

> [!WARNING] 
> 注意事项
> 
> - **禁止**带电时拔插除 USB、HDMI、网线之外的任何设备
> - RDK X5 的 Type-C USB 接口仅用作供电 
> - 选用正规品牌的 USB Type-C 口供电线，否则会出现供电异常，导致系统异常断电的问题


## YOLO算法测试

本示例主要实现以下功能：

1. 加载 `yolov5s_672x672_nv12` 目标检测模型
2. 读取 `kite.jpg` 静态图片作为模型的输入
3. 分析算法结果，渲染检测结果

### 运行方法

本示例的完整代码和测试数据安装在 `/app/pydev_demo/07_yolov5_sample/` 目录下，调用以下命令运行

```shell
cd /app/pydev_demo/07_yolov5_sample/
sudo python3 ./test_yolov5.py
```

运行成功后，会输出目标检测结果，

```bash
bbox: [593.949768, 80.819038, 672.215027, 147.131607], score: 0.856997, id: 33, name: kite
bbox: [215.716019, 696.537476, 273.653442, 855.298706], score: 0.852251, id: 0, name: person
bbox: [278.934448, 236.631256, 305.838867, 281.294922], score: 0.834647, id: 33, name: kite
bbox: [115.184196, 615.987, 167.202667, 761.042542], score: 0.781627, id: 0, name: person
bbox: [577.261719, 346.008453, 601.795349, 370.308624], score: 0.705358, id: 33, name: kite
bbox: [1083.22998, 394.714569, 1102.146729, 422.34787], score: 0.673642, id: 33, name: kite
bbox: [80.515938, 511.157104, 107.181572, 564.28363], score: 0.662, id: 0, name: person
bbox: [175.470078, 541.949219, 194.192871, 572.981812], score: 0.623189, id: 0, name: person
bbox: [518.504333, 508.224396, 533.452759, 531.92926], score: 0.597822, id: 0, name: person
bbox: [469.970398, 340.634796, 486.181305, 358.508972], score: 0.5593, id: 33, name: kite
bbox: [32.987705, 512.65033, 57.665741, 554.898804], score: 0.508812, id: 0, name: person
bbox: [345.142609, 486.988464, 358.24762, 504.551331], score: 0.50672, id: 0, name: person
bbox: [530.825439, 513.695679, 555.200256, 536.498352], score: 0.459818, id: 0, name: person
```

并且输出渲染结果到 `output_image.jpg` 文件中，如下图：

![image](https://github.com/user-attachments/assets/b60f44b5-a962-4b49-805e-90aeba38bc41)

## 摄像头应用

### 摄像头图像本地保存

本示例 `vio_capture` 示例实现了 `MIPI` 摄像头图像采集，并将 `RAW` 和 `YUV` 两种格式的图像本地保存的功能。示例流程框图如下：
![image-capture](https://github.com/user-attachments/assets/fd6903ec-514a-47cc-bf81-35434ac9ccf8)

#### 环境准备

- 开发板**断电**状态下，将 `MIPI` 摄像头接入开发板
- 通过 HDMI 线缆连接开发板和显示器
- 开发板上电，并通过命令行登录

#### 运行方式

示例代码以源码形式提供，需要使用 `make` 命令进行编译后运行，步骤如下：

```bash
sunrise@ubuntu:~$ cd /app/cdev_demo/vio_capture/
sunrise@ubuntu:/app/cdev_demo/vio_capture$ sudo make
sunrise@ubuntu:/app/cdev_demo/vio_capture$ sudo ./capture -b 16 -c 10 -h 1080 -w 1920
```

参数说明：

- `-b`: RAW 图 `bit` 数，`IMX219` / `IMX477` / `OV5647` 都设置为 `16`，只有极少数 Camera Sensor 需要设置为 `8`
- `-c`: 保存图像的数量
- `-w`: 保存图像的宽度
- `-h`: 保存图像的高度


#### 预期效果

程序正确运行后，当前目录保存指定数量的图片文件，`RAW` 格式以 `raw_*.raw` 方式命名，`YUV` 格式以 `yuv_*.yuv` 方式命名。运行log如下：

```bash
sunrise@ubuntu:/app/cdev_demo/vio_capture$ sudo ./capture -b 16 -c 10 -h 1080 -w 1920
Camera 0:
        i2c_bus: 6
        mipi_host: 0
Camera 1:
        i2c_bus: 4
        mipi_host: 2
Camera 2:
        i2c_bus: 0
        mipi_host: 0
Camera 3:
        i2c_bus: 0
        mipi_host: 0
mipi mclk is not configed.
Searching camera sensor on device: /proc/device-tree/soc/cam/vcon@0 i2c bus: 6 mipi rx phy: 0
INFO: Found sensor name:imx219-30fps on mipi rx csi 0, i2c addr 0x10, config_file:linear_1920x1080_raw10_30fps_2lane.c
2024/12/14 12:38:17.478 !INFO [CamInitParam][0279]Setting VSE channel-0: input_width:1920, input_height:1080, dst_w:1920, dst_h:1080
2024/12/14 12:38:17.479 !INFO [CamInitParam][0279]Setting VSE channel-1: input_width:1920, input_height:1080, dst_w:1920, dst_h:1080
2024/12/14 12:38:17.479 !INFO [vp_vin_init][0041]csi0 ignore mclk ex attr, because mclk is not configed at device tree.
... 省略 ...
capture time :0
temp_ptr.data_size[0]:4147200
capture time :1
temp_ptr.data_size[0]:4147200
capture time :2
temp_ptr.data_size[0]:4147200
capture time :3
temp_ptr.data_size[0]:4147200
capture time :4
temp_ptr.data_size[0]:4147200
... 省略 ...
```

保存的文件：

```bash
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_0.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_1.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_2.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_3.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_4.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_5.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_6.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_7.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_8.raw
-rw-r--r-- 1 root video 4147200 Dec 14 12:38 raw_9.raw
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_0.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_1.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_2.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_3.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_4.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_5.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_6.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_7.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_8.yuv
-rw-r--r-- 1 root video 3110400 Dec 14 12:38 yuv_9.yuv
```

## ROS 应用

### 功能介绍

本示例使用 YOLOv8 目标检测算法，实现订阅 MIPI 摄像头发布的图片，经过算法推理后发布算法 msg，通过 websocket package 实现在 PC 端浏览器上渲染显示发布的图片和对应的算法结果。

模型使用[COCO数据集](http://cocodataset.org/)进行训练，支持的目标检测类型包括人、动物、水果、交通工具等共 80 种类型。

代码仓库：https://github.com/D-Robotics/hobot_dnn

### 实现步骤

```bash
# 配置tros.b环境
source /opt/tros/humble/setup.bash

# 配置MIPI摄像头
export CAM_TYPE=mipi

# 启动launch文件
ros2 launch dnn_node_example dnn_node_example.launch.py dnn_example_config_file:=config/yolov8workconfig.json dnn_example_image_width:=640 dnn_example_image_height:=640
```

终端会输出如下 Log :

```bash
[example-3]  model_name: 
[example-3] [WARN] [1734150496.829080954] [dnn_example_node]: model_file_name_: /opt/hobot/model/x5/basic/yolov8_640x640_nv12.bin, task_num: 4
[example-3] [BPU_PLAT]BPU Platform Version(1.3.6)!
[example-3] [HBRT] set log level as 0. version = 3.15.54.0
[example-3] [DNN] Runtime version = 1.23.10_(3.15.54 HBRT)
[example-3] [A][DNN][packed_model.cpp:247][Model](2024-12-14,12:28:16.917.894) [HorizonRT] The model builder version = 1.23.6
[example-3] [WARN] [1734150497.032746099] [dnn_example_node]: Get model name: yolov8n_640x640_nv12 from load model.
[example-3] [WARN] [1734150497.032959099] [dnn_example_node]: Create ai msg publisher with topic_name: hobot_dnn_detection
[example-3] [WARN] [1734150497.046993438] [dnn_example_node]: Create img hbmem_subscription with topic_name: /hbmem_img
[mipi_cam-1] [WARN] [1734150499.038839668] [mipi_cam]: [init]->cap F37 init success.
[mipi_cam-1] 
[mipi_cam-1] [WARN] [1734150499.039121668] [mipi_cam]: Enabling zero-copy
[example-3] [WARN] [1734150499.136643335] [dnn_example_node]: Loaned messages are only safe with const ref subscription callbacks. If you are using any other kind of subscriptions, set the ROS_DISABLE_LOANED_MESSAGES environment variable to 1 (the default).
[hobot_codec_republish-2] [WARN] [1734150499.136643251] [hobot_codec]: Loaned messages are only safe with const ref subscription callbacks. If you are using any other kind of subscriptions, set the ROS_DISABLE_LOANED_MESSAGES environment variable to 1 (the default).
[hobot_codec_republish-2] [WARN] [1734150499.136958250] [HobotVenc]: init_pic_w_: 640, init_pic_h_: 640, alined_pic_w_: 640, alined_pic_h_: 640, aline_w_: 16, aline_h_: 16
[example-3] [WARN] [1734150499.136956459] [dnn_example_node]: Recved img encoding: nv12, h: 640, w: 640, step: 640, index: 0, stamp: 1734150499_136107128, data size: 614400, comm delay [0.8273]ms
[example-3] [WARN] [1734150500.175391091] [dnn_example_node]: Sub img fps: 31.25, Smart fps: 31.25, pre process time ms: 3, infer time ms: 8, post process time ms: 4
[hobot_codec_republish-2] [WARN] [1734150500.259715501] [hobot_codec]: sub nv12 640x640, fps: 34.0136, pub jpeg, fps: 34.0136, comm delay [2.5714]ms, codec delay [4.4857]ms
[example-3] [WARN] [1734150501.195177356] [dnn_example_node]: Sub img fps: 30.36, Smart fps: 30.42, pre process time ms: 1, infer time ms: 7, post process time ms: 4
[hobot_codec_republish-2] [WARN] [1734150501.285234834] [hobot_codec]: sub nv12 640x640, fps: 30.2439, pub jpeg, fps: 30.2439, comm delay [0.0000]ms, codec delay [1.5806]ms
[example-3] [WARN] [1734150502.140048396] [dnn_example_node]: Recved img encoding: nv12, h: 640, w: 640, step: 640, index: 91, stamp: 1734150502_139378439, data size: 614400, comm delay [0.6543]ms
```

输出 log 显示，发布算法推理结果的 `topic` 为 `hobot_dnn_detection`，订阅图片的 `topic` 为 `/hbmem_img`。

在 PC 端的浏览器输入 `http://IP:8000/TogetheROS/` 即可查看图像和算法渲染效果（IP为RDK的IP地址）：

![render_web](https://developer.d-robotics.cc/rdk_doc/assets/images/yolov2_render_web-aabc0e6d5533da341af44c67c11e20ee.jpeg)

## 结语

以上就是本教程的全部内容啦，欢迎各位继续探索😁
