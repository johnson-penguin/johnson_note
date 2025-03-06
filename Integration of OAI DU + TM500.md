---
title: Integration of OAI DU + TM500

---

![](https://i.imgur.com/JORnn3y.png =150x)@NTUST
# Integration of OAI DU + TM500
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

**OAI DU (Dell R740) :**
- IP Address : 192.168.8.29
	- Username : oai72
	- Password : bmwlab
	- Root Password : bmwlab

:::

## topology
![image](https://hackmd.io/_uploads/r1d0AcADC.png)

- TM500 server

![image](https://hackmd.io/_uploads/Synl1gQKT.png)

## Current status
### operate status
| Item                                                           | Status             |
| -------------------------------------------------------------- | ------------------ |
| [PTP sync of TM500](#PTP-sync-in-TM500)                        |  |
| [Bring up TM500 using TMA](#Bring-up-TM500-using-TMA)          |  |
| [PTP sync of OSC DU](#PTP-sync-of-OSC-DU)                      |  |
| [Bring up [I] OSC DU + [F] L1app](#Bring-up-I-OSC-DU--F-L1app) |  |
| [Check RU status](#Check-RU-status)                            |  |
| [TM500 configure setting](#TM500-configure-setting)            |  |
| [UESIM do cell search](#UESIM-do-cell-search)                  |  |

### Msg status
| Item                            | Status |
| ------------------------------- |:------ |
| NG Setup                        | :heavy_check_mark:       |
| F1 Setup                        | :heavy_check_mark:    |
| DU Configuration Update         | :heavy_check_mark:    |
| RU get C/U Plane packet from DU | :heavy_check_mark:    |
| UE get mib and sib1 for DL sync | :heavy_check_mark:    |
| UE send PRACH for UL sync       | :heavy_check_mark:    |
| UE send RRC setup request       | :heavy_check_mark:    |
| RRC setup complete              | :heavy_check_mark:    |
| NAS registration                | :heavy_check_mark:    |
| Service request                 | :heavy_check_mark:    |
| Service response                | :heavy_check_mark:    |
| PDU session complete            | :heavy_check_mark:    |
| Data network connection         | :x:    |
| Performance                     | :x:    |
:::info
### status
- Still using BFP 9 bit with static compression.
- But due to RUSIM requirement, the U-plane DL/UL not have udCompHdr.
- Need to remove the udCompHdr in DL for static compression mode.
- two option for integration
	- Intel : compression mode with udCompHdr remove
	- VIAVI : uncompression mode in TM500
:::
## Integration Environment
### OAI
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
| OAI Branch | `fhidriver_E`                                      |
### TM500
**Hardware:**
|     Item     | Info                                                     |
|:------------:| -------------------------------------------------------- |
|     CPU      | Intel(R) Xeon(R) CPU E5-2695 v4 @ 2.10GHz (18 cores) x 2 |
|    Memory    | 132GB                                                    |
|     Disk     | 1T                                                       |
|     NIC      | MT27800                                                  |
| Server Model | Supermicro SSG-6028R-E1CR12L                             |


## Configurations

### OAI
- File location : ` /home/oai72/FH_7.2_dev/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.4x4-liteon.conf
`


| Parameters                  |                     value | config parameter                                                                                         |
| --------------------------- | -------------------------:| -------------------------------------------------------------------------------------------------------- |
| CC config                   |                       4x4 | [reference](https://hackmd.io/LmBA5ogYTOaXQE60jIbeuw#Other-env-compare)                                  |
| DL Point A frequency(ARFCN) |  3401.58MHz (626772)  | `dl_absoluteFrequencyPointA`            |
| DL center frequency     |  3450.72 MHz | Check calculation [here](#DL-center-frequency-calculation)      |
| SSB center frequency           |  3450.72 MHz | Check calculation [here](#SSB-frequency-calculation)                                                     |
| System Bandwidth            |                   100 MHz |                                                                                                          |
| SCS                         |                     30KHZ | `    dl_subcarrierSpacing                                      = 1; 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120` |
| PCI(Physical Cell ID)       |                         0 | `physCellId = 0; `                                                                                       |
| Duplex mode                 |                       TDD | `nFrameDuplexType=1 # 0 - FDD;# 1 - TDD`                                                                 |

#### DL center frequency calculation
- SCS 30KHZ, Bandwidth 100 MHz can have 273 PRBs.

![image](https://hackmd.io/_uploads/BJWoK-NY6.png)

- DL center frequency is **DL Point A frequency + (273PRBs/2)**
- 1 PRB has 12 subcarrier, 1 subcarrier is 30 KHZ=0.03 MHz.
- So **DL center frequency=3401.58+136.5x12x0.03=3450.72 MHz**
---

#### SSB center frequency calculation
- SSB frequency identifies the position of resource element RE=#0 (subcarrier #0) of resource block RB#10 of the SS block.

<iframe frameborder="0" style="width:100%;height:500px;" src="https://viewer.diagrams.net/?tags=%7B%7D&highlight=0000ff&edit=_blank&layers=1&nav=1&title=SSB_freq.drawio#Uhttps%3A%2F%2Fdrive.google.com%2Fuc%3Fid%3D1sEdNnniE0G9rWaid7DuCrtFqD9ar-1X3%26export%3Ddownload"></iframe>

:::spoiler 
- So **SSB frequency=3401.5+24(RB)x12(subcarrier)x0.015(SCS)+0+10(RB)x12(subcarrier)x0.03(SCS)=3525.9 MHz**
:::

---

#### System Bandwidth	
- subcarrierSpacing 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
    - dl_subcarrierSpacing = 1;
    - dl_carrierBandwidth  = 273;

Push back the system bandwidth = 100MHz based on the number of SCS and PRB
![image](https://hackmd.io/_uploads/BJtxBNrA6.png)

---

| Parameters               | value             | config parameter   |
| ------------------------ | ----------------- | ------------------ |
| vlan id                  | 5                 | `<c_plane_vlan_tag>5</c_plane_vlan_tag>``<u_plane_vlan_tag>5</u_plane_vlan_tag>`                                                                                                                                   |
| pdsch/pusch eaxcid       | 0, 1, 2, 3	       | automatically set by L1 based on CC config, it will start from 0                                                                                                                                                   |
| prach eaxcid             | 4, 5, 6, 7        | automatically set by L1 based on CC config, it will start after end of sch eaxcid                                                                                                                                  |
| dl/ul compression method | 8bitBFP, static   | `<xranCompMethod>1</xranCompMethod>`</br>`<xranCompHdrType>1</xranCompHdrType>`</br>`<xraniqWidth>9</xraniqWidth>`</br>`<xranPrachCompMethod>1</xranPrachCompMethod>`</br>`<xranPrachiqWidth>9</xranPrachiqWidth>` |
| DU MAC address           | 00:11:22:33:44:66 | create in `/home/oran/G_O-DU/phy/setupvf.sh`                                                                                                                                                                       |
| RU MAC address           | 10:70:fd:14:1c:10 | `<oRuRem0Mac0>10:70:fd:14:1c:10</oRuRem0Mac0>`                                                                                                                                                                     |
| MTU size                 | 1500              | `<MTU>9000</MTU>`                                                                                                                                                                                                  |
| T1a_min_cp_dl            | 285 μs            | `<T1a_min_cp_dl>258</T1a_min_cp_dl>`                                                                                                                                                                               |
| T1a_max_cp_dl            | 429 μs            | `<T1a_max_cp_dl>470</T1a_max_cp_dl>`                                                                                                                                                                               |
| T1a_min_cp_ul            | 285 μs            | `<T1a_min_cp_ul>285</T1a_min_cp_ul>`                                                                                                                                                                               |
| T1a_max_cp_ul            | 429 μs            | `<T1a_max_cp_ul>429</T1a_max_cp_ul>`                                                                                                                                                                               |
| T1a_min_up               | 96 μs             | `<T1a_min_up>50</T1a_min_up>       `                                                                                                                                                                               |
| T1a_max_up               | 196 μs            | `<T1a_max_up>196</T1a_max_up>      `                                                                                                                                                                               |
| Ta4_min                  | 110 μs              | `<Ta4_min>0</Ta4_min>              `                                                                                                                                                                               |
| Ta4_max                  | 180 μs             | `<Ta4_max>75</Ta4_max>             `                                                                                                                                                                               |
| Tadv_cp_dl               | 25 μs             | `<Tadv_cp_dl>25</Tadv_cp_dl>       `                                                                                                                                                                               |
| T2a_min_cp_dl            | 285 μs            | `<T2a_min_cp_dl>285</T2a_min_cp_dl>`                                                                                                                                                                               |
| T2a_max_cp_dl            | 479 μs            | `<T2a_max_cp_dl>479</T2a_max_cp_dl>`                                                                                                                                                                               |
| T2a_min_cp_ul            | 285 μs            | `<T2a_min_cp_ul>285</T2a_min_cp_ul>`                                                                                                                                                                               |
| T2a_max_cp_ul            | 429 μs            | `<T2a_max_cp_ul>429</T2a_max_cp_ul>`                                                                                                                                                                               |
| T2a_min_up               | 71 μs             | `<T2a_min_up>71</T2a_min_up>       `                                                                                                                                                                               |
| T2a_max_up               | 428 μs            | `<T2a_max_up>428</T2a_max_up>      `                                                                                                                                                                               |
| Ta3_min                  | 20 μs             | `<Ta3_min>20</Ta3_min>             `                                                                                                                                                                               |
| Ta3_max                  | 32 μs             | `<Ta3_max>32</Ta3_max>             `                                                                                                                                                                               |

### RUSIM
- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\oran_2_0_defaults.csv`

| Parameters    | RUSIM     | config parameter                                                                                                                                               |
| ------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Tadv_cp_dl    | 25000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/tcp-adv-dl,SR_UINT32_T`    |
| T2a_min_cp_dl | 258000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-dl,SR_UINT32_T` |
| T2a_max_cp_dl | 479000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-dl,SR_UINT32_T` |
| T2a_min_cp_ul | 285000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-ul,SR_UINT32_T` |
| T2a_max_cp_ul | 429000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-ul,SR_UINT32_T` |
| T2a_min_up    | 71000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-up,SR_UINT32_T`    |
| T2a_max_up    | 428000 ns | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-up,SR_UINT32_T`    |
| Ta3_min       | 20000 ns      | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-min,SR_UINT32_T`       |
| Ta3_max       | 32000 ns  | `o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-max,SR_UINT32_T`       |

- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\o-ran.cfg`

| Parameters     | RUSIM             | config parameter                        |
| -------------- | ----------------- |:--------------------------------------- |
| DU MAC address | 00:11:22:33:44:66 | `DefaultDuMacAddress=00:11:22:33:44:66` |

#### Timing window compare
| Parameters    | RUSIM  | OAI    |
| ------------- | ------ |:------ |
| Tadv_cp_dl    | 25 μs  | 25 μs  |
| T2a_min_cp_dl | 258 μs | 285 μs |
| T2a_max_cp_dl | 470 μs | 479 μs |
| T2a_min_cp_ul | 285 μs | 285 μs |
| T2a_max_cp_ul | 859 μs | 429 μs |
| T2a_min_up    | 50 μs  | 71 μs  |
| T2a_max_up    | 196 μs | 428 μs |
| Ta3_min       | 0 ns   | 20 μs  |
| Ta3_max       | 75 μs  | 32 μs  |

### UESIM
#### Configure Radio Contexts
| Parameters                           | value   |
| ------------------------------------ |:------- |
| Duplex                               | TDD     |
| Cell Id                              | 0       |
| Downlink carrier frequency (100 kHz) | 34507.2 |
| System bandwidth (MHz)               | 100     |
| AbsoluteFrequencyPointA (100 kHz)    | 34015.8 |
| AbsoluteFrequencySSB (100 kHz)       | 34507.2 |

<!-- #### Define MTS Cells
| Parameters             | value |
| ---------------------- | ----- |
| Cell Id                | 1     |
| DL Frequency (100 kHz) | 34002 | -->

### TM 500 RUSIM csv file generate
- VIAVI have a tool to generate csv file. Only need to provide yaml and oran.cfg and fill in the parameters.
:::spoiler `NR_1CC4x4_num1_Greigns.yaml`
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

| Parameters                                    |   value |
| --------------------------------------------- | -------:|
| VLAN ID                                       |       5 |
| CC configuration                              |     4x4 |
| Numerology                                    |       1 |
| Bandwidth                                     |     100 |
| Alpha value                                   |       0 |
| Beta value                                    |       0 |
| DL Carrier Frequency                          |       0 |
| UL Carrier Frequency                          |       0 |
| DL compression                                | 9bitBFP |
| DL (PDSCH) eaxcid list                        | 0,1,2,3 |
| DL mixed numerology?                          |      no |
| UL compression                                | 9bitBFP |
| UL (PUSCH) eaxcid list                        | 0,1,2,3 |
| Is PUSCH and RACH traffic on separate eaxcid? |     yes |
| Additional RACH eaxcid list                   | 4,5,6,7 |

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

