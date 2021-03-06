## BoneEngine@Lite系统框图


![image | left](https://gw.alicdn.com/tfs/TB14Cb5xamWBuNjy1XaXXXCbXXa-1992-1076.jpg)

## 芯片及硬件

BoneEngine@Lite 可支持多种不同架构的Soc,CPU,MCU:

* 芯片模组：
    * 乐鑫ESP32，ESP8266，
    * 庆科EMW3060，EMW3080，
    * STMicroelectronics的STM32xx等模组芯片；
* 可以运行在IA32/IA64 PC上，用于调试或开发的仿真器；
* 也可以运行在边缘 Linux 网关（MIPS），树莓派（ARM）等资源丰富的设备上。

## 内核及驱动

BoneEngine@Lite 可以运行在各种嵌入式设备的RTOS内核上:

* AliOS Things;
* FreeRTOS;
* None OS；

BoneEngine@Lite 还提供虚拟设备，可运行在Windows，Linux，MacOS等Host主机上，做为调试器使用。

## 系统及硬件抽象层

BoneEngine@Lite 为支持跨平台，跨OS系统能力，针对不同OS，Soc芯片和硬件，统一封装抽象接口，这样客户在应用或产品切换到不同平台时，上层应用不用替换，从而可以保证快速移植和产品化，也能达到“一份代码，处处运行”的目标。

OSAL（系统抽象层）将 BoneEngine 所需的与操作系统的相关函数进行封装。 目前 BoneEngine 主要需要内核提供任务调度，定时器，进程间通讯等基本功能，由于AOS和其他RTOS函数接口不一致，因此系统抽象层将完成操作系统函数的统一。

硬件抽象层则统一不同平台的硬件驱动接口。针对不同平台提供的外部硬件接口，对接硬件接口或者驱动实现统一封装，供BoneEngine@Lite的Native eXtension Modules 如 HW 模块调用。 目前Gravity支持的硬件模块包括:

* GPIO
* I2C
* I2S
* ADC
* PWM
* UART
* SPI
* Board
* WIFI
* Bluetooth
* Flash

## BoneEngine@Lite 应用编程框架

应用编程框架是 BoneEngine@Lite 最主要部分，既包括JS应用的运行环境，也包含了大量的扩展模块，扩展模块通过API的方式暴露给JS应用开发者调用。框架的目的在于：一方面隔离应用对底层硬件和操作系统的依赖，一方面封装大量的扩展接口，方便让开发者在JS层调用，另外，扩展模块由Native C实现，可以提高性能，整个框架包括 :

* Javascript 引擎（BoneEngine@Lite）
* 硬件内置扩展模块： GPIO，I2S，I2C，ADC，PWM，SPI，WIFI，Flash
* IoT相关服务，组件的本地扩展模块： 如 MQTT，HTTP，CoAP
* 服务
    * AppManager 应用管理
    * BoardManager 板级配置管理
    * debugger 设备调试
* 应用组件
    * 标准设备模型：阿里标准设备模型，包括设备三要素（属性，事件，方法）
    * 硬件驱动代理：应用程调用硬件驱动的代理，
    * Cloud-client ：通过MQTT和阿里 Link Platform 平台连接

### OS 抽象及硬件设备抽象


![image | left](https://gw.alicdn.com/tfs/TB1xyKIxkyWBuNjy0FpXXassXXa-904-548.jpg)

OSAL 与硬件抽象层的目的是：

* 隔离 BoneEngine@Lite 与底层 OS 系统的依赖，便于跨OS移植
* 隔离 BoneEngine@Lite 与底层硬件平台依赖，便于跨芯片移植

OS抽象层设计原则：

* 根据目前 AOS 已提供的API封装BoneEngine需要的OSAL接口
* 其他 OS( FreeRTOS,Linux,MacOS)，统一按照以上定义接口实现和封装

硬件抽象层设计原则：

* AOS 已经提供了HAL层，BoneEngine直接和验证使用该接口
* 其他 OS( FreeRTOS,Linux,MacOS)按照以上接口实现和封装

数据接口定义：

* 任务(task)：create,destroy，delay
* 事件（event)：post,register
* 定时器（timer) post,register
* 硬件 IO： \* GPIO：init/deinit,ouput\_high/low,input\_get,enable/disable\_irq \* UART：init/deinit,send,recv \* ADC：init/deinit,get \* I2C：init/deinit,master\_send/recv, slave\_send/recv, mem\_send/recv \* SPI：init/deinit,send,recv,send\_recv \* Timer：init/deinit,start,stop \* FLASH：get\_info,erase,erase\_write,read,secure,desecure

###应用管理 AppManager AppManager管理应用的安装，下载，运行. 

![image | left](https://gw.alicdn.com/tfs/TB1nMhUxgmTBuNjy1XbXXaMrVXa-954-696.jpg)


在 BoneEngine@Lite 中，为了便于JS应用程序的安全存储，分发和管理，对应用程序打成APP-PACK，该包中，包含了文件应用程序的文件列表，版本，包签名证书等信息。在云端和本地开发环境包含了打包和拆包工具，可以把整个JS程序，board-cfg.json配置文件以及其他库文件打包成整个应用包。

例如：一个普通Gravity应用程序可以如下几个文件组成：

```
    .
├── drivers
│   ├── door.js
│   ├── fan.js
│   ├── light.js
│   └── temp.js
├── index.js
├── device.js
├── README.md
└── version.js
```

具体说明：

```
index.js
   JS应用程序执行入口
device.js
   设备上云相关操作类
version.js
   JS应用程序版本信息模块文件
drivers
   JS应用需要使用到的设备对象模块，与具体硬件相关，操作buildin的hw对象
```

### 板级配置管理 BoardManager

板级配置管理服务主要负责：

* 动态配置板级的管脚，端口映射
* 生成 BoneEngine 的静态HW全局JS对象
* 管理板级硬件资源初始化，资源分配以及释放


![image | left](https://gw.alicdn.com/tfs/TB1N3NUxgmTBuNjy1XbXXaMrVXa-998-614.jpg)

典型板级配置 board\_config.json 文件如下：

```
{
    /*串口配置*/
    "UART":[
        {
        "id":"uart1",
        "port":1,
        "data_width":3,
        "baud_rate":115200,
        "stop_bits":0,
        "flow_control":0,
        "parity_config":0
        },
        {
        "id":"uart2",
        "port":2,
        "data_width":3,
        "baud_rate":115200,
        "stop_bits":0,
        "flow_control":0,
        "parity_config":0
        }
    ]
}
```

board\_config.json 配置文件可以和APP-PACK一起下发，也可以在出厂时直接烧写，如果系统没有该配置文件，使用缺省映射。

### Javascript Engine

Javascript Engine 专门为资源有限的设备提供的轻量级的 Javascript 引擎，相对于V8，SpideMonkey 功能强大的JS引擎，BoneEngine@Lite 不支持JIT功能，但是对JS语法集支持做了精简，而针对IoT特定需求，对硬件驱动，操作系统函数，以及MQTT，MESH等功能模块实现了JS语法扩展，开发者可以直接通过JS API调用这些功能模块

BoneEngine@Lite 具有如下特点：

* 资源占用：RAM<15KB,ROM(Footprint) <15KB
* CPU性能：优化的词法和句法分析器，降低CPU使用率
* JS 支持能力：面向IOT的 ES 精简语法 ，CommonJS 支持，IoT 内置模块

 