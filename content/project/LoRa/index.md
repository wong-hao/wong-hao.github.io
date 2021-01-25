---
title: LoRa
summary: 111
tags:
- Wireless Communication
date: "2021-01-17T00:00:00Z"

# Optional external URL for project (replaces project detail page).
external_link: ""

image:
  caption: 111
  focal_point: Smart

#links:
#- icon: twitter
#  icon_pack: fab
#  name: Follow
#  url: https://twitter.com/georgecushen
url_code: ""
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

### 1 LoRa调制解调基础知识
1. [Semtech Modulation Basics](https://www.semtech.com/search/results?keywords=Modulation+Basics%2A)
2. [SX1276-7-8-9 Datasheet](https://www.semtech.com/search/results?keywords=SX1276-7-8-9+Datasheet%2A)
3. Everything: [Sakshama Ghoslya](https://www.sghoslya.com/p/lora_9.html)
4. [LoRa重要知识](https://www.jianshu.com/p/7b6a1732ff05)

### 2 chip、chirp/symbol、bit
1. [stackexchange](https://electronics.stackexchange.com/questions/278192/understanding-the-relationship-between-lora-chips-chirps-symbols-and-bits)，结合上面的看
2. [Mobilefish.com](https://lora.readthedocs.io/en/latest/#id11)
3. [CSDN](https://blog.csdn.net/qq_36389327/article/details/82382299)
4. [扩频](https://blog.csdn.net/Ttian6/article/details/94216682?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522161131582716780255248770%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=161131582716780255248770&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-4-94216682.first_rank_v2_pc_rank_v29&utm_term=%E6%89%A9%E9%A2%91&spm=1018.2226.3001.4187)

### 3 逆向工程
1. [Complete Reverse Engineering of LoRa PHY](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVdHa3JTSE4zWlZEc3ptZGIzN2tKUmNCZFhQMU1sSTFHcURFdXJjcGdYTkJrZz9lPXNYOHRIdA==.mp3)
2. [Matthew Knight and Balint Seeber. 2016. Decoding LoRa: Realizing a Modern LPWAN with SDR. Proceedings ofthe GNURadio Conference 1, 1 (2016).](https://pubs.gnuradio.org/index.php/grcon/article/view/8)

### 4 数据包格式 /  Lora Packet Format
1. [Core LoRaWAN Specification](https://lora-alliance.org/lorawan-for-developers)：最官方的文件，下面的东西都有包含
2. [AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/6206706.html)
3. [Sakshama Ghoslya](https://www.sghoslya.com/p/lora-is-chirp-spread-spectrum.html)
4. [DecodingLora](https://revspace.nl/DecodingLora)
5. [Implicit header and Explicit header](https://www.rfwireless-world.com/Terminology/What-is-difference-between-Implicit-header-and-Explicit-header-in-LoRaWAN-packet.html)
6. [Demodulation](https://myriadrf.org/news/lora-modem-limesdr/)
7. [Semtech](https://lora-developers.semtech.com/knowledge-base/faq/P40)
8. [LoRa数据包在线解码](https://lorawan-packet-decoder-0ta6puiniaut.runkit.sh/)
   - [Live LoRaWAN frames网页](http://47.110.36.225:8080/#/organizations/1/gateways/0016c001ff10d3f6/frames)
     - [base64解码1](https://cryptii.com/)
     - [base64解码2](https://forum.chirpstack.io/t/receiving-decrypted-device-data-frmpayload/501/24?u=haowong)
     - [base64解码3](https://www.base64decode.org/)
     - [ascii码表](https://baike.baidu.com/item/ASCII/309296?fromtitle=ascii%E7%A0%81%E8%A1%A8&fromid=19660475&fr=aladdin)
   - [gateway-bridge](https://www.chirpstack.io/gateway-bridge/payloads/events/)得到base64 encoded LoRaWAN frame
   - [applicaiton](https://www.chirpstack.io/application-server/integrations/mqtt/)自动解码

### 5 仿真代码
1. [Sakshama Ghoslya](https://www.sghoslya.com/p/how-to-buy-lora-matlab-codes.html)

### 6 传输时间
1. [test_loragw_toa.c](https://github.com/Lora-net/sx1302_hal/blob/master/libloragw/tst/test_loragw_toa.c)
2. [calculator](https://www.semtech.com/search/results?keywords=calculator%2A)
3. [lora tools](https://www.loratools.nl/#/airtime)
4. [TTN Limitations fair access policy](https://www.thethingsnetwork.org/forum/t/limitations-data-rate-packet-size-30-seconds-uplink-and-10-messages-downlink-per-day-fair-access-policy-guidelines/1300)
5. [TTN calculator](https://www.thethingsnetwork.org/airtime-calculator/#)

### 7 功耗
1. [msoon](https://doc.sm.tc/station/index.html)
2. [利尔达](https://zhuanlan.zhihu.com/p/107189375)