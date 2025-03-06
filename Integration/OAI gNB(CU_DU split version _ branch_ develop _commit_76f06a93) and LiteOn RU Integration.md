---
title: OAI gNB(CU/DU split) and LiteOn RU Integration

---

![](https://i.imgur.com/JORnn3y.png =150x)@NTUST
# OAI gNB(CU/DU split) and LiteOn RU Integration
:::success
**🎯 Readme:** 
1. Corresponding Titles and Numbers are available in [**OAI Tutorial**](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/F1-design.md)


**:mahjong:Reference:**
1. [Note template](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2F%40Summer063%2FByEVeA3Ya#Template-of-E2E-Integration-Guide)
:::
:::info
#### :memo: **Outline:**
[TOC]
:::
## Topology
### OAI gNB(CU/DU split) + LiteOn RU + open5GS
![image](https://hackmd.io/_uploads/ByKnOMPCT.png)

## Environment
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
| OAI Commit | `c99db6`                                      |

### RU
|       Item       | Info                      |
|:----------------:| ------------------------- |
|   product_name   | `FlexFi-RU`               |
|     model id     | `FF-RFI078I4`             |
| firmware version | `02.00.03`                |

## Current Status
### Integration of OAI gNB and LiteOn RU + open5GS

| Item                            | Status             |
| ------------------------------- |:------------------ |
| NG Setup                        | :heavy_check_mark: |
| F1 Setup                        | :heavy_check_mark: |
| DU Configuration Update         | :heavy_check_mark: |
| RU get packet from DU           | :heavy_check_mark: |
| RU send U Plane packet to DU    | :heavy_check_mark:        |
| UE get mib and sib1 for DL sync | :heavy_check_mark:                |
| UE send RRC setup request       | :heavy_check_mark:                |
| RRC setup complete              | :heavy_check_mark:                |
| NAS registration                | :heavy_check_mark:                |
| Service request                 | :heavy_check_mark:                |
| Service response                | :heavy_check_mark:                |
| PDU session complete            | :heavy_check_mark:                |
| Internet access                 | :heavy_check_mark:                |
| Performance                     | :heavy_check_mark:                |




## Access method
:::success
**OAI gNB/open5GS (DELL R740)**
- IP : 192.168.8.29
- user name : oai72
- Root Password : bmwlab

**free5GC**
- IP : 192.168.8.21
- user name : free5gc
- Root Password : bmwlab

**LiteOn RU**
- IP : 192.168.8.80
- root name/password : root/root
- user name/password : user/user
- enable password : liteon168
:::



## Timming window compare
- LiteOn RU timming parameter is fix.

| Parameters    | OAI DU | LiteOn RU |
| ------------- | ------ | --------- |
| T2a_min_cp_dl | 74 μs  | 74 μs     |
| T2a_max_cp_dl | 464 μs | 464 μs    |
| T2a_min_cp_ul | 36 μs  | 36 μs     |
| T2a_max_cp_ul | 392 μs | 392 μs    |
| T2a_min_up    | 80 μs  | 80 μs     |
| T2a_max_up    | 464 μs | 464 μs    |
| Ta3_min       | 84 μs  | 84 μs     |
| Ta3_max       | 120 μs | 120 μs    |

# Run Application
## Prerequisite
### Step 1 Git clone split version
```c=
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
```
### Step 2 Build ORAN Fronthaul Interface Library (branch : oran_e_maintenance_release_v1.0)
**Download ORAN FHI library**
```c=
cd /home/oai72/oai_split/
git clone https://gerrit.o-ran-sc.org/r/o-du/phy.git
cd /home/oai72/oai_split/phy
git checkout oran_e_maintenance_release_v1.0
```

**Apply the patch**
- available in `/home/oai72/oai_split/openairinterface5g/cmake_targets/tools/oran_fhi_integration_patches/E`

```c=
cd /home/oai72/oai_split/phy
# Copy openairinterface files and update patch
git apply /home/oai72/oai_split/openairinterface5g/cmake_targets/tools/oran_fhi_integration_patches/E/oaioran_E.patch

# Set Gobal
export XRAN_LIB_DIR=/home/oai72/oai_split/phy/fhi_lib/lib/build
export XRAN_DIR=/home/oai72/oai_split/phy/fhi_lib
export RTE_SDK=~/dpdk-stable-20.11.8 # SDK is locally
export RTE_TARGET=x86_64-native-linuxapp-gcc
export RTE_INCLUDE=${RTE_SDK}/${RTE_TARGET}/include
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/oai72/oai_split/phy/fhi_lib/lib/build
export PKG_CONFIG_PATH=/opt/dpdk/lib64/pkgconfig/

```

- Change pmc command in `/home/oai72/oai_split/phy/fhi_lib/lib/src/xran_sync_api.c`

- Build fhi library

```c=
cd /home/oai72/oai_split/phy/fhi_lib/lib
make clean
RTE_SDK=/home/oai72/dpdk-stable-20.11.8/ XRAN_DIR=/home/oai72/oai_split/phy/fhi_lib make XRAN_LIB_SO=1
```

### Step 3 Build OAI gNB
**Build OAI**
- location of the FH library: `--cmake-opt -Dxran_LOCATION=PATH`
- Note that we cannot use ~ here, we resort to `$HOME`

```c=
cd /home/oai72/oai_split/openairinterface5g/cmake_targets
./build_oai --ninja -I # if this is the first time on this machine
./build_oai --gNB --ninja -t oran_fhlib_5g --cmake-opt -Dxran_LOCATION=$HOME/oai_split/phy/fhi_lib/lib
```
:::success
**Build OAI success log**
```c=
Running "cmake3 --build .  --target nr-softmodem nr-cuup oran_fhlib_5g params_libconfig coding rfsimulator dfts -- -j24" 
Log file for compilation is being written to: /home/oai72/oai_split/openairinterface5g/cmake_targets/log/all.txt
/home/oai72/oai_split/phy/fhi_lib/lib/api/../src/xran_common.h:185:16: warning: inline function ‘prepare_sf_slot_sym’ declared but never defined
  185 | inline int32_t prepare_sf_slot_sym (enum xran_pkt_dir direction,
      |                ^~~~~~~~~~~~~~~~~~~
In file included from /home/oai72/oai_split/phy/fhi_lib/lib/api/../src/xran_dev.h:44,
--
/home/oai72/oai_split/phy/fhi_lib/lib/api/xran_up_api.h:95:12: warning: inline function ‘xran_prepare_iq_symbol_portion’ declared but never defined
   95 | inline int xran_prepare_iq_symbol_portion(
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
In file included from ../../../radio/fhi_72/oaioran.c:34:
/home/oai72/oai_split/phy/fhi_lib/lib/api/../src/xran_common.h:192:23: warning: ‘prepare_symbol_opt’ declared ‘static’ but never defined [-Wunused-function]
  192 | static inline int32_t prepare_symbol_opt(enum xran_pkt_dir direction,
      |                       ^~~~~~~~~~~~~~~~~~
[10662/10675] Linking C static library libf1ap.a
WARNING: 3 warnings. See /home/oai72/oai_split/openairinterface5g/cmake_targets/log/all.txt
nr-softmodem nr-cuup oran_fhlib_5g params_libconfig coding rfsimulator dfts compiled
BUILD SHOULD BE SUCCESSFUL
```
:::

## Simulation Test
### Use analog RF and analog UE
### Step 1 Bring up CN
```c=
sudo ./createtun.sh # not persistent after rebooting
./start_open5gs.sh
```
**Path:**`~/oai_split/openairinterface5g`
```c=
cd ~/oai_split/openairinterface5g
```
### Step 2 Launch the CU with RFSIMULATOR
- **Terminal 1 :** 
```c=
sudo RFSIMULATOR=server cmake_targets/ran_build/build/nr-softmodem --rfsim --sa -O ci-scripts/conf_files/sim_conf/gnb-cu.sa.band78.106prb.conf
```
### Step 3 Launch the DU with RFSIMULATOR

- **Terminal 2 :** 
```c=
sudo RFSIMULATOR=server cmake_targets/ran_build/build/nr-softmodem --rfsim --sa -O ci-scripts/conf_files/sim_conf/gnb-du.sa.band78.106prb.rfsim.conf
```
### Step 4 Launch the UE SIMULATOR

- **Terminal 3 :**
```c=
sudo RFSIMULATOR=127.0.0.1 cmake_targets/ran_build/build/nr-uesoftmodem -r 106 --numerology 1 --band 78 -C 3619200000 --rfsim --sa --nokrnmod -O ci-scripts/conf_files/sim_conf/nrue.band78.106prb.l2sim.conf
```
- **Test result**
![image](https://hackmd.io/_uploads/Bk5atDckR.png)



## 1. RU Status
### Step1.1 Configure RU
#### [Configure LiteOn RU user guide](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2F%40Summer063%2FS1ZhZ2u33#config-setting)
- LiteOn  FlexFi-RU

| parameter          | FlexFi-RU      |
| ------------------ | -------------- |
| bandwidth          | `100000000`    |
| c/u-plane-vlan     | `4`            |
| center-frequency   | `3450720000`   |
| compression-bit    | `8`            |
| du-mac-address     | `001122334466` |
| gain-rx            | `14`           |
| env-tx-power       | `20`           |
| phasecomp-mode     | `Disable`      |
| subcarrier-spacing | `1` (30kHz)    |
| sync-source        | `PTP`          |

#### set the PRACH port and slot mapping
- set PRACH eAxC ID to 4, 5, 6, 7
- set slot ID to 0, 1
:::danger
Need to do this **after** you reboot RU
:::
##### Two ways to set the eAxC ID in RU
- Use user to login LiteOn RU
```bash=
> enable
Enter Password:
Auto exit privileged commands in 300 Seconds
# configure terminal

Entering configuration mode...
(config)# set_devmem 0x80001014 32 0x00050004
(config)# set_devmem 0x80001018 32 0x00070006
(config)# set_devmem 0x8000201C 32 0x00000001
```
- Use user to login LiteOn RU
```bash=
devmem 0x80001014 32 0x00050004
devmem 0x80001018 32 0x00070006
devmem 0x8000201C 32 0x00000001
```

### Step1.2 RU PTP Synchronization
```bash=
Please enter help
> enable
Enter Password:
Auto exit privileged commands in 300 Seconds
# show oru-status
Sync State  : SYNCHRONIZED
RF State    : Ready
DPD         : Ready
DuConnected : notReady
```

![](https://hackmd.io/_uploads/H17fa86lp.png =x100)

## 2. Core Network and gNB Connection
### **open5GS**
#### Step2.1 Core Network setting
- [Configure Open5GS](https://hackmd.io/@Summer063/HJyq005rT#Configure-Open5GS).
- [Add UE information in open5GS](https://hackmd.io/@Summer063/HJyq005rT#Add-UE-information-in-open5GS)
- [Connect Data Network](https://hackmd.io/@Summer063/HJyq005rT#Adding-a-route-for-the-UE-to-have-WAN-connectivity)

#### Step2.2 Bring up CN
- login open5GS server and start open5GS.
```bash=
sudo ./createtun.sh # not persistent after rebooting
./start_open5gs.sh
```
- To check if everything is running fine, you can check the amf log by attaching it's screen session.
```bash=
screen -r amf
```
>press ctrl+a+d to exit the screen

### **free5GC v3.3.0**
#### Step2.1 Configure CN 
- [Configure free5GC](https://hackmd.io/@Yueh-Huan/S1eC0g3Gi#43-Edit-AMF-Config)
- [Add UE information in free5GC](https://hackmd.io/@Yueh-Huan/S1eC0g3Gi#53-Execute-Web-Console)
- [Connect Data Network](https://hackmd.io/@Yueh-Huan/S1eC0g3Gi#42-Host-Network-Setting)

#### Step2.2 Bring up CN
- login free5GC server and start free5GC(version : 3.3.0).
```bash=
cd ~/free5gc/
sudo ./run.sh
```

### **free5GC v3.2.1**
#### Step2.1 Configure CN 
- [Configure free5GC](https://hackmd.io/@Yueh-Huan/S1eC0g3Gi#43-Edit-AMF-Config)
- [Add UE information in free5GC](https://hackmd.io/@Yueh-Huan/S1eC0g3Gi#53-Execute-Web-Console)
- [Connect Data Network](https://hackmd.io/@Yueh-Huan/S1eC0g3Gi#42-Host-Network-Setting)

#### Step2.2 Bring up CN
- login free5GC server and start free5GC(version : 3.3.0).
```bash=
cd ~/free5gc_3.2.1/
sudo ./run.sh
```

## 3. Bring up CU/DU
### Step 3.1 Configure CU
- original file : `home/oai72/oai_split/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/cu_gnb.conf`
- Configuration File Path : `/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/test_conf/cu_gnb.conf`
- Can check the [cu_config_file](https://drive.google.com/file/d/15_Do1Xy6GJS7U0IFevmiQWjzNkRd8rCU/view?usp=sharing)

### Step 3.2 Configure DU
- original file : `home/oai72/oai_split/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/du_gnb.conf`
- Configuration File Path : `/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/test_conf/du_gnb.conf`
- Can check the [du_config_file](https://drive.google.com/file/d/1RYOQQx9dnUCaGTP2dTd8yGm1lak6cmal/view?usp=sharing)
:::success
- 0425 update
```c=
    ////////// Physical parameters:
force_256qam_off = 1; #defult 1

    pdsch_AntennaPorts_XP = 2; # logical antenna ports
    pusch_AntennaPorts    = 4; # logical antenna ports
    do_CSIRS              = 1;
    do_SRS                = 0 ;
    sib1_tda			  = 15;
    pdcch_ConfigSIB1 = (
      {
        controlResourceSetZero = 11;
        searchSpaceZero = 0;
      }
    );
```
:::
:::success
- 0506 update(change TDD pattern)
- Result : The iperf3 test is unstable and cannot reach the same 60M as the old pattern.

- Case1. 3D1S1U;(S:6D4G4U)
```c=
#tdd-UL-DL-ConfigurationCommon
# subcarrierSpacing
# 0=kHz15, 1=kHz30, 2=kHz60, 3=kHz120
      referenceSubcarrierSpacing                                    = 1;
      # pattern1
      # dl_UL_TransmissionPeriodicity
      # 0=ms0p5, 1=ms0p625, 2=ms1, 3=ms1p25, 4=ms2, 5=ms2p5, 6=ms5, 7=ms10
      dl_UL_TransmissionPeriodicity                                 = 5;
      nrofDownlinkSlots                                             = 3;
      nrofDownlinkSymbols                                           = 6;
      nrofUplinkSlots                                               = 1;
      nrofUplinkSymbols                                             = 4;

  ssPBCH_BlockPower                                                 = 0;
  }
```
- Case2. 7D1SU;(S:6D4G4U)
```c=
      dl_UL_TransmissionPeriodicity                                 = 6 # default 5
      nrofDownlinkSlots                                             = 7 # default 3
      nrofDownlinkSymbols                                           = 6 # default 6
      nrofUplinkSlots                                               = 2 # default 1
      nrofUplinkSymbols                                             = 4 # default 4

```
:::
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


### Step 3.3 Configure O-RAN Fronthaul Interface C/U Plane
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

### Step 3.4 gNB PTP synchronization
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

### Step 3.5 Run CU
- remember change log's name
- `~/ci-scripts/conf_files/test_conf/cu_gnb.conf` is the cu configfile path.
```bash=
cd ~/oai_split/openairinterface5g

sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/test_conf/cu_gnb.conf
> /home/oai72/OAI_CU_LOG/0000_dev.log 2>&1
```

### Step 3.6 Run DU
- remember change log's name
- `~/ci-scripts/conf_files/test_conf/du_gnb.conf` is the du configfile path.
```bash=
cd ~/oai_split/openairinterface5g

sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/test_conf/du_gnb.conf
> /home/oai72/OAI_DU_LOG/0000_dev.log 2>&1
```
- You can check the open5gs amf log by attaching it's screen session to make sure it have connection with gNB.
```bash=
screen -r amf
```
:::spoiler amf log
```bash
03/12 10:35:51.677: [amf] INFO: gNB-N2 accepted[127.0.0.1]:44917 in ng
-path module (../src/amf/ngap-sctp.c:113)
03/12 10:35:51.677: [amf] INFO: gNB-N2 accepted[127.0.0.1] in master_s
m module (../src/amf/amf-sm.c:741)
03/12 10:35:51.681: [amf] INFO: [Added] Number of gNBs is now 1 (../sr
c/amf/context.c:1231)
03/12 10:35:51.681: [amf] INFO: gNB-N2[127.0.0.1] max_num_of_ostreams 
: 2 (../src/amf/amf-sm.c:780)
```
:::

>press ctrl+a, 'd' to exit the screen

### Step 3.7 Check RU connection with DU
#### DU side
- If your DU & RU connect successfully. You can find the received and transmitted traffic from the log.

![image](https://hackmd.io/_uploads/BJ4LVhtRa.png)

#### RU side
```bash=
Please enter help
> enable
Enter Password:
Auto exit privileged commands in 300 Seconds
# show oru-status
Sync State  : SYNCHRONIZED
RF State    : Ready
DPD         : Ready
DuConnected : Ready
```
![image](https://hackmd.io/_uploads/Bkgl4Jqe2T.png =x100)

## 4. Nemo handy setting
### Step 4.1 Setting APN: free5GC
![image](https://hackmd.io/_uploads/S1JizB6p6.png)

#### APN information
![image](https://hackmd.io/_uploads/S1OjXr6pp.png =x400)

### Step 4.2 Open nemo handy APP
![image](https://hackmd.io/_uploads/ByTCvF36a.png =x400)

### Step 4.3 Lock the band of SSB center frequency and SCS
![image](https://hackmd.io/_uploads/SJHyS2tRp.png =x400)

#### In [OAI's gNB](#SSB-center-frequency) case we use `ARFCN 630048`(3450.72 MHz)
![image](https://hackmd.io/_uploads/BylXPtnpT.png =x400)

### Step 4.4 Signaling log
![image](https://hackmd.io/_uploads/BJ9aRqna6.png =x400)

<!-- :::info
You can try to on off the **flight mode** if the log stops.

![image](https://hackmd.io/_uploads/rJ8IyohTa.png =x500)
::: -->
## 5. Result
### OAI gNB result
- [PDUsession complete OAI BBU running log](https://drive.google.com/file/d/1xKm8DsetyOLbLidGMzh_Ac1gzWqGvefY/view?usp=sharing)
- [PDUsession complete OAI BBU PCAP](https://drive.google.com/file/d/1umlsty161z2YvZAHNyqPCwmQdvnGOxMQ/view?usp=sharing)
:::info
#### PCAP syntax
- Catch procedure between UE, gNB and CN.
```bash=
sudo tcpdump -i any -w xxx.pcap
```
- If you want to catch FH pacp. You need to use other server.
```bash=
sudo tcpdump -i <NIC interface> -w xxx.pcap
```
:::
### Nemo log
- PDUsession complete

![image](https://hackmd.io/_uploads/r18KqhFAa.png =x400)

![image](https://hackmd.io/_uploads/ByB2cnFA6.png =x400)
## 6. Performance measure
### **LiteOn RU + OAI gNB(CU/DU split) + open5gs**
#### Step 6.1 ping
- Command in CN terminal
```bash=
ping -c 1000 <UE IP>
# example:ping -c 1000 10.45.0.1
```
:::info
Test the delay between UE and CN using ping. → **Average time is ms.**
:::


#### Step 6.2 iperf3
- Use Magic iPerf APP
#### **server : UE**
<!-- ![image](https://hackmd.io/_uploads/SkShKYTTa.png =x400) -->

#### **client : open5gs**
UL
```bash=
╭─oai72@Dell-R740-Server ~ 
╰─$ iperf3 -c 10.45.02 -u -l 1300 -b 800m -i 1 -t 10 -R -p 5201
Connecting to host 10.45.02, port 5201
Reverse mode, remote host 10.45.02 is sending
[  5] local 10.45.0.1 port 52431 connected to 10.45.0.2 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec  1.46 MBytes  12.2 Mbits/sec  0.641 ms  327/1504 (22%)  
[  5]   1.00-2.00   sec  1.71 MBytes  14.4 Mbits/sec  2.380 ms  0/1382 (0%)  
[  5]   2.00-3.00   sec   910 KBytes  7.46 Mbits/sec  0.582 ms  0/717 (0%)  
[  5]   3.00-4.00   sec  2.66 MBytes  22.3 Mbits/sec  1.518 ms  0/2144 (0%)  
[  5]   4.00-5.00   sec  1.03 MBytes  8.61 Mbits/sec  0.460 ms  7/835 (0.84%)  
[  5]   5.00-6.00   sec  1.33 MBytes  11.1 Mbits/sec  2.460 ms  0/1070 (0%)  
[  5]   6.00-7.00   sec  1.41 MBytes  11.8 Mbits/sec  0.718 ms  0/1138 (0%)  
[  5]   7.00-8.00   sec  2.34 MBytes  19.6 Mbits/sec  2.417 ms  0/1887 (0%)  
[  5]   8.00-9.00   sec  1.19 MBytes  10.0 Mbits/sec  0.529 ms  0/962 (0%)  
[  5]   9.00-10.00  sec  2.37 MBytes  19.9 Mbits/sec  2.667 ms  0/1911 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-10.00  sec  17.4 MBytes  14.6 Mbits/sec  0.000 ms  0/13550 (0%)  sender
[  5]   0.00-10.00  sec  16.4 MBytes  13.7 Mbits/sec  2.667 ms  334/13550 (2.5%)  receiver
```

|      | UL Throguhput  | UL loss          |
|:---- |:-------------- |:---------------- |
| 800M | 13.7 Mbits/sec | 334/13550 (2.5%) |
| 400M | 13.3 Mbits/sec | 371/13126 (2.8%) |
| 100M | 9.55 Mbits/sec | 0/9181 (0%)      |




#### DL
- 4/19_(Original BBU settings)
    - 16.9 Mbits/sec
- 4/30_(MIMO + phy_parameter + multi threadDU_16 + multi threadiperf_4)
    - 47.1 Mbits/sec  3.482 ms  3655/139568 (2.6%)  receiver
```bash= 
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[  5]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.608 ms  457/17446 (2.6%)  receiver
[  7]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[  7]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.419 ms  456/17446 (2.6%)  receiver
[  9]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[  9]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.404 ms  455/17446 (2.6%)  receiver
[ 11]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[ 11]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.405 ms  456/17446 (2.6%)  receiver
[ 13]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[ 13]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.578 ms  456/17446 (2.6%)  receiver
[ 15]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[ 15]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.474 ms  456/17446 (2.6%)  receiver
[ 17]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[ 17]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.462 ms  459/17446 (2.6%)  receiver
[ 25]   0.00-30.00  sec  22.2 MBytes  6.20 Mbits/sec  0.000 ms  0/17885 (0%)  sender
[ 25]   0.00-30.00  sec  21.1 MBytes  5.89 Mbits/sec  3.504 ms  460/17446 (2.6%)  receiver
[SUM]   0.00-30.00  sec   177 MBytes  49.6 Mbits/sec  0.000 ms  0/143080 (0%)  sender
[SUM]   0.00-30.00  sec   169 MBytes  47.1 Mbits/sec  3.482 ms  3655/139568 (2.6%)  receiver
```


#### 基於不同頻寬的小測試
(僅用於錯誤率的提升與頻寬的關係以及確認是否有限制流量的效果)
|       | Throguhput     |                loss |
|:----- |:-------------- | -------------------:|
| 400M  | 16.8 Mbits/sec | 368262/384442 (96%) |
| 50M   | 16.8 Mbits/sec |   32146/48026 (67%) |
| 20M   | 16.8 Mbits/sec |    3089/19178 (16%) |
| 18M   | 16.8 Mbits/sec |   1200/17283 (6.9%) |
| 17.5M | 16.6 Mbits/sec |    742/16673 (4.5%) |
| 17M   | 16.7Mbits/sec  |    7/16322 (0.043%) |
| 10M   | 9.99Mbits/sec  |         0/9603 (0%) |


```bash=
iperf3 -s 10.45.0.6 -p 5201 #server
iperf3 -c 10.45.0.2 -u -l 1300 -b 17.5m -i 1 -t 10 -p 5201 # client
```
#### Step 6.3 Internet access
- Check in ping tool APP


### **LiteOn RU + OAI gNB + free5gc v3.3.0**
##### Step 6.1 ping
- Command in CN terminal
```bash=

```
:::
#### Step 6.2 iperf3
##### **server : UE**
- Use ping tool to check UE IP

![image](https://hackmd.io/_uploads/By8qFtc0a.png =x400)

- Use Magic iPerf to run iperf server in UE

![image](https://hackmd.io/_uploads/rJVwh3t06.png =x400)

#### **client : free5GC v3.3.0**

##### UL

```c=
free5gc@free5gc-virtual-machine:~/free5gc$ iperf3 -c 10.60.0.1 -u -l 1300 -b 50m -i 1 -t 10 -R -p 5201
Connecting to host 10.60.0.1, port 5201
Reverse mode, remote host 10.60.0.1 is sending
[  5] local 192.168.8.21 port 51068 connected to 10.60.0.1 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec   929 KBytes  7.61 Mbits/sec  1.020 ms  0/732 (0%)  
[  5]   1.00-2.00   sec  1.48 MBytes  12.4 Mbits/sec  1.720 ms  0/1190 (0%)  
[  5]   2.00-3.00   sec   366 KBytes  2.99 Mbits/sec  1.321 ms  0/288 (0%)  
[  5]   3.00-4.00   sec   410 KBytes  3.36 Mbits/sec  2.453 ms  0/323 (0%)  
[  5]   4.00-5.00   sec   427 KBytes  3.50 Mbits/sec  3.892 ms  0/336 (0%)  
[  5]   5.00-6.00   sec   510 KBytes  4.18 Mbits/sec  5.632 ms  0/402 (0%)  
[  5]   6.00-7.00   sec   359 KBytes  2.94 Mbits/sec  5.433 ms  0/283 (0%)  
[  5]   7.00-8.00   sec   264 KBytes  2.16 Mbits/sec  18.949 ms  0/208 (0%)  
[  5]   8.00-9.00   sec   378 KBytes  3.10 Mbits/sec  4.754 ms  0/298 (0%)  
[  5]   9.00-10.00  sec   518 KBytes  4.24 Mbits/sec  4.360 ms  0/408 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     BitrateJitter    Lost/Total Datagrams
[  5]   0.00-10.00  sec  6.06 MBytes  5.08 Mbits/sec  0.000 ms  0/4468 (0%)  sender
[  5]   0.00-10.00  sec  5.54 MBytes  4.65 Mbits/sec  4.360 ms  0/4468 (0%)  receiver
```


UL(Supplementary parameter settings)
```c=
free5gc@free5gc-virtual-machine:~/free5gc$ iperf3 -c 10.60.0.4 -u -l 1300 -b 50m -i 1 -t 10 -R -p 5201
Connecting to host 10.60.0.4, port 5201
Reverse mode, remote host 10.60.0.4 is sending
[  5] local 192.168.8.21 port 38172 connected to 10.60.0.4 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec   254 KBytes  2.08 Mbits/sec  282269.468 ms  0/200 (0%)  
[  5]   1.00-2.00   sec  1.49 MBytes  12.5 Mbits/sec  0.419 ms  0/1202 (0%)  
[  5]   2.00-3.00   sec  2.61 MBytes  21.9 Mbits/sec  0.438 ms  0/2103 (0%)  
[  5]   3.00-4.00   sec  1.66 MBytes  13.9 Mbits/sec  6.321 ms  0/1339 (0%)  
[  5]   4.00-5.00   sec  2.25 MBytes  18.9 Mbits/sec  2.212 ms  0/1813 (0%)  
[  5]   5.00-6.00   sec  1.40 MBytes  11.8 Mbits/sec  3.198 ms  0/1132 (0%)  
[  5]   6.00-7.00   sec  2.65 MBytes  22.3 Mbits/sec  0.563 ms  0/2140 (0%)  
[  5]   7.00-8.00   sec  1.73 MBytes  14.5 Mbits/sec  2.379 ms  0/1393 (0%)  
[  5]   8.00-9.00   sec  2.64 MBytes  22.1 Mbits/sec  0.839 ms  0/2129 (0%)  
[  5]   9.00-10.00  sec  1.49 MBytes  12.5 Mbits/sec  2.260 ms  0/1200 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-10.00  sec  18.7 MBytes  15.6 Mbits/sec  0.000 ms  0/14651 (0%)  sender
[  5]   0.00-10.00  sec  18.2 MBytes  15.2 Mbits/sec  2.260 ms  0/14651 (0%)  receiver
```

:::info
UL Throughput is **5.54Mbits/sec**
:::
:::success
After Supplementary parameter settings:
UL Throughput is **15.2Mbits/sec**
:::

##### DL
- 4/19_(Original BBU settings)
    - **12.8 Mbits/sec**
- 4/26_(MIMO + ph_parameter)
    - **27.8 Mbits/sec**
- 4/30_(MIMO + ph_parameter + multi threadDU_16 + multi threadiperf_4)
    - **42.1 Mbits/sec**  4.331 ms  0/122335 (0%)  receiver
- 5/6_(MIMO + ph_parameter + multi threadDU_8 +multi threadCU_8 +multi threadiperf_4)
    - 60.0 Mbits/sec  2.234 ms  20809/115400 (18%) 
    - 60x(1-0.18)= **55.2 Mbits/sec**

```c=
[ ID] Interval           Transfer     Bandwidth       Jitter    Lost/Total Datagrams
[  4]   0.00-20.00  sec  35.8 MBytes  15.0 Mbits/sec  2.251 ms  5168/28850 (18%) 
[  4] Sent 28850 datagrams
[  6]   0.00-20.00  sec  35.8 MBytes  15.0 Mbits/sec  2.287 ms  5250/28850 (18%) 
[  6] Sent 28850 datagrams
[  8]   0.00-20.00  sec  35.8 MBytes  15.0 Mbits/sec  2.249 ms  5170/28850 (18%) 
[  8] Sent 28850 datagrams
[ 10]   0.00-20.00  sec  35.8 MBytes  15.0 Mbits/sec  2.148 ms  5221/28850 (18%) 
[ 10] Sent 28850 datagrams
[SUM]   0.00-20.00  sec   143 MBytes  60.0 Mbits/sec  2.234 ms  20809/115400 (18%) 
```

:::info
DL Throughput is **42.1Mbits/sec**
:::


#### Step 6.3 Internet access
- Check in ping tool APP

![image](https://hackmd.io/_uploads/SJUn_t5A6.png =x400)

### **LiteOn RU + OAI gNB + free5gc v3.2.1**
##### Step 6.1 ping
- Command in CN terminal
```bash=
ping -c 1000 <UE IP>
# example:ping -c 1000 10.45.0.1
```

:::info
Test the delay between UE and CN using ping. → **Average time is 9.93 ms.**
:::
:::spoiler ping log
```bash=

```
:::


#### Step 6.2 iperf3
- Use Magic iPerf APP
##### **server : UE**


#### **client : free5GC v3.2.1**
> UL
```bash=
```
:::info
UL Throughput is **Mbits/sec**
:::

> DL
```bash=
```
:::info
DL Throughput is **400 Mbits/sec**
:::
Same problem as version 3.3.0

#### Step 6.3 Internet access
- Check in ping tool APP

![image](https://hackmd.io/_uploads/SJUn_t5A6.png =x400)

#### Step 6.4 Signal strength
##### Test the impact of different distances on RSRP of UE
- Test environment

![image](https://hackmd.io/_uploads/S1F-Y4_ap.png)

| Item          |           |         |         |
|:------------- | --------- | ------- | ------- |
| RSRP(NEMO)    | -61 dbm   | -97 dbm | -99 dbm |
| RSRP(OAI_log) | -62 dbm   | -97 dbm | -99 dbm |
| Location      | on the RU | 45 cm   | 55 cm   |

- new RU



## 7. Troubleshooting note


## 8. Appendix
### 5G NR SA Registration/Attach Call Flow
![image](https://hackmd.io/_uploads/SJotNRhFp.png) 
![image](https://hackmd.io/_uploads/S1guCV03tp.png)
