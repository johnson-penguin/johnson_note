---
title: Foxconn O-RU

---

:::success
Reference From Rico
[Foxconn RU Setup guide](https://hackmd.io/ehotH1QaRzmgqw1YArSmUQ?view)
[[RP0-401] RPQN7801_L1_Network Parameters Setting Guide_v1.1.pdf](https://drive.google.com/file/d/1WWWHae0YzOwXlWsMPd-3G8F7TvFdngul/view)
[Foxconn RPQN O-RU Installation and Operation Guide.pdf](https://drive.google.com/file/d/1bqYEiWrKPLBAebWEsoQoYnRLrAz0-7w_/view)
Reference From Ferlinda
[[10/05,07,17] Foxconn RU + [F] SampleApp Test](https://hackmd.io/yBRxkeWUTXCGp43DEnLs1w?view#B-Run)
:::
## Milestone



## Current issues 
### 1 (be fixed)
:::warning
From O-DU/RU整合 Line group
![image](https://hackmd.io/_uploads/SJ1sjNSUa.png)

---

**已經做過的檢查** 
1. RU透過SFP+直接接上DU網卡，DU網卡的燈有亮
2. RU透過SFP+直接接上Switch，Switch燈有亮
3. 目前已經向富士康工程師確認過，在==目前的v1.6.18 不支援SFP+==，直到==v2.4==才開始支援

---

**目前的狀態** 
1. 交大Fox_RU版本與台科一致
2. 交大已經對接成功

---
**目前打算嘗試的作法** 
- [ ] 聯絡SY確認他們正常的RU的網卡狀態，是否與我們一致，因為我們現在是沒有==qse-eth==這個interface

    台科:
    ![image](https://hackmd.io/_uploads/B1P00NSLp.png)
    
    交大:
    ![image](https://hackmd.io/_uploads/Hk6H5Ed8T.png)


    ![image](https://hackmd.io/_uploads/S1EfftHUa.png)

    - [ ] 若交大方面==沒有==這個interface，那台科與交大的網卡狀態一致
    - [ ] 若交大方面==有==這個interface，那台科與交大網卡狀態不一致，詢問富宏網此狀態的可能原因
    
    
    
- [ ] 聯絡SY能不能提供正常且能運作的FoxRU的RRHconfig_xran.xml以及運作時的Log
    - [ ] 對照設定檔及Log進行排錯
:::




## Topology
![image](https://hackmd.io/_uploads/ryG1nK48p.png)


## RRHconfig_xran.xml
### Default

- original file : RRHconfig_xran.xml
- Path :`/home/root/sdcard/RRHconfig_xran_default.xml`
:::spoiler 

RRHconfig_xran_default.xml
```c=
<!--                          -->
<!--            Common        -->
<!--                          -->
<!-- RRH_DST_MAC_ADDR: Destination MAC address, fill with 6 bytes and separate each others by colon -->
RRH_DST_MAC_ADDR = 00:11:22:33:44:77
<!-- RRH_SRC_MAC_ADDR: Source MAC address, fill with 6 bytes and separate each others by colon -->
RRH_SRC_MAC_ADDR = aa:bb:cc:dd:ee:ff
<!-- RRH_EN_EAXC_ID: Enable using eAxC ID field defined in O-RAN spec.                            -->
<!--                 When 0 is set, RU port ID=0,1,2,3 are used for PDSCH/PUSCH if RRH_TRX_EN_BIT_MASK = 0x0F -->
<!--                 When 0 is set, RU port ID=4,5,6,7 are used for PRACH       if RRH_TRX_EN_BIT_MASK = 0x0F -->
<!--                 When 0 is set, RU port ID=0,1     are used for PDSCH/PUSCH if RRH_TRX_EN_BIT_MASK = 0x03 -->
<!--                 When 0 is set, RU port ID=2,3     are used for PRACH       if RRH_TRX_EN_BIT_MASK = 0x03 -->
RRH_EN_EAXC_ID = 0
<!-- RRH_EAXC_ID_TYPE1: Specify the eAxC ID for type1 message -->
RRH_EAXC_ID_TYPE1 = 0x0, 0x1, 0x2, 0x3
<!-- RRH_EAXC_ID_TYPE3: Specify the eAxC ID for type3 message -->
RRH_EAXC_ID_TYPE3 = 0x8, 0x9, 0xA, 0xB
<!-- RRH_EN_SPC: Enable SPC or not, 0:OFF, 1:ON -->
RRH_EN_SPC = 1
<!-- RRH_RRH_LTE_OR_NR: Indicate the spec of xRAN, 0:LTE, 1:NR -->
RRH_RRH_LTE_OR_NR = 1
<!-- RRH_TRX_EN_BIT_MASK: Bit-mask of 4 TRx, bit 0: TRx0, bit1: TRx1, bit2: TRx2, bit3: TRx3 -->
RRH_TRX_EN_BIT_MASK = 0x0f
<!-- RRH_RF_EN_BIT_MASK: Bit-mask of 4 PA/LNA, bit0: PA0/LNA0, bit1: PA1/LNA1, bit2: PA2/LNA2, bit3: PA3/LNA3 -->
RRH_RF_EN_BIT_MASK = 0x0f
<!-- RRH_CMPR_HDR_PRESENT: Indicate the UdCompHdr/reserved field is present or not, 0:no present; 1:present -->
RRH_CMPR_HDR_PRESENT = 1
<!-- RRH_MSGS_IN_A_SYM: Number of messages in a symbol time, (1 or 2) -->
RRH_MSGS_IN_A_SYM = 1
<!-- RRH_CMPR_TYPE: Indicate compress type. 1st for PDSCH/PUSCH, 2nd for PRACH. 0: No Cmpr; 1:block-floating; 2:u-law -->
RRH_CMPR_TYPE = 1, 0
<!-- RRH_CMPR_BIT_LENGTH: Indicate the bit length after compression. 1st for PDSCH/PUSCH, 2nd for PRACH. -->
RRH_CMPR_BIT_LENGTH = 9, 9
<!-- RRH_UL_INIT_SYM_ID: Initial symbol ID in UL message -->
RRH_UL_INIT_SYM_ID = 0
<!-- RRH_TX_TRUNC_BITS: The extra truncation in fractional part of IFFT output -->
RRH_TX_TRUNC_BITS = 4
<!-- RRH_RX_TRUNC_BITS: The extra truncation in fractional part of FFT output -->
RRH_RX_TRUNC_BITS = 4
<!-- RRH_MAX_PRB: Maximum PRBs -->
RRH_MAX_PRB = 273
<!-- RRH_C_PLANE_VLAN_TAG: C-plane V-LAN tag express by hex number -->
RRH_C_PLANE_VLAN_TAG = 0x0003
<!-- RRH_U_PLANE_VLAN_TAG: U-plane V-LAN tag express by hex number -->
RRH_U_PLANE_VLAN_TAG = 0x0004
<!-- RRH_SLOT_TICKS_IN_SEC: Number of slot tick in a second (2000 slots for u=1) -->
RRH_SLOT_TICKS_IN_SEC = 2000
<!-- RRH_SLOT_PERIOD_IN_SAMPLE: Slot period in 122.88MHz (61440 for u=1) -->
RRH_SLOT_PERIOD_IN_SAMPLE = 61440
<!-- RRH_LO_FREQUENCY_KHZ: Tx and Rx PLL LO Frequency in kHz(internal or external LO) -->
RRH_LO_FREQUENCY_KHZ = 3749700
RRH_TX_POWER = 2
<!-- RRH_TX_ATTENUATION: Tx attenuation value with 1-digit fraction for each layer (>10dB. NOTE: The attenuation value must be larget than 10dB) -->
RRH_TX_ATTENUATION = 12.0, 12.0, 12.0, 12.0
<!-- RRH_RX_ATTENUATION: Rx attenuation value with 1-digit fraction for each layer (<30dB) -->
RRH_RX_ATTENUATION = 0.0, 0.0, 0.0, 0.0
<!-- RRH_BB_GENERAL_CTRL: General control words for Baseband      -->
<!-- Bit[0] of 1st word: Enable the filtering of MAC address      -->
<!-- Bit[1] of 1st word: Enable the UL slot tick packets per port -->
<!-- Others: Reserved                                             -->
RRH_BB_GENERAL_CTRL = 0x0, 0x0, 0x0, 0x0
<!-- RRH_RF_GENERAL_CTRL: General control words for RF            -->
<!-- Bit[0] of 4th word: 1=for N77, 0=else                        -->
RRH_RF_GENERAL_CTRL = 0x3, 0x0, 0x0, 0x0
<!--                           -->
<!--            PTPV2 Related  -->
<!--                           -->
<!-- RRH_PTPV2_GRAND_MASTER_MODE: 0: Unicast, 1:Multicast -->
RRH_PTPV2_GRAND_MASTER_MODE = 1
<!-- RRH_PTPV2_OBSERVATION_TIME: 0: Stop xRAN immediately when OOL; >0: Stop xRAN after specific seconds when OOL -->
RRH_PTPV2_OBSERVATION_TIME = 0
<!-- RRH_PTPV2_GRAND_MASTER_IP: IP address of grand-master -->
RRH_PTPV2_GRAND_MASTER_IP = 192.168.3.150
<!-- RRH_PTPV2_SUB_DOMAIN_NUM: The sub-domain number -->
RRH_PTPV2_SUB_DOMAIN_NUM = 24

```

:::
### Changed(12/14)


- original file : RRHconfig_xran.xml
- Path :`/home/root/sdcard/RRHconfig_xran.xml`
:::spoiler RRHconfig_xran.xml
```xml=
<!--                          -->
<!--            Common        -->
<!--                          -->
<!-- RRH_DST_MAC_ADDR: Destination55 MAC address, fill with 6 bytes and separate each others by colon -->
RRH_DST_MAC_ADDR = 00:11:22:33:44:66
<!-- RRH_SRC_MAC_ADDR: Source MAC address, fill with 6 bytes and separate each others by colon -->
RRH_SRC_MAC_ADDR = 6c:ad:ad:00:08:6c
RRH_DISABLE_USING_CAL_TABLES = YES
<!-- RRH_RU_PORT_ID: RRH RU PORT ID, fill with 4 bytes and separate each others by common and space -->
RRH_RU_PORT_ID = 0, 1, 2, 3
<!-- RRH_EN_SPC: Enable SPC or not, 0:OFF, 1:ON -->
RRH_EN_SPC = 1
<!-- RRH_RRH_LTE_OR_NR: Indicate the spec of xRAN, 0:LTE, 1:NR -->
RRH_RRH_LTE_OR_NR = 1
<!-- RRH_TRX_EN_BIT_MASK: Bit-mask of 4 TRx, bit 0: TRx0, bit1: TRx1, bit2: TRx2, bit3: TRx3 -->
RRH_TRX_EN_BIT_MASK = 0x0f
<!-- RRH_RF_EN_BIT_MASK: Bit-mask of 4 PA/LNA, bit0: PA0/LNA0, bit1: PA1/LNA1, bit2: PA2/LNA2, bit3: PA3/LNA3 -->
RRH_RF_EN_BIT_MASK = 0x0f
<!-- RRH_CMPR_HDR_PRESENT: Indicate the UdCompHdr/reserved field is present or not, 0:no present; 1:present -->
RRH_CMPR_HDR_PRESENT = 1
<!-- RRH_MSGS_IN_A_SYM: Number of messages in a symbol time, (1 or 2) -->
RRH_MSGS_IN_A_SYM = 1
<!-- RRH_CMPR_TYPE: Indicate compress type. 1st for PDSCH/PUSCH, 2nd for PRACH. 0: No Cmpr; 1:block-floating; 2:u-law -->
RRH_CMPR_TYPE = 1, 0
<!-- RRH_CMPR_BIT_LENGTH: Indicate the bit length after compression. 1st for PDSCH/PUSCH, 2nd for PRACH. -->
RRH_CMPR_BIT_LENGTH = 9, 9
<!-- RRH_UL_INIT_SYM_ID: Initial symbol ID in UL message -->
RRH_UL_INIT_SYM_ID = 0
<!-- RRH_TX_TRUNC_BITS: The extra truncation in fractional part of IFFT output -->
RRH_TX_TRUNC_BITS = 4
<!-- RRH_RX_TRUNC_BITS: The extra truncation in fractional part of FFT output -->
RRH_RX_TRUNC_BITS = 6
<!-- RRH_MAX_PRB: Maximum PRBs -->
RRH_MAX_PRB = 273
<!-- RRH_C_PLANE_VLAN_TAG: C-plane V-LAN tag express by hex number -->
RRH_C_PLANE_VLAN_TAG = 0x0003
<!-- RRH_U_PLANE_VLAN_TAG: U-plane V-LAN tag express by hex number -->
RRH_U_PLANE_VLAN_TAG = 0x0003
<!-- RRH_SLOT_TICKS_IN_SEC: Number of slot tick in a second (2000 slots for u=1) -->
RRH_SLOT_TICKS_IN_SEC = 2000
<!-- RRH_SLOT_PERIOD_IN_SAMPLE: Slot period in 122.88MHz (61440 for u=1) -->
RRH_SLOT_PERIOD_IN_SAMPLE = 61440
<!-- RRH_LO_FREQUENCY_KHZ: Tx and Rx PLL LO Frequency in kHz(internal or external LO) -->
RRH_LO_FREQUENCY_KHZ = 3749700
<!-- RRH_TX_ATTENUATION: Tx attenuation value with 1-digit fraction for each layer (>10dB. NOTE: The attenuation value must be larget than 10dB) -->
RRH_TX_ATTENUATION = 20.0, 20.0, 20.0, 20.0
<!-- RRH_RX_ATTENUATION: Rx attenuation value with 1-digit fraction for each layer (<30dB) -->
RRH_RX_ATTENUATION = 0.0, 0.0, 0.0, 0.0
<!-- RRH_BB_GENERAL_CTRL: General control words for Baseband      -->
<!-- Bit[0] of 1st word: Enable the filtering of MAC address      -->
<!-- Bit[1] of 1st word: Enable the UL slot tick packets per port -->
<!-- Others: Reserved                                             -->
RRH_BB_GENERAL_CTRL = 0x0, 0x0, 0x0, 0x0
<!-- RRH_RF_GENERAL_CTRL: General control words for RF -->
RRH_RF_GENERAL_CTRL = 0x3, 0x0, 0x0, 0x0
<!--                           -->
<!--            PTPV2 Related  -->
<!--                           -->
<!-- RRH_PTPV2_GRAND_MASTER_MODE: 0: Unicast, 1:Multicast -->
RRH_PTPV2_GRAND_MASTER_MODE = 1
<!-- RRH_PTPV2_OBSERVATION_TIME: 0: Stop xRAN immediately when OOL; >0: Stop xRAN after specific seconds when OOL -->
RRH_PTPV2_OBSERVATION_TIME = 0
<!-- RRH_PTPV2_GRAND_MASTER_IP: IP address of grand-master -->
<!-- RRH_PTPV2_GRAND_MASTER_IP = 192.168.6.150 -->
<!-- RRH_PTPV2_SUB_DOMAIN_NUM: The sub-domain number -->
RRH_PTPV2_SUB_DOMAIN_NUM = 24

```
:::
### NYCU(From Prof.葉)

- original file : RRHconfig_xran.xml from NYCU
- Path :`/home/root/sdcard/RRHconfig_xran.xml`
:::spoiler RRHconfig_xran.xml
```xml=
<!--                          -->
<!--            Common        -->
<!--                          -->
<!-- RRH_DST_MAC_ADDR: Destination MAC address, fill with 6 bytes and separate each others by colon -->
RRH_DST_MAC_ADDR = 00:11:22:33:44:11
<!-- RRH_SRC_MAC_ADDR: Source MAC address, fill with 6 bytes and separate each others by colon -->
RRH_SRC_MAC_ADDR = aa:bb:cc:dd:ee:ff
RRH_DISABLE_USING_CAL_TABLES = YES
<!-- RRH_EN_EAXC_ID: Enable using eAxC ID field defined in O-RAN spec.                            -->
<!--                 When 0 is set, RU port ID=0,1,2,3 are used for PDSCH/PUSCH if RRH_TRX_EN_BIT_MASK = 0x0F -->
<!--                 When 0 is set, RU port ID=4,5,6,7 are used for PRACH       if RRH_TRX_EN_BIT_MASK = 0x0F -->
<!--                 When 0 is set, RU port ID=0,1     are used for PDSCH/PUSCH if RRH_TRX_EN_BIT_MASK = 0x03 -->
<!--                 When 0 is set, RU port ID=2,3     are used for PRACH       if RRH_TRX_EN_BIT_MASK = 0x03 -->
RRH_EN_EAXC_ID = 0
<!-- RRH_EAXC_ID_TYPE1: Specify the eAxC ID for type1 message -->
RRH_EAXC_ID_TYPE1 = 0x0, 0x1, 0x2, 0x3
<!-- RRH_EAXC_ID_TYPE3: Specify the eAxC ID for type3 message -->
RRH_EAXC_ID_TYPE3 = 0x8, 0x9, 0xA, 0xB
<!-- RRH_EN_SPC: Enable SPC or not, 0:OFF, 1:ON -->
RRH_EN_SPC = 1
<!-- RRH_RRH_LTE_OR_NR: Indicate the spec of xRAN, 0:LTE, 1:NR -->
RRH_RRH_LTE_OR_NR = 1
<!-- RRH_TRX_EN_BIT_MASK: Bit-mask of 4 TRx, bit 0: TRx0, bit1: TRx1, bit2: TRx2, bit3: TRx3 -->
RRH_TRX_EN_BIT_MASK = 0x0f
<!-- RRH_RF_EN_BIT_MASK: Bit-mask of 4 PA/LNA, bit0: PA0/LNA0, bit1: PA1/LNA1, bit2: PA2/LNA2, bit3: PA3/LNA3 -->
RRH_RF_EN_BIT_MASK = 0x0f
<!-- RRH_CMPR_HDR_PRESENT: Indicate the UdCompHdr/reserved field is present or not, 0:no present; 1:present -->
RRH_CMPR_HDR_PRESENT = 1
<!-- RRH_MSGS_IN_A_SYM: Number of messages in a symbol time, (1 or 2) -->
RRH_MSGS_IN_A_SYM = 1
<!-- RRH_CMPR_TYPE: Indicate compress type. 1st for PDSCH/PUSCH, 2nd for PRACH. 0: No Cmpr; 1:block-floating; 2:u-law -->
RRH_CMPR_TYPE = 1, 0
<!-- RRH_CMPR_BIT_LENGTH: Indicate the bit length after compression. 1st for PDSCH/PUSCH, 2nd for PRACH. -->
RRH_CMPR_BIT_LENGTH = 9, 9
<!-- RRH_UL_INIT_SYM_ID: Initial symbol ID in UL message -->
RRH_UL_INIT_SYM_ID = 0
<!-- RRH_TX_TRUNC_BITS: The extra truncation in fractional part of IFFT output -->
RRH_TX_TRUNC_BITS = 4
<!-- RRH_RX_TRUNC_BITS: The extra truncation in fractional part of FFT output -->
RRH_RX_TRUNC_BITS = 6
<!-- RRH_MAX_PRB: Maximum PRBs -->
RRH_MAX_PRB = 273
<!-- RRH_C_PLANE_VLAN_TAG: C-plane V-LAN tag express by hex number -->
RRH_C_PLANE_VLAN_TAG = 0x0002
<!-- RRH_U_PLANE_VLAN_TAG: U-plane V-LAN tag express by hex number -->
RRH_U_PLANE_VLAN_TAG = 0x0002
<!-- RRH_SLOT_TICKS_IN_SEC: Number of slot tick in a second (2000 slots for u=1) -->
RRH_SLOT_TICKS_IN_SEC = 2000
<!-- RRH_SLOT_PERIOD_IN_SAMPLE: Slot period in 122.88MHz (61440 for u=1) -->
RRH_SLOT_PERIOD_IN_SAMPLE = 61440
<!-- RRH_LO_FREQUENCY_KHZ: Tx and Rx PLL LO Frequency in kHz(internal or external LO) -->
RRH_LO_FREQUENCY_KHZ = 3749700
<!-- RRH_TX_ATTENUATION: Tx attenuation value with 1-digit fraction for each layer (>10dB. NOTE: The attenuation value must be larget
RRH_TX_ATTENUATION = 20.0, 20.0, 20.0, 20.0
<!-- RRH_RX_ATTENUATION: Rx attenuation value with 1-digit fraction for each layer (<30dB) -->
RRH_RX_ATTENUATION = 0.0, 0.0, 0.0, 0.0
<!-- RRH_BB_GENERAL_CTRL: General control words for Baseband      -->
<!-- Bit[0] of 1st word: Enable the filtering of MAC address      -->
<!-- Bit[1] of 1st word: Enable the UL slot tick packets per port -->
<!-- Others: Reserved                                             -->
RRH_BB_GENERAL_CTRL = 0x0, 0x0, 0x0, 0x0
<!-- RRH_RF_GENERAL_CTRL: General control words for RF            -->
<!-- Bit[0] of 4th word: 1=for N77, 0=else                        -->
RRH_RF_GENERAL_CTRL = 0x3, 0x0, 0x0, 0x0
<!--                           -->
<!--            PTPV2 Related  -->
<!--                           -->
<!-- RRH_PTPV2_GRAND_MASTER_MODE: 0: Unicast, 1:Multicast -->
RRH_PTPV2_GRAND_MASTER_MODE = 0
<!-- RRH_PTPV2_OBSERVATION_TIME: 0: Stop xRAN immediately when OOL; >0: Stop xRAN after specific seconds when OOL -->
RRH_PTPV2_OBSERVATION_TIME = 0
<!-- RRH_PTPV2_GRAND_MASTER_IP: IP address of grand-master -->
RRH_PTPV2_GRAND_MASTER_IP = 192.168.6.150
<!-- RRH_PTPV2_SUB_DOMAIN_NUM: The sub-domain number -->
RRH_PTPV2_SUB_DOMAIN_NUM = 44
```
:::

## DU side
:::success
- **Setup maximum performance, vf, and environment**
    ==If reboot please do it or you can ignore==
```c=
source /home/oai72/Script/setupvf.sh
```
- **Configuration**
- original file : config_o_du_3450_E.dat
- Path :`/home/oai72/fhidriver_E/openairinterface5g/foxconn_conf_file`

```bash=
# Eth 0
duMac0=00:11:22:33:44:66 # asigned MAC of O-DU VF
ruMac0=00:f2:9e:16:c3:de # O-RU VF for O-RU app
duMac1=00:11:22:33:44:66 # asigned MAC of O-DU VF
ruMac1=00:f2:9e:16:c3:de # O-RU VF for O-RU app
```

- **Run OAI gNB (Foxconn_RU)**

```c=
cd /home/oai72/fhidriver_E/openairinterface5g/cmake_targets/ran_build/build
```
```c=
sudo ./nr-softmodem -O ../../../foxconn_conf_file/mwc_20899_newfhi_E_3450.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/1005_gNB_1.log 2>&1
```
:::
## RU side

**Power on**
```bash=
./init_rrh_config_enable_cuplane
```
### Vlan
:::success
- original file : RRHconfig_xran.xml
- Path : `/home/root/sdcard/`

```bash=
vi /home/root/sdcard/RRHconfig_xran.xml
```
![image](https://hackmd.io/_uploads/Sy8l5H48T.png)
:::
### MAC_address
:::success
- original file : RRHconfig_xran.xml
- Path : `/home/root/sdcard/`


BBU’s MAC address in the field “RRH_DST_MAC_ADDR”.
 RU’s MAC address in the field “RRH_SRC_MAC_ADDR”.

- **OAI**
RRH_DST_MAC_ADDR : ==00:11:22:33:44:66==
- Foxconn_RU
RRH_SRC_MAC_ADDR : ==00:f2:9e:16:c3:de==


```bash=
vi /home/root/sdcard/RRHconfig_xran.xml
```
![image](https://hackmd.io/_uploads/SkN7cr4Ia.png)
:::

## Reboot quick setting
:::success

- **Setup maximum performance, vf, and environment**
```c=
source /home/oai72/Script/setupvf.sh
```

```c=
# Run OAI gNB
cd /home/oai72/fhidriver_E/openairinterface5g/cmake_targets/ran_build/build
```


**Foxconn**
```c=
sudo ./nr-softmodem -O ../../../foxconn_conf_file/mwc_20899_newfhi_E_3450.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/1005_gNB_1.log 2>&1
```
:::
## Power-on Sequence to bring up the Radio
:::success
### Scenario(1) Bring up from power off
1. Stop O-DU
2. Power on O-RU
3. RU terminal 
```c=
./init_rrh_config_enable_cuplane
```
4. Wait PTP lock at O-RU (圖片補充)
5. Start O-DU
```c=
Run OAI gNB
cd /home/oai72/fhidriver_E/openairinterface5g/cmake_targets/ran_build/build
```
```c=
sudo ./nr-softmodem -O ../../../foxconn_conf_file/mwc_20899_newfhi_E_3450.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/1214_gNB_1.log 2>&1
```
### Scenario (2) Restart O-RU
1. Stop O-DU
2. Power down O-RU
3. Power up O-RU
4. RY terminal
```c=
./init_rrh_config_enable_cuplane
```
5. wait PTP lock at O-RU
6. start O-DU
```c=
sudo ./nr-softmodem -O ../../../foxconn_conf_file/mwc_20899_newfhi_E_3450.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/1005_gNB_1.log 2>&1
```
:::
























## Test 12/11

### 完成設定DU上的RU_adress,u-vlan,c-vlan

- Result
:::danger
**Error log**

IOT instruction  sudo ./nr-softmodem -O ../../../foxconn_conf_file/mwc_20899_newfhi_E_3450.con
:::
:::spoiler OAI gNB log
```c=
nSectorIndex[0] = 0
ru_0_cc_0_idx_0: [ handle 0x9529ae80 0 0 ] [nPoolIndex 0] nNumberOfBuffers 560 nBufferSize 14432
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 0] mb pool 0x2dd051a80 
ru_0_cc_0_idx_1: [ handle 0x9529ae80 0 0 ] [nPoolIndex 1] nNumberOfBuffers 4480 nBufferSize 32
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 1] mb pool 0x2dc6d3040 
size_of_prb_map 9872
ru_0_cc_0_idx_2: [ handle 0x9529ae80 0 0 ] [nPoolIndex 2] nNumberOfBuffers 560 nBufferSize 9872
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 2] mb pool 0x2dc2caa00 
ru_0_cc_0_idx_3: [ handle 0x9529ae80 0 0 ] [nPoolIndex 3] nNumberOfBuffers 560 nBufferSize 14432
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 3] mb pool 0x2dbbb1940 
ru_0_cc_0_idx_4: [ handle 0x9529ae80 0 0 ] [nPoolIndex 4] nNumberOfBuffers 4480 nBufferSize 32
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 4] mb pool 0x2db232f00 
ru_0_cc_0_idx_5: [ handle 0x9529ae80 0 0 ] [nPoolIndex 5] nNumberOfBuffers 560 nBufferSize 9872
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 5] mb pool 0x2dae2a8c0 
ru_0_cc_0_idx_6: [ handle 0x9529ae80 0 0 ] [nPoolIndex 6] nNumberOfBuffers 560 nBufferSize 3360
CC:[ handle 0x9529ae80 ru 0 cc_idx 0 ] [nPoolIndex 6] mb pool 0x2da711800 
app_io_xran_interface:659: xran_max_ant_arr[HW]   [RAU] has loaded ETHERNET trasport protocol.
[0mmalloc_consolidate(): invalid chunk size
```
:::

### 修改mwc_20899_newfhi_E_3450上的sdr_addrs路徑
path:`/home/oai72/fhidriver_E/openairinterface5g/foxconn_conf_file/mwc_20899_newfhi_E_3450`

:::success
sdr_addrs = "dummy --usecasefile /home/oai72/fhidriver_E/openairinterface5g/==foxconn_conf_file==/usecase_du_3450.cfg --num_eth_vfs 2 --vf_addr_o_xu_a \"0000:3b:0a.0,0000:3b:0a.1\""
:::

- Result
:::warning
DU log顯示DU成功輸出流量但RU並沒有成功回傳
:::
:::spoiler OAI gNB log
```c=
[PHY]   RU 0 no rf device
[0m[PHY]   RU 0 RF started opp_enabled 0
[0m[PHY]   first_rx is set
[0m[PHY]   before adjusting, OAI: frame=0 slot=0, XRAN: frame=0 slot=10
[0m[PHY]   After adjusting, OAI: frame=0 slot=10, XRAN: frame=0 slot=10
[0m[NR_MAC]   Frame.Slot 128.0

[0m[NR_PHY]   [o-du 0][rx       0 pps       0 kbps       0][tx   63804 pps   63804 kbps 1686460][Total Msgs_Rcvd 0]
[0m[NR_PHY]   [o_du0][pusch0       0 prach0       0]
[0m[NR_PHY]   [o_du0][pusch1       0 prach1       0]
[0m[NR_MAC]   Frame.Slot 256.0

[0m[NR_PHY]   [o-du 0][rx       0 pps       0 kbps       0][tx  127546 pps   63742 kbps 2107388][Total Msgs_Rcvd 0]
[0m[NR_PHY]   [o_du0][pusch0       0 prach0       0]
[0m[NR_PHY]   [o_du0][pusch1       0 prach1       0]
[0m[NR_MAC]   Frame.Slot 384.0

[0m[NR_PHY]   [o-du 0][rx       0 pps       0 kbps       0][tx  191290 pps   63744 kbps 2107388][Total Msgs_Rcvd 0]
[0m[NR_PHY]   [o_du0][pusch0       0 prach0       0]
[0m[NR_PHY]   [o_du0][pusch1       0 prach1       0]
[0m[NR_MAC]   Frame.Slot 512.0

[0m[NR_PHY]   [o-du 0][rx       0 pps       0 kbps       0][tx  255034 pps   63744 kbps 2107388][Total Msgs_Rcvd 0]
[0m[NR_PHY]   [o_du0][pusch0       0 prach0       0]
[0m[NR_PHY]   [o_du0][pusch1       0 prach1       0]
```
:::

### 額外發現
重新開啟RU發現address有異動，根據[Foxconn RPQN O-RU Installation And Operating Manual page 2](https://www.manualslib.com/manual/2844870/Foxconn-Rpqn-O-Ru.html?page=2#manual)，我們注意到，版本上的問題導致不支援SFP+(Small Form-factor Pluggables)，並且透過ifconfig上也讀不到interface

:::success
- 正常情況(版本 v2.4 up)
![image](https://hackmd.io/_uploads/By3VEcVIT.png)
:::

---

:::warning
- 配合交大的情況(版本 v1.6.18)，並沒有讀取到 ==qse-eth==
![image](https://hackmd.io/_uploads/ByPom5N8a.png)
:::

