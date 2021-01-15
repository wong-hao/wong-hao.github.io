---
title: Code Review of Helper programs 'LoRa_PKT_FWD'
summary: This is an code review and comments
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
url_code: https://github.com/wong-hao/sx1302_hal
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

# 1 JSON configuration file: global.config
```cpp
{
    "SX130x_conf": { //parameters for the Lora concentrator board；大部分prase的参数都通过lgw函数写到concentrator
        "spidev_path": "/dev/spidev0.0",
        "lorawan_public": true,
        "clksrc": 0,
        "antenna_gain": 0, /* antenna gain, in dBi */
        "full_duplex": false,
        "precision_timestamp": {
            "enable": false,
            "max_ts_metrics": 255,
            "nb_symbols": 1
        },
        //第一个SX1250
        "radio_0": {
            "enable": true, //包含了rx_enable与tx_enable
            "type": "SX1250",
            "single_input_mode": true,
            "freq": 470600000, //RX frequency
            "rssi_offset": -207.0,
            "rssi_tcomp": {"coeff_a": 0, "coeff_b": 0, "coeff_c": 20.41, "coeff_d": 2162.56, "coeff_e": 0},
            "tx_enable": true, 
            /* check TX frequency before trying to queue packet */ //thread_down
            "tx_freq_min": 470000000, 
            "tx_freq_max": 510000000,
            /* check TX power before trying to queue packet, send a warning if not supported */ //thread_down
            //tx_gain: transmit gain; lut: Look-Up-Table
            "tx_gain_lut":[ 
                {"rf_power": -6, "pa_gain": 0, "pwr_idx":  0},
                {"rf_power": -3, "pa_gain": 0, "pwr_idx":  1},
                {"rf_power":  0, "pa_gain": 0, "pwr_idx":  2},
                {"rf_power":  3, "pa_gain": 1, "pwr_idx":  3},
                {"rf_power":  6, "pa_gain": 1, "pwr_idx":  4},
                {"rf_power": 10, "pa_gain": 1, "pwr_idx":  5},
                {"rf_power": 11, "pa_gain": 1, "pwr_idx":  6},
                {"rf_power": 12, "pa_gain": 2, "pwr_idx":  7},
                {"rf_power": 13, "pa_gain": 1, "pwr_idx":  8},
                {"rf_power": 14, "pa_gain": 2, "pwr_idx":  9},
                {"rf_power": 16, "pa_gain": 2, "pwr_idx": 10},
                {"rf_power": 20, "pa_gain": 3, "pwr_idx": 11},
                {"rf_power": 23, "pa_gain": 3, "pwr_idx": 12},
                {"rf_power": 25, "pa_gain": 3, "pwr_idx": 13},
                {"rf_power": 26, "pa_gain": 3, "pwr_idx": 14},
                {"rf_power": 27, "pa_gain": 3, "pwr_idx": 15}
            ]
        },
        //第二个SX1250
        "radio_1": {
            "enable": true,
            "type": "SX1250",
            "single_input_mode": true,
            "freq": 471400000,
            "rssi_offset": -207.0,
            "rssi_tcomp": {"coeff_a": 0, "coeff_b": 0, "coeff_c": 20.41, "coeff_d": 2162.56, "coeff_e": 0},
            "tx_enable": false //SELECTED RF_CHAIN IS DISABLED FOR TX ON SELECTED BOARD
        },
        //The SX1302 digital baseband chip contains ten programmable reception paths.
        //Receive 8 LoRa channels multi-data rates (SF5 ~ SF12 / 125 Simultaneously kHz) 
        "chan_multiSF_0": {"enable": true, "radio": 0, "if": -300000},
        "chan_multiSF_1": {"enable": true, "radio": 0, "if": -100000},
        "chan_multiSF_2": {"enable": true, "radio": 0, "if":  100000},
        "chan_multiSF_3": {"enable": true, "radio": 0, "if":  300000},
        "chan_multiSF_4": {"enable": true, "radio": 1, "if": -300000},
        "chan_multiSF_5": {"enable": true, "radio": 1, "if": -100000},
        "chan_multiSF_6": {"enable": true, "radio": 1, "if":  100000},
        "chan_multiSF_7": {"enable": true, "radio": 1, "if":  300000},
        //high mono-data rate
        //用于网关间通讯
        "chan_Lora_std":  {"enable": true, "radio": 1, "if": -200000, "bandwidth": 250000, "spread_factor": 7,
                           "implicit_hdr": false, "implicit_payload_length": 17, "implicit_crc_en": false, "implicit_coderate": 1},
        //and FSK 50 kbps
        "chan_FSK":       {"enable": true, "radio": 1, "if":  300000, "bandwidth": 125000, "datarate": 50000}
    },

    "gateway_conf": { //gateway parameters；所有prase的参数都存为全局变量
        "gateway_ID": "fffedca6320e9516",
        /* change with default server address/ports */
        "server_address": "loragw.things.qcloud.com",
        "serv_port_up": 1700,
        "serv_port_down": 1700,
        /* adjust the following parameters for your network */
        "keepalive_interval": 10,
        "stat_interval": 30,
        "push_timeout_ms": 100,
        /* forward only valid packets */
        "forward_crc_valid": true,
        "forward_crc_error": false,
        "forward_crc_disabled": false,
        /* Beaconing parameters */
        "beacon_period": 0,
        "beacon_freq_hz": 869525000,
        "beacon_datarate": 9,
        "beacon_bw_hz": 125000,
        "beacon_power": 14,
        "beacon_infodesc": 0
    },

    "debug_conf": {
        "ref_payload":[
            {"id": "0xCAFE1234"},
            {"id": "0xCAFE2345"}
        ],
        "log_file": "loragw_hal.log"
    }
}
```

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

