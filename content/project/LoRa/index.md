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

# 1 LoRa调制解调基础知识
1. [Semtech Modulation Basics](https://www.semtech.com/search/results?keywords=Modulation+Basics%2A)
2. [SX1276-7-8-9 Datasheet](https://www.semtech.com/search/results?keywords=SX1276-7-8-9+Datasheet%2A)
3. chip、chirp/symbol、bit：
   - [stackexchange](https://electronics.stackexchange.com/questions/278192/understanding-the-relationship-between-lora-chips-chirps-symbols-and-bits)，结合上面的看
   - [Mobilefish.com](https://lora.readthedocs.io/en/latest/#id11)
4. Everything: [Sakshama Ghoslya](https://www.sghoslya.com/p/lora_9.html)
5. 阅读过的paper

# 2 逆向工程
1. [Complete Reverse Engineering of LoRa PHY](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVdHa3JTSE4zWlZEc3ptZGIzN2tKUmNCZFhQMU1sSTFHcURFdXJjcGdYTkJrZz9lPXNYOHRIdA==.mp3)
2. [Matthew Knight and Balint Seeber. 2016. Decoding LoRa: Realizing a Modern LPWAN with SDR. Proceedings ofthe GNURadio Conference 1, 1 (2016).](https://pubs.gnuradio.org/index.php/grcon/article/view/8)

# 3 数据包格式 /  Lora Packet Format
1. [Core LoRaWAN Specification](https://lora-alliance.org/lorawan-for-developers)：最官方的文件，下面的东西都有包含
2. [AnswerInTheWind](https://www.cnblogs.com/answerinthewind/p/6206706.html)
3. [Sakshama Ghoslya](https://www.sghoslya.com/p/lora-is-chirp-spread-spectrum.html)
4. [DecodingLora](https://revspace.nl/DecodingLora)
5. [Implicit header and Explicit header](https://www.rfwireless-world.com/Terminology/What-is-difference-between-Implicit-header-and-Explicit-header-in-LoRaWAN-packet.html)
6. [Demodulation](https://myriadrf.org/news/lora-modem-limesdr/)
7. [Semtech](https://lora-developers.semtech.com/knowledge-base/faq/P40)
8. [LoRa数据包在线解码base64](https://lorawan-packet-decoder-0ta6puiniaut.runkit.sh/)

# 1.3 仿真代码
1. [Sakshama Ghoslya](https://www.sghoslya.com/p/how-to-buy-lora-matlab-codes.html)

