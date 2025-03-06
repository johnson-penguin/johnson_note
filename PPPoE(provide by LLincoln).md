---
title: PPPoE(provide by LLincoln)

---

[TOC]
```
sudo apt update
```

```
sudo apt install ppp pppoeconf
```

```
dpkg -l | grep ppp
```
![image](https://hackmd.io/_uploads/rklXJh-ENJl.png)

- 手動測試
```c=
sudo pppd call tm500-ue0 debug nodetach
```














## Issue
### DNS FAIL
![image](https://hackmd.io/_uploads/rJJYKM4Ekl.png)

#### Solution
- In the VM, manually configure the DNS server.
```c=
echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf > /dev/null
```

- Result

![image](https://hackmd.io/_uploads/BynMqGVVkg.png)


## 安裝 VirtualBox Guest Additions 方便腳本放入
```c=
sudo apt update && sudo apt upgrade -y
sudo apt install build-essential dkms linux-headers-$(uname -r)
```
```c= 
sudo sh /media/$USER/VBox_GAs_<version>/VBoxLinuxAdditions.run
```
:::success
- $USER:替換為自己的名稱
- VBox_GAs_<version> 替換為自己的版本
:::
- 使用 VBoxClient 手動啟動拖放功能
```c=
VBoxClient --draganddrop
```

:::danger
仍然無法將檔案直接移入，推測可能該版本linux不支援
:::
## PPPoE 設定
```c=
sudo pppoeconf
```
## 禁用防火牆

```c=
root@Ubunt:/home/vboxuser# sudo ufw disable
Firewall stopped and disabled on system startup
```

![image](https://hackmd.io/_uploads/HJBRXV4N1g.png)

## 檢查是否能夠檢測到 PPPoE 伺服器：

```c=
sudo pppoe-discovery -I enp0s3
```

## 檢查虛擬機中的防火牆設置

```c=
sudo ufw disable
```

## 關閉所有進程

```c=
ps aux | grep pppd
```

```c=
sudo killall pppd

```