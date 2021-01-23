---
title: 'A Cloud-Optimized Link Layer for Low-Power Wide-Area Networks'
subtitle: '第四次汇报'
summary: 
authors:
- admin
tags:
categories:
- Error correction
date: "2020-07-22T00:00:00Z"
lastmod: ""
featured: false
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Placement options: 1 = Full column width, 2 = Out-set, 3 = Screen-width
# Focal point options: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight
image:
  placement: 2
  caption: ''
  focal_point: ""
  preview_only: false

url_project: https://wise.ece.cmu.edu/projects/opr.html

url_video: https://www.youtube.com/watch?v=7Yv2q1auvK4

url_source: https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVRLXzBKYnZYdUJPbmpoYXZYeWZEcndCVEkxZUlPWWY2QjQzTzVwLXFyS0JnQT9lPVp2eXlhOQ==.mp3

url_slides: https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVgtZ1lOM3BJZnRKbnRYY1ZoX2VESVVCZWFobXRoUXM0NGxkRGFtdm51bzkxdz9lPXp4TWttcQ==.mp3

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
<!--more-->
### 1 问题

- [x] 删去一些不要的内容
- [x] 可以写稿子，但一定不能按稿念，会对不上
- [x] Frame Structure和7.2里的安全部分
- [ ] 如何获得原来的frame，比如获得哪些位置错了
- [ ] 是PHYpayload吗，如果不是可以只看FRAMEpayload吗
- [x] [FEC与CRC的区别](https://bbs.csdn.net/topics/80119439)
- [ ] 怎么禁用FEC的
- [ ] 给作者发邮件请教：BCL长什么样，其0-100体现在哪，如何trace RSSI throughout the duration of a packet，纠错的过程究竟是什么，是对所有copy都纠错吗
- [ ] LoRaServer[7]
- [x] focus on upstream packets
- [x] [no open sourced](https://github.com/OpenChirp/lorawan-service/issues/3#issuecomment-765803005)


### 2 源文件


#### 2.1 作者的主页 
[点击下载](http://artur.balanuta.com/)

#### 2.2 会议的详情 
[MobiSys 2020](https://www.sigmobile.org/mobisys/2020/)
