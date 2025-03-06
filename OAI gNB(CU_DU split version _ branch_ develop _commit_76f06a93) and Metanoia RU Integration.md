---
title: 'OAI gNB(CU/DU split version / branch: develop /commit:76f06a93) and Metanoia RU Integration'

---

![](https://i.imgur.com/JORnn3y.png =150x)@NTUST
# OAI gNB(CU/DU split version / branch: develop /commit:76f06a93) and Metanoia RU Integration
:::success
**🎯 Readme:** 
1. Corresponding Titles and Numbers are available in [**OAI Tutorial**](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/F1-design.md)


**:mahjong:Reference:**
1. [Note template](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2F%40Summer063%2FByEVeA3Ya#Template-of-E2E-Integration-Guide)
2. [OAI gNB(CU/DU split) and LiteOn RU Integration](https://hackmd.io/@Johnson-72/S10kOrckC)
:::
:::info
#### :memo: **Outline:**
[TOC]
:::
## Topology
### OAI gNB(CU/DU split) + Metanoia RU + open5GS

![image](https://hackmd.io/_uploads/B1mTle7XC.png)


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
| OAI Commit | `76f06a9386`                                      |
| Branch | `Develop`                                      |

### RU
|       Item       | Info                      |
|:----------------:| ------------------------- |
|   product_name   | `FlexFi-RU`               |
|     model id     | `FF-RFI078I4`             |
| firmware version | `02.00.03`                |

## Current Status
### Integration of OAI gNB and Metanoia RU + open5GS

| Item                            | Status             |
| ------------------------------- |:------------------ |
| [NG Setup](#NG-Setup)                       | :heavy_check_mark: |
| [F1 Setup](#Step-33-F1-Setup)                        | :heavy_check_mark: |
| [DU Configuration Update](#Step-32-Configure-DU)         | :heavy_check_mark: |
| [RU get packet from DU](#RU-side)           | :heavy_check_mark: |
| [RU send U Plane packet to DU](#DU-side)    | :heavy_check_mark: |
| [UE get mib and sib1 for DL sync](#Result) | :heavy_check_mark:        |
| [UE send RRC setup request](#Result)       | :hourglass:                |
| RRC setup complete              | :x:                |
| NAS registration                | :x:                |
| Service request                 | :x:                |
| Service response                | :x:                |
| PDU session complete            | :x:                |
| Internet access                 | :x:                |
| Performance                     | :x:                |




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

**Metanoia RU**
- IP : 192.168.8.93
- console : COM 16
- user name : root
- Root Password : root
:::



## Timming window compare(TODO)
- Metanoia RU timming parameter is fix.

| Parameters    | OAI DU | Metanoia RU |
| ------------- | ------ | --------- |


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



## 1. RU Status(TODO)
### Step1.1 Confirm Serial port number
Different device will have different serial port number, please go to the ==device administrator== to check first.
![image](https://hackmd.io/_uploads/ryJ8ng7QR.png)

- Serial port: COM5 (Standard COM Port)(network processor)
- Serial port: COM4 (Antenna Procesor 0)
- Serial port: COM3 (Antenna Procesor 1)
- Serial port: COM7 (Antenna Procesor 2)
- Serial port: COM8 (Antenna Procesor 3)
### Step1.2 Download and open MobaXterm
- Serial port: `COM5`
- Speed(bps): `115200`
![image](https://hackmd.io/_uploads/ryRPplX70.png)

### Step1.3 Login RU
- Login : root
- Password : root
![image](https://hackmd.io/_uploads/BJVsAeXQR.png)


### Step1.4 RU PTP Synchronization
- Check `'synchronized': 'YES'`

Use commend to check
```bash=
/root/bin/mpcli.py -m get -p splane/ptp-time
```
![image](https://hackmd.io/_uploads/S1yeybQmR.png)

### Step1.5 RU/Antenna Status Check

```bash=
tail -f /var/log/rumanager.log
```
![image](https://hackmd.io/_uploads/r1CVx-7mC.png)

### Step1.6 IOP test(TODO)

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
- Configuration File Path : `/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/meta_conf/cu_gnb.conf`
- Can check the [cu_config_file](https://docs.google.com/document/d/1WHLiqeLsIxHVzkMvqvcXCAYAtkSJvS3-oYLUOH_tKqc/edit?usp=sharing) 



### Step 3.2 Configure DU
- original file : `home/oai72/oai_split/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/du_gnb.conf`
- Configuration File Path : `/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/meta_conf/du_gnb.conf`
- Can check the [du_config_file](https://docs.google.com/document/d/1x0VTs9ZSOb8DL4zuc1y-P2RRxhgN_8UGMydtjca8kBA/edit)
:::success
- Jura differernt
```c=
  phase_compensation = 0; # needs to match O-RU configuration
```
```c=
   mtu = 1500; # check if xran uses this properly
```
```c=
  fh_config = ({
    
    Tadv_cp_dl = 125;
    T2a_cp_dl = (255, 490); # (min, max)
    T2a_cp_ul = (90, 429); # (min, max)
    T2a_up = (140, 350); # (min, max)
    Ta3 = (50, 170); # (min, max)

    #IPO with DU_A
    T1a_cp_dl = (385, 500); # (min, max)
    T1a_cp_ul = (300, 435); # (min, max)
    T1a_up = (300, 355); # (min, max)
    Ta4 = (60, 200); # (min, max)
```
```c=
  ru_addr = ("00:e0:0c:00:ae:06", "00:e0:0c:00:ae:06");
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


#### NG Setup
If the Core Network (CN) successfully establishes a connection with the CU
![image](https://hackmd.io/_uploads/BkGuC7XmC.png)

### Step 3.3 F1 Setup 
- CU
- Configuration File Path : `/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/meta_conf/cu_gnb.conf`

Ports should match the ones in the DU config, but for simplicity and standards-conformity, simply set all to 2152:

1. `local_s_portc` in CU should match `remote_n_portc` in DU
2. `local_s_portd` in CU should match `remote_n_portd` in DU
3. `remote_s_portc` in CU should match `local_n_portc` in DU
4. `remote_s_portd` in CU should match `local_n_portd` in DU


```bash=
    tr_s_preference = "f1";
    local_s_if_name = "lo";
    local_s_address = "127.0.0.5";
    remote_s_address = "127.0.0.3";
    local_s_portc   = 501;
    local_s_portd   = 2152;
    remote_s_portc  = 500;
    remote_s_portd  = 2152;
```
---

- DU
- Configuration File Path : `/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/meta_conf/du_gnb.conf`

1. Set `MACRLCs.[0].tr_n_preference` to f1
2. Update `MACRLCs.[0].local_n_address` (local north-bound address of the DU) to `127.0.0.5` This IP address is used to bind the F1-C/SCTP and F1-U/GTP/UDP socket.
```bash=
MACRLCs = (
  {
    num_cc           = 1;
    tr_s_preference  = "local_L1";
    tr_n_preference  = "f1";

    local_n_if_name = "lo";
    local_n_address = "127.0.0.3";
    remote_n_address = "127.0.0.5";

    local_n_portc   = 500;
    local_n_portd   = 2152;
    remote_n_portc  = 501;
    remote_n_portd  = 2152;

  }
);
```

### Step 3.4 Configure O-RAN Fronthaul Interface C/U Plane
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
  system_core = 0;
  io_core = 4;
  worker_cores = (2);
  du_addr = ("00:11:22:33:44:66", "00:11:22:33:44:66");
  ru_addr = ("00:e0:0c:00:ae:06", "00:e0:0c:00:ae:06");
  mtu = 9600; # check if xran uses this properly
  fh_config = ({
    
    Tadv_cp_dl = 125;
    T2a_cp_dl = (285, 429); # (min, max)
    T2a_cp_ul = (285, 429); # (min, max)
    T2a_up = (71, 428); # (min, max)
    Ta3 = (20, 32); # (min, max)

    # #IPO with DU_A
    # T1a_cp_dl = (385, 435); # (min, max)
    # T1a_cp_ul = (385, 435); # (min, max)
    # T1a_up = (300, 340); # (min, max)
    # Ta4 = (0, 70); # (min, max)


    #IPO with DU_B
    T1a_cp_dl = (258, 392); # (min, max)
    T1a_cp_ul = (285, 300); # (min, max)
    T1a_up = (155, 350); # (min, max)
    Ta4 = (0, 200); # (min, max)




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

### Step 3.5 gNB PTP synchronization
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

### Step 3.6 Run CU
- Remember to change the log name to avoid overwriting
- `~/ci-scripts/conf_files/meta_conf/cu_gnb.conf` is the cu configfile path.
```bash=
cd ~/oai_split/openairinterface5g

sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/meta_conf/cu_gnb.conf
> /home/oai72/OAI_metaCU.log 2>&1
```

### Step 3.7 Run DU
- Remember to change the log name to avoid overwriting
- `~/ci-scripts/conf_files/meta_conf/du_gnb.conf` is the du configfile path.
```bash=
cd ~/oai_split/openairinterface5g

sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/meta_conf/du_gnb.conf
> /home/oai72/OAI_metaDU.log 2>&1
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

### Step 3.8 Check RU connection with DU
#### DU side
- If your DU & RU connect successfully. You can find the received and transmitted traffic from the log.

![image](https://hackmd.io/_uploads/ryy74mXmA.png)

#### RU side
- Make sure
    - C/U plane is on time 
    - no packets are dropped
![image](https://hackmd.io/_uploads/rJxsT47m70.png)

If your C/U plane does not have on time, you need to readjust your timing windows according to whether the time is too C/U plane early or late.

#### h4
![image](https://hackmd.io/_uploads/HJVj57XmC.png)

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


## 6. Performance measure
### **Metanoia RU + OAI gNB(CU/DU split) + open5gs**
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
#### UL



#### DL




```bash=
iperf3 -s 10.45.0.6 -p 5201 #server
iperf3 -c 10.45.0.2 -u -l 1300 -b 17.5m -i 1 -t 10 -p 5201 # client
```
#### Step 6.3 Internet access
- Check in ping tool APP


### **Metanoia RU + OAI gNB + free5gc v3.3.0**
##### Step 6.1 ping
- Command in CN terminal
```bash=

```
:::
#### Step 6.2 iperf3
##### **server : UE**
- Use ping tool to check UE IP

![image](https://hackmd.io/_uploads/By8qFtc0a.png =x400)

#### **client : free5GC v3.3.0**

##### UL




UL(Supplementary parameter settings)
```c=

```


##### DL




#### Step 6.3 Internet access
- Check in ping tool APP

![image](https://hackmd.io/_uploads/SJUn_t5A6.png =x400)

### **Metanoia RU + OAI gNB + free5gc v3.2.1**
##### Step 6.1 ping
- Command in CN terminal
```bash=
ping -c 1000 <UE IP>
# example:ping -c 1000 10.45.0.1
```



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


#### Step 6.3 Internet access
- Check in ping tool APP

![image](https://hackmd.io/_uploads/SJUn_t5A6.png =x400)

#### Step 6.4 Signal strength
##### Test the impact of different distances on RSRP of UE
- Test environment






## 7. Troubleshooting note
### :heavy_check_mark:[PDCP]discard NR PDU, integrity failed
:::success

![image](https://hackmd.io/_uploads/HyKBXHBHA.png)

- **Solution** : set ciphering algorithm, which is in the configuration of gNB, as nea0.

```c=
ciphering_algorithms = ( "nea0" );
```
:::


## 8. Appendix

### 5/16
:::info
Try to adjust digital Tx gain to make Tx power more reliable for purpose.

Check the output ``"PwrAvg(dBFs)"`` of the 4 AP consoles on the downlink side.

Based on the target to make the value close to `-8.7dBFs` as an adjustment, set the deployment  "tx_gain_Correction".

>Ex:
>If show -13.0dB, then the lack of 4.3 dB will be >made up by the digital gain.
>
>4.3 = (-8.7) - (-13.0)
:::


:::success
Before
- interface 0
![image](https://hackmd.io/_uploads/r1GLONQm0.png)
6100 = ((-8.7) - (-69.7)) x 100
- interface 1
![image](https://hackmd.io/_uploads/BJrjO4mmA.png)
6100 = ((-8.7) - (-69.7)) x 100
- interface 2
![image](https://hackmd.io/_uploads/rJ-CONQ7R.png)
6210 = ((-8.7) - (-70.8)) x 100
- interface 3
![image](https://hackmd.io/_uploads/BkNeFE7mA.png)
6460 = ((-8.7) - (-73.3)) x 100

---

Edit "tx_gain_correction" to correct the power scale.
Ex: made up `4.3dB` "tx_gain_correction": `430`, (e-2dB in unit), and reboot to save
```bash=
"tx_gain_correction":[
    6100, 
    6100,
    6200,
    6460
],
```
![image](https://hackmd.io/_uploads/BygW2N7QR.png)

:::

:::success
After
- interface 0
![image](https://hackmd.io/_uploads/H1EFkSXmR.png)
- interface 1
![image](https://hackmd.io/_uploads/rJ7eySQ7A.png)
- interface 2
![image](https://hackmd.io/_uploads/ByqoyrQX0.png)
- interface 3
![image](https://hackmd.io/_uploads/rJj21HQmC.png)

Check again whether the 4 AP console outputs can be close to "PwrAvg `-8.7dBFs`", and then system get a close value of ``"PwrAvg 24dBm"``.

##### Result
And after this we can receive SIB/MIB
![image](https://hackmd.io/_uploads/S1m9bHmQ0.png)

:::
### 5/17
:::success
Replace Metanoia_Jura Antenna from Foxconn back to the original one
![image](https://hackmd.io/_uploads/rysXHDEQR.png)

According to the steps [here](#516) ,recalculate appropriate `"tx_gain_correction"` table:

4840 = ((-8.7) - (-57.1)) x 100
4540 = ((-8.7) - (-54.1)) x 100
4760 = ((-8.7) - (-53.3)) x 100
4760 = ((-8.7) - (-56.3)) x 100

```bash=
    "tx_gain_correction": [
        4840,
        4540,
        4760,
        4760
    ],
```
Metanoia engineers recommend that the four settings be the same.

```bash=
    "tx_gain_correction": [
        4760,
        4760,
        4760,
        4760
    ],
```


According to the steps [here](#516) ,recalculate appropriate `"rx_gain_correction"` table:
![image](https://hackmd.io/_uploads/HkenYFV7R.png)

Metanoia engineers recommend that the RX value should be between ==-40 ~ -100==

rx_gain_correction calculation : 
```bash=
-4570 = ((-70) - (-24.3)) x 100
-4850 = ((-70) - (-21.5)) x 100
-4610 = ((-70) - (-23.9)) x 100
-4590 = ((-70) - (-24.1)) x 100
```

However, the parameter setting cannot be set to negative, so the RX part has not been changed for the time being.
```bash=
    "rx_gain_correction": [
        0,
        0,
        0,
        0
    ],
```

:::
### 5/23
:::success
![image](https://hackmd.io/_uploads/HkakztnmA.png)

![image](https://hackmd.io/_uploads/HJF-fY3XA.png)

---
:::warning
#### 問題1
假如我有一個二進制16位元的訊號
```bash=
dBFS=20*log(採樣訊號/1111 1111 1111 1111)
```
- 採樣訊號是指數位信號的取樣值，通常用二進制表示。1111 1111 1111 1111 是一個 16 位元的二進制數字，代表最大的數位滿幅度值（Full Scale）。
- 公式中的 `20*log` 是計算分貝值的標準公式之一，用於將一個信號的比例轉換為分貝單位，用於表示信號的相對大小或強度。

---
#### 問題2
[CFR (Crest Factor Reduction)](https://helpfiles.keysight.com/csg/n7614/Content/Main/Crest_Factor_Reduction_Concept.htm)



:::

:::success
#### rf_rx_gain_manual調整
“rf_rx_gain_manual” support changing MT3812 RX gain in non-hash mode.

---
#### rf_rx_gain_manual = 66，其二進制表示為 1000010：
- 第6位元~第4位元 => (==100==0010): RF gain, each step 6 dB
- 第3位元~第0位元 => (100==0010==): baseband gain, each step is 2dB

#### rf_rx_gain_manual = 0，將觸發 RX auto gain control
 
“rx_power_agc_threshold” is the trigger threshold, -740 is equivalent to -74 dBm. 
“rx_power_agc_update_period” is update period, default is 4 sec

```bash=
rf_rx_gain_manual = 0
rx_power_agc_threshold = -740  # -74 dBm
rx_power_agc_update_period = 4 # 每 4 秒更新一次
```
#### result
##### rf_rx_gain_manual = 0
![image](https://hackmd.io/_uploads/B1qnH6T7A.png)
##### rf_rx_gain_manual = 66
![image](https://hackmd.io/_uploads/SJaNwTaXC.png)



:::
### 6/6
Update and build OAI CU/DU split version
:::success
- new commit : 76f06a9386fbd8ea79d701ff8143608d1b72a760
- old commit : 883724b977e8ae2c0b8798f3c317188ef55313f6
![image](https://hackmd.io/_uploads/B1et2BrrC.png)
:::
### 6/7
Fix discard NR PDU, integrity failed
:::success

![image](https://hackmd.io/_uploads/HyKBXHBHA.png)

- **Solution** : set ciphering algorithm, which is in the configuration of gNB, as nea0.

```c=
ciphering_algorithms = ( "nea0" );
```
:::






## 5G NR SA Registration/Attach Call Flow
![image](https://hackmd.io/_uploads/SJotNRhFp.png) 
![image](https://hackmd.io/_uploads/S1guCV03tp.png)


