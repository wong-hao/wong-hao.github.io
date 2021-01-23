---
title: 'SCLoRa Leveraging Multi-Dimensionality in Decoding Collided LoRa Transmissions'
subtitle: '保留的第二篇文章'
summary: 
authors:
- admin
tags:
categories:
- Multiple access
date: "2020-09-09T00:00:00Z"
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

url_source: https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOmI6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRVdGYnVIUlBydUpDcXRQWUE4X0E3NFFCUmdNWHhxdTN3dFhJYnNMN0tmc2lwdz9lPXV4ekwwZw==.mp3

url_pdf: 

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---
<!--more-->

### 1 问题

- [x] 快速傅里叶变换加窗
- [x] 快速傅里叶变换频谱泄露
- [ ] 快速傅里叶变换频谱信道衰落
- [x] 离散傅里叶变换采样
- [ ] MATLAB信号处理
- [x] 频率偏移相关和semenar no.2相关
- [ ] 美化ppt布局
- [x] 根据作者演讲和ppt修改自己的ppt
- [x] 与第四次汇报的区别：(1)Physical Layer / Link Layer，(2)**many**(devices)-**to**-**one**(gateway) / **one**(device)-**to**-**many**(gateways)


### 2 源文件


#### 2.1 作者的演讲
[46′10″](https://www.youtube.com/watch?v=Fnw-Qz6QyLc&feature=youtu.be)

#### 2.2 作者的ppt
[下载](https://onedrive.gimhoy.com/sharepoint/aHR0cHM6Ly9zZXVlZHVjbjEtbXkuc2hhcmVwb2ludC5jb20vOnA6L2cvcGVyc29uYWwvMjIwMjA0NjAxX3NldV9lZHVfY24vRWNnWW1PV1dLVGxJZ2dpOFMza3kxRGtCS1BYaDZ2R0pOMS1KS1NKY1RiMnFndz9lPVdLd3RCUg==.mp3)

#### 2.3 会议的详情 
[ICNP 2020](https://icnp20.cs.ucr.edu/program.html#posterdemo)

### 3 傅里叶变换 (Fourier transform)
1. [FT](https://www.jianshu.com/p/4cb34e716fd1)
2. - [FFT1](https://blog.csdn.net/shenziheng1/article/details/52891807)
   - [FFT2](https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E5%82%85%E9%87%8C%E5%8F%B6%E5%8F%98%E6%8D%A2)：用FFT计算DFT会得到与直接用DFT定义计算相同的结果；最重要的区别是FFT更快
3. 算法课给了相关PPT
4. 频谱泄漏与窗函数：
   - [1](https://zhuanlan.zhihu.com/p/24318554)
   - [2](https://zhuanlan.zhihu.com/p/77275353)
   - [3](https://books.google.com.sg/books?id=zqsLHyQFu5wC&pg=PA118&lpg=PA118&dq=%E9%A2%91%E8%B0%B1%E6%B3%84%E6%BC%8F&source=bl&ots=5GNlc_x05D&sig=ACfU3U29UElO5Mm5FcMRDefZNXwDLBFfwg&hl=zh-CN&sa=X&ved=2ahUKEwiazNCh6L3tAhXww4sBHU-sC68Q6AEwBHoECAUQAg#v=onepage&q=%E9%A2%91%E8%B0%B1%E6%B3%84%E6%BC%8F&f=false)
   - [4](https://zhidao.baidu.com/question/514716417.html)
   - [matlab](https://au.mathworks.com/matlabcentral/answers/435508-how-can-i-get-proper-length-of-fft)
5. 采样：https://zhuanlan.zhihu.com/p/74736706
