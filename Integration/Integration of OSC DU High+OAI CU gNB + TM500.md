---
title: Integration of OSC DU High+OAI CU gNB + TM500

---

![](https://i.imgur.com/JORnn3y.png =150x)@NTUST
# Integration of OSC DU + TM500
[TOC]
## Access method 
:::success
**TM500 Server (Supermicro) :**
- IP Address : 192.168.8.67
- Username : viavi
- Password : viavi

**TM500 Control PC :**
- IP Address : 192.168.8.68
	- User Password : bmwee809
- Anydesk ID : 980393298
	- Anydesk Password : bmwbmwbmwee809

**OSC DU (Dell R750) :**
- IP Address : 192.168.8.43
	- Username : oai72
	- Password : bmwlab
	- Root Password : bmwlab
- IDRAC IP : 192.168.10.123
	- IDRAC user name : root
	- IDRAC Password : bmwbmwbmwee809
:::

## topology

TODO

- TM500 server

![image](https://hackmd.io/_uploads/Synl1gQKT.png)

## Current status
### operate status
| Item                                                           | Status |
| -------------------------------------------------------------- |:------ |
| [PTP sync of TM500](#PTP-sync-in-TM500)                        | :x:    |
| [Bring up TM500 using TMA](#Bring-up-TM500-using-TMA)          | :x:    |
| [PTP sync of OSC DU](#PTP-sync-of-OSC-DU)                      | :x:    |
| [Bring up [I] OSC DU + [F] L1app](#Bring-up-I-OSC-DU--F-L1app) | :x:    |
| [Check RU status](#Check-RU-status)                            | :x:    |
| [TM500 configure setting](#TM500-configure-setting)            | :x:    |
| [UESIM do cell search](#UESIM-do-cell-search)                  | :x:    |

### msg status
| Item                            | Status |
| ------------------------------- |:------ |
| NG Setup                        | N/A    |
| F1 Setup                        | :x:    |
| DU Configuration Update         | :x:    |
| RU get C/U Plane packet from DU | :x:    |
| RU send U Plane packet to DU    | :x:    |
| UE get mib and sib1 for DL sync | :x:    |
| UE send PRACH for UL sync       | :x:    |
| UE send RRC setup request       | :x:    |
| RRC setup complete              | :x:    |
| NAS registration                | :x:    |
| Service request                 | :x:    |
| Service response                | :x:    |
| PDU session complete            | :x:    |
| Data network connection         | :x:    |
| Performance                     | :x:    |

:::info
### status
- Change to use uncompressed mode to speed up the process.
- RUSIM will not crash at bring up for uncompressed config.
- But after OSD DU bring up it will make RUSIM have assert error and crash.
	- [Assert error shows that ecpri packet from OSC DU is wrong for REMask.](https://hackmd.io/DRoP8KsoTTWWxQCPaU5Kew?view#error-of-using-uncompressed-mode-and-static-9BFP-mode)
:::
## Integration Environment
### OSC DU + l1app
**Hardware:**
|     Item     | Info                                                               |
|:------------:| ------------------------------------------------------------------ |
|     CPU      | Intel(R) Xeon(R) Gold 6326 CPU @ 2.90GHz (16 cores) x 2            |
|    Memory    | 192GB (64GB x 3)                                                   |
|     Disk     | 600GB                                                              |
|     NIC      | Intel X710-DA2 (i40e,8.30 0x8000a4db) (Bus-Info: ca:00.0, ca:00.1) |
| Server Model | Dell PowerEdge R750                                                |

**Software:**
|   **Item**    | **Info**                                                          |
|:-------------:|:----------------------------------------------------------------- |
|      OS       | CentOS Linux release 7.9.2009 (Core)                              |
|    Kernel     | Linux localhost.localdomain 3.10.0-1160.99.1.rt56.1245.el7.x86_64 |
|     DPDK      | 20.11.3                                                           |
|   LinuxPTP    | 3.1.1                                                             |
| OSC DU Branch | i-release                                                         |
| l1app Branch  | oran_f_release_v1.0                                               |

### TM500
**Hardware:**
|     Item     | Info                                                     |
|:------------:| -------------------------------------------------------- |
|     CPU      | Intel(R) Xeon(R) CPU E5-2695 v4 @ 2.10GHz (18 cores) x 2 |
|    Memory    | 132GB                                                    |
|     Disk     | 1T                                                       |
|     NIC      | MT27800                                                  |
| Server Model | Supermicro SSG-6028R-E1CR12L                             |

**Software:**
|  **Item**   | **Info**                                          |
|:-----------:|:------------------------------------------------- |
|     OS      | CentOS 9                                          |
|   Kernel    | Linux localhost.localdomain 5.14.0-391.el9.x86_64 |
|  Boot disk  | FlexBoot v3.6.403                                 |
| TMA version | NLC 1.4.0                                         |

## Configurations
### Configuration Files

|     | O-DU High | O-DU Low         | RUSIM                           | UESIM                    |
| --- | --------- | ---------------- | ------------------------------- | ------------------------ |
| L2  | du_cfg.h  | N/A              | N/A                             | Configure Radio Contexts |
| L1  | N/A       | xrancfg_sub6.xml | oran_2_0_default.csv, o-ran.cfg | N/A                      |
| FH  | N/A       | xrancfg_sub6.xml | oran_2_0_default.csv, o-ran.cfg | N/A                      |

### O-DU High
- File location : `l2/src/du_app/du_cfg.h`

| Parameters                  | value                     | config parameter                                                |
| --------------------------- | ------------------------- | --------------------------------------------------------------- |
| CC config                   | 2x2                       | Set in function `lwr_mac_procConfigReqEvt`                      |
| DL Point A frequency(ARFCN) | 3351.06 MHz (623404)      | `NR_DL_ARFCN 623404`                                            |
| DL center frequency         | 3400.2 MHz (3400.095 MHz) | Check calculation [here](#DL-center-frequency-calculation)      |
| SSB frequency               | 3358.98 MHz               | Check calculation [here](#SSB-center-frequency-calculation)            |
| System Bandwidth            | 100 MHz                   | `NR_BANDWIDTH BANDWIDTH_100MHZ`                                 |
| SCS                         | 30KHZ                     | `NR_SCS SCS_30KHZ             `                                 |
| PCI(Physical Cell ID)       | 1                         | `NR_CELL_ID 1                 `                                 |
| Duplex mode                 | TDD                       | `DUPLEX_MODE DUP_MODE_TDD` Need to set when you compile DU HIGH |

#### DL center frequency calculation
- SCS 30KHZ, Bandwidth 100 MHz can have 273 PRBs.

![image](https://hackmd.io/_uploads/BJWoK-NY6.png)

- DL center frequency is **DL Point A frequency + (273PRBs/2)**
- 1 PRB has 12 subcarrier, 1 subcarrier is 30 KHZ=0.03 MHz.
- So **DL center frequency=3351.06+136.5x12x0.03=3400.2 MHz**

#### SSB center frequency calculation
- SSB frequency identifies the position of resource element RE=#0 (subcarrier #0) of resource block RB#10 of the SS block.

<iframe frameborder="0" style="width:100%;height:500px;" src="https://viewer.diagrams.net/?tags=%7B%7D&highlight=0000ff&edit=_blank&layers=1&nav=1&title=SSB_freq.drawio#Uhttps%3A%2F%2Fdrive.google.com%2Fuc%3Fid%3D1sEdNnniE0G9rWaid7DuCrtFqD9ar-1X3%26export%3Ddownload"></iframe>

- So **SSB frequency=3351.06+24(RB)x12(subcarrier)x0.015(SCS)+0+10(RB)x12(subcarrier)x0.03(SCS)=3358.98 MHz**


### O-DU Low
- File location : `/FlexRAN/l1/bin/nr5g/gnb/l1/xrancfg_sub6.xml`

| Parameters               | value             | config parameter                                                                                                                                                                                                                                                                                                                                       |
| ------------------------ | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| vlan id                  | 5                 | `<c_plane_vlan_tag>5</c_plane_vlan_tag>``<u_plane_vlan_tag>5</u_plane_vlan_tag>`                                                                                                                                                                                                                                                                       |
| pdsch/pusch eaxcid       | 0, 1              | automatically set by L1 based on CC config, it will start from 0                                                                                                                                                                                                                                                                                       |
| prach eaxcid             | 2, 3              | automatically set by L1 based on CC config, it will start after end of sch eaxcid                                                                                                                                                                                                                                                                      |
| dl/ul compression method | 16bitUncompressed | `<xranCompMethod>0</xranCompMethod>`</br>`<xranCompHdrType>1</xranCompHdrType>`</br>`<xraniqWidth>16</xraniqWidth>`</br>`<xranPrachCompMethod>0</xranPrachCompMethod>`</br>`<xranPrachiqWidth>16</xranPrachiqWidth>`</br>`<oRu0PrbElemDl0>0,273,0,14,0,0,0,16,0,0,0</oRu0PrbElemDl0>`</br>`<oRu0PrbElemUl0>0,273,0,14,0,0,0,16,0,0,0</oRu0PrbElemUl0>` |
| DU MAC address           | 00:11:22:33:44:66 | create in `/home/oran/G_O-DU/phy/setupvf.sh`                                                                                                                                                                                                                                                                                                           |
| RU MAC address           | 10:70:fd:14:1c:10 | `<oRuRem0Mac0>10:70:fd:14:1c:10</oRuRem0Mac0>`                                                                                                                                                                                                                                                                                                         |
| MTU size                 | 9000              | `<MTU>9000</MTU>`                                                                                                                                                                                                                                                                                                                                      |
| T1a_min_cp_dl            | 258 μs            | `<T1a_min_cp_dl>258</T1a_min_cp_dl>`                                                                                                                                                                                                                                                                                                                   |
| T1a_max_cp_dl            | 470 μs            | `<T1a_max_cp_dl>470</T1a_max_cp_dl>`                                                                                                                                                                                                                                                                                                                   |
| T1a_min_cp_ul            | 285 μs            | `<T1a_min_cp_ul>285</T1a_min_cp_ul>`                                                                                                                                                                                                                                                                                                                   |
| T1a_max_cp_ul            | 429 μs            | `<T1a_max_cp_ul>429</T1a_max_cp_ul>`                                                                                                                                                                                                                                                                                                                   |
| T1a_min_up               | 50 μs             | `<T1a_min_up>50</T1a_min_up>       `                                                                                                                                                                                                                                                                                                                   |
| T1a_max_up               | 196 μs            | `<T1a_max_up>196</T1a_max_up>      `                                                                                                                                                                                                                                                                                                                   |
| Ta4_min                  | 0 μs              | `<Ta4_min>0</Ta4_min>              `                                                                                                                                                                                                                                                                                                                   |
| Ta4_max                  | 75 μs             | `<Ta4_max>75</Ta4_max>             `                                                                                                                                                                                                                                                                                                                   |
| Tadv_cp_dl               | 25 μs             | `<Tadv_cp_dl>25</Tadv_cp_dl>       `                                                                                                                                                                                                                                                                                                                   |
| T2a_min_cp_dl            | 285 μs            | `<T2a_min_cp_dl>285</T2a_min_cp_dl>`                                                                                                                                                                                                                                                                                                                   |
| T2a_max_cp_dl            | 479 μs            | `<T2a_max_cp_dl>479</T2a_max_cp_dl>`                                                                                                                                                                                                                                                                                                                   |
| T2a_min_cp_ul            | 285 μs            | `<T2a_min_cp_ul>285</T2a_min_cp_ul>`                                                                                                                                                                                                                                                                                                                   |
| T2a_max_cp_ul            | 429 μs            | `<T2a_max_cp_ul>429</T2a_max_cp_ul>`                                                                                                                                                                                                                                                                                                                   |
| T2a_min_up               | 71 μs             | `<T2a_min_up>71</T2a_min_up>       `                                                                                                                                                                                                                                                                                                                   |
| T2a_max_up               | 428 μs            | `<T2a_max_up>428</T2a_max_up>      `                                                                                                                                                                                                                                                                                                                   |
| Ta3_min                  | 20 μs             | `<Ta3_min>20</Ta3_min>             `                                                                                                                                                                                                                                                                                                                   |
| Ta3_max                  | 32 μs             | `<Ta3_max>32</Ta3_max>             `                                                                                                                                                                                                                                                                                                                   |

### RUSIM
- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\oran_2_0_defaults.csv`

| Parameters    | RUSIM     | config parameter                                                                                                                                               |
| ------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tadv_cp_dl    | 125000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/tcp-adv-dl,SR_UINT32_T`    |
| T2a_min_cp_dl | 258000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-dl,SR_UINT32_T` |
| T2a_max_cp_dl | 470000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-dl,SR_UINT32_T` |
| T2a_min_cp_ul | 285000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-ul,SR_UINT32_T` |
| T2a_max_cp_ul | 859000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-ul,SR_UINT32_T` |
| T2a_min_up    | 50000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-up,SR_UINT32_T`    |
| T2a_max_up    | 196000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-up,SR_UINT32_T`    |
| Ta3_min       | 0 ns      | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-min,SR_UINT32_T`       |
| Ta3_max       | 75000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-max,SR_UINT32_T`       |

- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\o-ran.cfg`

| Parameters     | RUSIM             | config parameter                        |
| -------------- | ----------------- |:--------------------------------------- |
| DU MAC address | 00:11:22:33:44:66 | `DefaultDuMacAddress=00:11:22:33:44:66` |

#### Timing window compare
| Parameters    | RUSIM  | O-DU LOW |
| ------------- | ------ | -------- |
| Tadv_cp_dl    | 25 μs  | 25 μs    |
| T2a_min_cp_dl | 258 μs | 285 μs   |
| T2a_max_cp_dl | 470 μs | 479 μs   |
| T2a_min_cp_ul | 285 μs | 285 μs   |
| T2a_max_cp_ul | 859 μs | 429 μs   |
| T2a_min_up    | 50 μs  | 71 μs    |
| T2a_max_up    | 196 μs | 428 μs   |
| Ta3_min       | 0 ns   | 20 μs    |
| Ta3_max       | 75 μs  | 32 μs    |

### UESIM
#### Configure Radio Contexts
| Parameters                           | value   |
| ------------------------------------ | ------- |
| Duplex                               | TDD     |
| Cell Id                              | 1       |
| Downlink carrier frequency (100 kHz) | 34002   |
| System bandwidth (MHz)               | 100     |
| AbsoluteFrequencyPointA (100 kHz)    | 33510.6 |
| AbsoluteFrequencySSB (100 kHz)       | 33589.8 |

<!-- #### Define MTS Cells
| Parameters             | value |
| ---------------------- | ----- |
| Cell Id                | 1     |
| DL Frequency (100 kHz) | 34002 | -->

### TM500 RUSIM csv file generate
- VIAVI have a tool to generate csv file. Only need to provide yaml file and fill in the parameters.
:::success
#### csv file generate guide
1. Download the csv file generate tool
	- [csv file generate tool download](https://drive.google.com/drive/folders/1kHT4-YUZXDo3Mnssf59fUXME64ry38yy)
2. Open cmd in your laptop.

![image](https://hackmd.io/_uploads/H1MkuiURp.png)

3. Go in the path you download csv generator tool folder(`__tool rusim csv generator`).
```bash=
cd C:\Users\user\Desktop\School\NTUST\BMW\TM500\virtualize TM500\__tool rusim csv generator
```
4. Create a folder and put the yaml file in.

![image](https://hackmd.io/_uploads/HkfewsURp.png)

5. Use `csvGen.exe` to generate csv.
	- Put yaml file path after `-f` parameter.
```bash=
# example : csvGen -f <path of yaml file>
C:\Users\user\Desktop\School\NTUST\BMW\TM500\virtualize TM500\__tool rusim csv generator>csvGen -f ntust_oai/ntust_oai_du.yaml
--------------------------------------------------------------------------------
Viavi ORAN .csv file generator version 0.4 - 31st July 23
--------------------------------------------------------------------------------
###############
input file name: C:\Users\user\Desktop\School\NTUST\BMW\TM500\virtualize TM500\__tool rusim csv generator\ntust_oai\ntust_oai_du.yaml
###############

Suggested MCI command set:


CONF TEMPLATE_ORU SysrepoInitFile ntust_oai_du.csv
CONF TEMPLATE_ORU MtuOverride 1500
CONF TEMPLATE_ORU DuMacAddress 00:11:22:33:44:66
CONF TEMPLATE_ORU AutoStart ON
SCXT 0 NR
SELR 0 1 ORU1-P1 0
ADDR 0 1 ORU1-P1
```
6. The generated `.csv` file and `_cmd.txt` file will create at the same path of yaml file.
	- A _cmd.txt file containing the suggested MCI command associated with the RU described by the .csv file.

![image](https://hackmd.io/_uploads/HJtwwsIC6.png)
7. Change csv file name to `oran_2_0_defaults.csv`.

![image](https://hackmd.io/_uploads/SkzvFo80a.png)
:::
:::spoiler `NR_1CC4x4_num1_Greigns.yaml`
```bash=
############### Common parameters #####
ORAN YANG model version: 2 #2,3,4,5,6,7 or 8  - optional: default is 2
MTU size: 9000                         #optional: default is 1500bytes
O-RU configuration mode: 0                  # 0: semi-static - 1: ODU configured - optional: default is 0
O-DU MAC address: 00:11:22:33:44:66           #only required in semi-static mode
M-plane VLAN ID: 20                         #only required in ODU configured mode
M-plane DHCP state: on                      #on/off only required in ODU configured mode
M-plane IP version: IPV4                   #IPV4/IPV6 - only required in ODU configured mode
M-plane IP address: 10.10.10.2/24        #only required in ODU configured mode and when DHCP is off
M-plane CallHome Ip address: 10.10.10.1   #only required in ODU configured mode and when DHCP is off
PTP domain number: 24                    #optional - default is 24
PTP- G_8275_1- multicast-mac-address:   #NONFORWARDABLE/FORWARDABLE - optional - default is NONFORWARDABLE

############### per CC config #####
perCC config:

- CCid: 1
  Ethernet O-RU port: ORU1-P1          #(2x2 or 4x4 or 4layerMUMIMO or 8layerMUMIMO or 16layerMUMIMO) - optional: default is 2x2
  VLAN ID: 5
  CC configuration: 2x2               #(2x2 or 4x4) - optional: default is 2x2
  Traffic type: NR                    #(LTE or NR )- optional: default is NR
  Numerology: 1                        #(0,1,3) - optional: default is 1
  Bandwidth: 100                       #(in MHz) - optional: default is 100MHz
  Alpha value: 0                       #optional: default is 0
  Beta value: 0                        #optional: default is 0
  DL Carrier Frequency: 0               #optional: default is 0
  UL Carrier Frequency: 0               #optional: default is 0
  DL compression: 9bitBFP          # (16BitUncompressed, 14bitBFP, 12bitBFP, 9bitBFP or 4bitModComp) assumes static compression controlled on a per direction basis
  DL (PDSCH) eaxcid list: 0,1        #order in list is used to control mapping on multiple BB cards
  DL mixed numerology?: no               #(yes/no) only set to yes for FR2 mixed numerology/numerology =3
  Additional SSB eaxcid list:             #only required for FR2 mixed numerology/numerology =3
  UL compression: 9bitBFP               #(16BitUncompressed, 14bitBFP, 12bitBFP or 9bitBFP) assumes static compression controlled on a per direction basis
  UL (PUSCH) eaxcid list: 0,1         #order in list could be used to control mapping on multiple BB cards
  Is PUSCH and RACH traffic on separate eaxcid?: yes #(yes/no)
  Additional RACH eaxcid list: 2,3    #only required if PUSCH and RACH are on different eaxcid
  Number of UL streams dedicated to SRS:  #only required for MU-MIMO scenarios
  Eaxcid of first SRS dedicated stream:   #subsequent eaxcid values are incremented from this initial value
```
:::

| Parameters                                    | value   |
| --------------------------------------------- | ------- |
| MTU                                           | 9000    |
| VLAN ID                                       | 5       |
| CC configuration                              | 2x2     |
| Numerology                                    | 1       |
| Bandwidth                                     | 100     |
| Alpha value                                   | 0       |
| Beta value                                    | 0       |
| DL Carrier Frequency                          | 0       |
| UL Carrier Frequency                          | 0       |
| DL compression                                | 9bitBFP |
| DL (PDSCH) eaxcid list                        | 0,1     |
| DL mixed numerology?                          | no      |
| UL compression                                | 9bitBFP |
| UL (PUSCH) eaxcid list                        | 0,1     |
| Is PUSCH and RACH traffic on separate eaxcid? | yes     |
| Additional RACH eaxcid list                   | 2,3     |

:::spoiler `oran.cfg`
```bash=
#
# o-ran.cfg
#
# File for manually overriding configuration options for the m-plane
#
# Authors: Nick Thorn, Wade Oram
#
# Notes:
#   For more info please refer to:
#   https://aeronet.aeroflex.corp/5Gwiki/index.php?title=OranSysRepoCallHomeConfig
#

# ##################################################################################
# Eventually come from radio selection - common to all RU's
# ##################################################################################

# Enable DHCP (default 0)
#UseDhcp=1

# Use ipv6 (default 0)
UseIPv6=0

# Set the vendor id, used in DHCP (default "o-ran-ru/viavi")
#VendorId=xran-ru/viavi

# Automatically start the cu-plane (1) or wait for the m-plane activation to start (0) (default 1)
bAutoStartCUPlane=1

# ##################################################################################
# Eventually come from radio selection - Set individually per RU
# ##################################################################################

# THe uncompressed bit width used by the DU.
# Must be in the range 1 to 255
# Defaults to 16 if no value is provided or the value is ooutside the allowed range
#UncompressedBitWidth=16

# Set the uplane's use of compression. If this entry is not present, the compression settings in this file will not be used
UseUplaneCompression=1

# Set the u-plane compressed bit width - legal values are 9 (default) or 14
# only has significance when UseUplaneCompression=1
UplaneCompressedBitWidth=9

# set the type of RU - single server, RU - over multple servers - split processing, RU over multiple servers symetrical processing
# Values are RuSingleServer, RuMultipleServerSplit and RuMultipleServerSymetrtical. The default value is RuSingleServer
#RUType=SingleServerRu

# Set callhome ip address (default "")
#CallhomeIpAddresses=

# Set m-plane interface ip address (default "", ignored if DHCP is active)
MplaneIpAddress=2001:db8:1:a::1000/64

# set the du mac address - only used if the the mplane data is not populated.
# see:
#   For O-RAN_2_0: /o-ran-processing-element:processing-elements/ru-elements[name='????']/transport-flow/eth-flow/o-du-mac-address
#   For X-RAN_2_0: /xran-processing-element:processing-elements/ru-elements[name='????']/transport-flow/eth-flow/llscu-mac-address
DefaultDuMacAddress=00:11:22:33:44:66

# Sets/overrides the default xml data to load - must match the MPlaneStandard if present
# If not present the file will default to
#    xran_1_1_defaults.csv if the MPlaneStandard is set to xran_1.1
#    oran_2_0_defaults.csv for oran_2.0
#    oran_4_0_FHM_defaults_v001.csv for oran_4.0
SysrepoInitFile=oran_2_0_defaults.csv

# ##################################################################################
#  Common RU configuration and items that can be modified by DU
# ##################################################################################

# Sets the yang models to use, currently xran_1.1 and oran_2.0
# If a CSV is explicitely selected, this value is used only if there is not a header containing the mplane standard in the csv file
# otherwise this is used to select the default csv file when none is specified
# Defaults to oran_2.0 if not present
# MPlaneStandard=oran_4.0

# Set the DHCP DUID enterprise number & string, used for DHCPv6 (default "0002000000ec53363135373430393839")
# Note this is ascii encoded hex string
DuidEn=0002000000ec53363135373430393834

# Set the u-plane vlan-id.
# Eventually this will be unneeded because it is obtained from the m-plane data store (sysrepo)
# which is initialised with the csv file and subsequently modified by the DU (if the DU is M-plane aware).
# Valid values are 1 to 4094. Values outside this range imply no vlan.
LegacyUplaneVlanId=5

# Set the M-plane vlan-id.
# Valid values are 1 to 4094. Values outside this range imply no vlan.
#MplaneVlanId=1

# Set the S-plane vlan-id.
# Valid values are 1 to 4094. Values outside this range imply no vlan.
#SplaneVlanId=3

# Set the console debug verbosity (0 = off, 1 = error, 2 = debug, 3 = info)
#MplaneDebugLevel=3

# Wait for PTP to sync before starting the CU plane. When set to 0, the CU plane will be started without
# waiting for PTP to Sync. This should only be use for M-PLANE tests.
# Note: only applies to standalone builds
#bWaitForPtpSync=1

# Emulate SyncE notifications - this will send a syncE notification along with the PTP notification for DUs that require it.
#bEmulateSyncE=1

# Force single numerology even if multiple numerologies have been configured.
#bSingleNumerology=1

# Schedule PRACH on PUSCH
bPrachOnPusch=0

# Add uniform noise to UL Data, if enabled the minimum bits need to be 4 with a max of 15
#NumNoiseBits=4

# Set CU plane to override Frequency offset value using CplaneType3FreqOffsetVal
#bCplaneType3FreqOffsetOverride=0

# The CplaneType3FreqOffsetVal use by RU.
# Cplane Type 3 freqOffset has a range of +/- BW/2 in units of half SC, so range = +/- (12 * NumRbsInBw / 2) * (ScsForNumerology/PrachScs) * 2.
# For 100Mhz bandwidth below are valid range of values for each numerology considering minimum PRACH SCS 1.25kHz for Mid-band.
# Numerology0:-39600 to 39600 Numerology1:-78624 to 78624 Numerology2:-77760 to 77760 Numerology3:-792 to 792 Numerology4:-408 to 408
#CplaneType3FreqOffsetVal=-3276
```
:::

| Parameters               | value                 |
| ------------------------ | --------------------- |
| bAutoStartCUPlane        | 1                     |
| UseUplaneCompression     | 1                     |
| UplaneCompressedBitWidth | 9                     |
| DefaultDuMacAddress      | 00:11:22:33:44:66     |
| SysrepoInitFile          | oran_2_0_defaults.csv |
| LegacyUplaneVlanId       | 5                     |

## cloudUE
- In Control PC, use SSH to access TM500 server(192.168.8.67).

![image](https://hackmd.io/_uploads/B13RcGuaT.png)

- Fill in FTP_USER and ETH0_ADDR in `cuedock.yaml`.
```bash=
cd installcue/
vim cuedock.yaml
FTP_ADDR = 192.168.8.68 // control PC IP
ETH0_ADDR = 192.168.8.67 // cloudUE ETH0 IP
```
- start docker and run up cloudUE
```bash=
# start docker
sudo systemctl start docker

# Install cloudUE on docker:
cd installcue/
sudo docker compose -f cuedock.yaml up -d
```
![image](https://hackmd.io/_uploads/BkzASkg5T.png)
- check cloudUE container is running up properly
```bash=
sudo docker ps
```
![image](https://hackmd.io/_uploads/HkYqIPHC6.png)

- Then you can telnet to TM500(192.168.8.67) to see the log.

![image](https://hackmd.io/_uploads/BkNvkqMY6.png)

<!-- :::warning
- If you get crash dump you can try to restart docker.
```bash=
# Remove cloudUE from docker:
sudo docker compose -f cuedock.yaml down
```
- After remove you need to bring up cloudue again
```bash=
sudo docker compose -f cuedock.yaml up -d
```
- Make licience up
```bash=
sudo docker ps
sudo docker exec -it  <container id> /bin/bash
rm -rf sda1
exit
```
:::
 -->
## PTP sync in TM500
>Check in control PC telnet window
- PTP in TM500 will sync automatically. If you see the log below it sync successfully.

![image](https://hackmd.io/_uploads/Hk9-uTgqT.png)




## Bring up TM500 using TMA
### Connection with TM500
![image](https://hackmd.io/_uploads/Byq1Lye9a.png)

#### Wait until you see this log stop in ==telnet== window, you can use TMA to connect TM500.
- Click TMA icon in control PC, it is in the path of `C:\Program Files (x86)\VIAVI\TM500\5G NR - NLC 1.4.0\Test Mobile Application/TMA`

![image](https://hackmd.io/_uploads/H1D5mq3o6.png)
- Chose EXT-MUE then click start TMA.

![image](https://hackmd.io/_uploads/S12zVc2sT.png =x300)
#### New Session
- optional:If you don't need to catch measurement log then don't need to do this step.

![image](https://hackmd.io/_uploads/BJBDIWaeR.png =x300)

#### 1. Put two configuration(`o-ran.cfg` and `oran_2_0_defaults.csv`) under the path `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root`
- `oran_2_0_defaults.csv` generate method can check [here](#csv-file-generate-guide).

![image](https://hackmd.io/_uploads/HkeDS9U6np.png)

#### 2. Press green key to connect TM500.
![image](https://hackmd.io/_uploads/SyJOtnzap.png)

##### Set TCP/IP address to TM500 server(192.168.8.67)
![image](https://hackmd.io/_uploads/SJUV_3MpT.png)

##### Radio card setting
- Set **I/F Mode to ORAN**. It is FH mode. Will run RUSIM.
![image](https://hackmd.io/_uploads/rypfBZ2x0.png)

- If your **CC config is 2x2** then click one radio card
![image](https://hackmd.io/_uploads/ByD1F3f66.png)

- If your **CC config is 4x4** then click two radio card
![image](https://hackmd.io/_uploads/HkO1kHgha.png)

- Set SCS and BW of radio card
![image](https://hackmd.io/_uploads/HJUTfVg3a.png)

##### Set the DU MAC and MTU in **ORAN O-RU Settings**
![image](https://hackmd.io/_uploads/SyRXFhfp6.png)

#### 3. Press OK.

![image](https://hackmd.io/_uploads/BJMqt6fpT.png)

#### 4. Result
- When you see log like this means you connect successfully.

![image](https://hackmd.io/_uploads/ByWMe7U96.png)

:::warning
#### If you edit oran_2_0_defaults.csv. Remember to disconnect first. Then connect TM500 again as [step 1](#1-Press-connection-setting).
![image](https://hackmd.io/_uploads/H1rr6zI9p.png)
:::
## Measurement log(optional)
### Logging Controller
- For each log, there are two options: "log" or "view".
	- When "log" is selected, the related log information will be recorded in a file.
	- When "view" is selected, the related log information will be displayed in the TMA interface.
- Protocol logs are generally required.

![image](https://hackmd.io/_uploads/r1oIsbTgR.png =x500)

### Start logging
![image](https://hackmd.io/_uploads/Hy9KIi3e0.png =x300)

- result

![image](https://hackmd.io/_uploads/SJOayzaxA.png)

## Running OSC DU
### First Time Use
#### Set VF
```bash
sudo su
cd /home/ubuntu/intel_sw/phy
./cvl.sh 
```

#### Set Virtual IP
```bash
sudo ifconfig lo:ODU 192.168.130.81 up
sudo ifconfig lo:CU_STUB 192.168.130.82 up
```

### Free5gc
```
cd free5gc
sudo ./run.sh
```
:::info
- if occur any error 
```
sudo ./force kill.sh
```
and run again
:::

### FlexRAN
```bash=
sudo su
cd /home/ubuntu/intel_sw/FlexRAN/l1/bin/nr5g/gnb/l1/
source ../../../../../../phy/setupenv.sh 
./l1.sh -xran
```
**Wait belows logs show:**
```
'''
L1 start tick is 1420814686800159
step  1 end tick[1420817993294819], used time[2363.470215(ms)]
step  2 end tick[1420822824888853], used time[3453.605225(ms)]
step  3 end tick[1420827714811245], used time[3495.298096(ms)]
total elapsed time [9312.373000(ms)]

PHY>welcome to application console
```

### xFAPI
```bash=
sudo su
cd /home/ubuntu/intel_sw/xFAPI/bin/
source ../loadenvvar.sh 
./run_xfapi.sh 
```

### CU
```
cd ~/openairinterface5g/cmake_targets/ran_build/build
sudo RFSIMULATOR=server ./nr-softmodem --rfsim --sa -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/cu_gnb.conf
```

### ODU-High
```bash=
sudo su
cd /home/ubuntu/intel_sw/o-du-l2/bin/odu/
./odu 
```

## Check RU status
### After running DU, need to check the packet status of RU.
- Type `FORW MTE GETRUSTATS` in command line.
- Make sure there is no too much early and late packet.

![image](https://hackmd.io/_uploads/SkN7rBl3T.png)

## Catch PCAP in TM500
### using dpdk_cap()
- Telnet to the FH Server port 23
- Format : dpdk_cap(`<index>`, `<cap_size_in_mb>`)
	- `<index>` is the eth port eth2=0, eth3=1
	- `<cap_size_in_mb>` is the total size to capture
	- example : dpdk_cap(0,1000), captures1000MB files from eth2
#### 1. CTRL + ENTER in telnet command line
![image](https://hackmd.io/_uploads/rkiRi5u9a.png)

#### 2. enter dpdk_cap(0,50) and use CTRL + ENTER to stop catching log
- dpdk_cap(0,50) will catch 50 mb.

![image](https://hackmd.io/_uploads/Byun3c_qT.png)

#### 3. Log will save in folder
- path : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\`

![image](https://hackmd.io/_uploads/S1f8Aqu5p.png)

## Cell search and UE configure script setting
### Edit properties
1. Press Edit properties

![image](https://hackmd.io/_uploads/B1MZTgI5a.png)

2. Validate the parameter you change then save it.

![image](https://hackmd.io/_uploads/HJNg2YUcT.png)

### Init command
![image](https://hackmd.io/_uploads/BkXRHshe0.png =x500)

:::spoiler Init command detail
```bash=
SETP L0_UL_CTRL_NR_ENABLE_UL_POWER_STATS 1
SETP L0_UL_CTRL_NR_ENABLE_UL_POWER_CONTROL 0
SETP L0_UL_CTRL_NR_APPLY_SUCCESSIVE_TIMING_ADVANCE 2

SETP NR_L0_PDSCH_BRP_ENABLE_HARQ_COMBINING 1
#SETP NR_L0_DL_SRP_PDSCH_FREQ_CORRECTION 2

#####SETP L1_NR_ENABLE_CSI_CONFIG 1
SETP L1_NR_MAX_NUM_SIMULTANEOUS_BLIND_DETECTS 9999
SETP L1_NR_ENABLE_CSI_RESOURCE_UNIQUENESS_WARNINGS 0

SETP L2_MAC_NR_UL_DL_SLOT_OFFSET 2
SETP L2_MAC_NR_UL_DL_PUCCH_OFFSET 2
#SETP L2_MAC_NR_ENABLE_PUCCH_WITH_PUSCH 1
SETP L2_MAC_NR_ENABLE_HARQ_CONTENTION_CHECK 0
SETP L2_MAC_NR_NUM_UES_PUCCH 64
SETP L2_MAC_NR_NUM_UES_PUSCH 64
SETP L2_MAC_NR_NUM_UES_PREAMBLE 1

#SETP NAS_NR_ENABLE_R15_DEC_18 1
SETP NAS_NR_ENABLE_R15_JUNE_19 1
#SETP NAS_NR_ENABLE_R15_DEC_19 1

SETP NAS_NR_ENABLE_EN_NW_SLICING 0
SETP NAS_ENABLE_INDICATIONS_IN_MTS_MODE 1

# SETP NR_UL_RTT_VERBOSE_REPORT 1
# SETP MTE_NR_RAV_INTERNAL_BAND_79_WORKAROUND 1

SETP L0_DL_SRP_NR_SET_MAX_PDCCH_DMRS_QUALITY_IND 1
SETP L0_ORP_NR_DL_GAIN 30

#SETP L1_NR_SCS30KHZ_USE_CASE_B_FOR_CELL_SEARCH 1

SETP L1_NR5G_VECTOR_GENERATED_BCH_DATA 1
```
:::

### Configure Radio Contexts

| Parameters                           | value   |
| ------------------------------------ | ------- |
| Duplex                               | TDD     |
| Cell Id                              | 1       |
| Downlink carrier frequency (100 kHz) | 34002   |
| System bandwidth (MHz)               | 100     |
| AbsoluteFrequencyPointA (100 kHz)    | 33510.6 |
| AbsoluteFrequencySSB (100 kHz)       | 33589.8 |

![image](https://hackmd.io/_uploads/BkEOxAPJC.png)

<!-- ### Define MTS Cells for Attach UE test

![image](https://hackmd.io/_uploads/HJu_FfIqT.png =x600)

| Parameters             | value |
| ---------------------- | ----- |
| Cell Id                | 1     |
| DL Frequency (100 kHz) | 34002 | -->

## UESIM do cell search
### 1. Run the init command
![image](https://hackmd.io/_uploads/HyHPIBgnp.png =x500)

### 2. Run the cell ssearch test case
![image](https://hackmd.io/_uploads/BklX7989p.png)

### Result
![image](https://hackmd.io/_uploads/S1NA8reha.png)

## MUX LOG
### 1. Create start logging command
- press green key of TMA and click debug log
- Set the dat file size
- Set configure type to OverrideDefaults
- Enter log mask in custom logging command

![image](https://hackmd.io/_uploads/r1UvbL6gR.png)

### 2. Copy start logging command
![image](https://hackmd.io/_uploads/ryvYl8alR.png)

### 3. Create script for logging MUX LOG
#### Create a raw text command script and edit properties

![image](https://hackmd.io/_uploads/B1iKXUalC.png =x350)

#### Paste the logging command in raw command that you copy from step 2

![image](https://hackmd.io/_uploads/rJGSm86eR.png)

### 4. Run the mux log script before you run cell search script

![image](https://hackmd.io/_uploads/SkbYHLaeA.png =x500)

### 5. dat file
#### dat file will save at `C:\Users\bmwla\Documents\VIAVI\TM500\5G NR\Test Mobile Application\Logged Data\240417_204907_session\DebugLog`

![image](https://hackmd.io/_uploads/HyRSv8pe0.png)

### 6. Transfer dat file to txt file
#### Copy dat file to HSDL path
- HSDL path : `C:\Users\bmwla\Desktop\cloudUE\NLA_7_4_0\TM500_NR_5G_EXT-MUE_Release_NLA_7_4_0_CloudUE\HSDL`

![image](https://hackmd.io/_uploads/HJfoFITlC.png)

#### Open cmd using administrator
![image](https://hackmd.io/_uploads/BkfOu8TxR.png)

#### Use loganalyse.exe to transfer dat to txt
```bash=
loganalyse.exe 20240417_210436_CHASSIS0_DBG_2_1.dat > 20240417_210436_CHASSIS0_DBG_2_1.txt
```
![image](https://hackmd.io/_uploads/HkoJqLagR.png)

## Appendix
### Integration Record
[OSC DU + TM500 integration record](https://hackmd.io/DRoP8KsoTTWWxQCPaU5Kew?both)

### Timing window adjust
:::success
#### FORW MTE GETRUSTATS
```bash=
19/02/24 11:03:11:145	forw mte getrustats
19/02/24 11:03:11:154	C: FORW 0x00 Ok MTE GETRUSTATS [ALL]
19/02/24 11:03:11:154	RU Id: 0
19/02/24 11:03:11:154	RU Name: ORU1
19/02/24 11:03:11:154	ELAPSED_TIME:     1566.0 s
19/02/24 11:03:11:154	[RX-WINDOW-STATS] 
19/02/24 11:03:11:154	[EAXCID]: ALL 
19/02/24 11:03:11:154	[RX_TOTAL]: 12632724 
19/02/24 11:03:11:154	[RX_ERR_DROP]: 7800 
19/02/24 11:03:11:154	[RX_EARLY(_U)]: 0 
19/02/24 11:03:11:154	[RX_ON_TIME(_U)]: 11435874 
19/02/24 11:03:11:154	[RX_LATE(_U)]: 0 
19/02/24 11:03:11:154	[RX_SEQID_ERR(_U)]: 0 
19/02/24 11:03:11:154	[RX_EARLY_C_DL]: 0 
19/02/24 11:03:11:154	[RX_ON_TIME_C_DL]: 831182 
19/02/24 11:03:11:154	[RX_LATE_C_DL]: 520 
19/02/24 11:03:11:154	[RX_EARLY_C_UL]: 0 
19/02/24 11:03:11:154	[RX_ON_TIME_C_UL]: 365148 
19/02/24 11:03:11:154	[RX_LATE_C_UL]: 0 
19/02/24 11:03:11:154	[RX_EARLIEST(_U)]: -44.199 
19/02/24 11:03:11:154	[RX_LATEST(_U)]: -14.553 
19/02/24 11:03:11:154	[RX_EARLIEST_C_UL]: -3.976 
19/02/24 11:03:11:154	[RX_LATEST_C_UL]: -44.586 
19/02/24 11:03:11:154	[RX_EARLIEST_C_DL]: -19.248 
19/02/24 11:03:11:154	[RX_LATEST_C_DL]: 999856.712 
19/02/24 11:03:11:154	[RX_SEQID_ERR_C]: 0 
19/02/24 11:03:11:154	[RX_CORRUPT]: 0 
19/02/24 11:03:11:154	[RX_INVALID_PRB]: 7280 
19/02/24 11:03:11:154	[RX_INCONS_SYM_COUNT]: 7289
```
- RX_EARLIEST : The **earliest** packet recieve within the elapsed time. Units in **microsecs**. Positive is **Early** relative to Window **Start**.
	- Need to adjust T2amax to bigger value. Let recieve window start earlier.
- RX_LATEST : The **latest** packet recieve within the elapsed time. Units in **microsecs**. Positive is **late** relative to Window **End**.
	- Need to adjust T2amin to smaller value. Let recieve window end later.

![image](https://hackmd.io/_uploads/BkOP3aNnT.png)
:::


### Other env. compare
- RU and DU have to match FH's needs.

| Parameters               | OSC DU            | OAI DU + LiteOn RU | Prof.TY + NKG RU  | srsRAN + Foxconn RU |
| ------------------------ | ----------------- | ------------------ | ----------------- | ------------------- |
| vlan id                  | 5                 | 5                  | 5                 | 5                   |
| CC config                | 2x2               | 4x4                | 4x4               | 4x4                 |
| pdsch/pusch eaxcid       | 0, 1              | 0, 1, 2, 3         | 1, 2, 3, 4        | 0, 1, 2, 3          |
| prach eaxcid             | 2, 3              | 4, 5, 6, 7         | 1, 2, 3, 4        | 4, 5, 6, 7          |
| dl/ul compression method | 9bitBFP           | 8bitBFP            | 9bitBFP           | 9bitBFP             |
| DU MAC address           | 00:11:22:33:44:66 | 00:11:22:33:44:66  | 00:11:22:33:44:33 | 00:11:22:33:44:66   |
| MTU size                 | 9600              | 1500               | 9600              | 9600                |
| T1a_min_cp_dl            | 285               | 285                | 258               | 250                 |
| T1a_max_cp_dl            | 400               | 429                | 392               | 500                 |
| T1a_min_cp_ul            | 285               | 285                | 155               | 250                 |
| T1a_max_cp_ul            | 429               | 429                | 300               | 465                 |
| T1a_min_up               | 96                | 96                 | 210               | 80                  |
| T1a_max_up               | 196               | 196                | 230               | 250                 |
| Ta4_min                  | 0                 | 110                | 0                 | 0                   |
| Ta4_max                  | 75                | 180                | 70                | 200                 |

- UESIM parameters need to fill in Configure Radio Contexts and do cell search.

| Parameters                  | OSC DU            | OAI DU + LiteOn RU   | Prof.TY + NKG RU      | srsRAN + Foxconn RU   |
| --------------------------- | ----------------- | -------------------- | --------------------- | --------------------- |
| DL Point A frequency(ARFCN) | 3351 MHz (623400) | 3401.58 MHz (626772) | 3700.560 MHz (646704) | 3749.70 MHz (649980)  |
| DL center frequency         | 3400.140 MHz      | 3450.72 MHz (630048) | 3749.700 MHz (649980) | 3749.70 MHz (649980)  |
| SSB frequency               | 3354.600 MHz      | 3450.72 MHz (630048) | 3708.480 MHz (647232) | 3708.480 MHz (647232) |
| System Bandwidth            | 100 MHz           | 100 MHz              | 100MHZ                | 100MHZ                |
| SCS                         | 30KHZ             | 30KHZ                | 30KHZ                 | 30KHZ                 |
| PCI(Physical Cell ID)       | 1                 | 0                    | 21                    | 12                    |
| Duplex mode                 | TDD               | TDD                  | TDD                   | TDD                   |

### SSB center frequency calculation

- SSB frequency identifies the position of resource element RE=#0 (subcarrier #0) of resource block RB#10 of the SS block.

![image](https://hackmd.io/_uploads/BJVWA1m66.png)

- So **SSB frequency=3355.38 + 10(RB)x12(subcarrier)x0.03(SCS)=3358.98 MHz**
:::info
### src code
#### OffsetTo PointA
- path : `/l2/src/du_app/du_cfg.c`
```c=186
duCfgParam.macCellCfg.ssbCfg.ssbOffsetPointA = OFFSET_TO_POINT_A;
```
- path : `/l2/src/du_app/du_cfg.h`
```h=88
#define OFFSET_TO_POINT_A 24
```
- path : `/phy/fapi_5g/source/api/fapi2phy/p7/nr5g_fapi_proc_dl_tti_req.c`
```c=743
    p_bch_pdu->nSSBPrbOffset =
        nr5g_fapi_calculate_nSSBPrbOffset(p_ssb_pdu->ssbOffsetPointA,
            p_phy_instance->phy_config.sub_c_common);
```
```c=711
uint8_t nr5g_fapi_calculate_nSSBPrbOffset(
    uint16_t ssb_offset_point_a, uint8_t sub_c_common)
{
    return ssb_offset_point_a/pow(2, sub_c_common);
}
```
#### ssb-subcarrierOffset(k_ssb)
- path : `/l2/src/du_app/du_cfg.c`
```c=196
duCfgParam.macCellCfg.ssbCfg.ssbScOffset = SSB_SUBCARRIER_OFFSET;
```
- path : `/l2/src/du_app/du_cfg.h`
```h=91
#define SSB_SUBCARRIER_OFFSET 0 
```
:::
:::success
**ref:**
- [Relationship of PointA，offsetToPonitA，kSSB](https://blog.csdn.net/wowricky/article/details/102986189)
- [SS/PBCH](https://www.sharetechnote.com/html/5G/5G_FrameStructure.html#SS_PBCH_FrequencyDomainResourceAllocation)
:::

### Config for RUSIM
| Parameters                          | OSC DU previous              | OAI gNB                  |
| ----------------------------------- | ---------------------------- | ------------------------ |
| vlan id                             | 5                            | 5                        |
| CC config                           | 2x2                          | 2x2                      |
| pdsch/pusch eaxcid                  | 0, 1                         | 0, 1                     |
| prach eaxcid                        | 2, 3                         | 2, 3                     |
| dl/ul compression method            | 16bitUnCompressed            | 16bitUnCompressed        |
| DU MAC address                      | 00:11:22:33:44:66            | 00:11:22:33:44:66        |
| MTU size                            | 9000                         | 1500                     |
| **==DL Point A frequency(ARFCN)==** | **==3351.06 MHz (623400)==** | **3401.58 MHz (626772)** |
| **==DL center frequency==**         | **==3400.2 MHz==**           | **3450.72 MHz (630048)** |
| **==SSB frequency==**               | **==3358.98 MHz==**          | **3450.72 MHz (630048)** |
| System Bandwidth                    | 100 MHz                      | 100 MHz                  |
| SCS                                 | 30KHZ                        | 30KHZ                    |
| PCI(Physical Cell ID)               | 1                            | 1                        |
| Duplex mode                         | TDD                          | TDD                      |


## 5G repeater sample
| Parameters                  | OAI DU + LiteOn RU    | Prof.TY + NKG RU      |
| --------------------------- | --------------------- | --------------------- |
| Band                        | n78                   | n78                   |
| DL Point A frequency(ARFCN) | 3401.58 MHz (626772)  | 3700.560 MHz (646704) |
| DL center frequency         | 3450.72 MHz (630048)  | 3749.700 MHz (649980) |
| SSB NR-ARFCN                | 3450.72 MHz (630048)  | 3708.480 MHz (647232) |
| System Bandwidth            | 100 MHz               | 100MHZ                |
| Frequency range             | 3401.58 MHz ~ 3501.58 | 3700.56 MHz ~ 3800.56 |
| SCS                         | 30KHZ                 | 30KHZ                 |
| TDD Pattern                 | DDDSU                 | DDDSU                 |
| Special Slot Config         | 6:4:4                 | 12:2:0                |
