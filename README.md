# QNAP 301w 免拆刷机方法

#### 特别感谢 [@coolsnowwolf](https://github.com/coolsnowwolf) [@asushugo](https://github.com/asushugo) 等一众牛B的开发者适配301w路由器！

#### 本教程仅限在lean的qsdk闭源固件和[lede开源固件](https://github.com/coolsnowwolf/lede)测试通过，衍生固件刷机请自行测试。

#### [QNAP QHora-301W 产品介绍](./301w_Specs.md)  
#### [QNAP QHora-301W OpenWRT官网介绍](https://openwrt.org/inbox/toh/qnap/301w) 
#### [QNAP QHora-301W 第三方带Web操作&支持原厂双分区Uboot](./uboot.md)  

### 一. 开启SSH服务

在开启路由器可以正常进系统的状态下，长按路由器后的 WPS 键，直到听到**第二声**“嘟”松开（大约 12 秒）就 OK。

---

### 二. SSH连接路由器后台

这里需要注意一点

- 路由器默认开启的SSH的端口号是 **22200**

- 用户名是`admin`，密码是路由器网页登录密码

ssh命令就是 `ssh admin@192.168.100.1 -p 22200`（ 如果端口没开重复步骤1 ）

或者使用putty连接

![putty](pic/putty.jpg)

---

### 三. 将启动分区切换到第二分区

`sudo fw_setenv current_entry 1` **提示要输入的密码就是刚刚你登录 ssh 的密码，以下不再赘述**

`sudo reboot`  重启

---

### 四. 检查路由器当前启动分区

*按照上述步骤二操作重新打开ssh*

`sudo fw_printenv -n current_entry`

查看分区，确保这里会输出 1，如果不是，重复步骤 三。

---

### 五. 使用 Winscp 上传QSDK的 kernel.bin 和 rootfs.bin 到 /tmp 
kernel文件和rootfs文件可以从sysupgrade格式的固件解压获得

---

### 六. 将QSDK到dd刷入第一分区

*注意：强烈建议使用dd命令（方法自行搜索引擎）备份一些重要的分区例如mtd闪存分区以及mmc闪存分区，以便以后可以[恢复官方固件](./recovery_oem.md)*

```sh
sudo dd if=/tmp/kernel.bin of=/dev/mmcblk0p1
sudo dd if=/tmp/rootfs.bin of=/dev/mmcblk0p4
sudo fw_setenv current_entry 0
sudo fw_setenv boot_0 good
sudo reboot
```
截图参考：

![putty](pic/flash_qsdk.jpg)

执行完上述命令之后即可刷入QSDK并从第一分区启动QSDK，注意由于目前还没有刷10G PHY的firmware，需刷入firmware。

---

### 七. 刷入 10G PHY 的 firmware

1. 将AQR_ethphyfw_5.6.7.mbn用scp命令或者winscp工具传输到QSDK的/tmp目录下
2. 使用putty或其他ssh工具进路由器后台，抹除原ethfw分区 mtd10的数据：`mtd erase /dev/mtd10`
3. 刷入fw文件：`mtd -n write /tmp/AQR_ethphyfw_5.6.7.mbn /dev/mtd10`
4. 修改bootcmd环境变量：`fw_setenv bootcmd "aq_load_fw 0; aq_load_fw 8; bootipq"`
5. `fw_printenv` 看下是否有这条记录 bootcmd=aq_load_fw 0; aq_load_fw 8; bootipq ，如果有就没问题。
6. 重启

