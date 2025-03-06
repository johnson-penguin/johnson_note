---
title: pppoe Install guide

---

[TOC]
## Status



| Item                                             | Status             |
|:------------------------------------------------ |:------------------ |
| PPPOE Send PADI                                  | :heavy_check_mark: |
| PPPOE receives PADO                              |                    |
| PPPOE sends PADR                                 |                    |
| PPPOE receives PADS                              |                    |
| PPPOE sends authenticated user name and password |                    |
| PPPOE account and password authentication passed |                    |
| PPPOE obtained IP successfully                   |                    |









## Example topology

PPPoE dial-up Internet access is the most common way for terminal devices to connect to ISP (Internet Service Provider) to achieve broadband access.
![image](https://hackmd.io/_uploads/SJe-5KSmJx.png)


![image](https://hackmd.io/_uploads/SyHWfg4Xke.png)

## Install Oracle VirtualBox.
- Download from the VirtualBox site
[Download VirtualBox - 選擇windows](https://www.virtualbox.org/wiki/Downloads)

- Run the installer (latest version at time of writing: 7.0.20) with the default options and 
submodules selected.
![image](https://hackmd.io/_uploads/BJshFf7fye.png)


## Download the chosen Linux distribution ISO
- Ubuntu 22.04 LTS desktop: [Ubuntu 22.04.4 LTS (Jammy Jellyfish)](https://releases.ubuntu.com/jammy/)
 
 
 3. Create a new VM and install Linux
 4. Launch VirtualBox, and click the New button to add a new VM, browsing to the newly 
downloaded ISO which, if supported, should support an unattended installation:

![image](https://hackmd.io/_uploads/SJfnjf7fyl.png)

![image](https://hackmd.io/_uploads/ry9k3GXfye.png)

![image](https://hackmd.io/_uploads/SkL-hfXzyx.png)

![image](https://hackmd.io/_uploads/rkDQ2zQGyl.png)

![image](https://hackmd.io/_uploads/r1pN2fXfyg.png)
![image](https://hackmd.io/_uploads/Skhv3fQzyg.png)

![image](https://hackmd.io/_uploads/BkHfhL2MJg.png)



## Configuring a Linux PC as the Data Test Device
1. 下載並安裝先決條件。核心模式外掛程式需要 ppp-dev – 未包含在 Ubuntu 的基礎套件集。安裝：
```c=
sudo apt install ppp-dev
``` 

![image](https://hackmd.io/_uploads/Hk8hX2XGJx.png)

2、下載rp-pppoe源碼包
 在撰寫本文時，可以在 Ubuntu Launchpad (https://launchpad.net/ubuntu) 上找到它：
 https://launchpad.net/ubuntu/+source/rp-pppoe
 3、Jammy（22.04）對應的版本是3.12：
https://launchpad.net/ubuntu/+archive/primary/+sourcefiles/rp-pppoe/3.12-1.2ubuntu3/rp-pppoe_3.12.orig.tar.gz。
下載到本機資料夾。
- 3.12
```c=
wget http://archive.ubuntu.com/ubuntu/pool/universe/r/rp-pppoe/rp-pppoe_3.12.orig.tar.gz
```
- 3.11
```c=
wget http://archive.ubuntu.com/ubuntu/pool/universe/r/rp-pppoe/rp-pppoe_3.11.orig.tar.gz

```

![image](https://hackmd.io/_uploads/rkwlX67M1g.png)


4. 安裝 rp-pppoe 和核心模式插件
```c= 
tar -zxf rp-pppoe_3.12.orig.tar.gz
```

```c=
cd rp-pppoe-3.12/src
```

```c=
sudo ./configure --enable-plugin
```
![image](https://hackmd.io/_uploads/ryAOP67Gkg.png)

![image](https://hackmd.io/_uploads/B135DT7zyl.png)

```c=
sudo make install
```
![image](https://hackmd.io/_uploads/SJ8aJ0Qzyx.png)
:::danger
IF u have error please check [Compile rp-pppoe error](#Issue-6--Compile-rp-pppoe-error)
:::



---


## Create conf
- 使用以下命令開始設置 PPPoE 帳戶：
```c=
sudo pppoe-setup

```
- 設置參數
```c=
PPPoE 用戶名：
PPPoE user name: tm500

介面：
INTERFACE: eth1
(如果你的網絡接口是 eth0，請相應地更改)

按需撥號：
on demand: no

DNS 伺服器：
DNS: server

PPPoE 密碼：
PPPoE password: tm500

防火牆設置：
FIREWALL: 0 (NONE)
```
- 檢查配置文件
```c=
cat /etc/ppp/pppoe.conf
```

## Create configuration file

```c=
 sudo cp /etc/ppp/pppoe.conf /etc/ppp/pppoe-ue0pdn0.conf
```

```c=
sudo gedit /etc/ppp/pppoe-ue0pdn0.conf
```
識別 `TM500 IP`、`UE ID` 和 `PDN ID`：

例如，如果您的 TM500 IP 是 ==192.168.10.67==，UE ID 是 ==0==，PDN ID 是 ==0==，則服務名稱為<br>tm500_lte_192.168.10.70_0_0



| 參數     | 數值          |
|:-------- |:------------- |
| TM500 IP | 192.168.8.67 |
| UE ID    | 0             |
| PDN ID   | 0             |

- 將 SERVICENAME 更改為您的連接 (SERVICENAME=`tm500_lte_192.168.8.67_0_0`)

![image](https://hackmd.io/_uploads/HkKuFdNzJe.png)

- 設置 PIDFILE = `"/var/run/$CF_BASE-pppoe-ue0pdn0.pid"`

![image](https://hackmd.io/_uploads/HkwXjONGJx.png)


- 確保 LINUX_PLUGIN =`/etc/ppp/plugins/rp-pppoe.so`

![image](https://hackmd.io/_uploads/ByUQn_4M1l.png)


- ==可選地==，調整 MTU 和 MRU 設置：
    - PPPD_EXTRA="mtu 1452 mru 1452"
## PPPoE connect
### Test Connection (now status)

啟動 PPPoE 連接：
```c=
cd /rp-pppoe-3.12/src
```
```c=
sudo pppoe-start /etc/ppp/pppoe-ue0pdn0.conf
```

驗證連接，檢查 ppp0 接口，確保其已分配 IP 地址:
```c=
ifconfig
```

添加路由：
```c=
sudo route add <目標主機地址> ppp0

sudo route add 192.168.8.67 ppp0
```


### Running Client Application
例如，使用 ping 測試連接：
```c=
ping 10.1.2.3.4
```
### Disconnect stopped

```c=
sudo pppoe-stop /etc/ppp/pppoe-ue0pdn1.conf
```

## Control PC Settings (Optional)

![image](https://hackmd.io/_uploads/SkeFAUNsG1e.png)

![image](https://hackmd.io/_uploads/BkS1vEofkl.png)

![image](https://hackmd.io/_uploads/HkggPVof1e.png)

![image](https://hackmd.io/_uploads/HyplwEif1x.png)


![image](https://hackmd.io/_uploads/rJ5zD4sfkl.png)


![image](https://hackmd.io/_uploads/HyjPDEjMke.png)

![image](https://hackmd.io/_uploads/SylqDVjMJl.png)

![image](https://hackmd.io/_uploads/r1B2w4ozkg.png)

![image](https://hackmd.io/_uploads/B1QCwEoz1g.png)

![image](https://hackmd.io/_uploads/HJFNdNsMJg.png)

## Issue

### Issue 1 : VERR_NEM_NOT_AVAILABLE

進入 BIOS 設定：

開機時按下特定的鍵（通常是 F2、Delete、Esc 或 F10，具體取決於你的主機板）以進入 BIOS 設定。
啟用 VT-x：

在 BIOS 設定中，尋找與 CPU 或虛擬化技術相關的選項，通常在「Advanced」或「CPU Configuration」中。
找到「Intel Virtualization Technology」或「VT-x」選項，並將其設置為「Enabled」。


```c=
VM 名稱: Ubutu

Not in a hypervisor partition (HVP=0) (VERR_NEM_NOT_AVAILABLE).
VT-x is disabled in the BIOS for all CPU modes (VERR_VMX_MSR_ALL_VMX_DISABLED).

結果碼:
E_FAIL (0x80004005)
元件:
ConsoleWrap
介面:
IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}
```

![image](https://hackmd.io/_uploads/r1JigQ7Gye.png)
![image](https://hackmd.io/_uploads/S1VSZQQM1l.png)
![image](https://hackmd.io/_uploads/SyPm-mXM1e.png)

---
### Issue 2 : CPU Over Temperature Error

![image](https://hackmd.io/_uploads/B1LeRjQfJx.png)

- Please remove the mercury battery (cmos), discharge it and then reinsert it
- Please ==power off the device== before unplugging it

![image](https://hackmd.io/_uploads/HkIC6smzkx.png)

---

### Issue 3 : Ubuntu terminal no response or can't open

- Using tty terminal
`Ctrl + Alt + F3` : in tty terminal
`Ctrl + Alt + F2` : exit tty terminal

安裝 xterm 作為替代終端，嘗試能否開啟：
```c=
sudo apt update
sudo apt install xterm
```

- xterm 正常
![image](https://hackmd.io/_uploads/S1tmZhmG1g.png)

---

### Issue 4 : rp-pppoe source package download link Invalid

![image](https://hackmd.io/_uploads/r1vYg6QM1l.png)

- using new link
```c=
wget http://archive.ubuntu.com/ubuntu/pool/universe/r/rp-pppoe/rp-pppoe_3.12.orig.tar.gz
```

![image](https://hackmd.io/_uploads/Hk31XpmMkl.png)


### Issue 5 : no acceptable C compliler found in PATH

- 更新包列表：
```c=
sudo apt update
```
- 安裝 GCC，執行以下命令安裝 GCC 和相關的開發工具：
```c=
sudo apt install build-essential
```

- 確認安裝：
安裝完成後，可以使用以下命令確認 GCC 是否安裝成功：

```c=
gcc --version
```
- Result

![image](https://hackmd.io/_uploads/BkXc8TQG1x.png)

---
### Issue 6 : Compile rp-pppoe error


![image](https://hackmd.io/_uploads/S1pR267zJx.png)


Open rp-pppoe-3.12/src and check `plugin.c`

在 ==#include <linux/if_pppox.h>== 之前，添加以下兩行：
```c=
#define _LINUX_IN_H
#define _LINUX_IN6_H
```

- Before
![image](https://hackmd.io/_uploads/r1J7JC7Gyg.png)

After

![image](https://hackmd.io/_uploads/HyQOk0mGye.png)

- remake

![image](https://hackmd.io/_uploads/SJ8aJ0Qzyx.png)


### Issue 7 : ping connect network is unreachable
- before
![image](https://hackmd.io/_uploads/SkfvRsXXyg.png)

- change network card setting

![image](https://hackmd.io/_uploads/BJkTCsQX1e.png)

- After

![image](https://hackmd.io/_uploads/BJvly2Qm1e.png)

### Issue 8 : pppoe no receive PADO packet
![image](https://hackmd.io/_uploads/H16tMp4NJx.png)

![image](https://hackmd.io/_uploads/HyOGJL2Gyl.png)

:::success
Correct parameter `DTE_INTERFACE_NAME` of TM500 Clouede UE conf: `cuedock.yaml`

![image](https://hackmd.io/_uploads/H13ChaLEkl.png)

:::