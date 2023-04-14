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

感谢七彩虹主板BIOS强大的功能，cfg-lock这种偏僻的设置当然是无法直接在BIOS中修改的。

对于这种情况，直接刷BIOS的硬核做法当然是可以的，但是其中的风险显然也不需要说明。

因此这里我们需要借助EFI工具来实现cfg-unlock操作：

TODO：上传截图




