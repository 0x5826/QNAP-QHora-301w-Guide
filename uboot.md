# 威联通 QNAP 301W Uboot 不死

[Uboot购买&下载地址](https://mbd.pub/o/bread/mbd-ZZWVlZ1v)

兼容原厂双分区

1. 通过 Openwrt luci web 界面的 “文件传输” -> "上传" ， 把解压后的2个文件上传到路由器的 /tmp/upload/ 目录下
2. 通过 putty 的 ssh 登录路由器，输入以下命令成功后，等 10s

```
cd /tmp/upload

mtd write uboot.bin /dev/mtd8
```

-------------------------------------------------------
PS: 切换分区命令（ OpenWrt 固件中）

切换到原厂

```
fw_setenv current_entry 1

fw_setenv boot_1 good

reboot
```
------------------------------------------

原厂切换到 OpenWrt（ QNAP 原厂固件中）

```
sudo fw_setenv current_entry 0

sudo fw_setenv boot_0 good

sudo reboot
```
