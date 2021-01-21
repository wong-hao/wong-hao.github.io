---
title: LoRaWAN
summary: Rejeee M-HL9-EV, SX1302S and Raspberry pi 4B
tags:
- Wireless Communication
date: "2020-10-06T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption: Photo by Rejeee Company
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

# 1 实验环境
## 1.1 操作系统
> Raspberry Pi OS (32-bit) with desktop

**注意仅能使用Linux Kernel version:[4.19.118](http://downloads.raspberrypi.org/raspios_armhf/release_notes.txt)及以下的版本，否则由于硬件使用GPIO7作为SX1302的RESET，与树莓派的SPI0.1的CS脚[冲突](https://www.cnblogs.com/answerinthewind/p/13154904.html)**
```cpp
// 报错提示
./reset_lgw.sh: 22: echo: echo: I/O error
./reset_lgw.sh: 25: ./reset_lgw.sh: cannot create /sys/class/gpio/gpio7/direction: Directory noexistent
CoreCell reset through GPIO7...
./reset_lgw.sh: 32: ./reset_lgw.sh: cannot create /sys/class/gpio/gpio7/value: Directory noexistent
./reset_lgw.sh: 33: ./reset_lgw.sh: cannot create /sys/class/gpio/gpio7/value: Directory noexistent
ERROR: failed to connect
// 或者是
ERROR: [main] failed to start the concentrator 
```

- 安装与测试教程：
  - [Semtech SX1302CxxxGW1](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cxxxgw1)：[Corecell ref design User Guide V1.2](https://semtech.my.salesforce.com/sfc/p/#E0000000JelG/a/2R000000HV3G/an5Oh4KfiY7tAO1MO7RiPOj08WUpOTjPHSF57_.NlJ0)
  - [M-GW1302S](http://www.njrjzn.com/chanpinzhongxin/SX1302mozuxilie/21.html)：[M-GW1302S 用户手册(2)(1)(1)](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVFEYUo0OHJlX1pPcUxjTUIwR1RyQThCaWVnS0lhVnJYeEVRUmNOLUVyb0xEQT9lPTVudm5BTA==.mp3)
	- 寄存器读写测试：1%概率成功
    ```cpp
    pi@raspberrypi:~/sx1302_hal/bin $ ./test_loragw_reg
    CoreCell reset through GPIO7...
    SX1261 reset through GPIO7...
    CoreCell power enable through GPIO18...
    CoreCell ADC reset through GPIO13...
    Opening SPI communication interface
    Note: chip version is 0x10 (v1.0)
    ## TEST#1: read all registers and check default value for non-read-only registers
    ERROR: default value for register at index 26 is 1, should be 0
    ERROR: default value for register at index 27 is 1, should be 0
    ERROR: default value for register at index 30 is 1, should be 0
    ERROR: default value for register at index 31 is 1, should be 0
    ------------------
    TEST#1 FAILED
    ------------------

    ## TEST#2: read/write test on all non-read-only, non-pulse, non-w0clr, non-w1clr registers
    ------------------
    TEST#2 PASSED
    ------------------

    Closing SPI communication interface
    CoreCell reset through GPIO7...
    SX1261 reset through GPIO7...
    CoreCell power enable through GPIO18...
    CoreCell ADC reset through GPIO13...
    ```
	- 发送测试成功
    ```cpp
    pi@raspberrypi:~/sx1302_hal/bin $ ./test_loragw_hal_tx -r 1250 -f 480.1 -m LORA -b 125 -s 12 -z 20
    Sending 1 LoRa packets on 480100000 Hz (BW 125 kHz, SF 12, CR 1, 20 bytes payload, 8 symbols preamble, explicit header, non-inverted polarity) at 0 dBm
    CoreCell reset through GPIO7...
    SX1261 reset through GPIO7...
    CoreCell power enable through GPIO18...
    CoreCell ADC reset through GPIO13...
    Opening SPI communication interface
    Note: chip version is 0x10 (v1.0)
    INFO: using legacy timestamp //本程序默认值，与global.config配置文件无关
    ARB: dual demodulation disabled for all SF
    TX done

    Nb packets sent: 1 (1)
    Closing SPI communication interface
    CoreCell reset through GPIO7...
    SX1261 reset through GPIO7...
    CoreCell power enable through GPIO18...
    CoreCell ADC reset through GPIO13...
    =========== Test End ===========
    ```
	- 接受测试：官方建议[M-HL9-EV连金黄天线](https://www.bilibili.com/video/BV1Yt411N7MV)，但实测用直黑天线也行，且发包间隔要长，避免丢包接收不到
        ```cpp
        pi@raspberrypi:~/sx1302_hal/bin $ ./test_loragw_hal_rx -r 1250 -a 475.5 -b 476.5
        ===== sx1302 HAL RX test =====
        INFO: rxpkt buffer size is set to 16
        INFO: Select channel mode 0
        CoreCell reset through GPIO7...
        Opening SPI communication interface
        Note: chip version is 0x10 (v1.0)
        INFO: using legacy timestamp
        ARB: dual demodulation disabled for all SF
        Waiting for packets...

        ----- LoRa packet -----
          count_us: 11242393
          size:     18
          chan:     7
          status:   0x10
          datr:     12
          codr:     1
          rf_chain  0
          freq_hz   475900000
          snr_avg:  -10.2
          rssi_chan:128.0
          rssi_sig :118.0
          crc:      0xD228
        81 0C 20 50 13 00 00 00 00 00 31 32 33 34 35 36 0D 0A 
        Received 1 packets (total:1)

        ----- LoRa packet -----
          count_us: 21244909
          size:     18
          chan:     2
          status:   0x11
          datr:     12
          codr:     1
          rf_chain  1
          freq_hz   476500000
          snr_avg:  -6.5
          rssi_chan:142.0
          rssi_sig :136.0
          crc:      0x7ABE
        81 0C 20 50 13 00 01 00 01 10 31 32 33 34 35 06 09 0A 

        ----- LoRa packet -----
          count_us: 21244961
          size:     18
          chan:     7
          status:   0x10
          datr:     12
          codr:     1
          rf_chain  0
          freq_hz   475900000
          snr_avg:  -10.0
          rssi_chan:128.0
          rssi_sig :119.0
          crc:      0xFABE
        81 0C 20 50 13 00 01 00 01 00 31 32 33 34 35 36 0D 0A 
        Received 2 packets (total:2)
        ^C
        Nb valid packets received: 2 CRC OK (1)
        Closing SPI communication interface
        CoreCell reset through GPIO7...
        =========== Test End ===========
      ```

	- 发送接收均用的**Hex**


# 2 实验步骤

## 2.1 [LoRaWAN Specification](https://lora-alliance.org/lorawan-for-developers/)
1. Core LoRaWAN Specification
2. LoRaWAN Regional Parameters
   - 中国
     - [RimeLink](https://blog.csdn.net/jiangjunjie_2005/article/details/54754927)
     - [iotisan](https://blog.csdn.net/iotisan/article/details/55056092)
   - [EU](https://www.sghoslya.com/p/table-01-data-rate-configuration.html)
3. Back-End Interfaces


## 2.2 Devices/End Nodes

  **[M-HL9-EV](http://www.njrjzn.com/chanpinzhongxin/SX127xmozuxilie/4.html)**
  1. [进入配置方法](http://www.rejeee.com/documents/APP_DOC/M-KL9_how_to_enter_configuration_mode.pdf)
  2. [**M-KL9**参数意义](http://www.rejeee.com/documents/APP_DOC/M-KL9_parameter.pdf)，具体配置参数看用户手册
  3. 发送接收均用的**Ascii**
  4. 串口工具注册码: **CCCCC-AAAAA-FFFFF-0E013-6EE85**
  5. 配置
   
  ```cpp
  NET:       Node to Gateway
  TFREQ:     475.5MHz
  RFREQ:     506.5MHz
  POW:       20dBm
  BW:        125kHz
  TSF:       12
  RSF:       12
  CR:        4/5
  MODE:      LORA
  SYNC:      0x34
  PREM:      8, 10
  FIX:       0, 0
  CRC:       ON
  TIQ:       OFF
  RIQ:       ON
  SEQ:       OFF
  IP:        ON
  AES:       OFF
  ACK:       ON //Confirmed message
  LDR:       0, 0
  LCP:       0
  LFT:       0
  RXW:       0
  FNB:       0x00
  TYPE:      0x00
  Flash:     0
  ```
### 2.2.1 入网

#### 2.2.1.1 概述
1. [Back-End Interfaces](https://lora-alliance.org/lorawan-for-developers)：最官方的文件，下面的东西都有包含
2. [AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/6213027.html)
3. [TTN](https://www.thethingsnetwork.org/docs/lorawan/addressing.html)
4. [quending](https://blog.csdn.net/quending/article/details/86481598)


#### 2.2.1.2 ABP
[RAKwireless](https://blog.csdn.net/RAKwireless/article/details/106186894)

[Chirpstack](https://www.chirpstack.io/application-server/use/devices/)

#### 2.2.1.3 OTAA
[RAKwireless](https://blog.csdn.net/RAKwireless/article/details/106135373)

[AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/6213529.html)

#### 2.2.1.4 Security
1. [TTN](https://www.thethingsnetwork.org/docs/lorawan/security.html)
2. [Cyberark](https://www.cyberark.com/resources/threat-research-blog/lorawan-mqtt-what-to-know-when-securing-your-iot-network)
3. [CSDN](https://ask.csdn.net/questions/3109264)

#### 2.2.1.5 Rejeee

[这家的节点不支持LoRaWAN，节点只有私有协议的](https://www.cnblogs.com/answerinthewind/p/13517529.html#4758586)

[8. 芮捷LPWAN协议](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRWVfQkIxUFl2M3RKb01ZUTMxQ1hidXdCZWVoZmdnNkpPUEJxX1FGdzBhb3NzUT9lPWJmbmM4TA==.mp3)

[模块不支持标准LoRaWAN协议模块](http://www.rejeee.com/documents/SPEC/RJDS0052018-M-KL9_Specification_V1.1.pdf)

[视频](https://space.bilibili.com/7682653?spm_id_from=333.788.b_765f7570696e666f.1)

- [ ] 弄清楚[私有网络](https://forum.chirpstack.io/t/relation-between-sync-word-private-network-and-end-nodes/191/13?u=haowong)/非LoRaWAN网络怎么导出数据



## 2.3 Gateway
### 2.3.1 Real module
**SX1302**
1. [Semtech](https://www.semtech.com/products/wireless-rf/lora-gateways/sx1302cxxxgw1)
2. [Rejeee](http://www.njrjzn.com/chanpinzhongxin/SX1302mozuxilie/28.html)

**LoRaWAN gateway software implementation**
1. [UDP Packet forwarder project](https://github.com/Lora-net/sx1302_hal)，默认
2. [LoRa Basics Station](https://tech-journal.semtech.com/expert-series-5-things-you-need-to-know-about-lorawan-based-gateways)
   - LoRa Basics Station = Semtech UDP packet forwarder + ChirpStack Gateway Bridge
   - [源码](https://github.com/lorabasics/basicstation)

**global_conf.json**
  - [Gitee版本](https://gitee.com/rejeee/gw1302s/commit/c425b67a5ccaaef0dd1042da08974245124c069b)直接增加了一个文件**gw1302s/packet_forwarder/global_conf.json**，其特点是**gateway_conf**下面的**server_address**[由localhost修改为了loragw.things.qcloud.com](https://blog.csdn.net/RAKwireless/article/details/107101582)
  - ``make install_conf``即安装**global_conf.json**
  - 卖家给的[攻略](https://www.cnblogs.com/answerinthewind/p/13154904.html)跟用``install.sh``生成的主要差别在radio_1与radio_2的**ferq**不同，攻略的效果好；官方的[参考文献](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVQzOWdIRUc4YU5DbHFMVzBjRGkwVjBCZjZGQmUzWUpkSnh0YVhXbUlLdnFtUT9lPW9qNjEzcg==.mp3)给出了**global_conf.json**的参数意义

### 2.3.2 SDR
1. [GUNRadio](https://www.gnuradio.org/)
2. 与Software-defined radio (SDR)设备共用，如USRP N210、USRP B210、hackrf

## 2.4 LoraWAN Network Server
---
### 2.4.1 Cloud-hosted: Close Source LoRaWAN Network Servers Platforms

  - **global.config**中的**lorawan_public: true**，[连接不通](http://lora.timeddd.com/forum.php?mod=viewthread&tid=521)的元凶，true对应于sync word **0x34**
  - 使用树莓派搭建网关注册到腾讯物联网开发平台
	- 主要步骤
		- [AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/13517529.html#4758586)
	- 补充
		- [获取GwEUI](https://www.wpgdadatong.com/cn/solution/detail?PID=5357)，即在**gw1302s/bin**下使用``./chip_id``，注意还要用该值替换掉**gw1302s/bin/global_conf.json**中的**gateway_ID`**，且需要去掉开头的0x，这是代表十六进制的开头
		- 运行``./lora_pkt_fwd``即可看到[状态在线](https://console.cloud.tencent.com/iotexplorer/project/prj-xpc7prs7/gateway/list?table)
		- 云解析数据
		   - [数据模板1](https://blog.csdn.net/RAKwireless/article/details/107251605)
		   - [数据模板2](https://cloud.tencent.com/document/product/1081/44921)
    - 作用
        - 判断是否真正上网了，或者说真正处理数据了
        ```cpp
        网关名称            GwEUI            描述            区域            运行状态       最后上报时间       添加时间
        raspberrypi1    0016c001ff10d3f6    -              苏州市           在线         2020-10-01        2020-10-01
        raspberrypi2    0016c001ff10d3f7    -              苏州市           在线         2020-10-01        2020-10-01
        ```
    - **[腾讯物联网开发平台](https://console.cloud.tencent.com/iotexplorer)** 对标 **[阿里云物联网络管理平台](https://linkwan.console.aliyun.com/)**
    - **[腾讯物联网通信](https://console.cloud.tencent.com/iothub)** 对标 **[阿里云物联网平台](https://iot.console.aliyun.com/)**
  
---

### 2.4.2 Cloud-hosted: Open Source LoRaWAN Network Servers Platforms
1. - [The Things Network：在线版](https://www.thethingsnetwork.org/)
     - [TTN官方入门](https://console.thethingsnetwork.org/)
     - [TTN](https://learn.adafruit.com/the-things-network-for-feather?view=all)
     - [TTN global.config1](https://www.thethingsnetwork.org/docs/gateways/packet-forwarder/semtech-udp.html)
     - [TTN global.config2](https://www.thethingsnetwork.org/docs/gateways/start/connection.html#semtech-udp-protocol)
     - [Mbed-Os: TTN](https://os.mbed.com/docs/mbed-os/v5.15/tutorials/LoRa-tutorial.html)
     - [SX1302网关转发器修改版讨论](https://www.thethingsnetwork.org/forum/t/sx1302-using-semtech-packet-forwarder-can-not-register-the-gateway/41211/34)：server_address选择[router.cn.thethings.network](https://blog.csdn.net/freemote/article/details/90315395)；温度改为int可保证console显示connected
     - [Limitations](https://www.thethingsnetwork.org/forum/t/limitations-data-rate-packet-size-30-seconds-uplink-and-10-messages-downlink-per-day-fair-access-policy-guidelines/1300)
   - [The Things Stack：安装版](https://www.thethingsindustries.com/docs/)
     - Docket install， [LoRa数据包在线解码base64](https://lorawan-packet-decoder-0ta6puiniaut.runkit.sh/)
   - [Rakwireless](https://forum.rakwireless.com/) 
2. - [ChirpStack：安装版](https://www.chirpstack.io/)
      - Debian install
        - [ChirpStack:Debian 1](https://www.chirpstack.io/project/guides/debian-ubuntu/)
        - [ChirpStack:Debian 2](https://blog.csdn.net/a1989214/article/details/106832570)
          - NS配置文件：**dsn="postgres://chirpstack_ns:dbpassword@localhost/chirpstack_ns?sslmode=disable"**
          - NS配置文件：注释掉所有**extral chanels**
          - AS配置文件：**dsn="postgres://chirpstack_as:dbpassword@localhost/chirpstack_as?sslmode=disable"**
          - AS配置文件：服务器运行``openssl rand -base64 32``，添加到 **JWT_SECRET**：解决了长时间运行导致的**authentication failed: jwt parse error: token is expired by ?h?m?s**问题
          - 查看日志方法与Docker不同：通过命令行
      - Docket install
        - [Fireware: ChirpStack+TTN](https://fiware-lorawan.readthedocs.io/en/latest/users_manual/index.html) 
        - [ChirpStack:docker0](https://www.debugself.com/2019/12/08/lorawan_guide/)
        - [ChirpStack:docker1](https://www.chirpstack.io/project/guides/docker-compose/)
        - [ChirpStack:docker2](https://blog.csdn.net/Mculover666/article/details/104059771)
        - [ChirpStack:docker3](https://www.freesion.com/article/1023121538/)
        - [ChirpStack:docker4](https://blog.csdn.net/iotisan/article/details/90412873)
        - [ChirpStack:docker5](https://blog.csdn.net/iotisan/article/details/78398978)
        - [ChirpStack global.config](https://www.chirpstack.io/gateway-bridge/backends/semtech-udp/)
          - 配置文件：所有都在**chirpstack-docker/configuration**目录中
            - 注释掉NS配置文件中所有[**extral chanels**](https://blog.csdn.net/zoug16/article/details/104928565)
          - Docker分离模式后台启动 ``docker-compose up -d``:需要进入**root/chirpstack-docker**目录，且恢复镜像之后docker不会自动重启
          - 停止部分容器 ``docker ps -a`` + ``docker stop 容器ID``
          - 停止所有容器 ``docker stop $(docker ps -aq)``
          - 查看日志方法：与Debian不同，不通过命令行
            - 显示部分容器日志 ``docker logs -f <container_ID>``
            - 显示所有容器日志  ``docker-compose logs``
      - ["server_address"与ChirpStack Gateway Bridge运行地址一致](https://forum.chirpstack.io/t/semtech-udp-packet-forwarder-setting/9490/2)，Docker安装时ChirpStack Gateway Bridge默认安装在[云端](https://www.chirpstack.io/project/architecture/)，如故这里设为47.110.36.225而不是localhost 
      - [纠错查看网关是否通过网关桥连接上了网络服务器](https://www.chirpstack.io/project/guides/connect-gateway/)
        - ``tcpdump``指令需要通过``sudo apt update && sudo apt install tcpdump``安装
        - 总结下来即查看日志显示、订阅mqtt
    - [Semtech Network Server：在线版](https://lora-developers.semtech.com/resources/network-server/)
      - [指南](https://lora-developers.semtech.com/resources/network-server/faq-network-server/)
        - 在网关上安装ChirpStack Gateway Bridge后与Semtech UDP packet forwarder联用
        - 或直接使用LoRa Basics Station 
      - 优点：可同时选用不同频段，如CN470，EU868... 
    - Mainflux
      - [docs](https://mainflux.readthedocs.io/en/latest/lora/) 
      - [medium](https://medium.com/mainflux-iot-platform/mainflux-lorawan-tutorial-c54632ffee99) 
3. [OpenChirp](https://openchirp.io/)
4. [LoraSim](https://www.lancaster.ac.uk/scc/sites/lora/lorasim.html)
5. [Loriot](https://cn1.loriot.io/dashboard)
6. 上面所有需要自行安装Stack的方法都需要注意：**[阿里云服务器配置](https://help.aliyun.com/document_detail/25475.html?spm=a2c4g.11186623.2.14.81c071afVJYuXn#allowRemoteAccess)**
- ```cpp
  协议类型          端口范围          授权对象
  UDP             8080/8080        0.0.0.0/0
  UDP             1700/1700        0.0.0.0/0
  ```
- ```cpp
  account  password
   admin    admin
  ```
- **服务器地址**: http://47.110.36.225:8080/，不能设置域名解析（需要备案）
- [ECS Linux解决SSH会话连接超时问题](https://help.aliyun.com/document_detail/38034.html)

---

### 2.4.3 Integrated: Gateway-Embedded LoRaWAN Network Servers Platforms

[特点](https://www.actility.com/lorawan-network-server/)

[RAKwireless](https://blog.csdn.net/RAKwireless/article/details/105298319)

---

### 2.4.4 ADR
[Core LoRaWAN Specification](https://lora-alliance.org/lorawan-for-developers)：NS发送``LinkADRReq``到device

[LoraWAN论坛](http://lora.timeddd.com/forum.php?mod=viewthread&tid=428&extra=page%3D1)

[Semtech1](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVZiNEttMzVTVWRMclNTMl91elRLaW9CZTNSLVJCakVVb3JINEFtZ0VpVW5jdz9lPXlFWXI1bA==.mp3)

[Sakshama Ghoslya](https://www.sghoslya.com/p/how-does-lorawan-nodes-changes-their.html)，对上面的补充

[Semtech2](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVNxdEZVbm9hQUpQaGZycl9nbTBDMjBCZFM4Wk1hQzFBZ2l6Z0p5bXJFcTdhQT9lPUhTOU1Gdw==.mp3)

[Semtech3](https://lora-developers.semtech.com/library/tech-papers-and-guides/understanding-adr/)


## 2.5 Application Server console

### 2.5.1 去重
[LoraWAN论坛](http://lora.timeddd.com/forum.php?mod=viewthread&tid=478&extra=page%3D1)

[Semtech](https://lora-developers.semtech.com/library/tech-papers-and-guides/lora-and-lorawan/)

### 2.5.2 下行通信
[freemote](https://blog.csdn.net/freemote/article/details/90315395)

[Chirpstack](https://forum.chirpstack.io/t/how-to-get-downlink-communication/9816/3)

[TheThingsNetwork1](https://www.thethingsnetwork.org/forum/t/how-to-get-downlink-communication/41712/12)

[TheThingsNetwork2](https://www.thethingsnetwork.org/forum/u/haowong/messages/archive)

[TheThingsNetwork3](https://www.thethingsnetwork.org/docs/devices/uno/quick-start.html)

## 2.6 End-application
### 2.6.1 API
#### 2.6.1.1 gRPC
http://47.110.36.225:8080/api

#### 2.6.1.2 MQTT上行通信

1. [流程](https://blog.hobairiku.site/2018/02/26/LoRa-Server-Project/) 
2. Tools
   - Mosquitto
     - 配置方法
       - [MQTT authentication & authorization](https://www.chirpstack.io/project/guides/mqtt-authentication/)
       - [hangge](https://www.hangge.com/blog/cache/detail_2896.html)
       - [Docker ](https://blog.csdn.net/hanhui22/article/details/106693684)
     - Event-topic-template
       - 所有组件都可以在各自的Configuration里找到
       - [坚持坚持](https://www.cnblogs.com/liujiabing/p/13692308.html)
       - [Cyberark](https://www.cyberark.com/resources/threat-research-blog/lorawan-mqtt-what-to-know-when-securing-your-iot-network)
       - 以gateway-bridge为例
         - 注意：gateway-bridge在云端安装的就在云服务器上操作，在网关上安装的就在网关上操作
         - 可以从运行日志``sudo journalctl -u chirpstack-gateway-bridge -f -n 50``可看到topic为**gateway/0016c001ff10d3f6/event/up**，故使用``mosquitto_sub -t "gateway/0016c001ff10d3f6/event/up" -v``
         - 也可以从配置文件``vi /etc/chirpstack-gateway-bridge/chirpstack-gateway-bridge.toml``中找到
         - [在配置文件中设置marshaler="json"后](https://www.chirpstack.io/project/guides/connect-gateway/)``sudo systemctl restart chirpstack-gateway-bridge``重启，可以得到和Live LoRaWAN frames网页接收一样的效果，但没有fcnt；且两者都不接收CRC_EEROR的数据包
       - 以Application server为例
         - [ChirpStack1](https://forum.chirpstack.io/t/forming-downlink-packet-to-lora-node/215/16)
         - [ChirpStack2](https://www.chirpstack.io/application-server/integrations/mqtt/)
         - [Thethingsstack](https://thethingsstack.io/integrations/mqtt/)
         - [Thethingsnetwork1](https://www.thethingsnetwork.org/docs/applications/mqtt/quick-start.html)
         - [Thethingsnetwork2](https://www.thethingsnetwork.org/docs/applications/mqtt/api.html)
   - 其他
     - [mqttorg](https://mqtt.org/software/)
     - [TTN](https://www.thethingsnetwork.org/docs/applications/mqtt/)
     - [2020 年常见 MQTT 客户端工具比较](https://www.emqx.io/cn/blog/mqtt-client-tools)
     - [MQTT.fx1](https://blog.csdn.net/RAKwireless/article/details/106711672)
     - [MQTT.fx2](https://blog.csdn.net/freemote/article/details/106361732)
3. [MQTT数据（有效负载）存储到数据库中的方法](https://www.instructables.com/Store-Messages-From-Mosquitto-MQTT-Broker-Into-SQL/)



#### 2.6.1.3 MQTT下行通信

1. Command-topic-template
   - 所有组件都可以在各自的Configuration里找到
2. 概念性
   - [Semtech1](https://lora-developers.semtech.com/library/tech-papers-and-guides/lorawan-class-a-devices)
   - [Semtech2](https://lora-developers.semtech.com/library/tech-papers-and-guides/lorawan-class-b-devices)
   - [Chirpstack2](https://forum.chirpstack.io/t/how-to-get-downlink-communication/9816/3)
   - [AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/13154904.html)
3. 实际操作
   - [Rimelink](https://blog.csdn.net/jiangjunjie_2005/article/details/104439355)




#### 2.6.1.4 Metadata元数据
[TTN](https://www.thethingsnetwork.org/forum/t/how-to-retrieve-metadata-from-data-storage-integration/19259/2?u=haowong)

### 2.6.2 SDKs & Libraries


### 2.6.3 Integrations
[Lora Cloud](https://www.loracloud.com/portal)
- [Chirpstack](https://www.chirpstack.io/application-server/integrations/loracloud/)
- [TTN](https://www.loracloud.com/documentation/device_management?url=gettingstarted.html#example-minimal-integration-with-the-things-network)

