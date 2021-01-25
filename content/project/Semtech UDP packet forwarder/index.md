---
title: Semtech UDP packet forwarder
summary: Code Review of Helper programs 'LoRa_PKT_FWD'
tags:
- Wireless Communication
date: "2020-10-21T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: 

image:
  caption: Photo by Chirpstack
  focal_point: Smart

#links:
#- icon: twitter
#  icon_pack: fab
#  name: Follow
#  url: https://twitter.com/georgecushen
url_code: https://github.com/wong-hao/sx1302_hal/blob/master/packet_forwarder/src/lora_pkt_fwd.c
url_pdf: ""
url_slides: ""
url_video: ""

# Slides (optional).
#   Associate this project with Markdown slides.
#   Simply enter your slide deck's filename without extension.
#   E.g. `slides = "example-slides"` references `content/slides/example-slides.md`.
#   Otherwise, set `slides = ""`.
#slides: example
---
# 0 Code Version
## 0 Github
1. [repo](https://github.com/Lora-net/sx1302_hal)

2. Reference design: 
   - [SX1303CTSXXXGW1](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1303ctsxxxgw1)，可从[Added support for SX1303 chip, for further Fine Timestamping support.](https://github.com/Lora-net/sx1302_hal/releases/tag/V2.0.0)看出
   - [SX1302CSSXXXGW1](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cssxxxgw1)，可从[Added support for Listen-Before-Talk for AS923 region / Added support for Spectral Scan with additional sx1261 radio](https://github.com/Lora-net/sx1302_hal/releases/tag/V2.0.0)看出
   - [SX1302CFDXXXGW1](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cfdxxxgw1)：可从[Merged the master-fdd-cn490 branch to bring support for CN490 Full-Duplex reference design](https://github.com/Lora-net/sx1302_hal/releases/tag/V2.0.0)看出

## 1 Gitee
1. [repo](https://gitee.com/rejeee/gw1302s)

2. Reference design: 
   - [SX1302CxxxGW1](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cxxxgw1)
   - 与GitHub的[区别](https://gitee.com/rejeee/gw1302s/commit/b06b85a7c87fce9f7d462f55b5913a3dc0c47df9)：
     - 使用``#if USE_I2C_SENSOR ... #endif``注释了 [I2C](https://github.com/Lora-net/sx1302_hal/issues/28#issuecomment-755148306) 相关代码 (Source Insight 4.0不能看出哪些代码不能实际运行，而VS code可以通过虚化代码显示)
     - 测试程序中默认的**boardconf.lorawan_public = true**改成了**boardconf.lorawan_public = false**，即 **private** 即 **0x12**；但这与**global.config**默认[同步字](https://forum.chirpstack.io/t/relation-between-sync-word-private-network-and-end-nodes/191/13?u=haowong)设置不同，故实际收发与测试时，终端同步字设置相反
     - **SX1302_RESET_PIN=23**改成了**SX1302_RESET_PIN=7**且去掉了其他GPIO引脚代码 (必须用GPOI7，其他没啥用)


# 1 JSON configuration file: global.config
[本人配置好的](https://github.com/wong-hao/sx1302_hal/blob/master/packet_forwarder/global_conf.json)

## 1.1 参数含义
1. [AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/13154904.html)
2. [Rimelink](https://blog.csdn.net/jiangjunjie_2005/article/details/54603122)
3. IF chain: [Semetech](https://lora-developers.semtech.com/knowledge-base/faq/P40)
4. antenna gain: [Semetech](https://lora-developers.semtech.com/knowledge-base/faq/P40)

# 2 DEPENDANCIES

## 2.1 c library function
```cpp
#include <stdint.h>         /* C99 types */
#include <stdbool.h>        /* bool type */
#include <stdio.h>          /* printf, fprintf, snprintf, fopen, fputs */
#include <inttypes.h>       /* PRIx64, PRIu64... */

#include <string.h>         /* memset */
#include <signal.h>         /* sigaction */
#include <time.h>           /* time, clock_gettime, strftime, gmtime */
#include <sys/time.h>       /* timeval */
#include <unistd.h>         /* getopt, access */
#include <stdlib.h>         /* atoi, exit */
#include <errno.h>          /* error messages */
#include <math.h>           /* modf */

#include <sys/socket.h>     /* socket specific definitions */
#include <netinet/in.h>     /* INET constants and stuff */
#include <arpa/inet.h>      /* IP address conversion stuff */
#include <netdb.h>          /* gai_strerror */

#include <pthread.h>
```

### 2.1.1 复杂函数

#### 2.1.1.1 signal
1. https://blog.csdn.net/pmt123456/article/details/53544295
2. [```system()```](https://blog.csdn.net/joker0910/article/details/6646336)

#### 2.1.1.2 unistd
1. https://blog.csdn.net/u014470361/article/details/84110216

#### 2.1.1.3 thread
1. [```pthread_mutex_lock() / pthread_mutex_unlock()```](https://blog.csdn.net/yusiguyuan/article/details/40627775)，注意每次使用时lock的pthread_mutex_t对象都不同
2. [```pthread_create()```](https://blog.csdn.net/liangxanhai/article/details/7767430)
3. [```pthread_join()```](https://ask.csdn.net/questions/267690?utm_medium=distribute.pc_relevant_t0.none-task-ask_topic-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-ask_topic-BlogCommendFromBaidu-1.control)
4. [```phtread_cancel()```](https://www.cnblogs.com/lijunamneg/archive/2013/01/25/2877211.html)
5. - [```time()```](https://blog.csdn.net/zyex1108/article/details/45154211)
   - [```gtime()```](https://www.runoob.com/cprogramming/c-function-gmtime.html)
   - [```strftime()```](https://blog.csdn.net/SanQi_Abao/article/details/94964665)：获得stat_timestamp：目前的格林威治时间
   - [```clock_gettime```](https://blog.csdn.net/weixin_44880138/article/details/102605681)

#### 2.1.1.4 socket: 用于gateway与server通信
1. [```getaddrinfo()```](https://www.jianshu.com/p/df165c54d0b2)
2. [```getaddrinfo()```](https://blog.csdn.net/u011003120/article/details/78277133)
   - for (q=result; q!=NULL; q=q->ai_next)：简单来说就是遍历。因为一个域名可能不止一个IP地址，所以，需要遍历res中的next，如下，是否还有下一个节点(即是否还有下一个IP地址信息)
3. [```socket()```](http://c.biancheng.net/view/2131.html)
4. [```getnameinfo()```](https://blog.csdn.net/ahafg/article/details/52908705)
5. [```connect()```](https://blog.csdn.net/cj83111/article/details/5364138)
6. [```send()```](https://www.cnblogs.com/jianqiang2010/archive/2010/08/20/1804598.htmll)
7. [```recv()```](https://www.cnblogs.com/fnlingnzb-learner/p/8523206.html)
8. [```setsockopt()```](https://allen.blog.csdn.net/article/details/71078044?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.control)
9. [字节序](https://www.ruanyifeng.com/blog/2016/11/byte-order.html)

sock_up：
- init：main
- send：PUSH_DATA 
- recv：PUSH_ACK 

sock_down：
- init：main
- send：PULL_DATA 
- recv：
  - PULL_ACK 
  - PULL_RESP 

#### 2.1.1.5 String
1. [```snprintf()```](https://baike.baidu.com/item/snprintf())
2. [```modf()```](https://www.runoob.com/cprogramming/c-function-modf.html)

#### 2.1.1.6 Sequence
[```#if...#endif ```](https://blog.csdn.net/dwell548560/article/details/76187095): Source Insight 4.0不能看出哪些代码不能实际运行，而VS code可以通过虚化代码显示

## 2.2 Third party libraries：libtools
```cpp
#include "parson.h"  //.. / libtools / src / parson.c
#include "base64.h" //.. / libtools / src / base64.c
```

### 2.2.1 复杂函数
[Parson library](http://kgabis.github.io/parson/)


## 2.3 Core library: libloragw
> statically linked with the libloragw Lora concentrator library

> The sx1302_hal is a host driver/HAL to build a Corecell reference design which communicates through SPI with a concentrator board based on Semtech SX1302 multi-channel modem and SX1250 RF transceivers
```cpp
#include "loragw_hal.h" //.. / libloragw / src / loragw_hal.c
#include "loragw_aux.h" //.. / libloragw / src / loragw_aux.c
#include "loragw_reg.h" //.. / libloragw / src / loragw_reg.c
#include "loragw_gps.h" //.. / libloragw / src / loragw_gps.c
```
## 2.4 jitqueue
```cpp
#include "jitqueue.h" //.. / packet_forwarder / src / jitqueue.c
#include "trace.h" //.. / packet_forwarder / inc / trace.h
```

## 2.5 tools
lgw_receive接收到的包调用其crc函数校验，与private function: crc16有本质区别

## 2.6 注意
1. .h文件介绍用法，.c文件介绍实现，都需要看
2. jit_*函数和lgw _*_*函数的用法：
   - a = function(b, &c, ...)
     - a：判断函数是否调用 (执行)成功的返回值
     - b：函数利用的变量
     - c：***函数改变的/得到的变量***



# 3 PRIVATE FUNCTIONS and threads
## 3.1 Theory
### 3.1.1 main
   ```main```：
   1. ```usage```：``./lora_pkt_fwd -h``
   2. ```parse_SX130x_configuration```
   3. ```parse_gateway_configuration```
   4. ```parse_debug_configuration```
   5. ```sig_handler```
   6. ```6 threads```

   作用：2~4显示固定内容，thread_up显示UPSTREAM json内容，main显示6 threads得到的统计内容，详见3.2节
   
---
### 3.1.2 5 threads
/* --- THREAD 1: RECEIVING PACKETS AND FORWARDING THEM ---------------------- */

   ```thread_up```：
   1. ```sig_handler```
   2. ```difftimespec```
   
   ``lgw_receive``: gateway从node接收rxpkt，并根据其属性构建UPSTREAM的datagram
---
/* --- THREAD 2: POLLING SERVER AND ENQUEUING PACKETS IN JIT QUEUE ---------- */

   ```thread_down```：
   1. ```sig_handler```
   2. ```difftimespec```
   3. ```crc16```：对发射的包进行crc检验
   4. ```get_tx_gain_lut_index``` //jit_enqueue前查tx_gain_lut表
   5. ```send_tx_ack```：返回jit_enqueue相关错误；gps_enabled与gps_ref_valid任意一个不满足都会发送JIT_ERROR_GPS_UNLOCKED，说明gps出问题统一用**UNLOCKED**代替

   - ```jit_init```：对应THREAD 2：thread_down

   - ```jit_enqueue```：对应THREAD 2：thread_down

   - ``lgw_receive``: gateway向node发送数据包
  
---
/* --- THREAD 3: CHECKING PACKETS TO BE SENT FROM JIT QUEUE AND SEND THEM --- */
//大部分的处理都在thread_down jit_enqueue时判断，发射时需要判断的东西很少，最多看是否lgw发射成功
   ```thread_jit```：
   1. ```sig_handler```
   2. ```print_tx_status```

   - ```jit_peek```：对应THREAD 3：thread_jit

   - ```jit_dequeue```：对应THREAD 3：thread_jit

   - ```jit_queue_is_full/jit_queue_is_empty```：被上述四个函数调用的private functions
      
   分析：是从gateway到node的通信,  [Just-In-Time" downlink scheduling](https://github.com/Lora-net/sx1302_hal/tree/master/packet_forwarder)
---
/* --- THREAD 4: PARSE GPS MESSAGE AND KEEP GATEWAY IN SYNC ----------------- */ 

- [In order for a LoRaWAN network to support Class B devices, all the LoRaWAN gateways in this network need to have a built-in GPS timing source, so they all can be synchronized to the exact beacon timing.](https://lora-developers.semtech.com/library/tech-papers-and-guides/lorawan-class-b-devices)

- https://lora-developers.semtech.com/library/tech-papers-and-guides/lora-and-lorawan/

   ```thread_gps```：
   1. ```gps_process_sync```：time_reference_gps；具体的GPS <-> timestamp TIME REFERENCE
   2. ```gps_process_coords```

---
/* --- THREAD 5: CHECK TIME REFERENCE AND CALCULATE XTAL CORRECTION --------- */

   ```thread_valid```：
   1. ```sig_handler```
   2. 变量控制
   - TIME REFERENCE：gps_ref_valid；用于判断是否启用TIME REFERENCE
   - XTAL CORRECTION：
     - xtal_correct_ok
     - xtal_correct
---
/* --- THREAD 6: BACKGROUND SPECTRAL SCAN                           --------- */

```thread_spectral_scan```：略

---

## 3.2 Empirical experiment

```cpp
pi@raspberrypi:~/sx1302_hal/bin $ ./lora_pkt_fwd
*** Packet Forwarder ***
Version: 2.0.1
*** SX1302 HAL library version info ***
Version: 2.0.1;
***
INFO: Little endian host
INFO: found configuration file global_conf.json, parsing it
INFO: global_conf.json does contain a JSON object named SX130x_conf, parsing SX1302 parameters
INFO: com_type SPI, com_path /dev/spidev0.0, lorawan_public 1, clksrc 0, full_duplex 0
INFO: antenna_gain 0 dBi
INFO: Configuring legacy timestamp
INFO: no configuration for SX1261
INFO: Configuring Tx Gain LUT for rf_chain 0 with 16 indexes for sx1250
INFO: radio 0 enabled (type SX1250), center frequency 475500000, RSSI offset -207.000000, tx enabled 1, single input mode 1
INFO: radio 1 enabled (type SX1250), center frequency 476500000, RSSI offset -207.000000, tx enabled 0, single input mode 1
INFO: Lora multi-SF channel 0>  radio 0, IF -300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 1>  radio 0, IF -100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 2>  radio 0, IF 100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 3>  radio 0, IF 300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 4>  radio 1, IF -300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 5>  radio 1, IF -100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 6>  radio 1, IF 100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 7>  radio 1, IF 300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora std channel> radio 1, IF -200000 Hz, 250000 Hz bw, SF 7, Explicit header
INFO: FSK channel> radio 1, IF 300000 Hz, 125000 Hz bw, 50000 bps datarate
INFO: global_conf.json does contain a JSON object named gateway_conf, parsing gateway parameters
INFO: gateway MAC address is configured to 0016C001FF10D3F6
INFO: server hostname or IP address is configured to "47.110.36.225"
INFO: upstream port is configured to "1700"
INFO: downstream port is configured to "1700"
INFO: downstream keep-alive interval is configured to 10 seconds
INFO: statistics display interval is configured to 30 seconds
INFO: upstream PUSH_DATA time-out is configured to 100 ms
INFO: packets received with a valid CRC will be forwarded
INFO: packets received with a CRC error will be forwarded
INFO: packets received with no CRC will NOT be forwarded
INFO: Reference latitude is configured to 31.268575 deg
INFO: Reference longitude is configured to 120.746857 deg
INFO: Reference altitude is configured to 100 meters
INFO: fake GPS is enabled
INFO: Beaconing period is configured to 0 seconds
INFO: Beaconing signal will be emitted at 869525000 Hz
INFO: Beaconing datarate is set to SF9
INFO: Beaconing modulation bandwidth is set to 125000Hz
INFO: Beaconing TX power is set to 14dBm
INFO: Beaconing information descriptor is set to 0
INFO: global_conf.json does contain a JSON object named debug_conf, parsing debug parameters
INFO: got 2 debug reference payload
INFO: reference payload ID 0 is 0xCAFE1234
INFO: reference payload ID 1 is 0xCAFE2345
INFO: setting debug log file name to loragw_hal.log
CoreCell reset through GPIO7...
SX1261 reset through GPIO7...
CoreCell power enable through GPIO18...
CoreCell ADC reset through GPIO13...
Opening SPI communication interface
Note: chip version is 0x10 (v1.0)
INFO: Configuring SX1250_0 in single input mode
INFO: Configuring SX1250_1 in single input mode
INFO: using legacy timestamp
INFO: LoRa Service modem: configuring preamble size to 8 symbols
ARB: dual demodulation disabled for all SF
INFO: [main] concentrator started, packet can now be received
```
---

```cpp
INFO: [down] PULL_ACK received in 45 ms
INFO: [down] PULL_ACK received in 44 ms
INFO: [down] PULL_ACK received in 54 ms

INFO: Received pkt from mote: 1350200C (fcnt=2)

JSON up: {"rxpk":[{"jver":1,"tmst":29446526,"chan":5,"rfch":1,"freq":476.400000,"mid": 1,"stat":1,"modu":"LORA","datr":"SF12BW125","codr":"4/5","rssis":-86,"lsnr":-1.2,"foff":-124,"rssi":-83,"size":20,"data":"QQwgUBMAAgACAEExQjJDM0Q0RTU="}]}
INFO: [up] PUSH_ACK received in 51 ms

##### 2021-01-25 03:17:49 GMT #####
### [UPSTREAM] ###
# RF packets received by concentrator: 1
# CRC_OK: 100.00%, CRC_FAIL: 0.00%, NO_CRC: 0.00%
# RF packets forwarded: 1 (20 bytes)
# PUSH_DATA datagrams sent: 1 (244 bytes)
# PUSH_DATA acknowledged: 100.00%
### [DOWNSTREAM] ###
# PULL_DATA sent: 3 (100.00% acknowledged)
# PULL_RESP(onse) datagrams received: 0 (0 bytes)
# RF packets sent to concentrator: 0 (0 bytes)
# TX errors: 0
### SX1302 Status ###
# SX1302 counter (INST): 30877572
# SX1302 counter (PPS):  0
# BEACON queued: 0
# BEACON sent so far: 0
# BEACON rejected: 0
### [JIT] ###
src/jitqueue.c:440:jit_print_queue(): INFO: [jit] queue is empty
#--------
src/jitqueue.c:440:jit_print_queue(): INFO: [jit] queue is empty
### [GPS] ###
# GPS *FAKE* coordinates: latitude 31.26857, longitude 120.74686, altitude 100 m
ERROR: invalid I2C file descriptor
### Concentrator temperature unknown ###
##### END #####

JSON up: {"stat":{"time":"2021-01-25 03:17:49 GMT","lati":31.26857,"long":120.74686,"alti":100,"rxnb":1,"rxok":1,"rxfw":1,"ackr":100.0,"dwnb":0,"txnb":0,"temp":0.0}}
INFO: [up] PUSH_ACK received in 52 ms
```

---


```cpp
INFO: End of upstream thread

INFO: End of JIT thread

INFO: End of downstream thread
Closing SPI communication interface
INFO: concentrator stopped successfully
CoreCell reset through GPIO7...
SX1261 reset through GPIO7...
CoreCell power enable through GPIO18...
CoreCell ADC reset through GPIO13...
INFO: Exiting packet forwarder program
```

## 3.3 Basic communication protocol between LoRa gateway and Network Server
> 可查的最详细的相关资料
1. [PROTOCOL](https://github.com/Lora-net/sx1302_hal/blob/master/packet_forwarder/PROTOCOL.md)
2. [LoRaWAN Network Server Demonstration: Gateway to Server Interface Definition](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVJ4VTcxc1RCNFJEaFhVU0RUTXlXdU1CNHc1NHQyZ3JhT3NvdUpVckszYkt0dz9lPUVoWXBMdw==.mp3)
3. [剖析 LoRaWAN Gateway 核心代码](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVJXaXJLdnJiVEZPaS1xUGk3OFFfVTRCUm4tM1dwLXVwNTNLdVJ6ZE9leDllZz9lPXR6OWh3eA==.mp3)
4. [PROTOCOL相关](https://blog.csdn.net/m0_38008027/article/details/89086175)

### 3.3.1 UPstream protocol
1. PUSH_DATA packet：thread_up
2. PUSH_ACK packet：thread_up

### 3.3.2 Downstream protocol
1. PULL_DATA packet：thread_down
2. PULL_ACK packet：thread_down
   - 与Confirmed message没有一点关系，这个是NS自动发送的
3. PULL_RESP packet：thread_down
   
   prase这些packet的json分辨downlink packet class，决定发射时间（与beacon无关）
   - Class C：imme (immediately)
   - Class A：tmst (timestamp)，一般不采用：因为global.config中gps_tty_path与beacon_period的设置
   - Class B：tmms (GPS time -> timestamp)
  
   根据数据包类型将其``jit_enqueue``到jit_queue
4. TX_ACK packet：thread_down


# 4 Compile, install and run instructions
卖家给的[攻略](https://www.cnblogs.com/answerinthewind/p/13154904.html)：

在**gw1302s**执行```make clean all```，调用所有helper program的make clean，即Clean and compile everything

再执行```make install```，调用所有helper program的make install，把可执行程序都```scp```到gw1302s/bin中

# 5 硬件
## 5.1 组成
1. multi-channel SX1302 baseband IC
2. two SX1250 RF transceivers
   
这与global.config中的"SX130x_conf"紧密相关：["SX130x_conf" that should contain the parameters for the Lora concentrator board (RF channels definition, modem parameters, etc) ](https://github.com/Lora-net/sx1302_hal/tree/master/packet_forwarder)

## 5.2 Concentrator
[SX1302CxxxGW1](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cxxxgw1)：[Corecell ref design User Guide V1.2](https://semtech.my.salesforce.com/sfc/p/E0000000JelG/a/2R000000HV3G/an5Oh4KfiY7tAO1MO7RiPOj08WUpOTjPHSF57_.NlJ0?__hstc=212684107.9a923fbb2062c017aeab3e39902f6094.1606449781040.1608212935544.1608641620506.43&__hssc=212684107.2.1608641620506&__hsfp=2049775356)

1. The SX1302 digital baseband chip is a highly innovative digital signal processing engine 
equipped with 16 modulators, capable of demodulating 64 combinations of LoRa packets, 
and 2 separate modulators
2. The SX1250 is a highly integrated RF to IQ transceiver capable of supporting multiple 
modulation schemes over the 150-960 MHz ISM frequency bands.  
3. One SX1302 and two SX1250 transceivers are used to complete an eight channel LoRa 
concentrator. 

## 5.3 与程序的关系
thread_jit入队出队的队列正是两个SX1250对应的radio_0、radio_1

# 6 问题
- [ ] DEPENDANCIES相关的函数定义及实现



