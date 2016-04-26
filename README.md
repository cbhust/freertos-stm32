# ZE_FreeRTOS_SDK`[freeRTOS V8.2.1+LwIp]`
采用Eclipse Paho MQTT C/C++ Client，兼容V3.1和V3.1.1 MQTT协议。

集成cJson包。

ZE_FreeRTOS_SDK使用说明：

> 开发板上已成功移植了LwIP的RTOS实现，实现了相关以太网（或Wi-Fi）芯片的LwIP驱动程序，可以使用LwIP协议的POSIX接口进行TCP/IP通信，有此基础的开发板才可以使用到ZE_FreeRTOS_SDK。

> 示例程序基于以C语言开发，实现了MQTT v3.1.1协议，适用于M2M和物联网应用。

> 本MQTT同步客户端基于paho项目的C/C++ MQTT Embedded clients开发，在EPL 1.0协议下发布。

> MQTT服务器平台采用[j1st.io](http://j1st.io/)或[j1st.io测试](http://139.198.0.174:3000/signin)。

> 本MQTT同步客户端示例程序环境：

   >- STM32Cube_FW_F4_V1.11.0（包含FreeRToS v8.2.1, LwIP）等
   >- MDK V5.14
   >- 硬件环境：STM32F407，RMII口连接DP83846PHY

## 目录
- Installation
- Usage and API
    - Client
        - Constructor / reinitialise
        - Option functions
        - Connect / reconnect / disconnect
        - Network loop
        - Publishing
        - Subscribe / Unsubscribe
        - Callbacks
        - External event loop support
        - Global helper functions
    - Publish
        - Single
        - Multiple
- Reporting bugs
- More information

### Installation
下载库函数，Inc文件包含.h文件，Src文件夹中包含.c文件，mywork.c是MQTT库的一个实用示例；

将这个包解压，并放入您的工程；

包括：
- cJSON.c, cJSON.h，这两个文件是JSON编解码包，最初期，你可以不管它们；
- 其它文件是MQTT库，其中，MQTTLwIP.c是新的，MQTTClient.c是修改过的，MQTTLwIP是MQTT的LwIP/FreeRToS适配；
- MQTTClient原实现处理得很不好，改进过了；

### Usage and API
#### Getting Started
mywork.c是MQTT库的一个实用示例
- 使用ZE_FreeRTOS_SDK只需导入\"cJSON.h\"和\"MQTTClient.h\";
```c
#include "cJSON.h"
#include "MQTTClient.h"
#include <string.h>
#include <stdlib.h>
```
#### Client
你可以使用SDK封装的函数来实现相关的MQTT操作，如下：

-  使用`` NewNetwork() ``完成网络适配
-  使用`` ConnectNetwork() ``检查TCP/IP栈是否就需并实施TCPIP连接
-  使用`` MQTTClient() `` 为MQTT连接准备空间（此处使用静态变量），创建一个MQTTClient
-  使用`` MQTTConnect() ``完成MQTT服务器登录
-  使用`` MQTTSubscribe() `` 去订阅一个topic然后接收MQTTMessages及消息处理
-  使用`` MQTTPublish() `` 去发送数据包
-  经常性调用`` MQTTYield() `` 以完成MQTT事务操作（例如检查是否有数据包等待处理，数据包的回应，KeepAlive的处理等）
-  使用`` MQTTDisconnect() `` 释放MQTT连接

##### Constructor / reinitialise

##### Option functions

##### Connect / reconnect / disconnect

##### Publishing

##### Subscribe / Unsubscribe

##### Callbacks

##### External event loop support

##### Global helper functions


- TCPClt：#174 - #218是主循环，实际是一个线程的执行函数，NewNetwork完成适配，ConnectNetwork检查TCP/IP栈是否就需并实施TCPIP连接，MQTTClient为MQTT连接准备空间（此处使用静态变量），MQTTConnect完成MQTT服务器登录，MQTTSubscribe注册感兴趣的话题（下行命令接收）,PublishLED实际通过MQTTPublish发送数据包，MQTTYield需要经常性调用以完成MQTT事务操作（例如检查是否有数据包等待处理，数据包的回应，KeepAlive的处理等）。MQTTDisconnect释放MQTT连接
- PublishLED是一个使用JSON格式封装数据并发送的函数;
- messageArrived供回调。当收到数据包以后，本函数被调用。这儿示范了一个对JSON解包的过程（并没有根据最新的格式调整），仅供参考;
- #10-#28 包括了常量定义。实际使用中，需要把相关ID和Token改为从网站上申请的数据。缓冲区也在此定义，请根据实际使用大小调整，以免溢出;
- JSON相关函数从堆中申请内存，请保证堆中有充足空间。
