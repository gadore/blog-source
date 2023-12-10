---
title: 群晖DSM系统下的VideoStation修改ffmpeg以支持DTS、AAC、HEVC等播放格式
date: 2023-06-21 13:32:29
postUrl: archives/1687328820096
cover: /upload/bliss%20600dpi.jpg
description: 可以看出我基本上是以购买尺寸、重量都比较便携的设备为主。
tags:
- 程序员
- NAS
categories:
- 明明白白玩NAS
---
![bliss 600dpi.jpg](/upload/bliss%20600dpi.jpg)前几天微软发布了WindowsXP 经典蓝天白云壁纸，但是是重制版，我找到了这个原版4K分辨率的，趁此机会发出来好了

如题，每次DSM更新都会导致我的VideoStation没法播放部分有DTS音轨的视频，手动更新了两次，后来又自己写了bash脚本来自动更新，但每次都会有各种各样的小问题，甚是困扰。

去网上搜索各种ln和mv backup脚本，每次更新之后脚本内容都不一定完全兼容，太痛苦了。

干脆，上Github。

果然，已经有人做了自动化修补脚本，在此记录分享一下。

有条件的可以直接看源项目：

**项目地址：**[**VideoStation-FFMPEG-Patcher**](https://github.com/AlexPresso/VideoStation-FFMPEG-Patcher)

如果网络条件不好可以参考我下方的搬运内容。

## 系统支持情况

-   DSM 6.2.2-24922 Update 4 (and above)
    
-   Video Station 2.4.6-1594 (and above)
    
-   SynoCommunity ffmpeg 4.x.x, ffmpeg 5.x.x or ffmpeg 6.x.x ([help](https://synocommunity.com/#easy-install))
    

2023-06-21 DSM7.2亲测可用。

## 当前支持和非支持功能

-   **\[DTS or EAC3 or TrueHD\] + \[Any non HEVC standard video format\]**: ✅
    
-   **\[no DTS, no EAC3, no TrueHD\] + \[HEVC\]**: ✅
    
-   **\[DTS or EAC3 or TrueHD\] + \[HEVC\]**: ⚠️ Not working on architectures where Advanced Media Extensions uses GStreamer for audio decoding
    

## 使用方式和可选参数

### 前提条件

-   Check that you meet the required [dependencies](https://github.com/AlexPresso/VideoStation-FFMPEG-Patcher#dependencies)
    
-   Install SynoCommunity ffmpeg ([help](https://synocommunity.com/#easy-install))
    
-   If you plan to play HEVC / AAC videos, install Advanced Media Extensions
    
-   Connect to your NAS using SSH (admin user required) ([help](https://www.synology.com/en-global/knowledgebase/DSM/tutorial/General_Setup/How_to_login_to_DSM_with_root_permission_via_SSH_Telnet))
    
-   Use the command `sudo -i` to switch to root user
    
-   Use the [following](https://github.com/AlexPresso/VideoStation-FFMPEG-Patcher#usage) command (Basic command) to execute the patch
    
    -   Note : Basic command uses **ffmpeg4** by default, if you want to use a different ffmpeg version (i.e. **ffmpeg5** or **ffmpeg6**), please check the command [flags](https://github.com/AlexPresso/VideoStation-FFMPEG-Patcher#usage).
        
-   You'll have to re-run the patcher everytime you update VideoStation, Advanced Media Extensions and DSM
    

### 运行脚本

![Snipaste_2023-06-21_14-28-21.jpg](/upload/Snipaste_2023-06-21_14-28-21.jpg)傻瓜脚本，一键运行

正常情况下，运行下方的第一行命令即可（没有科学的条件的话，网速会比较慢，耐心等待即可），高端玩家可以自行参考第二行命令及下方表格说明。

Basic command:  
```shell
curl https://raw.githubusercontent.com/AlexPresso/VideoStation-FFMPEG-Patcher/main/patcher.sh | bash
```
With options:  
```shell
curl https://raw.githubusercontent.com/AlexPresso/VideoStation-FFMPEG-Patcher/main/patcher.sh | bash -s -- <flags>
```

| Flags | Required | Description | Default |
| --- | --- | --- | --- |
| \-a | No | Action flag: choose between `patch` or `unpatch` ; example: `-a patch` | patch |
| \-b | No | Branch flag: allows you to choose the wrapper branch to use ; example `-b main` | main |
| \-p | No | Proxy flag: allows you to use a proxy to connect to github ; example `-p http://my-proxy` | [https://github.com](https://github.com/) |
| \-v | No | FFMpeg version flag: allows you to choose desired ffmpeg version ; example `-v 5` | `4` |
