---
title: 'Integration of OAI gNB(branch: develop) + TM500'

---

![](https://i.imgur.com/JORnn3y.png =150x)@NTUST
# Integration of OAI gNB(branch: develop) + TM500
[TOC]
## Access method
:::success
#### **TM500 Server (Supermicro) :**
- IP Address : 192.168.8.67
- Username : viavi
- Password : viavi

**TM500 Control PC :**
- IP Address : 192.168.8.68
	- User Password : bmwee809
- Anydesk ID : 980393298
	- Anydesk Password : bmwbmwbmwee809

**OAI gNB (Dell R740) :**
- IP Address : 192.168.8.29
	- Username : oai72
	- Password : bmwlab
	- Root Password : bmwlab
<!-- - IDRAC IP : 192.168.10.124
	- IDRAC user name : root
	- IDRAC Password : calvin -->
:::

## topology
![image](https://hackmd.io/_uploads/ByoaPSZC6.png)

- TM500 server

![image](https://hackmd.io/_uploads/Synl1gQKT.png)

## Current status
### operate status
| Item                                                  | Status             |
| ----------------------------------------------------- | ------------------ |
| [PTP sync of TM500](#PTP-sync-in-TM500)               | :heavy_check_mark: |
| [Bring up TM500 using TMA](#Bring-up-TM500-using-TMA) | :heavy_check_mark: |
| [PTP sync of OAI gNB](#PTP-sync-of-OSC-DU)            | :heavy_check_mark: |
| [Bring up OAI gNB](#Bring-up-I-OSC-DU--F-L1app)       | :heavy_check_mark: |
| [Check RU status](#Check-RU-status)                   | :hourglass:        |
| [TM500 configure setting](#TM500-configure-setting)   | :x:                |
| [UESIM do cell search](#UESIM-do-cell-search)         | :x:        |

### msg status
| Item                            | Status             |
| ------------------------------- | ------------------ |
| NG Setup                        | :heavy_check_mark: |
| F1 Setup                        | :heavy_check_mark: |
| DU Configuration Update         | :heavy_check_mark: |
| RU get C/U Plane packet from DU | :hourglass:        |
| RU send U Plane packet to DU    | :x:                |
| UE get mib and sib1 for DL sync | :x:                |
| UE send PRACH for UL sync       | :x:                |
| UE send RRC setup request       | :x:                |
| RRC setup complete              | :x:                |
| NAS registration                | :x:                |
| Service request                 | :x:                |
| Service response                | :x:                |
| PDU session complete            | :x:                |
| Data network connection         | :x:                |
| Performance                     | :x:                |
:::info
### status

:::
## Integration Environment
### OAI gNB
**Hardware:**
|     Item     | Info                                                                 |
|:------------:| -------------------------------------------------------------------- |
|     CPU      | Intel(R) Xeon(R) Gold 6226R CPU @ 2.90GHz (16 cores, disable HT) x 2 |
|    Memory    | 128GB (64GB x 2)                                                     |
|     Disk     | 600GB                                                                |
|     NIC      | Intel X710-DA2 (i40e,8.30 0x8000a4db) (Bus-Info: 3b:00.0, 3b:00.1)   |
| Server Model | Dell PowerEdge R740                                                  |

**Software:**
|  **Item**  | **Info**                                           |
|:----------:|:-------------------------------------------------- |
|     OS     | `Red Hat Enterprise Linux release 9.2 (Plow)`      |
|   Kernel   | `Linux R740 5.14.0-284.18.1.rt14.303.el9_2.x86_64` |
|    DPDK    | `20.11.8`                                          |
|  LinuxPTP  | `3.1.1`                                            |
| OAI branch | `develop`                                          |

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

|     | OAI gNB                                   | RUSIM                           | UESIM                    |
| --- | ----------------------------------------- | ------------------------------- | ------------------------ |
| L3  | gnb.sa.band78.273prb.fhi72.4x4-TM500.conf | N/A                             | N/A                      |
| L2  | gnb.sa.band78.273prb.fhi72.4x4-TM500.conf | N/A                             | Configure Radio Contexts |
| L1  | gnb.sa.band78.273prb.fhi72.4x4-TM500.conf | oran_2_0_default.csv, o-ran.cfg | N/A                      |
| FH  | gnb.sa.band78.273prb.fhi72.4x4-TM500.conf | oran_2_0_default.csv, o-ran.cfg | N/A                      |

### OAI gNB
- File location : `/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-TM500.conf`

| Parameters               | value                | config parameter                                                                                            |
| ------------------------ | -------------------- | ----------------------------------------------------------------------------------------------------------- |
| CC config                | 4x4                  | Define by the setting of pdsch/pusch eaxcid and prach eaxcid                                                |
| DL Point A frequency     | 3401.58 MHz (626772) | `dl_absoluteFrequencyPointA = 626772`                                                                       |
| DL center frequency      | 3450.72 MHz (630048) | Check calculation [here](#DL-center-frequency-calculation)                                                  |
| SSB frequency            | 3450.72 MHz (630048) | `absoluteFrequencySSB = 630048`                                                                             |
| System Bandwidth         | 100 MHz              | Check calculation [here](#System-Bandwidth)                                                                 |
| SCS                      | 30KHZ                | `dl_subcarrierSpacing = 1` `ul_subcarrierSpacing = 1`                                                       |
| PCI(Physical Cell ID)    | 1                    | `physCellId = 1`                                                                                            |
| Duplex mode              | TDD                  | `#tdd-UL-DL-ConfigurationCommon`                                                                            |
| vlan id                  | 5                    | create in `/home/oai72/Script/oaiTMvf.sh`                                                                   |
| pdsch/pusch eaxcid       | 0, 1, 2, 3           | `nb_tx = 4` `pdsch_AntennaPorts_XP = 4` `pusch_AntennaPorts = 4`                                            |
| prach eaxcid             | 4, 5, 6, 7           | `eAxC_offset = 4` `nb_rx = 4`                                                                               |
| dl/ul compression method | 9bitBFP              | `iq_width = 9` `iq_width_prach = 9`                                                                         |
| DU MAC address           | 00:11:22:33:44:66    | create in `/home/oai72/Script/oaiTMvf.sh` and set in `du_addr = ("00:11:22:33:44:66", "00:11:22:33:44:66")` |
| RU MAC address           | 10:70:fd:14:1c:10    | `ru_addr = ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10")`                                                      |
| MTU size                 | 1500                 | `mtu = 1500`                                                                                                |
| T1a_min_cp_dl            | 258 μs               | `T1a_cp_dl = (285, 429)`                                                                                    |
| T1a_max_cp_dl            | 429 μs               | `T1a_cp_dl = (285, 429)`                                                                                    |
| T1a_min_cp_ul            | 285 μs               | `T1a_cp_ul = (285, 429)`                                                                                    |
| T1a_max_cp_ul            | 429 μs               | `T1a_cp_ul = (285, 429)`                                                                                    |
| T1a_min_up               | 96 μs                | `T1a_up = (96, 196)`                                                                                        |
| T1a_max_up               | 196 μs               | `T1a_up = (96, 196)`                                                                                        |
| Ta4_min                  | 110 μs               | `Ta4 = (110, 180)`                                                                                          |
| Ta4_max                  | 180 μs               | `Ta4 = (110, 180)`                                                                                          |
| Tadv_cp_dl               | 25 μs                | `Tadv_cp_dl = 25`                                                                                           |
| T2a_min_cp_dl            | 74 μs                | `T2a_cp_dl = (74, 464)`                                                                                     |
| T2a_max_cp_dl            | 464 μs               | `T2a_cp_dl = (74, 464)`                                                                                     |
| T2a_min_cp_ul            | 36 μs                | `T2a_cp_ul = (36, 392)`                                                                                     |
| T2a_max_cp_ul            | 392 μs               | `T2a_cp_ul = (36, 392)`                                                                                     |
| T2a_min_up               | 80 μs                | `T2a_up = (80, 464)`                                                                                        |
| T2a_max_up               | 464 μs               | `T2a_up = (80, 464)`                                                                                        |
| Ta3_min                  | 84 μs                | `Ta3 = (84, 120)`                                                                                           |
| Ta3_max                  | 120 μs               | `Ta3 = (84, 120)`                                                                                           |

#### DL center frequency calculation
- SCS 30KHZ, Bandwidth 100 MHz can have 273 PRBs.

![image](https://hackmd.io/_uploads/BJWoK-NY6.png)

- DL center frequency is **DL Point A frequency + (273PRBs/2)**
- 1 PRB has 12 subcarrier, 1 subcarrier is 30 KHZ=0.03 MHz.
- So **DL center frequency=3351.06+136.5x12x0.03=3400.2 MHz**

#### System Bandwidth
- In OAI, carrierBandwidth is 273 PRBs. You can find out the BW is 100MHz in the following picture.
```bash=51
dl_carrierBandwidth                                         = 273;
```
```bash=71
ul_carrierBandwidth                                           = 273;
```
![image](https://hackmd.io/_uploads/HkNFKsKC6.png)

#### SSB center frequency calculation
<iframe frameborder="0" style="width:100%;height:500px;" src="https://viewer.diagrams.net/?tags=%7B%7D&highlight=0000ff&edit=_blank&layers=1&nav=1&page-id=WMxXAaMVdXCerrEFyPOU&title=SSB_freq.drawio#Uhttps%3A%2F%2Fdrive.google.com%2Fuc%3Fid%3D1sEdNnniE0G9rWaid7DuCrtFqD9ar-1X3%26export%3Ddownload"></iframe>
:::info
Reference : https://www.linkedin.com/pulse/illustration-5g-ssb-beam-sweeping-freqtime-domain-methods-eladawi/
:::

### RUSIM
- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\oran_2_0_defaults.csv`

| Parameters    | RUSIM     | config parameter                                                                                                                                               |
| ------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tadv_cp_dl    | 125000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/tcp-adv-dl,SR_UINT32_T`    |
| T2a_min_cp_dl | 74000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-dl,SR_UINT32_T` |
| T2a_max_cp_dl | 464000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-dl,SR_UINT32_T` |
| T2a_min_cp_ul | 36000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-ul,SR_UINT32_T` |
| T2a_max_cp_ul | 75800 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-ul,SR_UINT32_T` |
| T2a_min_up    | 24000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-up,SR_UINT32_T`    |
| T2a_max_up    | 464000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-up,SR_UINT32_T`    |
| Ta3_min       | 84000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-min,SR_UINT32_T`       |
| Ta3_max       | 120000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-max,SR_UINT32_T`       |

- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\o-ran.cfg`

| Parameters     | RUSIM             | config parameter                        |
| -------------- | ----------------- |:--------------------------------------- |
| DU MAC address | 00:11:22:33:44:66 | `DefaultDuMacAddress=00:11:22:33:44:66` |

#### Timing window compare
| Parameters    | RUSIM  | O-DU LOW |
| ------------- | ------ | -------- |
| Tadv_cp_dl    | 125 μs | 125 μs   |
| T2a_min_cp_dl | 74 μs  | 74 μs    |
| T2a_max_cp_dl | 464 μs | 464 μs   |
| T2a_min_cp_ul | 36 μs  | 36 μs    |
| T2a_max_cp_ul | 758 μs | 758 μs   |
| T2a_min_up    | 24 μs  | 24 μs    |
| T2a_max_up    | 464 μs | 464 μs   |
| Ta3_min       | 84 μs  | 84 μs    |
| Ta3_max       | 120 μs | 120 μs   |

### UESIM
#### Configure Radio Contexts
| Parameters                           | value   |
| ------------------------------------ | ------- |
| Duplex                               | TDD     |
| Cell Id                              | 1       |
| Downlink carrier frequency (100 kHz) | 34507.2 |
| System bandwidth (MHz)               | 100     |
| AbsoluteFrequencyPointA (100 kHz)    | 34015.8 |
| AbsoluteFrequencySSB (100 kHz)       | 34507.2 |

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
:::spoiler `ntust_oai_du.yaml`
```bash=
############### Common parameters #####
ORAN YANG model version: 2 #2,3,4,5,6,7 or 8  - optional: default is 2
MTU size: 1500                         #optional: default is 1500bytes
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
  CC configuration: 4x4               #(2x2 or 4x4) - optional: default is 2x2
  Traffic type: NR                    #(LTE or NR )- optional: default is NR
  Numerology: 1                        #(0,1,3) - optional: default is 1
  Bandwidth: 100                       #(in MHz) - optional: default is 100MHz
  Alpha value: 0                       #optional: default is 0
  Beta value: 0                        #optional: default is 0
  DL Carrier Frequency: 0               #optional: default is 0
  UL Carrier Frequency: 0               #optional: default is 0
  DL compression: 9bitBFP          # (16BitUncompressed, 14bitBFP, 12bitBFP, 9bitBFP or 4bitModComp) assumes static compression controlled on a per direction basis
  DL (PDSCH) eaxcid list: 0,1,2,3        #order in list is used to control mapping on multiple BB cards
  DL mixed numerology?: no               #(yes/no) only set to yes for FR2 mixed numerology/numerology =3
  Additional SSB eaxcid list:             #only required for FR2 mixed numerology/numerology =3
  UL compression: 9bitBFP               #(16BitUncompressed, 14bitBFP, 12bitBFP or 9bitBFP) assumes static compression controlled on a per direction basis
  UL (PUSCH) eaxcid list: 0,1,2,3         #order in list could be used to control mapping on multiple BB cards
  Is PUSCH and RACH traffic on separate eaxcid?: yes #(yes/no)
  Additional RACH eaxcid list: 4,5,6,7    #only required if PUSCH and RACH are on different eaxcid
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
UplaneCompressedBitWidth=16

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
| UplaneCompressedBitWidth | 16                    |
| DefaultDuMacAddress      | 00:11:22:33:44:66     |
| SysrepoInitFile          | oran_2_0_defaults.csv |
| LegacyUplaneVlanId       | 5                     |

## cloudUE

- In Control PC, use SSH to access TM500 server(192.168.8.67).
>[user name and password](https://hackmd.io/5v1zDsO_T4CbGNKw7ZWesg?view#TM500-Server-Supermicro-)

![image](https://hackmd.io/_uploads/B13RcGuaT.png)

- Fill in FTP_USER and ETH0_ADDR in `cuedock.yaml`.
```bash=
cd installcue/
vim cuedock.yaml
FTP_ADDR = 192.168.8.68 // control PC IP
ETH0_ADDR = 192.168.8.67 // cloudUE ETH0 IP
```
- start docker
```bash=
# start docker
sudo systemctl start docker

# Install cloudUE on docker:
cd installcue/
sudo docker compose -f cuedock.yaml up -d
```
![image](https://hackmd.io/_uploads/BkzASkg5T.png)

- Then you can telnet to TM500(192.168.8.67) to see the log.

![image](https://hackmd.io/_uploads/BkNvkqMY6.png)

:::spoiler Change version of cloudue
#### Change version of cloudue
1. Put the version you want to use of `cloudUE` and `cuedock.yaml` under TM500 server in path `/home/viavi/installcue`.

![image](https://hackmd.io/_uploads/BJT1raYKA.png)
2. Now we have two version

![image](https://hackmd.io/_uploads/Sy5_BaFYC.png)
3. Change ftp server path
- Open filezilla Server Interface

![image](https://hackmd.io/_uploads/H1DrIaKtC.png)
- Click the icon
- Press shared folder
- Click or add the path you want to set the `ftp_root`
- Press set as home dir

![image](https://hackmd.io/_uploads/B18TPpKtR.png)
:::
<!-- :::warning
- If you get crash dump you can try to restart docker.
```bash=
# Remove cloudUE from docker:
sudo docker compose -f cuedock.yaml down
sudo docker compose -f cuedock.yaml up -d
sudo docker ps
sudo docker exec -it <container id> /bin/bash
rm -rf sda1
exit
```
:::
 -->
## PTP sync in TM500
- PTP in TM500 will sync automatically. If you see the log below it sync successfully.

![image](https://hackmd.io/_uploads/Hk9-uTgqT.png)

## Bring up TM500 using TMA
### Connection with TM500
![image](https://hackmd.io/_uploads/Byq1Lye9a.png)

#### Wait until you see this log stop in ==telnet== window, you can use TMA to connect TM500.
- Click TMA icon in control PC, it is in the path of `C:\Users\bmwla\Desktop\cloudUE\NLA_7_4_0\TM500_NR_5G_EXT-MUE_Release_NLA_7_4_0_CloudUE\Test Mobile Application\TMA.exe`

![image](https://hackmd.io/_uploads/BkELhpKFC.png)
- Check the TMA version you want to use. And chose EXT-MUE then click start TMA.

![image](https://hackmd.io/_uploads/BkhIaTttC.png =x300)
:::spoiler NLC 1.4.0(old version of TMA)
- Click TMA icon in control PC, it is in the path of `C:\Program Files (x86)\VIAVI\TM500\5G NR - NLC 1.4.0\Test Mobile Application/TMA`

![image](https://hackmd.io/_uploads/H1D5mq3o6.png)
- Chose EXT-MUE then click start TMA.

![image](https://hackmd.io/_uploads/S12zVc2sT.png =x300)
:::
:::info
#### New Session
- optional:If you don't need to catch measurement log then don't need to do this step.

![image](https://hackmd.io/_uploads/BJBDIWaeR.png =x300)
:::

### Start setting TMA
#### 1. Put two configuration(`o-ran.cfg` and `oran_2_0_defaults.csv`) under the path `C:\Users\bmwla\Desktop\cloudUE\NLA_7_4_0\TM500_NR_5G_EXT-MUE_Release_NLA_7_4_0_CloudUE\ppc_pq\public\ftp_root`
<!-- - old path `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root` -->
- `oran_2_0_defaults.csv` generate method can check [here](https://hackmd.io/LmBA5ogYTOaXQE60jIbeuw?both#csv-file-generate-guide).

![image](https://hackmd.io/_uploads/r1XTPy9K0.png)

#### 2. Press green key to connect TM500.
![image](https://hackmd.io/_uploads/SyJOtnzap.png)

##### Set TCP/IP address to TM500 server(192.168.8.67)
![image](https://hackmd.io/_uploads/SJUV_3MpT.png)

##### Radio card setting
- Parameters need to be set
	- **card**
	- **I/F Mode** : **ORAN**. It is FH mode. Will run RUSIM.
	- **Load Context**
	- **FR Category**
	- **SubCarrier Spacing**
	- **Radio Bandwidth**
	- **RU Name**
![image](https://hackmd.io/_uploads/ryiwvWQq0.png)
![image](https://hackmd.io/_uploads/B1I0I-Q5R.png)

- If your **CC config is 2x2** then click one radio card
![image](https://hackmd.io/_uploads/HykWu-X9C.png)

- If your **CC config is 4x4** then click two radio card
![image](https://hackmd.io/_uploads/ry69ubXcC.png)

##### Set the SysrepoInitFile,  DuMacAddress and MtuOverride in **ORAN O-RU Settings**
![image](https://hackmd.io/_uploads/SyRXFhfp6.png)
>In NLA_7_4_0 version of TMA you need to manual set these parameters
```bash=
CONF TEMPLATE_ORU DuMacAddress 00:11:22:33:44:66
CONF TEMPLATE_ORU SysrepoInitFile oran_2_0_defaults.csv
CONF TEMPLATE_ORU MtuOverride 9000
# CONF TEMPLATE_ORU UncompressedBitWidth 16
```
#### 3. Press OK.

![image](https://hackmd.io/_uploads/BJMqt6fpT.png)

#### 4. Result
- When you see log like this means you connect successfully.

![image](https://hackmd.io/_uploads/ByWMe7U96.png)

#### You can check the setting of radio through these command
```bash=
CONF TEMPLATE_ORU DuMacAddress
CONF TEMPLATE_ORU SysrepoInitFile
CONF TEMPLATE_ORU MtuOverride
# CONF TEMPLATE_ORU UncompressedBitWidth
```
![image](https://hackmd.io/_uploads/ryutsb75A.png)

:::warning
#### If you edit oran_2_0_defaults.csv. Remember to disconnect first. Then connect TM500 again as [step 1](#1-Press-connection-setting).
![image](https://hackmd.io/_uploads/H1rr6zI9p.png)
:::
## Measurement log (optional)
### Logging Controller
- For each log, there are two options: "log" or "view".
	- When "log" is selected, the related log information will be recorded in a file.
	- When "view" is selected, the related log information will be displayed in the TMA interface.
- Protocol logs are generally required.

![image](https://hackmd.io/_uploads/r1oIsbTgR.png =x500)

### Start logging
![image](https://hackmd.io/_uploads/Hy9KIi3e0.png =x300)

- result

![image](https://hackmd.io/_uploads/H1d01zpeC.png)

## Bring up OAI gNB
After make sure RU sim is ready, we start to run the DU.
### Prerequisite
#### OAI gNB install
- You can refer to this [note](https://hackmd.io/@Summer063/HyFoqAlca) to install gNB.
:::info
#### **If you reboot the server you need to do this step :**
- setup maximum performance, vf, and environment
```bash=
source /home/oai72/Script/oaiLOvf.sh
```
:::
:::spoiler oaiTMvf.sh
```bash=
# every time you reboot you need to run this command
sudo cpupower idle-set -D 0
sudo tuned-adm profile realtime
# sudo tuned-adm profile network-latency

# starting sttp
sudo modprobe sctp

# set maximum ring buffers
sudo ethtool -G ens1f1 rx 4096
sudo ethtool -G ens1f1 tx 4096

# Set the maximum MTU
sudo ifconfig ens1f1 mtu 1500

# load the Linux "Base Driver for Intel Ethernet AdaptiveVirtual Function"
sudo modprobe iavf

# set two devices
sudo sh -c 'echo 0 > /sys/class/net/ens1f1/device/sriov_numvfs'
sudo sh -c 'echo 2 > /sys/class/net/ens1f1/device/sriov_numvfs'

# create virtual functions on physical one with MAC addresses and VLAN
sudo ip link set ens1f1 vf 0 mac 00:11:22:33:44:66 vlan 5 spoofchk off
sudo ip link set ens1f1 vf 1 mac 00:11:22:33:44:66 vlan 5 spoofchk off
sleep 1

# These are the DPDK bindings for C/U-planes on vlan 4
# unbind
sudo /usr/local/bin/dpdk-devbind.py --unbind 3b:0a.0
sudo /usr/local/bin/dpdk-devbind.py --unbind 3b:0a.1

# reload "Virtual Function I/O" driver 
sudo modprobe vfio_pci

# bind
sudo /usr/local/bin/dpdk-devbind.py --bind vfio-pci 3b:0a.0
sudo /usr/local/bin/dpdk-devbind.py --bind vfio-pci 3b:0a.1

# Check DPDK binding
sudo /usr/local/bin/dpdk-devbind.py --status

# Check configuration
ip link show ens1f1

# show the setting
sudo ethtool -g ens1f1
```
:::

### Step 1. Configure gNB
- original file : [gnb.sa.band78.273prb.fhi72.4x4-liteon.conf](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-liteon.conf)
- Configuration File Path : `/home/oai72/FH_7.2_dev/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-liteon.conf`
- Can check the [config table](#Configuration)
```bash=
vim /home/oai72/FH_7.2_dev/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-liteon.conf
```
#### Core Network setting
:::success
- `amf_ip_address` shall be the correct AMF IP address in your system
- `GNB_INTERFACE_NAME_FOR_NG_AMF` and `GNB_IPV4_ADDRESS_FOR_NG_AMF` shall match your DU N2 interface name and IP address
- `GNB_INTERFACE_NAME_FOR_NGU` and `GNB_IPV4_ADDRESS_FOR_NGU` shall match your DU N3 interface name and IP address
:::
- free5GC
```bash=173
    amf_ip_address      = ( { ipv4       = "192.168.8.21"; #free5GC
                              ipv6       = "192:168:30::17";
                              active     = "yes";
                              preference = "ipv4";
                            }
                          );

    NETWORK_INTERFACES :
    {
        GNB_INTERFACE_NAME_FOR_NG_AMF            = "eno1np0";
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "192.168.8.29/24";
        GNB_INTERFACE_NAME_FOR_NGU               = "eno1np0";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "192.168.8.29/24";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };
```
- Campus Genius
```bash=173
    amf_ip_address      = ( { ipv4       = "192.168.8.94"; #CG
                              ipv6       = "192:168:30::17";
                              active     = "yes";
                              preference = "ipv4";
                            }
                          );

    NETWORK_INTERFACES :
    {
        GNB_INTERFACE_NAME_FOR_NG_AMF            = "eno1np0";
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "192.168.8.29/24";
        GNB_INTERFACE_NAME_FOR_NGU               = "eno1np0";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "192.168.8.29/24";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };
```
- open5GS
```bash=173
    amf_ip_address      = ( { ipv4       = "127.0.0.5"; #Open5GS
                              ipv6       = "192:168:30::17";
                              active     = "yes";
                              preference = "ipv4";
                            }
                          );

    NETWORK_INTERFACES :
    {
        GNB_INTERFACE_NAME_FOR_NG_AMF            = "lo";
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "127.0.0.17";
        GNB_INTERFACE_NAME_FOR_NGU               = "lo";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "127.0.0.18";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };
```
>`en01np0` : Used for connecting to the laboratory network.
#### PLMN setting
```bash=5
gNBs =
(
 {
    ////////// Identification parameters:
    gNB_ID    =  0xe00;
    gNB_name  =  "gNB-OAI";

    // Tracking area code, 0x0000 and 0xfffe are reserved values
    tracking_area_code  =  1;
    plmn_list = ({ mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); });
```

#### phase_compensation need to be different between DU and RU side.
- Eaxmple : DU use phase_compensation = 1,then RU need to use phase_compensation = 0
```bash=208
L1s = (
{
  num_cc = 1;
  tr_n_preference = "local_mac";
  prach_dtx_threshold = 130
  pucch0_dtx_threshold = 80;
  pusch_dtx_threshold = 10;
  max_ldpc_iterations = 10;
  tx_amp_backoff_dB = 20; # needs to match O-RU configuration
  L1_rx_thread_core = 8;
  L1_tx_thread_core = 10; # relevant after merge of l1_tx_thread
  phase_compensation = 1; # needs to match O-RU configuration
}
);
```
#### RU parameters
:::success
- `nb_tx`: number of TX physical antennas. (Will effect eAxC ID)
- `nb_rx`: number of RX physical antennas. (Will effect eAxC ID)
:::
```bash=225
RUs = (
{
  local_rf       = "no";
  nb_tx          = 4; # physical antennas
  nb_rx          = 4; # physical antennas
  att_tx         = 0; # TODO relevant?
  att_rx         = 0; # TODO relevant?
  bands          = [78];
  max_pdschReferenceSignalPower = -27;
  max_rxgain                    = 75; # TODO relevant?
  sf_extension                  = 0; # TODO relevant?
  eNB_instances  = [0]; # TODO is relevant?
  ru_thread_core = 6;
  sl_ahead       = 5;
  ##beamforming 1x2 matrix: 1 layer x 2 antennas
  bf_weights = [0x00007fff, 0x0000,0x00007fff, 0x0000]; # necessary?

  tr_preference  = "raw_if4p5"; # important: activate FHI7.2
  do_precoding = 0; # needs to match O-RU configuration
  
}
);
```

### Step 2. Configure O-RAN Fronthaul Interface C/U Plane
:::success
- `dpdk_devices`: PCI addresses of NIC VFs binded to the DPDK
- `du_addr`: DU C- and U-plane MAC-addresses
- `ru_addr`: RU C- and U-plane MAC-addresses
- `mtu`: Maximum Transmission Unit for the RU
- `iq_width`: Width of DL/UL IQ samples: if 16, no compression, if <16, applies compression
- `eAxC_offset`:  PRACH antenna offset
:::
```bash=276
fhi_72 = {
  dpdk_devices = ("0000:3b:0a.0", "0000:3b:0a.1");
  io_core = 4;
  worker_cores = (2);
  du_addr = ("00:11:22:33:44:66", "00:11:22:33:44:66");
  ru_addr = ("00:aa:ff:bb:ff:cc", "00:aa:ff:bb:ff:cc");
  mtu = 1500; # check if xran uses this properly
  fh_config = ({
    Tadv_cp_dl = 125;
    T2a_cp_dl = (74, 464); # (min, max)
    T2a_cp_ul = (36, 392); # (min, max)
    T2a_up = (80, 464); # (min, max)
    Ta3 = (84, 120); # (min, max)
    T1a_cp_dl = (285, 429); # (min, max)
    T1a_cp_ul = (285, 429); # (min, max)
    T1a_up = (96, 196); # (min, max)
    Ta4 = (110, 180); # (min, max)
    ru_config = {
      iq_width = 8;
      iq_width_prach = 8;
      fft_size = 12;
    };
    prach_config = {
      eAxC_offset = 4;
      kbar = 0;
    };
  });
};
```

### Step 3. gNB PTP synchronization
**Execution:**
- Already run in system, use command below to check the status
```bash=
sudo systemctl status ptp4l.service
sudo systemctl status phc2sys.service
```
:::success
**Note:**
`rms in ptp4l < 100` and `phc offset in phc2sys < 100` represent sync

```bash=
Feb 19 10:22:11 Dell-R740-Server ptp4l[1956]: [231803.588] rms    6 max   13 freq -11561 +/-   8 delay   273 +/-   0
Feb 19 10:22:12 Dell-R740-Server ptp4l[1956]: [231804.582] rms    5 max   10 freq -11556 +/-   8 delay   270 +/-   1
Feb 19 10:22:13 Dell-R740-Server ptp4l[1956]: [231805.602] rms    7 max   14 freq -11567 +/-   9 delay   271 +/-   2
Feb 19 10:22:14 Dell-R740-Server ptp4l[1956]: [231806.599] rms    4 max    9 freq -11568 +/-   6 delay   272 +/-   0
Feb 19 10:22:15 Dell-R740-Server ptp4l[1956]: [231807.582] rms    6 max   14 freq -11576 +/-   8 delay   272 +/-   0
```
```bash=
Feb 19 10:22:43 Dell-R740-Server phc2sys[1957]: [231835.711] CLOCK_REALTIME phc offset       -11 s2 freq  +22612 delay    558
Feb 19 10:22:43 Dell-R740-Server phc2sys[1957]: [231835.836] CLOCK_REALTIME phc offset         4 s2 freq  +22623 delay    560
Feb 19 10:22:44 Dell-R740-Server phc2sys[1957]: [231835.962] CLOCK_REALTIME phc offset         4 s2 freq  +22624 delay    560
Feb 19 10:22:44 Dell-R740-Server phc2sys[1957]: [231836.087] CLOCK_REALTIME phc offset         5 s2 freq  +22627 delay    559
Feb 19 10:22:44 Dell-R740-Server phc2sys[1957]: [231836.212] CLOCK_REALTIME phc offset         3 s2 freq  +22626 delay    557
```

- Reference : [Checking Clocks Synchronization](https://tsn.readthedocs.io/timesync.html#checking-clocks-synchronization)
- If you want to know how to make ptp run in systemd. Here is the [guide](https://hackmd.io/@Spinnefarn/SJFvKX8ma#12-Time-Syncronization-PTP).
:::
:::spoiler PTP command line not using systemd
```bash=
# Run PTP
sudo ptp4l -i ens1f1 -m -H -2 -s -f /home/oai72/linux_ptp_config/ptp4l.conf > /home/oai/ptp_log/ptp4l.log 2>&1 &
sudo phc2sys -w -m -s ens1f1 -R 8 -f /home/oai72/linux_ptp_config/ptp4l.conf > /home/oai/ptp_log/phc2sys.log 2>&1 &
```
> ens1f1 is the NIC interface that can get ptp packet from FHG.
:::

### Step 4. Run gNB
- remember change log's name
- `/home/oai72/FH_7.2_dev/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-TM500.conf` is the configfile path.
```bash=
cd /home/oai72/FH_7.2_dev/openairinterface5g/cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-TM500.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/test.log 2>&1
```

### Step 5. Check RU connection with DU
#### DU side
- If your DU & RU connect successfully. You can find the received and transmitted traffic from the log.

![image](https://hackmd.io/_uploads/BysLhEWy0.png)

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
- path : `C:/Program Files(x86)\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\`

![image](https://hackmd.io/_uploads/S1f8Aqu5p.png)

## Cell search and UE configure setting
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

| Parameters                           | value              |
| ------------------------------------ | ------------------ |
| Duplex                               | TDD                |
| Cell Id                              | 1                  |
| Downlink carrier frequency (100 kHz) | 34507.2            |
| System bandwidth (MHz)               | 100                |
| AbsoluteFrequencyPointA (100 kHz)    | 34015.8            |
| AbsoluteFrequencySSB (100 kHz)       | 34507.2            |
| Acquire SIB 1                        | :heavy_check_mark: |

![image](https://hackmd.io/_uploads/ryP8mo3gA.png)

<!-- ### Define MTS Cells for Attach UE test

![image](https://hackmd.io/_uploads/HJu_FfIqT.png =x600)

| Parameters             | value |
| ---------------------- | ----- |
| Cell Id                | 1     |
| DL Frequency (100 kHz) | 34002 | -->

## UESIM do cell search
#### 1. Run the init command
![image](https://hackmd.io/_uploads/HyHPIBgnp.png =x500)

#### 2. Run the cell search test case
![image](https://hackmd.io/_uploads/BklX7989p.png)

#### Result
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
---
## Quick test
- cloude ue
```c=
# start docker
sudo systemctl start docker

# Install cloudUE on docker:
cd installcue/
sudo docker compose -f cuedock.yaml up -d

# If you get crash dump you can try to restart docker.
# Remove cloudUE from docker:
sudo docker compose -f cuedock.yaml down

# Make licience up(if you restart docker)
bash=
sudo docker ps
sudo docker exec -it  <container id> /bin/bash
# sudo docker exec -it  a5bf3385ba0a /bin/bash
rm -rf sda1
exit
```
- PTP gNB
```c=
sudo systemctl status ptp4l.service
sudo systemctl status phc2sys.service
```
- gNB terminal
```c=
cd /home/oai72/FH_7.2_dev/openairinterface5g/cmake_targets/ran_build/build
```
```c=
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.2x2-viavi_TM500.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/test.log 2>&1
```

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
## Appendix
