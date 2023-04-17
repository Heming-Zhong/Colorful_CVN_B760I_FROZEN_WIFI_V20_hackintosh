# 七彩虹CVN B760I FROZEN WIFI V20 ITX主板黑苹果

## 硬件信息

- CPU：12490F
- 主板：七彩虹CVN B760I FROZEN WIFI V20
- BIOS版本：1004（2023/01/05）
- 内存：金白达银爵DDR4 3200 长鑫Adie 16GB（8x2）
- SSD：三星980pro 1TB
- 显卡：翰讯竞技版 6600xt 8GB 矿
- 机箱：闪鳞S400ITX
- 电源：银欣SX500-LG 500W金牌

## BIOS设置

最关键的步骤。因为七彩虹主板BIOS的特点，以下设置都是我结合各方资料+自己不断尝试总结出来的。

### 关闭
- Resize BAR Support（也可以选择在OpenCore设置中将`Booter->Quirks->ResizeAppleGpuBars`设置为0，而不关闭ResizableBar支持）
- CSM启动
- Secure Boot（安全启动）
- cfg lock：**一定要关闭！否则安装时会卡EB！** BIOS没有提供关闭cfglock的功能，需要借助EFI中的工具。具体步骤下面单独介绍。
- ME写保护：关闭cfglock需要
### 开启

> 以下设置直接使用BIOS默认即可。我认为可能与安装过程有关因此记录一下

- vt-d
- vt-x（Intel虚拟化技术）
- above 4G Decoding

## 解锁cfg lock

感谢七彩虹主板BIOS"强大"的功能，cfg-lock这种偏僻的设置当然是无法直接在BIOS中修改的。

对于这种情况，直接刷BIOS的硬核做法当然是可以的，但是其中的风险显然也不需要说明。

因此这里我们需要借助EFI工具来实现cfg-unlock操作：

TODO：上传截图

将上图的EFI工具设置为启用。然后在进入OpenCore引导界面时，选择该工具执行即可。

一些老镜像中的类似EFI工具是无法自动修改该主板的cfg-lock设置的，但是用我的EFI中的携带的这个则可以正确修改。

## 安装流程

1. 正确设置BIOS；
2. 使用该EFI制作USB安装镜像（这个请自行查找教程。推荐OpenCore官方文档）；
3. 将U盘插到主板IO挡板上的USB2.0接口，并重启按F11，选择第一个U盘启动项，进入OpenCore引导界面；
4. 使用EFI工具解锁cfg-lock，成功后重启；
5. 再次进入OpenCore引导，选择"Install macOS Ventura"开始进行系统安装；
6. 等待，然后进入恢复系统。
7. 选择"磁盘工具"将目标磁盘/分区格式化为APFS；
8. 然后退出磁盘工具，选择安装系统，然后一直下一步即可。

## 注意事项

定制USB：通过安装时的试错发现，`USBInjectall.kext`无法很好地识别该主板的USB端口，因此使用未定制USB的EFI进行安装的过程中会遇到 `Still waiting for root device` 的报错，同时屏幕中央会显示一个禁止图标。这个报错说明在准备安装的过程中，安装U盘所在的USB端口无法被正确地识别，导致安装程序无法完成加载。

因此，面对这个问题最好的解决方法，就是在开始安装前先用USBToolBox完成主板+机箱的USB定制，然后用定制好的EFI去进行安装。

具体定制方法请参考：

## 跑分

TODO：上传图片

## 当前缺陷（持续更新）

- [ ] Intel网卡支持不完善，许多原生功能无法使用

- [ ] ACPI未定制，貌似有缺陷

- [ ] CPU调度不完美：通过`AppleXcpmForceBoost`强行开启睿频

- [x] 980 pro SSD TRIM：`sudo trimforce enable` 强行开启

- [ ] 使用时卡顿：最影响体验的缺陷，下面详细说明

### 使用时的卡顿问题

症状：无缘无故地突然卡住，可能伴随着彩虹光标。频繁出现于输入法切换、应用开启/关闭、网络视频播放时。而且联网状态下出现频率比未联网状态下高很多。此外开机时间越长，这种现象出现越频繁。

猜测：从联网变严重这一点来看，我猜测这个问题可能和网络驱动密切相关。但是由于RTL8125和AirportItlwm分别启用时都会导致类似的问题

补充信息：在kernel的日志信息中可以看到许多的 `kernel connect() - failed necp_set_socket domain attributes` 错误，但是关于该日志信息未能找到任何的相关资料。


