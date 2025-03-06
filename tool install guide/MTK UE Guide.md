---
title: MTK UE Guide

---

[toc]
# Reference
- [如何在 WIN10 安裝 Android ADB工具](https://hi-upchen.medium.com/%E5%A6%82%E4%BD%95%E5%9C%A8-win10-%E5%AE%89%E8%A3%9D-android-adb%E5%B7%A5%E5%85%B7-8546cb0b81ce)
- M70_Smartphone_Setup_and_Operate_SOP_External_v50_for_NTUST_BMW_Lab
- Ulefone_Armor_18_MT6877_EEA_V10_221215
# Part 1: Before Testing
## Download SP Flash Tool(optional)
:::success
[MTK TOOL](https://mtktool.com)
![image](https://hackmd.io/_uploads/Bk9G8m_rR.png)

- Here choose version ==5-1924(Newest)==, and the download source labeling seems to be wrong, just choose one of the ones that can work.

![image](https://hackmd.io/_uploads/HJB0LX_BR.png)

---
![image](https://hackmd.io/_uploads/S1vGv7drR.png)
:::

## Download Mediatek Driver Auto Installer
>purpose : The USB driver to identify MTK UE
:::danger
- Double click to install

![image](https://hackmd.io/_uploads/rkWSVMmv0.png)
- result of successfully install

![image](https://hackmd.io/_uploads/HJ2T7GXD0.png)
:::

## Download Modem Meta
:::info
[MTK TOOL](https://mtktool.com)
![image](https://hackmd.io/_uploads/H1g0yKMdrA.png)


- Here choose version ==10.2044.0.02(Newest)==, and the download source labeling seems to be wrong, just choose one of the ones that can work.


Ulefone_Armor_18_MT6877_EEA_V10_221215.zip

![image](https://hackmd.io/_uploads/SySwcMOrA.png)

---
![image](https://hackmd.io/_uploads/Skm05GuB0.png)

---
![image](https://hackmd.io/_uploads/SJxiizOSR.png)

- Once ModemMeta Tool is installed, Launch It. And you will be able to see the following Window:

![image](https://hackmd.io/_uploads/B1nAymdHA.png)
:::
---

## Enter Meta mode
>prupose : Set the mode of MTK UE. Only need to do one time
**Follow the instructions below**
:::success
1. Open META Tool (but ==**do not connect**== phone to PC)
2. Choose “==*Smart Phone*==” and click “==*Connect*==” 
3. Power off phone (battery should be connected to phone), then connect UE to PC
4. Wait for UE enter connect META mode (1~2 minute)
    - UE screen will show meta mode in bottom left
    - META tool will show connected in bottom left
5. Click “Load DB” and choose “From target
![未命名](https://hackmd.io/_uploads/HJJ3i7dHR.png)

- Successful connect

![image](https://hackmd.io/_uploads/H1nCb4OS0.png)

- Click ==Load DB== button and choose ==From Target==

Assuming you have downloaded the Stock Firmware of your Device; if not then you can find Stock Firmware on [Firmware File](https://firmwarefile.com/) or through Google)
>Recommend: Ulefone_Armor_18_MT6877_EEA_V10_221215.zip(2.4G)


![image](https://hackmd.io/_uploads/B1iGmVOr0.png)

And you will get 

![image](https://hackmd.io/_uploads/r1b4XNurA.png)
## Do RF Calibration (After Enter META Mode)


## Set IMEI (After Enter META Mode)

![image](https://hackmd.io/_uploads/Hy-QwXXvR.png)

dial text below to verify the IMEI of your device
```bash=
*#06#
```
to verify the IMEI of your device
- Result
![image](https://hackmd.io/_uploads/HJKlu7Qv0.png)

:::


## Download ADB
:::warning
ADB, or Android Debug Bridge, is a tool that is commonly used when developing or using Android. You can connect to your Android phone via USB from your computer and use the command line to control your phone.

Windows 版本：
https://dl.google.com/android/repository/platform-tools-latest-windows.zip
Mac 版本：
https://dl.google.com/android/repository/platform-tools-latest-darwin.zip
Linux 版本：
https://dl.google.com/android/repository/platform-tools-latest-linux.zip


- Step 1: Please download ,and then unzip it to C:\.
![image](https://hackmd.io/_uploads/H1wpwblDC.png)


- Step 2: Add the path where the ADB is placed to the system path.
    - win+R and type sysdm.cpl
![image](https://hackmd.io/_uploads/HyVeqblvR.png)

![image](https://hackmd.io/_uploads/Sk2-obxP0.png)

![image](https://hackmd.io/_uploads/S11V2ZxDR.png)

- Step 3 Open cmd check result

```bash=
adb --version
```
![image](https://hackmd.io/_uploads/HktNiW7PR.png)
:::

## Download ELT tool
:::success
### Register MTK account(Authority by MTK)
>[Link of MTK website](https://transfer.mediatek.com/Detail/Received/Mail30038765585)
- Download the tool

![image](https://hackmd.io/_uploads/SyM2cnBoC.png)

### Install ELT tool
#### Open ELT.exe in `ELT_exe_v3.2432.0_customer` folder
![image](https://hackmd.io/_uploads/Hyufj6BiR.png)

#### Install ELT tool

![image](https://hackmd.io/_uploads/Sk1LiaBi0.png =x300)

![image](https://hackmd.io/_uploads/SJBKs6HiC.png =x300)

![image](https://hackmd.io/_uploads/H1C9o6roA.png =x300)

![image](https://hackmd.io/_uploads/S1b3iaSsC.png =x300)

![image](https://hackmd.io/_uploads/Sy_6o6BsA.png =x300)

### Activate ELT Tool(Will tie with the PC)
![image](https://hackmd.io/_uploads/S1XG2aBjA.png =x300)
#### Use the following information to activate Logging Tool.
- Company Name: NTUST
- Password: 8b8UMwCR
- Admin Key: 6VE4RAB7

![image](https://hackmd.io/_uploads/BJXBhaHi0.png =x300)

#### result
![image](https://hackmd.io/_uploads/HkURh6rjA.png =x300)

### Open ELT tool
#### After install will generate a file
![image](https://hackmd.io/_uploads/SJhTyCroC.png)
#### Click ELT.exe to open the ELT Tool
![image](https://hackmd.io/_uploads/SJd0JRSjC.png)
#### Result
![image](https://hackmd.io/_uploads/r1lbxCHsA.png)
:::

# Part 2: How to get the log form DebugLoggerUI
## Step1. Set Developer Options
1. Setting --> About phone --> Click “Build number” 5 times
2. Setting --> System --> Developer options 
3. Turn on “USB debugging”
## Step2. Enable AP Log (Optional)
1. Open “Phone App” and key in *#*#3646633#*#* 
2. Scroll right to tab "Log and Debugging" 
3. "Telephony Log Setting"
4. "Enable"
5. Reboot Phone
![image](https://hackmd.io/_uploads/B1wh7EQwA.png)

## Step3. Enter DebugloggerUI
1. Open “Phone App” and key in ＊＃＊＃3646633＃＊＃＊
2. After key in, Engineer Mode will show in UI
![image](https://hackmd.io/_uploads/rylIVEXPR.png)

3. Find “Log and Debugging” tab and choose “DebugLoggerUI”
![image](https://hackmd.io/_uploads/r1bvNVQwR.png)


## Step4. Record Log by USB Mode
1. Open “DebugLoggerUI”
2. Press ![image](https://hackmd.io/_uploads/B1EREEmwA.png)” then press ![image](https://hackmd.io/_uploads/HksyHEQDA.png)
3. Press “ModemLog”
4. Press “Md1 Log Mode” and choose “USB Mode”

![image](https://hackmd.io/_uploads/SJdcHwcDC.png)

5. Go back to DebugLoggerUI screen 
6. After press ![image](https://hackmd.io/_uploads/HkkZr4QPR.png) start t
7. Press ![image](https://hackmd.io/_uploads/HkkZr4QPR.png) and then proceed to the next program (Don't click ![image](https://hackmd.io/_uploads/B1EREEmwA.png)).


## Step5. ELT Connection
### Prerequisite
:::success
#### Download Stock Firmware on [Firmware File](https://firmwarefile.com/) or through Google
- Check the chip model of UE
>In my case chip model is MT6877

![image](https://hackmd.io/_uploads/BkZ_mtwdR.png =x350)
- Search the chip model to find the 

![image](https://hackmd.io/_uploads/SJZ4JYvuC.png =x300)
>Recommend Firmware : Ulefone_Armor_18_MT6877_EEA_V10_221215.zip(2.4G)

![image](https://hackmd.io/_uploads/SJ41gKD_0.png =x200)
- Download firmware

![image](https://hackmd.io/_uploads/r1SxgKDu0.png =x300)
:::
:::success
#### Make sure your PC can detect UE through file system
- PC can read the device

![image](https://hackmd.io/_uploads/rJRnjOjd0.png)
- MTK UE can do file transfer(**remember to tick File transfer**)

![image](https://hackmd.io/_uploads/Hyopo_iuR.png =x350)
:::

### Start ELT Connection
1. Connect UE to PC
2. Click “…” to load file

![image](https://hackmd.io/_uploads/HymLB4mPA.png)

![image](https://hackmd.io/_uploads/SyA-Itv_R.png)

3. Choose “MDDB_MTxxxx_xxx.EDB” in MD load folder and press "OK"
>firmware file path

![image](https://hackmd.io/_uploads/rk9GrFvuC.png)

![image](https://hackmd.io/_uploads/rJRjH4QwA.png)

---
4. Click “connect” then click “OK”

![image](https://hackmd.io/_uploads/B1oQIN7wC.png)

![image](https://hackmd.io/_uploads/ryJurucD0.png)
>Result

![image](https://hackmd.io/_uploads/Bye6INmDA.png)

## Step6. Set ELT Target Filter
1. Choose “Control” -> “Set Target Filter”

![image](https://hackmd.io/_uploads/H1SIsKwO0.png)
2. Choose filter and click “OK”
:::info
### filter options
- “Gen97_Default_Standard” for **general debug purpose**
- “Gen97_Default_Slim” for **reducing log size**
- “Gen97_Default_UltraSlim” for **ultra-reducing log size**
:::
![image](https://hackmd.io/_uploads/SkV7fL9DR.png)

## Step7. Open PS TracePeer check packet status
“View” -> “PS TracePeer”


## Step8. Clear Log
Before enter test scenario, click “Clear” to clear previous unnecessary part (reduce log size)
![image](https://hackmd.io/_uploads/BJiB_VmwC.png)

## Step9. Save Log
After:
1. UE enter and leave flight mode
2. UE camp on gNB/eNB
3. NW/UE send data traffic

Click “Disconnect”
![image](https://hackmd.io/_uploads/BJCjd47vC.png)
Click “Save”– XXXX.elg 
![image](https://hackmd.io/_uploads/Hkggad47PA.png)


## Step10. Get the Other Log from Device (Optional)
### Get ap and network log
#### Termial way to get log
- Step 1 Search your device
```bash=
adb devices
```
![image](https://hackmd.io/_uploads/SkSZ2bQDR.png)

- Step 2 Connect to your device
```bash=
adb shell
```
![image](https://hackmd.io/_uploads/r1e5nWXDC.png)

- Step 3 Through the Log path
```bash=
storage/emulated/0/debuglogger/
```
![image](https://hackmd.io/_uploads/B1vTC-7D0.png)

***mobilelog folder*** is AP Log
```bash=
cd mobilelog folder
```
![image](https://hackmd.io/_uploads/SkuK1fmw0.png)

***netlog folder*** is Wireshark Log
```bash=
cd netlog folder
```
![image](https://hackmd.io/_uploads/SktvkMQDR.png)

#### GUI way to get log

![image](https://hackmd.io/_uploads/rJgkfbgDC.png)

---
![image](https://hackmd.io/_uploads/Hy9gGZevR.png)

---
![image](https://hackmd.io/_uploads/ryZ71-lPR.png)

---
![image](https://hackmd.io/_uploads/Syam1-gv0.png)

---
![image](https://hackmd.io/_uploads/HJRsZzmwA.png)

### ALL log you can get from MTK_UE
![image](https://hackmd.io/_uploads/H1mZsu7wA.png)



# ISSUE
## :heavy_check_mark:(1) Filter setting fail
:::spoiler
:::danger
![image](https://hackmd.io/_uploads/HyE_Y4Qw0.png)
![image](https://hackmd.io/_uploads/HJd5KNXvR.png)
![image](https://hackmd.io/_uploads/ByenY4QP0.png)
:::

### Solution(1)
:::success
don't press ![image](https://hackmd.io/_uploads/B1EREEmwA.png) befroe UE connect to PC ELT
:::

# Quick start
Once U already finish your environment, you can skip the previous steps 
## UE part
### Enter DebugloggerUI
1. Open “DebugLoggerUI”
2. Press ![image](https://hackmd.io/_uploads/B1EREEmwA.png)” then press ![image](https://hackmd.io/_uploads/HksyHEQDA.png)
3. Press “ModemLog”
4. Press “Md1 Log Mode” and choose “USB Mode”

![image](https://hackmd.io/_uploads/ByDorv9PR.png)
5. Go back to DebugLoggerUI screen 
6. After press ![image](https://hackmd.io/_uploads/HkkZr4QPR.png) start
7. Press ![image](https://hackmd.io/_uploads/HkkZr4QPR.png) and then proceed to the next program (Don't click ![image](https://hackmd.io/_uploads/B1EREEmwA.png)).

## PC part
### Enter ELT
:::info
Assuming you have downloaded the Stock Firmware of your Device; if not then you can follow the [guide](#Download-Stock-Firmware-on-Firmware-File-or-through-Google) here to download.
:::
1. Connect UE to PC
2. Click “…” to load file

![image](https://hackmd.io/_uploads/HymLB4mPA.png)

![image](https://hackmd.io/_uploads/SyA-Itv_R.png)

3. Choose “MDDB_MTxxxx_xxx.EDB” in MD load folder and press "OK"
>firmware file path

![image](https://hackmd.io/_uploads/rk9GrFvuC.png)

![image](https://hackmd.io/_uploads/rJRjH4QwA.png)

---
4. Click “connect” then click “OK”

![image](https://hackmd.io/_uploads/B1oQIN7wC.png)

![image](https://hackmd.io/_uploads/ryJurucD0.png)
>Result

![image](https://hackmd.io/_uploads/Bye6INmDA.png)
### Set ELT Target Filter
1. Choose “Control” -> “Set Target Filter”

![image](https://hackmd.io/_uploads/H1SIsKwO0.png)
2. Choose filter and click “OK”
:::info
### filter options
- “Gen97_Default_Standard” for **general debug purpose**
- “Gen97_Default_Slim” for **reducing log size**
- “Gen97_Default_UltraSlim” for **ultra-reducing log size**
:::
![image](https://hackmd.io/_uploads/SkV7fL9DR.png)


### Open PS TracePeer check packet status
“View” -> “PS TracePeer”
- E2E : OAI BBU

![image](https://hackmd.io/_uploads/Byd4SdqD0.png)


## Optional feature
### Check which sim slot you are using
![Screenshot_20240719-134527](https://hackmd.io/_uploads/SyAwdeV20.png =x400)

### Engineer Mode
1. Open “Phone App” and key in ＊＃＊＃3646633＃＊＃＊
2. After key in, Engineer Mode will show
![Screenshot_20240722-145604](https://hackmd.io/_uploads/S11rE0iuA.png =x400)

### Set Network (RAT) Type
1. Enter flight mode
2. Choose “Network selecting” and SIM

![image](https://hackmd.io/_uploads/HkIkBCo_A.png =x400)

![image](https://hackmd.io/_uploads/ryLEHAiOC.png =x200)
3. Choose preferred network type(”NR only”: SA test)

![image](https://hackmd.io/_uploads/S1FWSAsu0.png =x400)
4. Leave flight mode 

### Set SA option
1. Enter flight mode
2. Choose “Nr configure” and slot index. (slot0=sim1, slot1=sim2)

![image](https://hackmd.io/_uploads/r19ONCo_0.png =x400)
3. Enable SA option

![image](https://hackmd.io/_uploads/BkquH0jO0.png =x300)
4. Leave flight mode

### Lock NR Cell through ELT
1. Enter flight mode
2. Choose Lock NR Cell in command line : MOD_L4C(for SIM1)、MOD_L4C_2(for SIM2)
3. Fill in AT command : AT+EMMCHLCK=1,11,0,==630048==,==0==. 630048 is SSB ARFCN. 0 is PCI.
4. If you don't want to lock cell id then use this AT command : AT+EMMCHLCK=1,11,0,==630048==
5. Press `send` to activate

![image](https://hackmd.io/_uploads/B1otInY60.png)
<!-- ![image](https://hackmd.io/_uploads/HJ6do0s_C.png) -->
6. Leave flight mode

### 5G General
>EM -> 5G -> 5G General -> Protocal 1(SIM1),Protocal 2(SIM2)

![image](https://hackmd.io/_uploads/rytAuIosC.png)
#### There are lots of information you can see from this page like:
- NR info
- Signal view
- Scheduling view
- Preformance UP
- Preformance CP

![image](https://hackmd.io/_uploads/SygX9Uio0.png)

### Check RACH (Random Access)
>EM -> 5G -> 5G General -> Protocal 1(SIM1),Protocal 2(SIM2)

![image](https://hackmd.io/_uploads/rk3woIijR.png)
