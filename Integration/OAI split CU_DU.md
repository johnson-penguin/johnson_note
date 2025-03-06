---
title: OAI split CU/DU

---

[toc]

## Reference
[Enhancement of E1 and F1 documentation  Provided by Robert Schmidt ](https://gitlab.eurecom.fr/oai/openairinterface5g/-/blob/develop/doc/F1-design.md)
## ===== Environmental preparation =====
## Step 1-1 Git clone split version
```c=
git clone https://gitlab.eurecom.fr/oai/openairinterface5g.git
```
## Step 1-2 Build ORAN Fronthaul Interface Library (branch : oran_e_maintenance_release_v1.0)
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

## Step 1-3 Build OAI gNB
**Build OAI**
- location of the FH library: `--cmake-opt -Dxran_LOCATION=PATH`
- Note that we cannot use ~ here, we resort to `$HOME`

```c=
cd /home/oai72/oai_split/openairinterface5g/cmake_targets
./build_oai --ninja -I # if this is the first time on this machine
./build_oai --gNB --ninja --nrUE -w SIMU -t oran_fhlib_5g --cmake-opt -Dxran_LOCATION=$HOME/oai_split/phy/fhi_lib/lib
```

`ninja rfsimulator` is equivalent to using` -w SIMU` when running the build_oai script.
## Step 1-4 RF Simulator 

```c=
cd  /home/oai72/oai_split/openairinterface5g/
source oaienv
cd cmake_targets/
./build_oai --phy_simulators
```




- ASN.1
![image](https://hackmd.io/_uploads/BkbgMR_VR.png)









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

## Step 1-4 Local network deployment of F1
For a local deployment, you should update the following fields.

We assume that the CU will bind on `192.168.70.129` towards the core,
`127.0.0.3` towards the DU, and the DU `127.0.0.4` towards the CU.


### Step 1-4-1 CU file

**Path:**`/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/gnb-cu.sa.band78.106prb.conf`
:::info

Update the `gNBs.[0].amf_ip_address` and `gNBs.[0].NETWORK_INTERFACES` section towards the core (typically, OAI CN is configured to provide a docker bridge on 192.168.70.129 and the AMF is on 192.168.70.132):

```c=
amf_ip_address      = ( { ipv4       = "127.0.0.5";    #Open5GS
                          ipv6       = "192:168:30::17";
                          active     = "yes";
                          preference = "ipv4";
                            }
                          );
```
```c=
NETWORK_INTERFACES :
      {
        GNB_INTERFACE_NAME_FOR_NG_AMF            = "lo";
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "127.0.0.17";
        GNB_INTERFACE_NAME_FOR_NGU               = "lo";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "127.0.0.18";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
     };
```

| Data Structure     | Item                        | Value            |
|:------------------ |:--------------------------- |:---------------- |
| amf_ip_address     | ipv4                        | "127.0.0.5"      |
| NETWORK_INTERFACES | GNB_IPV4_ADDRESS_FOR_NG_AMF | "192.168.70.129" |
|                    | GNB_IPV4_ADDRESS_FOR_NGU    | "192.168.70.132" |
|                    | GNB_PORT_FOR_S1U            | 2152             |
| None               | tr_s_preference             | "f1"             |
| None               | local_s_address             | "127.0.0.3"      |
| None               | remote_s_address            | "0.0.0.0"        |




- Set `gNBs.[0].tr_s_preference` (transport south-bound) to f1
- Update the `gNBs.[0].local_s_address` (CU-local south address) for the F1-C south-bound interface: 127.0.0.3
- Note: the `gNBs.[0].remote_s_address` (CU-remote/DU south address) is ignored, but we recommend to put 0.0.0.0 ("any")
- Ports should match the ones in the DU config, but for simplicity and standards-conformity, simply set all to 2152:
    - local_s_portc in CU should match remote_n_portc in DU
    - local_s_portd in CU should match remote_n_portd in DU
    - remote_s_portc in CU should match local_n_portc in DU
    - remote_s_portd in CU should match local_n_portd in DU
:::


### Step 1-4-2 DU file
**Path:**`/home/oai72/oai_split/openairinterface5g/ci-scripts/conf_files/gnb-du.sa.band78.106prb.rfsim.conf`
:::info

```c=
MACRLCs = (
  {
    num_cc               = 1;
    tr_s_preference      = "local_L1";
    tr_n_preference      = "f1";
    local_n_if_name      = "eth0";
    local_n_address      = "127.0.0.4";
    remote_n_address     = "127.0.0.3";
    local_n_portc        = 500;
    local_n_portd        = 2153;
    remote_n_portc       = 501;
    remote_n_portd       = 2153;
    pusch_TargetSNRx10   = 200;
    pucch_TargetSNRx10   = 200;
  }
);
```
| Data Structure | Item            | Value       |
| -------------- | --------------- |:----------- |
| MACRLCs        | tr_n_preference | "f1"        |
|                | local_n_address | "127.0.0.4" |
|                | remote_n_address| "127.0.0.3" |


- Set `MACRLCs.[0].tr_n_preference` to `f1`
- Update `MACRLCs.[0].local_n_address` (local north-bound address of the DU) to 127.0.0.4. This IP address is used to bind the GTP socket (F1-U user plane traffic).
- Update `MACRLCs.[].remote_n_address` (remote north-bound address of the CU) to 127.0.0.3. This IP address is used as the CU destination IP address for F1AP communication.
- Note: all `local_*_if_name` parameters are ignored.
:::


## Before running something U need to know
As mentioned earlier, OAI uses F1 internally. It is always compiled in. To start CU/DU, you use ==./nr-softmodem== with the appropriate configuration files, for instance:


These files are tested in the CI, and are configured for use in docker,see this `docker-compose` file.

The rules to decide if a config triggers a start of a DU, CU, or monolithic gNB, are, in order:

1. If the `MACRLCs` section lists `f1` as **northbound transport** preference(`tr_n_preference`), it is a DU.
2. If the `gNBs` section lists `f1` as a **southound transport** preference(`tr_s_preference`), it is a CU.
3. It is a (monolithic) gNB.
## Step 1-5 RU prepare
:::success
LiteOn RU

IP : 192.168.8.10
console user name/password : root/root
ssh user name/password : user/user
enable password : liteon168
:::

### Use Mobaxterm
![image](https://hackmd.io/_uploads/r1GEadqRa.png)

### Root Access
- **login/password : root/root**
- ==**login/password : user/user**==

![image](https://hackmd.io/_uploads/Hyl6gY5Ca.png)


### Set the PRACH port and slot mapping

**Password:** ==liteon168==
```c=
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
![image](https://hackmd.io/_uploads/ryciHY506.png)

```
# configure terminal
(config)# set_devmem 0x80001014 32 0x00050004
(config)# set_devmem 0x80001018 32 0x00070006
(config)# set_devmem 0x8000201C 32 0x00000001


set_devmem 0x80001014 32 0x00050004
set_devmem 0x80001018 32 0x00070006
set_devmem 0x8000201C 32 0x00000001
```

## ===== Simulation Experiment =====

## Use analog RF and analog UE

- Bring up CN
```c=
sudo ./createtun.sh # not persistent after rebooting
./start_open5gs.sh
```
**Path**:`~/oai_split/openairinterface5g`
```c=
cd ~/oai_split/openairinterface5g/cmake_targets/ran_build/build/
```

- **terminal 1** : Launch the CU with RFSIMULATOR
```c=
sudo RFSIMULATOR=server ./nr-softmodem --rfsim --sa -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/cu_gnb.conf

```


- **terminal 2** : Launch the DU with RFSIMULATOR
```c=
sudo RFSIMULATOR=server ./nr-softmodem --rfsim --sa -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/du_gnb.conf

```

- **terminal 3** : Launch the UE SIMULATOR:
```c=
sudo RFSIMULATOR=127.0.0.1 ./nr-uesoftmodem -r 106 --numerology 1 --band 78 -C 3619200000 --rfsim --sa --nokrnmod -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/ue.conf

```

### Result
![image](https://hackmd.io/_uploads/By7gMC7JR.png)

### log
- [log_CU](https://drive.google.com/file/d/10dPwiJCc5qtz-GeH4KmWqafcTvHwmVmu/view?usp=sharing)
- [log_DU](https://drive.google.com/file/d/1LE2gf6WM6f0pO1M6JBabwzji8YT_rfIo/view?usp=sharing)
- [log_UE](https://drive.google.com/file/d/1qhV1C9tkJidY8sxN6v3brFtXmy8RWDmo/view?usp=sharing)


## ===== CU/DU+Liteon RU Experiment =====
:::success
**Status:** Liteon RU can connent with OAI CU/DU split
:::
## Use Liteon RU and NEMO
- setup maximum performance, vf, and environment(reboot)

```c=
source /home/oai72/Script/oaiLOvf.sh
```
- Bring up CN
```c=
sudo ./createtun.sh # not persistent after rebooting
./start_open5gs.sh
```
- Shut down CN(free5gc)
```c=
sudo ./force_kill.sh
```
**Path**:`~/oai_split/openairinterface5g`
```c=
cd ~/oai_split/openairinterface5g
```
- **terminal 1:** CU
    - [cu_config_file](https://drive.google.com/file/d/151-ZS099chQkyqnhXdgb7tmJFMSP5rKY/view?usp=sharing)
```c=
sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/test_conf/cu_gnb.conf  --thread-pool 1,3,5,7
```
- **terminal 2:** DU
    - [du_config_file](https://drive.google.com/file/d/17xrv9ciBHA3SeFXjnFJdv6qzhoMIusT5/view?usp=sharing)
```c=
sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/test_conf/du_gnb.conf --thread-pool 9,11,13,15
```

- iperf
```c=
#iperf

iperf3 -c 10.60.0.1 -u -l 1300 -b 100m -i 1 -t 10 -R -p 5201
    
iperf3 -c 10.45.0.5 -u -l 1300 -b 17.5m -i 1 -t 10 -p 5201

//多核心
iperf3 -c 10.45.0.5 -u -l 1300 -b 17.5m -i 1 -t 10 -p 5201 -P 4
```
>Note:
>>UL: OAI <- UE
>DL: OAI -> UE
>由於我們使用UE當server，OAI當client
>因此正常情況是DL -R是UL
>上行看核網
>下行看UE
>
>>UL: OAI <- UE
>DL: OAI -> UE
>由於我們使用OAI當server，UE當client
>因此正常情況是UL -R是DL
>上行看核網
>下行看UE


### Result
:::success
![image](https://hackmd.io/_uploads/SJHcRtMxA.png)


:::

### log
- [CU_log](https://drive.google.com/file/d/1je4tHtFQ8jT2xhHpnvY_L-Fvtp1mZ-94/view?usp=sharing)
```c=
 [0m [34m[F1AP]   CU Task Received SCTP_DATA_IND for instance 0: sending SCTP message via assoc_id 0
 [0m [34m[F1AP]   Calling handler with instance 0
 [0m [34m[F1AP]   CU_handle_INITIAL_UL_RRC_MESSAGE_TRANSFER
 [0m[NR_RRC]   Decoding CCCH: RNTI 2c56, payload_size 6
 [0m[NR_RRC]   Created new UE context: CU UE ID 1 DU UE ID 11350 (rnti: 2c56, random ue id ca94008df4000000)
 [0m[RRC]   activate SRB 1 of UE 1
 [0m[NR_RRC]   rrc_gNB_generate_RRCSetup for RNTI 2c56
 [0m [34m[F1AP]   CU Task Received F1AP_DL_RRC_MESSAGE for instance 0: sending SCTP message via assoc_id 66244
 [0m [34m[F1AP]   CU send DL_RRC_MESSAGE_TRANSFER 
 [0m [34m[F1AP]   CU Task Received SCTP_DATA_IND for instance 0: sending SCTP message via assoc_id 0
 [0m [34m[F1AP]   Calling handler with instance 0
 [0m [34m[F1AP]   CU_handle_UL_RRC_MESSAGE_TRANSFER 
 [0m [34m[F1AP]   UL RRC MESSAGE for srb_id 1 in DCCH 
 [0m [34m[F1AP]   Calling pdcp_data_ind for UE RNTI 1 srb_id 1 with size 86 (DCCH) 
 [0m [32m[NR_RRC]   UE 1 Processing NR_RRCSetupComplete from UE
 [0m[NR_RRC]   [FRAME 00000][gNB][MOD 00][RNTI 1] UE State = NR_RRC_CONNECTED 
 [0m[NGAP]   UE 1: Chose AMF 'open5gs-amf0' (assoc_id 66240) through selected PLMN Identity index 0 MCC 1 MNC 1
 [0m [34m[F1AP]   CU Task Received F1AP_DL_RRC_MESSAGE for instance 0: sending SCTP message via assoc_id 66244
 [0m [34m[F1AP]   CU send DL_RRC_MESSAGE_TRANSFER 
 [0m [34m[F1AP]   CU Task Received SCTP_DATA_IND for instance 0: sending SCTP message via assoc_id 0
 [0m [34m[F1AP]   Calling handler with instance 0
 [0m [34m[F1AP]   CU_handle_UL_RRC_MESSAGE_TRANSFER 
 [0m [34m[F1AP]   UL RRC MESSAGE for srb_id 1 in DCCH 
 [0m [34m[F1AP]   Calling pdcp_data_ind for UE RNTI 1 srb_id 1 with size 37 (DCCH) 
 [0m [34m[F1AP]   CU Task Received F1AP_DL_RRC_MESSAGE for instance 0: sending SCTP message via assoc_id 66244
 [0m [34m[F1AP]   CU send DL_RRC_MESSAGE_TRANSFER 
 [0m [34m[F1AP]   CU Task Received SCTP_DATA_IND for instance 0: sending SCTP message via assoc_id 0
 [0m [34m[F1AP]   Calling handler with instance 0
 [0m [34m[F1AP]   CU_handle_UL_RRC_MESSAGE_TRANSFER 
 [0m [34m[F1AP]   UL RRC MESSAGE for srb_id 1 in DCCH 
 [0m [34m[F1AP]   Calling pdcp_data_ind for UE RNTI 1 srb_id 1 with size 77 (DCCH) 
 [0m [34m[F1AP]   CU Task Received F1AP_DL_RRC_MESSAGE for instance 0: sending SCTP message via assoc_id 66244
 [0m [34m[F1AP]   CU send DL_RRC_MESSAGE_TRANSFER 
 [0m[NR_RRC]   [FRAME 00000][gNB][MOD 00][RNTI 1] Logical Channel DL-DCCH, Generate RRCRelease (bytes 3)
 [0m [34m[F1AP]   CU Task Received F1AP_UE_CONTEXT_RELEASE_CMD for instance 0: sending SCTP message via assoc_id 66244
 [0m [34m[F1AP]   CU Task Received SCTP_DATA_IND for instance 0: sending SCTP message via assoc_id 0
 [0m [34m[F1AP]   Calling handler with instance 0
 [0m[NR_RRC]   removed UE CU UE ID 1/RNTI 2c56 
 [0m[NR_RRC]   Removed UE context
 [0m [93m[SCTP]   Received SCTP SHUTDOWN EVENT
 [0m [34m[F1AP]   CU Task Received SCTP_NEW_ASSOCIATION_RESP for instance 0: sending SCTP message via assoc_id 0
 [0m[F1AP]   Received SCTP shutdown for assoc_id 66244, removing endpoint
 [0m[RRC]   releasing DU ID 3584 (gNB-Eurecom-DU) on assoc_id 66244
 [0m
```
- [DU_log](https://drive.google.com/file/d/1MQSgVoWIgg7kqDWWSOF44CGgcL1KJ_bE/view?usp=sharing)
```c=
 [0m[NR_PHY]   [o_du0][pusch0 5806080 prach0  193536]
 [0m[NR_PHY]   [o_du0][pusch1 5806080 prach1  193536]
 [0m[NR_PHY]   [o_du0][pusch2 5806080 prach2  193536]
 [0m[NR_PHY]   [o_du0][pusch3 5806080 prach3  193536]
 [0m[NR_MAC]   Frame.Slot 0.0

 [0m[NR_PHY]   [o-du 0][rx 24379392 pps  380928 kbps 3209668][tx 8159412 pps  127488 kbps 4214777][Total Msgs_Rcvd 24379392]
 [0m[NR_PHY]   [o_du0][pusch0 5898240 prach0  196608]
 [0m[NR_PHY]   [o_du0][pusch1 5898240 prach1  196608]
 [0m[NR_PHY]   [o_du0][pusch2 5898240 prach2  196608]
 [0m[NR_PHY]   [o_du0][pusch3 5898240 prach3  196608]
 [0m[PHY]   prach_I0 = 27.3 dB
 [0m[NR_MAC]   Frame.Slot 128.0

 [0m[NR_PHY]   [o-du 0][rx 24760320 pps  380928 kbps 3209582][tx 8286900 pps  127488 kbps 4214777][Total Msgs_Rcvd 24760320]
 [0m[NR_PHY]   [o_du0][pusch0 5990400 prach0  199680]
 [0m[NR_PHY]   [o_du0][pusch1 5990400 prach1  199680]
 [0m[NR_PHY]   [o_du0][pusch2 5990400 prach2  199680]
 [0m[NR_PHY]   [o_du0][pusch3 5990400 prach3  199680]
 [0m[NR_MAC]   Frame.Slot 256.0

 [0m[NR_PHY]   [o-du 0][rx 25141248 pps  380928 kbps 3209582][tx 8414388 pps  127488 kbps 4214777][Total Msgs_Rcvd 25141248]
 [0m[NR_PHY]   [o_du0][pusch0 6082560 prach0  202752]
 [0m[NR_PHY]   [o_du0][pusch1 6082560 prach1  202752]
 [0m[NR_PHY]   [o_du0][pusch2 6082560 prach2  202752]
 [0m[NR_PHY]   [o_du0][pusch3 6082560 prach3  202752]
 [0m[NR_MAC]   Frame.Slot 384.0

 [0m[NR_PHY]   [o-du 0][rx 25522176 pps  380928 kbps 3209582][tx 8541876 pps  127488 kbps 4214777][Total Msgs_Rcvd 25522176]
 [0m[NR_PHY]   [o_du0][pusch0 6174720 prach0  205824]
 [0m[NR_PHY]   [o_du0][pusch1 6174720 prach1  205824]
 [0m[NR_PHY]   [o_du0][pusch2 6174720 prach2  205824]
 [0m[NR_PHY]   [o_du0][pusch3 6174720 prach3  205824]
 [0m[NR_MAC]   Frame.Slot 512.0

 [0m[NR_PHY]   [o-du 0][rx 25903104 pps  380928 kbps 3209625][tx 8669364 pps  127488 kbps 4214777][Total Msgs_Rcvd 25903104]
 [0m[NR_PHY]   [o_du0][pusch0 6266880 prach0  208896]
 [0m[NR_PHY]   [o_du0][pusch1 6266880 prach1  208896]
 [0m[NR_PHY]   [o_du0][pusch2 6266880 prach2  208896]
 [0m[NR_PHY]   [o_du0][pusch3 6266880 prach3  208896]
 [0m
** Caught SIGTERM, shutting down
[PHY]   Killing gNB 0 processing threads
 [0m[PHY]   Stopping RU 0 processing threads
 [0m [93m[PHY]   No stop_rf() for RU 0 defined, cannot stop RF!
 [0m[PHY]   Freeing RU signal buffers (if_south IF4p5 RRU) nb_tx 4
 [0mfirst_call set from phy cb first_call_set=0x7f063c046711
Returned from ITTI signal handler
oai_exit=1
Exiting ru_thread 
ORAN: trx_oran_end
fh_rx_bbdev-2 worker thread finished on core 2 [worker id 0]
Closing timing source thread...
Bye.
```
## Quick test
- RU
==**login/password : user/user**==
```c=
enable
```
```c=
show oru-status
```
```c=
# configure terminal
(config)# set_devmem 0x80001014 32 0x00050004
(config)# set_devmem 0x80001018 32 0x00070006
(config)# set_devmem 0x8000201C 32 0x00000001

set_devmem 0x80001014 32 0x00050004
set_devmem 0x80001018 32 0x00070006
set_devmem 0x8000201C 32 0x00000001
```

```c=
source /home/oai72/Script/oaiLOvf.sh
```
- Bring up CN
```c=
sudo ./createtun.sh # not persistent after rebooting
./start_open5gs.sh
```
**Path**:`~/oai_split/openairinterface5g`
```c=
cd ~/oai_split/openairinterface5g
```

- **terminal 1:** CU
    - [cu_config_file](https://drive.google.com/file/d/151-ZS099chQkyqnhXdgb7tmJFMSP5rKY/view?usp=sharing)
```c=
sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/test_conf/cu_gnb.conf
```
- **terminal 2:** DU
    - [du_config_file](https://drive.google.com/file/d/17xrv9ciBHA3SeFXjnFJdv6qzhoMIusT5/view?usp=sharing)
```c=
sudo cmake_targets/ran_build/build/nr-softmodem  --sa -O ci-scripts/conf_files/test_conf/du_gnb.conf
```


## Issue
### Issue 1 (Already solved)
**Solution: 
Pay attention to whether the ==core network== configuration is set correctly**
:::danger
**Running error log**
```c=
[NR_RRC]   Entering main loop of NR_RRC message task
[ITTI]   Created Posix thread TASK_RRC_GNB
[LIBCONFIG] (root): 2/2 parameters successfully set, (0 to default value)
[LIBCONFIG] gNBs.[0]: 30/30 parameters successfully set, (16 to default value)
[LIBCONFIG] gNBs.[0].plmn_list.[0]: 3/3 parameters successfully set, (0 to default value)
[LIBCONFIG] gNBs.[0].plmn_list.[0].snssaiList.[0]: 2/2 parameters successfully set, (0 to default value)
[GTPU]   Configuring GTPu
[LIBCONFIG] (root): 2/2 parameters successfully set, (0 to default value)
[LIBCONFIG] gNBs.[0].NETWORK_INTERFACES: 10/10 parameters successfully set, (3 to default value)
[GTPU]   SA mode 
[GTPU]   Configuring GTPu address : 192.168.70.132, port : 2152
[GTPU]   Initializing UDP for local address 192.168.70.132 with port 2152
    
///////////////////////////////////////////////    
[GTPU]   bind: Cannot assign requested address
[GTPU]   failed to bind socket: 192.168.70.132 2152 
[GTPU]   can't create GTP-U instance
[GTPU]   Created gtpu instance id: -1
[E1AP]   Failed to create CUUP N3 UDP listener[NR_RRC]   Accepting new CU-UP ID 3584 name cu-rfsim (assoc_id -1)
///////////////////////////////////////////////    

[UTIL]   Creating thread TASK_GTPV1_U with affinity -1 and priority 50
[UTIL]   threadCreate for TASK_GTPV1_U, affinity ffffffff, priority 50
[ITTI]   Created Posix thread TASK_GTPV1_U
```
:::
### Issue 2 (Already solved)
:::success
- Solution add this file to your DU configuration

```c=
fhi_72 = {
  dpdk_devices = ("0000:3b:0a.0", "0000:3b:0a.1");
  system_core = 0;
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
:::

:::danger
```c=
Waiting for gNB L1 instances to all get configured ... sleeping 50ms (nb_nr_sL1_inst 1)
gNB L1 are configured
About to Init RU threads RC.nb_RU:1
Initializing RU threads
configuring RU from file
[LIBCONFIG] RUs.[0]: 43/43 parameters successfully set, (27 to default value)
[LIBCONFIG] (root): 2/2 parameters successfully set, (0 to default value)
[LIBCONFIG] gNBs.[0]: 30/30 parameters successfully set, (21 to default value)
Set RU mask to 1
Creating RC.ru[0]:0x427d520
RU 0: Transport raw_if4p5
[RU 0] Setting nr_flag 0, nr_band 78, nr_scs_for_raster 1
[RU 0] Setting half-slot parallelization to 1
[LIBCONFIG] device.recplay: 8/8 parameters successfully set, (8 to default value)
[LIBCONFIG] device: 1/1 parameters successfully set, (1 to default value)
[LIBCONFIG] loader: 2/2 parameters successfully set, (2 to default value)
[LIBCONFIG] loader.oai_transpro: 2/2 parameters successfully set, (1 to default value)
shlib_path liboai_transpro.so
[LOADER] library liboai_transpro.so successfully loaded
ORAN: transport_init
[LIBCONFIG] list fhi_72 not found in config file ci-scripts/conf_files/test_conf/du_gnb.conf 
Configuration section "fhi_72" not present: cannot initialize fhi_lib!
could not read FHI 7.2/ORAN config

```
:::