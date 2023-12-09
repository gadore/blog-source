---
title: 为小米 13 刷入 Andy 大佬的 LineageOS20 类原生系统
date: 2023-05-13 18:04:04
postUrl: archives/1683956999331
cover: /upload/Screenshot_20230513-123633_Trebuchet.png
tags:
- 程序员
- 手机
---

>前提声明

- 本文非正规或官方刷入教程，只是我对自己已知内容的一个汇总；
- 本人非 Rom 的开发人员，也非维护人员，只是按照原作者指引，在自己的小米手机上成功刷入了 GSI 镜像；
- 本人不对你的手机负责，如果由于你的或者我的原因导致手机数据丢失、死机、本人跟本网站不负任何责任；

你大可以把本文当成导航页，来跳转到 XDA 等其他网站去，对此我不排斥，毕竟这只是对我自己刷机流程的一个记录备忘；

## 关于 ROM 适配者
Rom 的适配者是 [Andy Yan (XDA)](https://forum.xda-developers.com/t/gsi-13-lineageos-20-trebledroid-based.4517345/)，首先感谢他的无私工作，让我们可以享受到这个纯净的镜像系统；

如果你不是同样的 Rom 适配的相关人员或对手机刷机有基本的知识了解，请不要打扰他，当然了，如果你想给作者打赏，那就没关系，但打赏不意味着你就是大爷，请自重；

不要问任何人为什么你手机卡死了、你的手机卡米了等问题，请先自己提前了解好所有要准备的知识和操作，这是互联网的基本礼仪；

大佬在酷安跟 XDA 都有账号，如非必然，不要打扰。

## 关于当前 LineageOS20 镜像可用度说明
刷机后你会失去这些东西，你必须知道

- 你会失去所有 MIUI 本地化的优势，NFC 公交卡、本地化快递查询、农历、节假日闹钟等等；
- 你会失去指纹识别（当前还不支持，以后支不支持也不明朗），但为了方便也有别的解决方法，后文会讲；
- 小米的莱卡摄像相关的优化肯定没了，但你可以自己搜索日文大佬的谷歌相机安装，听说还不错，我没试所以不知道；

当然，MIUI自带的反向充电，现在也没有打开的地方了（我反正没找到）

## 有失必有得，这里也有一些诱人的东西

- 干净的不像话的系统，终于不用跟小米广告系统斗智斗勇了
- 漂亮的不像安卓的界面，笑死，安卓原生明明很好看的
- 终于及时的消息通知，MIUI虽然集成了 Google 服务，但 GMS 太残废了，我在 Play store 上下载的应用大部分的推送要么延迟6、7个小时，要么就干脆干没了；现在终于正常了
- 小米百年不修的 Bug ，在原生里直接没有了，包含但不限于：系统字体大大小小粗粗细细非常不统一、B 站放后台十秒钟以上就丢失播放进度（设置后台电池忽略优化也没用）
- Gboard 输入法终于有了正常的高度，MIUI 上 Gboard 就像被 MIUI 砍掉了一双腿一样，非常地矮，打字体验极差。
- 非常小的系统占用和逻辑异常清晰简洁的系统设置，当然，你也可以理解为简陋

## 你还需要知道的非常重要的信息
### 解 BL 锁
小米解 Bootloader 锁直接清除数据，还有可能会失去保修，你的手机数据也会全部消失，所以损失你应该懂的，不要没想清楚就瞎折腾。

### 刷机有风险
这一点是肯定的，有极大可能性，你会刷机失败，手机就变砖了，救砖又是一个非常惊心动魄的经历。

正所谓：愿你。。。。，归来。。。。。

所以你最好还有另外一部手机作为备用，否则你可能会栽的很惨。

### 如果你还是想刷机，请一定看到文章末尾再考虑动手
先准备好电脑的adb环境，如果你之前没有接触过，那你还是不要刷机了。真的，赶紧走，现在还来得及。

下载好镜像资源，链接如下：[Andy Yan 的镜像](https://sourceforge.net/projects/andyyan-gsi/files/)，如果你不知道这些镜像命名意味着什么，赶紧跑。

Andy 大佬同样对 [TD 和 Light](https://forum.xda-developers.com/t/xiaomi-13-gsi.4572035/page-3) 进行了解释，对此我的理解是：如果你用得到 Google 套件，那你就自己找适合自己的镜像就行了，如果你不清楚自己到底选哪个，那就选下载量最高的那个镜像，人民检查过的，准没错。

解 BL 锁，然后手机就重置了，重置后重新简单设置一下进入手机，主要是开一下 adb ，在这个过程中不要升级、不要升级、不要升级
```shell
adb reboot fastboot
```
你应该进入一个使用蓝色字体写着 FASTBOOTD 的字样的界面

如果是橘红色的，那你肯定进错了，这也没关系，试试这个：fastboot reboot fastboot

我假设你已经下载好了镜像，解压，确保后缀是.img

运行：
```shell
fastboot flash system lineageos20.xxxxxx.img
```
正常情况下，命令行应该已经在正常走进度了，耐心等待，好了之后，直接跳到第 4 步

如果你命令行报错说：找不到lineageos20.xxxxx.img，赶紧滚。

如果你的系统还是不小心做了升级，（例如我），你可能会看到类似：Writing 'system' FAILED (remote: 'Partition not found') 或者 not enough space to resize system_b 的报错

别慌，运行：以下命令
```shell
fastboot delete-logical-partition system_ext_a
fastboot delete-logical-partition system_ext_b
fastboot delete-logical-partition system_a
fastboot delete-logical-partition system_b
fastboot delete-logical-partition product_a
fastboot delete-logical-partition product_b
fastboot create-logical-partition system_ext_a 0
fastboot create-logical-partition system_ext_b 0
fastboot create-logical-partition system_a 0
fastboot create-logical-partition system_b 0
fastboot create-logical-partition product_a 0
fastboot create-logical-partition product_b 0
```
这也是在 Andy 大佬的提示下发现的问题，因为小米 13 遵循安卓动态系统分区原则，升级后会把当前系统切换到 b 分区，我们把逻辑分区全部删掉重建为 0 长度后就可以正常刷机了

请保证以上命令运行都是 OK 的，然后你就可以重新尝试 fastboot flash system lineageos20.xxxxxx.img ，正常情况下，你就能跟我一样，看到系统在正常刷入了
```shell
fastboot reboot recovery
```
手动选择清空数据，然后确认，清空后，你就可以重启了。

恭喜你，成功了~

![](/upload/Screenshot_20230513-123633_Trebuchet.png)
![](/upload/Screenshot_20230513-123706_%E8%AE%BE%E7%BD%AE.png)
![](/upload/Screenshot_20230513-123648_Trebuchet.png)
![](/upload/Screenshot_20230513-123735_Trebuchet.png)

## 现在哪些功能是好的
基于我自己的体验和测试：

- 通话测试正常
- WiFi正常
- 4G正常（我移动卡，截图叹号是因为有关了数据网络，流量用完了），5G没测试，待补充
- 待机正常，没有异常耗电
- 可以正常震动反馈
- 推送啊、一般性地使用啊都正常
- 60 / 90 / 120 三档刷新帧率，MIUI 自己都只有 60 和 120 ！

## 哪些功能是不好的
就像前文提到的，指纹识别不行了，但我用 Smart Lock 曲线救国了，在设置、安全，更多安全设置找到它，把你家添加为常用地点，这样你就可以在家享受不输入密码解锁手机的快感了

还有自动亮度，也不能用了。但这个影响不大，手动调节又不是不能用。

## 回到 MIUI、救砖
当然，任何人可能会失败、反悔、变砖、卡米，我也是，这是我自己亲身经历的救砖流程：

到[这个网站](https://xiaomirom.com/)下载小米 13 的线刷包、刷机工具，在 fastboot 下连接电脑，把线刷包解压成文件夹，使用工具重新刷回去即可，只要它正常走刷机流程了，那就没问题，我亲测虽然最后给我的反馈结果是Error，但手机重新恢复了 MIUI 的系统并重启了，喔，刷入前右下角最好选不 Lock，友情提示一下。

如果你没成功救回来，那我也没办法，所以如果你看到这里，还没开始，或者已经怕了，我劝你赶紧走，刷机有风险，动手需谨慎，走啊！！！