#### 2.1.1.1 thread
1. [```pthread_mutex_lock() / pthread_mutex_unlock()```](https://blog.csdn.net/yusiguyuan/article/details/40627775)，注意每次使用时lock的pthread_mutex_t对象都不同
2. [```pthread_join()```](https://ask.csdn.net/questions/267690?utm_medium=distribute.pc_relevant_t0.none-task-ask_topic-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-ask_topic-BlogCommendFromBaidu-1.control)
3. [```phtread_cancel()```](https://www.cnblogs.com/lijunamneg/archive/2013/01/25/2877211.html)
4. - [```time()```](https://blog.csdn.net/zyex1108/article/details/45154211)
   - [```gtime()```](https://www.runoob.com/cprogramming/c-function-gmtime.html)
   - [```strftime()```](https://blog.csdn.net/SanQi_Abao/article/details/94964665)：获得stat_timestamp：目前的格林威治时间
   - [```clock_gettime```](https://blog.csdn.net/weixin_44880138/article/details/102605681)

#### 2.1.1.2 socket
1. [```setsockopt()```](https://zhuanlan.zhihu.com/p/77023584)
2. [```send()```](https://www.cnblogs.com/jianqiang2010/archive/2010/08/20/1804598.htmll)
3. [```recv()```](https://www.cnblogs.com/fnlingnzb-learner/p/8523206.html)
4. [```getaddrinfo()```](https://www.jianshu.com/p/df165c54d0b2)
5. [```getaddrinfo()```](https://blog.csdn.net/u011003120/article/details/78277133)
   - for (q=result; q!=NULL; q=q->ai_next)：简单来说就是遍历。因为一个域名可能不止一个IP地址，所以，需要遍历res中的next，如下，是否还有下一个节点(即是否还有下一个IP地址信息)
6. [```getnameinfo()```]()
7. [```connect()```](https://blog.csdn.net/cj83111/article/details/5364138)

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

#### 2.1.1.3 String
1. [```snprintf()```](https://baike.baidu.com/item/snprintf())
2. [```modf()```](https://www.runoob.com/cprogramming/c-function-modf.html)

#### 2.1.1.4 Sequence
[```#if...#endif ```](https://blog.csdn.net/dwell548560/article/details/76187095)

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

## 2.5 注意
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
   6. ```5 threads```

   作用：2~4显示固定内容，thread_up显示UPSTREAM json内容，main显示5 threads得到的统计内容，详见3.2节
   
---
### 3.1.2 5 threads
/* --- THREAD 1: RECEIVING PACKETS AND FORWARDING THEM ---------------------- */

   ```thread_up```：
   1. ```sig_handler```
   2. ```difftimespec```
   
   ``lgw_receive``接收数据包rxpkt，并根据其属性构建UPSTREAM的datagram
---
/* --- THREAD 2: POLLING SERVER AND ENQUEUING PACKETS IN JIT QUEUE ---------- */

   ```thread_down```：
   1. ```sig_handler```
   2. ```difftimespec```
   3. ```crc16```
   4. ```get_tx_gain_lut_index``` //jit_enqueue前查tx_gain_lut表
   5. ```send_tx_ack```：返回jit_enqueue相关错误；gps_enabled与gps_ref_valid任意一个不满足都会发送JIT_ERROR_GPS_UNLOCKED，说明gps出问题统一用**UNLOCKED**代替

   - ```jit_init```：对应THREAD 2：thread_down

   - ```jit_enqueue```：对应THREAD 2：thread_down
  
---
/* --- THREAD 3: CHECKING PACKETS TO BE SENT FROM JIT QUEUE AND SEND THEM --- */
//大部分的处理都在thread_down jit_enqueue时判断，发射时需要判断的东西很少，最多看是否lgw发射成功
   ```thread_jit```：
   1. ```sig_handler```
   2. ```print_tx_status```

   - ```jit_peek```：对应THREAD 3：thread_jit

   - ```jit_dequeue```：对应THREAD 3：thread_jit

   - ```jit_queue_is_full/jit_queue_is_empty```：被上述四个函数调用的private functions
      
   分析：是从gateway到node的通信,  [Just-In-Time" downlink scheduling](https://gitee.com/rejeee/gw1302s/tree/master/packet_forwarder)
---
/* --- THREAD 4: PARSE GPS MESSAGE AND KEEP GATEWAY IN SYNC ----------------- */ 

// [In order for a LoRaWAN network to support Class B devices, all the LoRaWAN gateways in this network need to have a built-in GPS timing source, so they all can be synchronized to the exact beacon timing.](https://lora-developers.semtech.com/library/tech-papers-and-guides/lorawan-class-b-devices)
https://lora-developers.semtech.com/library/tech-papers-and-guides/lora-and-lorawan/
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

## 3.2 Empirical experiment

```cpp
//main(): display version informations
*** Packet Forwarder ***
Version: 1.0.5
*** SX1302 HAL library version info ***
Version: 1.0.5;
***
//main(): display host endianness
INFO: Little endian host
INFO: found configuration file global_conf.json, parsing it

//parse_SX130x_configuration(): point to the gateway configuration object
INFO: global_conf.json does contain a JSON object named SX130x_conf, parsing SX1302 parameters
INFO: spidev_path /dev/spidev0.0, lorawan_public 1, clksrc 0, full_duplex 0
INFO: antenna_gain 0 dBi
INFO: Configuring legacy timestamp
INFO: Configuring Tx Gain LUT for rf_chain 0 with 16 indexes for sx1250
INFO: radio 0 enabled (type SX1250), center frequency 471400000, RSSI offset -207.000000, tx enabled 1, single input mode 1
INFO: radio 1 enabled (type SX1250), center frequency 475000000, RSSI offset -207.000000, tx enabled 0, single input mode 1
INFO: Lora multi-SF channel 0>  radio 0, IF -300000 Hz, 125 kHz bw, SF 5 to 12 //Rb与Rs都由SF和BW决定
INFO: Lora multi-SF channel 1>  radio 0, IF -100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 2>  radio 0, IF 100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 3>  radio 0, IF 300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 4>  radio 1, IF -300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 5>  radio 1, IF -100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 6>  radio 1, IF 100000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora multi-SF channel 7>  radio 1, IF 300000 Hz, 125 kHz bw, SF 5 to 12
INFO: Lora std channel> radio 1, IF -200000 Hz, 250000 Hz bw, SF 7, Explicit header
INFO: FSK channel> radio 1, IF 300000 Hz, 125000 Hz bw, 50000 bps datarate //Rb (bps = bit/s);

//parse_gateway_configuration(): point to the gateway configuration object
INFO: global_conf.json does contain a JSON object named gateway_conf, parsing gateway parameters
INFO: gateway MAC address is configured to 0016C001FF10D3F6
INFO: server hostname or IP address is configured to "47.110.36.225"
INFO: upstream port is configured to "1700"
INFO: downstream port is configured to "1700"
INFO: downstream keep-alive interval is configured to 10 seconds
INFO: statistics display interval is configured to 30 seconds
INFO: upstream PUSH_DATA time-out is configured to 100 ms
INFO: packets received with a valid CRC will be forwarded
INFO: packets received with a CRC error will NOT be forwarded
INFO: packets received with no CRC will NOT be forwarded
INFO: Beaconing period is configured to 0 seconds //disable class B beacon
INFO: Beaconing signal will be emitted at 869525000 Hz
INFO: Beaconing datarate is set to SF9
INFO: Beaconing modulation bandwidth is set to 125000Hz
INFO: Beaconing TX power is set to 14dBm
INFO: Beaconing information descriptor is set to 0

//parse_debug_configuration(): point to the gateway configuration object
INFO: global_conf.json does contain a JSON object named debug_conf, parsing debug parameters
INFO: got 2 debug reference payload
INFO: reference payload ID 0 is 0xCAFE1234
INFO: reference payload ID 1 is 0xCAFE2345
INFO: setting debug log file name to loragw_hal.log

//main(): Board reset
CoreCell reset through GPIO7...

//"single_input_mode": true
INFO: Configuring SX1250_0 in single input mode
INFO: Configuring SX1250_1 in single input mode

//main(): starting the concentrator
INFO: [main] concentrator started, packet can now be received
INFO: concentrator EUI: 0x0016c001ff10d3f6
```
---

```cpp
INFO: [down] PULL_ACK received in 35ms
INFO: [down] PULL_ACK received in 37ms

INFO: Received pkt from mote: 1350200C (fcnt=13)

//thread_up(): start of JSON structure
JSON up: {"rxpk":[{"jver":1,"tmst":137795171,"chan":4,"rfch":1,"freq":471.100000,"mid":0,"stat":1,"modu":"LORA","datr":"SF12Bw125","codr":"4/5","rssis":-84,"lsnr":-5.0,"foff":786,"rssi":-80,"size":13,"data":"gQwgUBMADQANAKvN7w=="}]} //datr由SF、Bw计算得；freq是frequence of Concentrator “IF” channel on which the frame was；rssis、foff是SX1302新增的；
received

INFO: [up] PUSH_ACK received in 40ms
INFO: [down] PULL_ACK received in 34ms

//main(): display a report
##### 2020-10-18 10:49:51 GMT #####

### [UPSTREAM] ###
# RF packets received by concentrator: 1 //thread_up
# CRC_OK: 100.00%, CRC_FAIL: 0.00%, NO_CRC: 0.00% //thread_up
# RF packets forwarded: 1 (13 bytes) //thread_up(thread_up)
# PUSH_DATA datagrams sent: 2 (360 bytes) //PUSH_DATA：thread_up(thread_up)：比packet多的原因是有stat发
# PUSH_DATA acknowledeged: 100.00% //PUSH_ACK：thread_up

### [DOWNSTREAM] ###
# PULL_DATA sent: 3 (100.00%) //PULL_DATA(PULL_ACK)：thread_down
# PULL_RESP(onse) datagrams received: 0 (0 bytes) //PULL_RESP：thread_down(thread_down)
# RF packets sent to concentrator: 0 (0 bytes) //thread_jit发送jit里的downlink packet(thread_down)
/*sent = ok + error*/
# TX errors: 0 //RF packets sent to concentrator失败的；其实这个应该在TX rejected下面，毕竟这里是lgw_send错误，跟jit_peek/jit_dequeue的错误都没关系，更别说下面的jit_enqueue错误

if (cp_nb_tx_requested != 0 ) //thread_down根据PULL_RESP得到的downlink_type使得packet enqueue jit
# TX rejected... //TX_ACK：packet send_tx_ack发送jit_enqueue得到的jit_result；注意与TX errors无关


### SX1302 Status ###
# SX1302 counter (INST): 150921885
# SX1302 counter (PPS): 0
# BEACON queued: 0 //thread_down根据JIT_PKT_TYPE_BEACON使得beacon enqueue jit
# BEACON sent so far: 0 //RF packets sent to concentrator对应的
# BEACON rejected: 0 //BEACON queued失败的

### [JIT] ### //jit = RF chains
src/jitqueue.c:442:jit_print_queue(): INFO: [jit] queue is empty //thread_down：已经enqueue jit[0]里的beacon和packet
#--------
src/jitqueue.c:442:jit_print_queue(): INFO: [jit] queue is empty //thread_down:已经enqueue jit[1]里的beacon和packet

### [GPS] ###
# GPS sync is disabled
### Concentrator temperature 0 C ###
##### END #####
```
```cpp
/*main()生成： generate a JSON report (will be sent to server by upstream thread)
thread_up()发送：add status report if a new one is available ('status report': "stat" object)

stat和上面的rxpt对象同属于一个json，即“同时发送显示”；但是因为程序运行顺序原因“同时发送不同时显示”了
优点：rxok和dwnb是接收/发射成功数，rxpt只有接收/发射总数，挺有用的；其他的都可以在rxpt找到
*/
JSON up: {"stat":{"time":"2020-10-18 10:49:51 GMT","rxnb":1,"rxok":1,"rxfw":1,"ackr":100.0,"dwnb":0,"txnb":0,"temp":0.0}}

INFO: [up] PUSH_ACK received in 34ms
```
---

```cpp
//thread_up()
INFO: End of upstream thread

//main(): if an exit signal was received, try to quit properly + Board reset
INFO: concentrator stopped successfully
CoreCell reset through GPIO7...
INFO: Exiting packet forwarder program
```

## 3.3 Basic communication protocol between LoRa gateway and Network Server
> 可查的最详细的相关资料
1. [PROTOCOL](https://gitee.com/rejeee/gw1302s/blob/master/packet_forwarder/PROTOCOL.md)
2. [LoRaWAN Network Server Demonstration: Gateway to Server Interface Definition](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVJ4VTcxc1RCNFJEaFhVU0RUTXlXdU1CNHc1NHQyZ3JhT3NvdUpVckszYkt0dz9lPUVoWXBMdw==.mp3)
3. [剖析 LoRaWAN Gateway 核心代码](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVJXaXJLdnJiVEZPaS1xUGk3OFFfVTRCUm4tM1dwLXVwNTNLdVJ6ZE9leDllZz9lPXR6OWh3eA==.mp3)
4. [PROTOCOL相关](https://blog.csdn.net/m0_38008027/article/details/89086175)

### 3.3.1 UPstream protocol
1. PUSH_DATA packet：thread_up
2. PUSH_ACK packet：thread_up

### 3.3.2 Downstream protocol
1. PULL_DATA packet：thread_down
2. PULL_ACK packet：thread_down
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
   
这与global.config中的"SX130x_conf"紧密相关：["SX130x_conf" that should contain the parameters for the Lora concentrator board (RF channels definition, modem parameters, etc) ](https://gitee.com/rejeee/gw1302s/tree/master/packet_forwarder)

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
- [ ] 温度相关
- [ ] RF packets forwarded比PUSH_DATA datagrams sent少
- [ ] DEPENDANCIES相关的函数定义及实现
- [ ] "SX130x_conf"相关，如RF chain、IF chain等参数
- [ ] 我现在发的是不是confirmed message：发帖问之前先实验不连接NS会不会被网关ACK就知道是不是NS发的downlink了
  When I look at the gateway log, do the following messages indicate that a **confirmed message** is being sent:
```cpp
...
INFO: [up] PUSH_ACK received in 34ms
...
INFO: [down] PULL_ACK received in 56ms
...
# PUSH_DATA acknowledeged: **100.00% **
...
```

Because I see it's better not to use comfirmed message, so if this is a sign that has already used it, what kind of settings should be adopted and how can I use uncomfirmed message instead.

# 7 TODO
- [ ]优化2.0.1版本代码
- [ ]测试2.0.1版本是否能够正常收发数据，注意修改global.config接受频率
- [ ]查看2.0.1是否适应新版本树莓派系统
- [ ]若上述都满足，保存旧版本修改代码，新fork代码并添加注释 



