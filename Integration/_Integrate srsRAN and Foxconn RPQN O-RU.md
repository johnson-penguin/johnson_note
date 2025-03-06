---
title: ' Integrate srsRAN and Foxconn RPQN O-RU'

---

:::success
Reference:
[srsRAN Projec :　O-RAN 7.2 RU Guide](https://docs.srsran.com/projects/project/en/latest/tutorials/source/oranRU/source/index.html#overview)
[Unable to detect SIBs using Foxconn RPQN 7801E RU](https://github.com/srsran/srsRAN_Project/issues/192)
Reference From Rico
[Foxconn RU Setup guide](https://hackmd.io/ehotH1QaRzmgqw1YArSmUQ?view)
[[RP0-401] RPQN7801_L1_Network Parameters Setting Guide_v1.1.pdf](https://drive.google.com/file/d/1WWWHae0YzOwXlWsMPd-3G8F7TvFdngul/view)
[Foxconn RPQN O-RU Installation and Operation Guide.pdf](https://drive.google.com/file/d/1bqYEiWrKPLBAebWEsoQoYnRLrAz0-7w_/view)
From Summer
[OAI 7.2 split(branch : fhidriver_E) & open FH(branch : e release) installation](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2Fs2XCcxOjTfiZyB0vS2x1Xw%3Fview#3-System-Tools-and-Dependency)

:::
# Foxconn RU with srsRAN Setup guide
---
[TOC]

---
# Topology
![image](https://hackmd.io/_uploads/Sk3Uc73dT.png)



| RU Item | State |
| -------- | -------- |
| Setting RU MAC_Address               |   :heavy_check_mark:	  |
| Setting DU MAC_Address               |   :heavy_check_mark:	  |
| C/U Plane vlane               |   :heavy_check_mark:	  |



| DU (srsRAN) Item | State |
| -------- | -------- |
| Install srsRAN                       |   :heavy_check_mark:	  |
| Configure DU MAC_address in RU   |   :heavy_check_mark:	  |
| Configure address of AMF         |   :heavy_check_mark:	  |
| Configure address of DU & RU     |   :heavy_check_mark:	  |
| NIC configuration                    |   :heavy_check_mark:	  |
| PTP configuration                    |   :heavy_check_mark:     |
| Verify DU connects to the RU successfully  | :heavy_check_mark:|
| DU send C-plane / U-plane to RU            | :heavy_check_mark:|
| RU send C-plane / U-plane to DU                    |        |
| NG Setup	                                |        |
| F1 Setup	                                |        |
| DU Configuration Update	  	            |        |
| RRC Setup               |  |
| Security Mode           | |
| UE Context              |  |
| RRC Reconfiguraiton     |  |
| Registration Complete   |  |
| PDU Session             |  |
# Some useful Tool
## Check the network card interface
- Confirm the device number from the interface form we use
```c=
sudo lshw -c network -businfo
```
![image](https://hackmd.io/_uploads/ByBfsghP6.png)

- Confirm the current usage and MTU usage based on the device number 
```c=
ip link show
```
![image](https://hackmd.io/_uploads/H1w4jl2wT.png)
## Socket Statistics
The ss command is a tool used to dump socket statistics and displays information in similar fashion (although simpler and faster) to netstat. The ss command can also display even more TCP and state information than most other tools.
```c=
sudo ss -plunt
```
**Be in common use**
- -p: Display detailed information about the process associated with each socket.
- -l: Show only listening sockets.
- -u: Show only sockets using the UDP protocol.
- -n: Display IP addresses and port numbers in numeric form, without reverse DNS resolution.
- -t: Show only sockets using the TCP protocol.


## Screen list
- To list all of the screen sessions for a user, run the following command as that user:
```c=
sl

#or

screen -ls 
```
![image](https://hackmd.io/_uploads/BJJlxe4_p.png)

![image](https://hackmd.io/_uploads/H12llxNuT.png)

## Tcpdump
```c=
sudo tcpdump -i ens1f1 -w ~/srsRAN_Project/log/pcap0104.pcap -c 20000 
```

add number after ==-c== can limit the packet number
## Confirm ptp synchronization status
```c=
systemctl start phc2sys.service
```
```c=
systemctl status ptp4l.service
```

![image](https://hackmd.io/_uploads/BJMLPbKu6.png)


# Configuration Files
## srsRAN 
- File : `gnb_ru_rpqn4800e_tdd_n78_20mhz.yml`
- Path : `~/srsRAN_Project/build/apps/gnb`
:::spoiler
```c=
# This example configuration outlines how to configure the srsRAN Project CU/DU to use an O-RU and split 7.2. This is specifically for use
# with the Foxconn RPQN O-RU. This config will create a single TDD cell transmitting in band 3, with 20 MHz bandwidth and 30 kHz sub-carrier-spacing.
# The parameters used to configure the RU are found in the `ru_ofh` sub-section. This configuration makes used of the OFH Lib from SRS to enable split 7.2.


amf:
  addr: 127.0.0.5                                                 # The address or hostname of the AMF.
  bind_addr: 127.0.0.6                                            # A local IP that the gNB binds to for traffic from the AMF.

ru_ofh:
  ru_bandwidth_MHz: 100                                           # RU instantaneous bandwidth.
  t1a_max_cp_dl: 500 ##470                                             # Maximum T1a on Control-Plane for Downlink in microseconds.
  t1a_min_cp_dl: 250 ##258                                             # Minimum T1a on Control-Plane for Downlink in microseconds.
  t1a_max_cp_ul: 465 ##429                                             # Maximum T1a on Control-Plane for Uplink in microseconds.
  t1a_min_cp_ul: 250 ##285                                             # Minimum T1a on Control-Plane for Uplink in microseconds.
  t1a_max_up: 250    ##196                                             # Maximum T1a on User-Plane in microseconds.
  t1a_min_up: 80     ##50                                              # Minimum T1a on User-Plane in microseconds.
  is_prach_cp_enabled: true                                       # Configures if Control-Plane messages should be used to receive PRACH messages.
  #is_dl_broadcast_enabled: true #################
  ignore_ecpri_payload_size: true                                 # Configures if eCPRI payload size field should be ignored by the eCPRI packet decoder.
  compr_method_ul: bfp                                            # Uplink compression method.
  compr_bitwidth_ul: 9                                            # Uplink IQ samples bitwidth after compression.
  compr_method_dl: bfp                                            # Downlink compression method.
  compr_bitwidth_dl: 9                                            # Downlink IQ samples bitwidth after compression.
  compr_method_prach: none #bfp                                         # PRACH compression method.
  compr_bitwidth_prach: 9                                         # PRACH IQ samples bitwidth after compression.
  enable_ul_static_compr_hdr: false                               # Configures if the compression header is present for uplink User-Plane messages (false) or not present (true).
  enable_dl_static_compr_hdr: false                               # Configures if the compression header is present for downlink User-Plane messages (false) or not present (true).
  iq_scaling: 0.8 #1.0                                                 # IQ samples scaling factor applied before compression, should be a positive value smaller than 1.
  cells:
    - network_interface: ens1f1                                 # Ethernet interface name used to communicate with the RU.
      ru_mac_addr: 6c:ad:ad:00:08:6c                              # RU MAC address.
      du_mac_addr: 00:11:22:33:44:66                              # DU MAC address.
      vlan_tag: 3                                                 # VLAN tag value.
      prach_port_id: [4, 5, 6, 7]                                 # PRACH eAxC port values.
      dl_port_id: [0, 1, 2, 3] #[1]                                             # Downlink eAxC port values.
      ul_port_id: [0, 1, 2, 3]                                    # Uplink eAxC port values.

cell_cfg:
  dl_arfcn: 649980 #637336                                                # ARFCN of the downlink carrier (center frequency).
  band: 78                                                        # The NR band.
  channel_bandwidth_MHz: 100 #20                                       # Bandwith in MHz. Number of PRBs will be automatically derived.
  common_scs: 30                                                  # Subcarrier spacing in kHz used for data.
  plmn: "00101"                                                   # PLMN broadcasted by the gNB.
  tac: 1                                                          # Tracking area code (needs to match the core configuration).
  pci: 12                                                         # Physical cell ID.
  nof_antennas_dl: 4 #1                                              # Number of transmission antennas.
  nof_antennas_ul: 4 #4                                              # Number of reception antennas.
  prach:
    prach_config_index: 159                                       # PRACH configuration index.
    prach_root_sequence_index: 1                                  # PRACH root sequence index.
    zero_correlation_zone: 0                                      # Zero correlation zone.
    prach_frequency_start: 12                                     # Offset in PRBs of lowest PRACH transmission occasion in frequency domain respective to PRB 0.

log:
  filename: /tmp/gnb.log                                          # Path of the log file.
  all_level: warning                                              # Logging level applied to all layers.

pcap:
  mac_enable: false                                               # Set to true to enable MAC-layer PCAPs.
  mac_filename: /tmp/gnb_mac.pcap                                 # Path where the MAC PCAP is stored.
  ngap_enable: false                                              # Set to true to enable NGAP PCAPs.
  ngap_filename: /tmp/gnb_ngap.pcap                               # Path where the NGAP PCAP is stored.
```
:::


- 更動細節
:::success
```c=
##5GS
vlan_tag: 3              # VLAN tag value.
amf:
  addr: 127.0.0.5  
  bind_addr: 127.0.0.6  


- network_interface: ens1f1                        
      ru_mac_addr: 6c:ad:ad:00:08:6c              
      du_mac_addr: 00:11:22:33:44:66
``` 
:::
## RPQN7801E(RU) 
- File : `RRHconfig_xran.xml`
- Path : `sdcard/RRHconfig_xran.xml `
:::spoiler
```c=
<!--                          -->
<!--            Common        -->
<!--                          -->
<!-- RRH_DST_MAC_ADDR: Destination MAC address, fill with 6 bytes and separate each others by colon -->
RRH_DST_MAC_ADDR = 00:11:22:33:44:66
<!-- RRH_SRC_MAC_ADDR: Source MAC address, fill with 6 bytes and separate each others by colon -->
RRH_SRC_MAC_ADDR = 6c:ad:ad:00:08:6c  
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
RRH_C_PLANE_VLAN_TAG = 0x0002
<!-- RRH_U_PLANE_VLAN_TAG: U-plane V-LAN tag express by hex number. Change to 1 for old setup -->
RRH_U_PLANE_VLAN_TAG = 0x0002
<!-- RRH_SLOT_TICKS_IN_SEC: Number of slot tick in a second (2000 slots for u=1) -->
RRH_SLOT_TICKS_IN_SEC = 2000
<!-- RRH_SLOT_PERIOD_IN_SAMPLE: Slot period in 122.88MHz (61440 for u=1) -->
RRH_SLOT_PERIOD_IN_SAMPLE = 61440
<!-- RRH_LO_FREQUENCY_KHZ: Tx and Rx PLL LO Frequency in kHz(internal or external LO) -->
RRH_LO_FREQUENCY_KHZ = 3749700, 0
<!-- RRH_TX_POWER: Target Tx power (Default=24dBm. Range:24dBm ~ 4dBm.  It can be applied only when Tx power of a RRH is calibrated) -->
RRH_TX_POWER = 24, 24
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
<!-- Bit[0] and Bit[1] of 1st word: 3=DPD ON, 0=DPD off           -->
<!-- Bit[0] of 2st word: 1=CLGC ON, 0=CLGC off                    -->
<!-- Bit[0] of 4th word: 1=for N77, 0=else                        -->
RRH_RF_GENERAL_CTRL = 0x3, 0x1, 0x0, 0x0
<!--                           -->
<!--            PTPV2 Related  -->
<!--                           -->
<!-- RRH_PTPV2_GRAND_MASTER_MODE: 0: Unicast over 1G, 1:Multicast over 1G; 2: Unicast over 10G; 3: Multicast over 10G -->
RRH_PTPV2_GRAND_MASTER_MODE = 1
<!-- RRH_PTPV2_JITTER_LEVEL: The estimated jitter of PTP time packets. 0:direct connection to GM/BC, 1:light, 2:medium, 3:heavy -->
RRH_PTPV2_JITTER_LEVEL = 0
<!-- RRH_PTPV2_VLAN_ID: VLAN ID of PTPv2.  0/1: No VLAN of PTPv2; [2~4092]: valid VLAN of PTPv2; >4092: Invalid and no VLAN will be applied -->
RRH_PTPV2_VLAN_ID = 0
<!-- RRH_PTPV2_IP_MODE: 4: IPv4 (default); 6: IPv6.  Note: this configuration must be defined before RRH_PTPV2_GRAND_MASTER_IP -->
RRH_PTPV2_IP_MODE = 4
<!-- RRH_PTPV2_GRAND_MASTER_IP: IP address of grand-master -->
RRH_PTPV2_GRAND_MASTER_IP = 192.168.1.90
<!-- RRH_PTPV2_SUB_DOMAIN_NUM: The sub-domain number -->
RRH_PTPV2_SUB_DOMAIN_NUM = 24
<!-- RRH_PTPV2_ACCEPTED_CLOCK_CLASS: The acceptable clockClass threshold [6~248] -->
RRH_PTPV2_ACCEPTED_CLOCK_CLASS = 135
<!-- RRH_TRACE_PERIOD: The period of trace log. It can be applied only when the start-trace-logs of M-plane is enabled -->
RRH_TRACE_PERIOD = 10
```
:::

- 更動細節
:::success
```c=
RRH_DST_MAC_ADDR = 00:11:22:33:44:66
RRH_SRC_MAC_ADDR = 6c:ad:ad:00:08:6c  

RRH_C_PLANE_VLAN_TAG = 0x0003
RRH_U_PLANE_VLAN_TAG = 0x0003
    
    
#Additional comments
<!-- RRH_PTPV2_ACCEPTED_CLOCK_CLASS: The acceptable clockClass threshold [6~248] -->
RRH_PTPV2_ACCEPTED_CLOCK_CLASS = 135
<!-- RRH_TRACE_PERIOD: The period of trace log. It can be applied only when the start-trace-logs of M-plane is enabled -->
RRH_TRACE_PERIOD = 10
``` 
:::
# 1. For install srsRAN Project
## Step1.1 Build Tools and Dependencies 
The srsRAN Project uses: 
- CMake 
- C++14. 

[srsRAN Project](https://docs.srsran.com/projects/project/en/latest/index.html) recommend the following build tools:
- cmake
- gcc (v9.4.0 or later) OR Clang (v10.0.0 or later)
```c=
sudo apt-get install cmake make gcc g++ pkg-config libfftw3-dev libmbedtls-dev libsctp-dev libyaml-cpp-dev libgtest-dev
```



## Step1.2 Clone way to install srsRAN Project
:::success
- Clone the srsRAN Project repository:
```c=
git clone https://github.com/srsRAN/srsRAN_Project.git
```

- Build the code-base
```c=
cd srsRAN_Project
mkdir build
cd build
cmake ../
make -j $(nproc)
make test -j $(nproc)
```
:::
## ===== IF U do first way you can skip this one =====
## Step1.3 Packages way to install srsRAN Project
:::info
- Ubuntu users can download the srsRAN Project packages using the following commands:
```c=
sudo add-apt-repository ppa:softwareradiosystems/srsran-project
sudo apt-get update
sudo apt-get install srsran-project -y
```
- Build the code-base
```c=
sudo gnb -c <config file>
```
Example configuration files can be found in the `configs/` folder in the srsRAN Project codebase.
:::


# 2. For setting srsRAN
## Step2.1 Configuration srsRAN
- Path :`configs/gnb_ru_rpqn4800e_tdd_n78_20mhz.yml`

[srsRAN recommended profiles for Foxconn RU](https://github.com/srsran/srsRAN_Project/blob/main/configs/gnb_ru_rpqn4800e_tdd_n78_20mhz.yml)

:::info
**Before we use it we need to adjust the settings to our RU and AMF**

- Default
```c=
amf:
  addr: 127.0.1.100        # The address or hostname of the AMF.
```
```c=
cells:
    - network_interface: enp1s0f1      # Ethernet interface name used to communicate with the RU.
      ru_mac_addr: 6c:ad:ad:00:08:c4   # RU MAC address.
      du_mac_addr: 80:61:5f:0d:df:ab   # DU MAC address.
```

- After
```c=
amf:
  addr: 127.0.0.5        # The address or hostname of the AMF.
```
```c=
cells:
    - network_interface: ens1f1      # Ethernet interface name used to communicate with the RU.
      ru_mac_addr: 6c:ad:ad:00:08:6c           # RU MAC address.
      du_mac_addr: 00:11:22:33:44:66           # DU MAC address.
```

:::

# 3. For setting Foxconn_RU(RPQN7801E)
## Step3.1 Download and open MobaXterm
## Step3.2 Turn on the power of Foxconn_RU
## Step3.3 Click Session to set the Serial port and Speed

Serial port: COM9 (Standard COM Port)
Speed (bps): 115200

## Step3.4 Setting Foxconn RU
- Path :`sdcard/RRHconfig_xran.xml`
:::success
```c=
RRH_DST_MAC_ADDR = 00:11:22:33:44:66
RRH_SRC_MAC_ADDR = 6c:ad:ad:00:08:6c  

RRH_C_PLANE_VLAN_TAG = 0x0003
RRH_U_PLANE_VLAN_TAG = 0x0003
    

#Additional comments
<!-- RRH_PTPV2_ACCEPTED_CLOCK_CLASS: The acceptable clockClass threshold [6~248] -->
RRH_PTPV2_ACCEPTED_CLOCK_CLASS = 135
<!-- RRH_TRACE_PERIOD: The period of trace log. It can be applied only when the start-trace-logs of M-plane is enabled -->
RRH_TRACE_PERIOD = 10
```
:::
## Step3.5 Configuration RU's own MAC_address 
Since the MAC_Address of the RU will change every time the RU is restarted.

To avoid having to change srsRAN every time, be sure to manually adjust MAC_Address after turning it on.

![image](https://hackmd.io/_uploads/S1HYUehPa.png)

:::success
- Turn off the network card device
>[root@localhost ~]# ifconfig ==eth0== down
```c=
ifconfig eth0 down
```
![image](https://hackmd.io/_uploads/Hymf4lhDp.png)

- Modify MAC address
>[root@localhost ~]# ifconfig eth0 hw ether aa:bb:cc:dd:ee:ff

```c=
ifconfig eth0 hw ether  6c:ad:ad:00:08:6c
```
![image](https://hackmd.io/_uploads/r1EFVehw6.png)

- Turn on the network card device
>[root@localhost ~]# ifconfig ==eth0== up
```c=
ifconfig eth0 up
```
![image](https://hackmd.io/_uploads/SkGKHg2vp.png)

- Confirm whether the change is successful
```c=
ifconfig
```
![image](https://hackmd.io/_uploads/Byog8l3wa.png)
:::

# 4. For 7.2 RU setting
## Step4.1 NIC configuration
```c=
ifconfig ens1f1 mtu 9600 up
```
![image](https://hackmd.io/_uploads/ryZ4-hf_a.png)

![image](https://hackmd.io/_uploads/HJ2H-2Gda.png)




## Initializing the Network
Try to use the configuration file provided by this [Reference](https://github.com/srsran/srsRAN_Project/issues/192) 


:::spoiler RRHconfig_xran.xml (RU configuration)
```c=
<!--                          -->
<!--            Common        -->
<!--                          -->
<!-- RRH_DST_MAC_ADDR: Destination MAC address, fill with 6 bytes and separate each others by colon -->
RRH_DST_MAC_ADDR = 00:11:22:33:44:55
<!-- RRH_SRC_MAC_ADDR: Source MAC address, fill with 6 bytes and separate each others by colon -->
RRH_SRC_MAC_ADDR = 55:44:33:22:11:00
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
RRH_C_PLANE_VLAN_TAG = 0x0002
<!-- RRH_U_PLANE_VLAN_TAG: U-plane V-LAN tag express by hex number. Change to 1 for old setup -->
RRH_U_PLANE_VLAN_TAG = 0x0002
<!-- RRH_SLOT_TICKS_IN_SEC: Number of slot tick in a second (2000 slots for u=1) -->
RRH_SLOT_TICKS_IN_SEC = 2000
<!-- RRH_SLOT_PERIOD_IN_SAMPLE: Slot period in 122.88MHz (61440 for u=1) -->
RRH_SLOT_PERIOD_IN_SAMPLE = 61440
<!-- RRH_LO_FREQUENCY_KHZ: Tx and Rx PLL LO Frequency in kHz(internal or external LO) -->
RRH_LO_FREQUENCY_KHZ = 3749700, 0
<!-- RRH_TX_POWER: Target Tx power (Default=24dBm. Range:24dBm ~ 4dBm.  It can be applied only when Tx power of a RRH is calibrated) -->
RRH_TX_POWER = 24, 24
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
<!-- Bit[0] and Bit[1] of 1st word: 3=DPD ON, 0=DPD off           -->
<!-- Bit[0] of 2st word: 1=CLGC ON, 0=CLGC off                    -->
<!-- Bit[0] of 4th word: 1=for N77, 0=else                        -->
RRH_RF_GENERAL_CTRL = 0x3, 0x1, 0x0, 0x0
<!--                           -->
<!--            PTPV2 Related  -->
<!--                           -->
<!-- RRH_PTPV2_GRAND_MASTER_MODE: 0: Unicast over 1G, 1:Multicast over 1G; 2: Unicast over 10G; 3: Multicast over 10G -->
RRH_PTPV2_GRAND_MASTER_MODE = 1
<!-- RRH_PTPV2_JITTER_LEVEL: The estimated jitter of PTP time packets. 0:direct connection to GM/BC, 1:light, 2:medium, 3:heavy -->
RRH_PTPV2_JITTER_LEVEL = 0
<!-- RRH_PTPV2_VLAN_ID: VLAN ID of PTPv2.  0/1: No VLAN of PTPv2; [2~4092]: valid VLAN of PTPv2; >4092: Invalid and no VLAN will be applied -->
RRH_PTPV2_VLAN_ID = 0
<!-- RRH_PTPV2_IP_MODE: 4: IPv4 (default); 6: IPv6.  Note: this configuration must be defined before RRH_PTPV2_GRAND_MASTER_IP -->
RRH_PTPV2_IP_MODE = 4
<!-- RRH_PTPV2_GRAND_MASTER_IP: IP address of grand-master -->
RRH_PTPV2_GRAND_MASTER_IP = 192.168.1.90
<!-- RRH_PTPV2_SUB_DOMAIN_NUM: The sub-domain number -->
RRH_PTPV2_SUB_DOMAIN_NUM = 24
<!-- RRH_PTPV2_ACCEPTED_CLOCK_CLASS: The acceptable clockClass threshold [6~248] -->
RRH_PTPV2_ACCEPTED_CLOCK_CLASS = 135
<!-- RRH_TRACE_PERIOD: The period of trace log. It can be applied only when the start-trace-logs of M-plane is enabled -->
RRH_TRACE_PERIOD = 10
```
:::

:::spoiler default.cfg (linuxptp configuration)
```c=
[global]
#
# Default Data Set
#
twoStepFlag             1
clientOnly              0
socket_priority         0
priority1               128
priority2               128
domainNumber            24
#utc_offset             37
clockClass              248
clockAccuracy           0xFE
offsetScaledLogVariance 0xFFFF
free_running            0
freq_est_interval       1
dscp_event              0
dscp_general            0
dataset_comparison      ieee1588
G.8275.defaultDS.localPriority  128
maxStepsRemoved         255
#
# Port Data Set
#
logAnnounceInterval     1
logSyncInterval         0
operLogSyncInterval     0
logMinDelayReqInterval  0
logMinPdelayReqInterval 0
operLogPdelayReqInterval 0
announceReceiptTimeout  3
syncReceiptTimeout      0
delay_response_timeout  0
delayAsymmetry          0
fault_reset_interval    4
neighborPropDelayThresh 20000000
serverOnly              0
G.8275.portDS.localPriority     128
asCapable               auto
BMCA                    ptp
inhibit_announce        0
inhibit_delay_req       0
ignore_source_id        0
#power_profile.2011.grandmasterTimeInaccuracy   0xFFFFFFFF
power_profile.2011.networkTimeInaccuracy        0
#power_profile.2017.totalTimeInaccuracy         0xFFFFFFFF
power_profile.grandmasterID                     0
power_profile.version                           none
#
# Run time options
#
assume_two_step         0
logging_level           6
path_trace_enabled      0
follow_up_info          0
hybrid_e2e              0
inhibit_multicast_service       0
net_sync_monitor        0
tc_spanning_tree        0
tx_timestamp_timeout    10
unicast_listen          0
unicast_master_table    0
unicast_req_duration    3600
use_syslog              1
verbose                 0
summary_interval        0
kernel_leap             1
check_fup_sync          0
clock_class_threshold   248
#
# Servo Options
#
pi_proportional_const   0.0
pi_integral_const       0.0
pi_proportional_scale   0.0
pi_proportional_exponent        -0.3
pi_proportional_norm_max        0.7
pi_integral_scale       0.0
pi_integral_exponent    0.4
pi_integral_norm_max    0.3
step_threshold          0.0
first_step_threshold    0.00002
max_frequency           900000000
clock_servo             pi
sanity_freq_limit       200000000
refclock_sock_address   /var/run/refclock.ptp.sock
ntpshm_segment          0
msg_interval_request    0
servo_num_offset_values 10
servo_offset_threshold  0
write_phase_mode        0
#
# Transport options
#
transportSpecific       0x0
ptp_dst_mac             01:2A:21:00:00:00
p2p_dst_mac             01:60:C3:00:00:0F
udp_ttl                 1
udp6_scope              0x0E
uds_address             /var/run/ptp4l
uds_file_mode           0660
uds_ro_address          /var/run/ptp4lro
uds_ro_file_mode                0666
#
# Default interface options
#
clock_type              OC
network_transport       UDPv4
delay_mechanism         E2E
time_stamping           hardware
tsproc_mode             filter
delay_filter            moving_median
delay_filter_length     10
egressLatency           0
ingressLatency          0
boundary_clock_jbod     0
phc_index               -1
#
# Clock description
#
productDescription      ;;
revisionData            ;;
manufacturerIdentity    00:00:00
userDescription         ;
timeSource              0xA0
```
:::

:::spoiler gnb_ru_rpqn4800e_tdd_n78_20mhz.yml (srsRAN configuration).
```c=
# This example configuration outlines how to configure the srsRAN Project CU/DU to use an O-RU and split 7.2. This is specifically for use
# with the Foxconn RPQN O-RU. This config will create a single TDD cell transmitting in band 3, with 20 MHz bandwidth and 30 kHz sub-carrier-spacing.
# The parameters used to configure the RU are found in the `ru_ofh` sub-section. This configuration makes used of the OFH Lib from SRS to enable split 7.2.


amf:
  addr: 10.20.1.20                                                # The address or hostname of the AMF.
  bind_addr: 10.20.1.168                                          # A local IP that the gNB binds to for traffic from the AMF.

ru_ofh:
  ru_bandwidth_MHz: 100                                           # RU instantaneous bandwidth.
  t1a_max_cp_dl: 500 ##470                                             # Maximum T1a on Control-Plane for Downlink in microseconds.
  t1a_min_cp_dl: 250 ##258                                             # Minimum T1a on Control-Plane for Downlink in microseconds.
  t1a_max_cp_ul: 465 ##429                                             # Maximum T1a on Control-Plane for Uplink in microseconds.
  t1a_min_cp_ul: 250 ##285                                             # Minimum T1a on Control-Plane for Uplink in microseconds.
  t1a_max_up: 250    ##196                                             # Maximum T1a on User-Plane in microseconds.
  t1a_min_up: 80     ##50                                              # Minimum T1a on User-Plane in microseconds.
  is_prach_cp_enabled: true                                       # Configures if Control-Plane messages should be used to receive PRACH messages.
  #is_dl_broadcast_enabled: true #################
  ignore_ecpri_payload_size: true                                 # Configures if eCPRI payload size field should be ignored by the eCPRI packet decoder.
  compr_method_ul: bfp                                            # Uplink compression method.
  compr_bitwidth_ul: 9                                            # Uplink IQ samples bitwidth after compression.
  compr_method_dl: bfp                                            # Downlink compression method.
  compr_bitwidth_dl: 9                                            # Downlink IQ samples bitwidth after compression.
  compr_method_prach: none #bfp                                         # PRACH compression method.
  compr_bitwidth_prach: 9                                         # PRACH IQ samples bitwidth after compression.
  enable_ul_static_compr_hdr: false                               # Configures if the compression header is present for uplink User-Plane messages (false) or not present (true).
  enable_dl_static_compr_hdr: false                               # Configures if the compression header is present for downlink User-Plane messages (false) or not present (true).
  iq_scaling: 0.1 #1.0                                                 # IQ samples scaling factor applied before compression, should be a positive value smaller than 1.
  cells:
    - network_interface: enp1s0f1                                 # Ethernet interface name used to communicate with the RU.
      ru_mac_addr: 55:44:33:22:11:00                              # RU MAC address.
      du_mac_addr: 00:11:22:33:44:55                              # DU MAC address.
      vlan_tag: 2                                                 # VLAN tag value.
      prach_port_id: [4, 5, 6, 7]                                 # PRACH eAxC port values.
      dl_port_id: [0, 1, 2, 3] #[1]                                             # Downlink eAxC port values.
      ul_port_id: [0, 1, 2, 3]                                    # Uplink eAxC port values.

cell_cfg:
  dl_arfcn: 649980 #637336                                                # ARFCN of the downlink carrier (center frequency).
  band: 78                                                        # The NR band.
  channel_bandwidth_MHz: 100 #20                                       # Bandwith in MHz. Number of PRBs will be automatically derived.
  common_scs: 30                                                  # Subcarrier spacing in kHz used for data.
  plmn: "00101"                                                   # PLMN broadcasted by the gNB.
  tac: 1                                                          # Tracking area code (needs to match the core configuration).
  pci: 12                                                         # Physical cell ID.
  nof_antennas_dl: 4 #1                                              # Number of transmission antennas.
  nof_antennas_ul: 4 #4                                              # Number of reception antennas.
  prach:
    prach_config_index: 159                                       # PRACH configuration index.
    prach_root_sequence_index: 1                                  # PRACH root sequence index.
    zero_correlation_zone: 0                                      # Zero correlation zone.
    prach_frequency_start: 12                                     # Offset in PRBs of lowest PRACH transmission occasion in frequency domain respective to PRB 0.

log:
  filename: /tmp/gnb.log                                          # Path of the log file.
  all_level: warning                                              # Logging level applied to all layers.

pcap:
  mac_enable: false                                               # Set to true to enable MAC-layer PCAPs.
  mac_filename: /tmp/gnb_mac.pcap                                 # Path where the MAC PCAP is stored.
  ngap_enable: false                                              # Set to true to enable NGAP PCAPs.
  ngap_filename: /tmp/gnb_ngap.pcap                               # Path where the NGAP PCAP is stored.
```
:::
## Step4.2 RU
To bring up the RU simply boot it and ensure it is running correctly before attempting to connect the DU. Check for RU synchronization and that the PTP process is running correctly.

These exact steps will vary depend on the RU being used.


## Step4.3 CU/DU
Before running the CU/DU, make sure you have used the commands outlined in the configuration section above to confirm the PTP sync between the DU and the fronthaul switch.

We can now run the CU/DU. First, navigate to srsRAN_Project/build/apps/gnb, and then run the gNB with the following command:
```c=
sudo ./gnb -c <RU_CONFIG>
```
```c=
sudo ./gnb -c gnb_ru_rpqn4800e_tdd_n78_20mhz.yml
```
Where <RU_CONFIG> is the configuration file associated with the RU being used. For my part I use ==gnb_ru_rpqn4800e_tdd_n78_20mhz.yml==

If the DU connects to the RU successfully, you will see the following output or similar:

- **Provide from website**
![image](https://hackmd.io/_uploads/HJdwNzLFa.png)

```c=
--== srsRAN gNB (commit ) ==--

Connecting to AMF on 10.53.1.2:38412
Initializing Open Fronthaul Interface with ul_comp=[BFP,9], dl_comp=[BFP,9], prach_cp_enabled=false, downlink_broadcast=true.
Operating a 20MHz cell over a RU with instantaneous bandwidth of 100MHz.
Warning: Configured PRACH occasion collides with PUCCH RBs ([0..1) intersects [0..3)). Some interference between PUCCH and PRACH is expected.
Warning: Configured PRACH occasion collides with PUCCH RBs ([0..1) intersects [0..3)). Some interference between PUCCH and PRACH is expected.
Cell pci=1, bw=20 MHz, dl_arfcn=634548 (n78), dl_freq=3518.22 MHz, dl_ssb_arfcn=634464, ul_freq=3518.22 MHz

==== gNodeB started ===
Type <t> to view trace
```
---

- **My result**
![image](https://hackmd.io/_uploads/ByYrYSFOT.png)

```c=
--== srsRAN gNB (commit dcd905cc6) ==--

Connecting to AMF on 127.0.0.5:38412
Initializing the Open FrontHaul Interface for sector#0: ul_compr=[BFP,9], dl_compr=[BFP,9], prach_compr=[none,9] prach_cp_enabled=true, downlink_broadcast=false.
Cell pci=12, bw=100 MHz, dl_arfcn=649980 (n78), dl_freq=3749.7 MHz, dl_ssb_arfcn=647232, ul_freq=3749.7 MHz

==== gNodeB started ===
Type <t> to view trace
```
## Step4.4 DU send C/U plane to RU
```c=
sudo tcpdump -i ens1f1 -w ~/srsRAN_Project/log/pcap0104.pcap -c 20000 
```
![image](https://hackmd.io/_uploads/rk6PbSztp.png)




# Examination
## If your server reboot or RU reboot, here's what you need to do
## Step 1.1 Open 5GS (DU side)
```c=
sudo ./createtun.sh 
# not persistent after rebooting
```
```c=
./start_open5gs.sh
```
## Step 1.2 NIC configuration (DU side)
```c=
ip link show
sudo ifconfig ens1f1 mtu 9600 up
ip link show
```
## ===if your RU didn't reboot you can skip this one===
## Step 1.3 RU port setting (RU side)
```c=
./set_port. sh
```

## ==========================================
## Choose your startup Scenario A or B according to different scenarios
## Use 60w Transformer 
**PS: It has pink note and write rpqn7801E**
:::danger
Using the wrong transformer ex: TY's Metanoia RU(72w) or Liteon RU(150w) may ==**cause burnout**==
:::
## Scenario (A) Bring up from power off
### Step A-1. Stop O-DU
### Step A-2. Power on O-RU
### Step A-3. RU terminal 
```c=
./init_rrh_config_enable_cuplane
```
### Step A-4. Wait PTP lock at O-RU

![image](https://hackmd.io/_uploads/BJ7nOFqua.png)

### Step A-6. Start srsRAN
```c=
cd ~/srsRAN_Project/build/apps/gnb
```
```c=
sudo ./gnb -c gnb_ru_RPQN7801E_tdd_n78_20mhz.yml 
```

## Scenario (B) Restart O-RU
### Step B-1. Stop O-DU
### Step B-2. Power down O-RU
### Step B-3. Power up O-RU
### Step B-4. RU terminal
```c=
./init_rrh_config_enable_cuplane
```
### Step B-5. wait PTP lock at O-RU
### Step B-6. start srsRAN





# Issue
## Issue - fix (1)
**srsGNB ERROR: CU-CP failed to connect to AMF** 
![image](https://hackmd.io/_uploads/B1jD0oMOa.png)

根據[相關的文章](https://github.com/srsran/srsRAN_Project/discussions/376)，The PRACH detector will not meet the performance requirements with the configuration {Format 0, ZCZ 0, SCS 1.25kHz, Rx ports 1}可以被忽略的。

- 嘗試修改內部ip_addr(無效)
```c=
struct amf_appconfig {
  std::string ip_addr                = "127.0.0.5";
  uint16_t    port                   = 38412;
  std::string bind_addr              = "127.0.0.1";
  std::string n2_bind_addr           = "auto";
  std::string n3_bind_addr           = "auto";
  int         sctp_rto_initial       = 120;
  int         sctp_rto_min           = 120;
  int         sctp_rto_max           = 500;
  int         sctp_init_max_attempts = 3;
  int         sctp_max_init_timeo    = 500;
  int         udp_rx_max_msgs        = 256;
  bool        no_core                = false;
};
```

- 嘗試修改srsRAN bind_addr(成功)

File : `gnb_ru_rpqn4800e_tdd_n78_20mhz.yml`
Path : `~/srsRAN_Project/build/apps/gnb`
```c=
bind_addr: 127.0.0.6
```
## Issue - fix (2) Finish Initializing the Network
**DU connects to the RU successfully**
[Link](https://docs.srsran.com/projects/project/en/latest/tutorials/source/oranRU/source/index.html#id5)




to complete the synchronization of CU/DU and RU using the same model of RU(Foxconn RPQN 7801E).

- First time setup 
>ERROR : srsGNB Failed to create SCTP gateway






We originally used the command to fix the MAC ADDRESS, because every time the RU is restarted, a random address will be generated, but later we found that the mac_address of the configuration file will not be affected when we start running, so we changed to use this mac_address and successfully connected to the srsRAN DU with RU

- Expected result ([source](https://docs.srsran.com/projects/project/en/latest/tutorials/source/oranRU/source/index.html#id5))
![image](https://hackmd.io/_uploads/H1zY_BK_T.png)

```c=
--== srsRAN gNB (commit ) ==--

Connecting to AMF on 10.53.1.2:38412
Initializing Open Fronthaul Interface with ul_comp=[BFP,9], dl_comp=[BFP,9], prach_cp_enabled=false, downlink_broadcast=true.
Operating a 20MHz cell over a RU with instantaneous bandwidth of 100MHz.
Warning: Configured PRACH occasion collides with PUCCH RBs ([0..1) intersects [0..3)). Some interference between PUCCH and PRACH is expected.
Warning: Configured PRACH occasion collides with PUCCH RBs ([0..1) intersects [0..3)). Some interference between PUCCH and PRACH is expected.
Cell pci=1, bw=20 MHz, dl_arfcn=634548 (n78), dl_freq=3518.22 MHz, dl_ssb_arfcn=634464, ul_freq=3518.22 MHz

==== gNodeB started ===
Type <t> to view trace
```
---

- Our result
![image](https://hackmd.io/_uploads/ByYrYSFOT.png)

```c=
--== srsRAN gNB (commit dcd905cc6) ==--

Connecting to AMF on 127.0.0.5:38412
Initializing the Open FrontHaul Interface for sector#0: ul_compr=[BFP,9], dl_compr=[BFP,9], prach_compr=[none,9] prach_cp_enabled=true, downlink_broadcast=false.
Cell pci=12, bw=100 MHz, dl_arfcn=649980 (n78), dl_freq=3749.7 MHz, dl_ssb_arfcn=647232, ul_freq=3749.7 MHz

==== gNodeB started ===
Type <t> to view trace
```
:::warning
Need to confirm whether it is simply a problem with ==RU_MAC_address== or ==bind_addr==
:::

## DU already send C/U plane packet ,but didn't get receive feedback from RU

**Troubleshoot server crash during initialization**

The settings are not changed to eliminate the problem, so I guass that when the server is restarted, ==5GS takes a while to complete the startup==, and it is found that the ==MTU Size== will be switched back to the default value when the server is restarted.

**DU send to RU**

![image](https://hackmd.io/_uploads/rk6PbSztp.png)

**RU send to DU**

![image](https://hackmd.io/_uploads/HJaj-BGFa.png)

**Use physical MAC (失敗)**
```c=
du_mac_addr: 40:a6:b7:92:c4:75       # DU MAC address.
```


