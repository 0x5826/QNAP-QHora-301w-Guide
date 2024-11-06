# FW-AQR-GEN4

## 一、将 cld 转换成 mbn 格式

```bash
python3 mkheader.py 0x44000000 0x13 AQR-G4_v5.5.6-AQR_CIG_WIFI_ID44858_VER1745.cld aqr_5.6.mbn
```

## 二、将10G fw刷入分区（以301w的mtd10为例）

```bash
mtd erase /dev/mtd10
mtd -n write /tmp/AQR_ethphyfw_5.6.7.mbn /dev/mtd10
fw_setenv bootcmd "aq_load_fw 0; aq_load_fw 8; bootipq"
```
