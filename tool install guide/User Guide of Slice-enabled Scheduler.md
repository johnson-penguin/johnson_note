---
title: User Guide of Slice-enabled Scheduler
tags: [demo, BMW Lab., '']

---

>[name=Dennis][time=Sun, Aug 6, 2023 2:36 PM]

[ToC]




## A. Installation

### I. Directory Structure

```bash
l2
|__ build #contains files required to compile the code
|   |__ common #contains individual module's makefile
|   |__ config #contains main makefile to generate an 
|   |__ odu #contains scripts for logging, installing executable binary
|   |__ scripts #contains the configuration files netconf libraries and starting netopeer server
|   |__ yang #contains the YANG modules
|   |__ Makefile
|__ docs #contains README and other configuration files for building docs
|__ releases
|__ src #contains layer specific source code
|   |__ 5gnrmac : #MAC source code
|   |__ 5gnrrlc : #RLC source code
|   |__ cm : #common, environment and interface files
|   |__ cu_stub : #Stub code for CU
|   |__ du_app : #DU application and F1 code 
|   |__ mt : #wrapper functions over OS
|   |__ phy_stub : #Stub code for Physical layer
|   |__ rlog : #logging module
|   |__ O1 : #O1 module
|__ test
|__ tools
```

### II. Pre-requisite for Compilation

1. Linux 32-bit/64-bit machine
2. GCC version 4.6.3 and above
3. Install LKSCTP
   - On Ubuntu : 
        ```bash
        sudo apt-get install -y libsctp-dev
        ```
   - On CentOS : 
        ```bash
        sudo yum install -y lksctp-tools-devel
        ```
4. Install PCAP:
   - On ubuntu : 
        ```bash
        sudo apt-get install -y libpcap-dev
        ```
   - On CentOS : 
        ```bash
        sudo yum install -y libpcap-devel
        ```


### III. How to Clean and Build:
- **If you just wanna build the odu quickly, please go to [6. Quick Build](#6-Quick-Build)**

#### 1. Build commands:
- ```odu```       - Builds all components of ODU
- ```cu_stub```   - Builds all CU Stub
- ```ric_stub```  - Builds all RIC_Stub
- ```clean_odu``` - clean up ODU
- ```clean_cu```  - clean up CU Stub
- ```clean_ric``` - clean up RIC Stub
- ```clean_all``` - cleanup everything

##### **Options**:
- ```MACHINE=BIT64/BIT32``` - Specify underlying machine type. Default is BIT32
- ```NODE=TEST_STUB```      - Specify if it is a test node. Mandatory for cu_stub/ric_stub. Must not be used for odu
- ```MODE=FDD/TDD```        - Specify duplex mode. Default is FDD
- ```PHY=INTEL_L1```        - Specify type of phy. If not specified, PHY stub is used
- ```PHY_MODE=TIMER```      - Specify mode of phy. Used only if PHY=INTEL_L1. Default is radio mode
- ```O1_ENABLE=YES```       - Specify if O1 interface is enabled. If not specified, it is disabled 

#### 2. Building ODU binary:
1. Change to building directory
    ```bash
    cd l2/build/odu
    ```
2. Building ODU binary
   
    Follow the [Options](#Options) commands in [Section III.1](#1-Build-commands)
    ```bash
    make odu MACHINE=...  MODE=...
    ```
3. Cleaning ODU binary
   
    Follow the [Options](#Options) commands in [Section III.1](#1-Build-commands)
    ```bash
    make clean_odu
    ```

#### 3. Building CU Stub binary:
1. Build folder
    ```bash
    cd l2/build/odu
    ```
2. Building CU Stub binary
   
    Follow the [Options](#Options) commands in [Section III.1](#1-Build-commands)
    ```bash
    make cu_stub NODE=... MACHINE=... MODE=...
    ```
3. Cleaning CU Stub binary
   
    Follow the [Options](#Options) commands in [Section III.1](#1-Build-commands)
    ```bash
    make clean_cu
    ```

#### 4. Building RIC Stub binary:
1. Build folder
    ```bash
    cd l2/build/odu
    ```
2. Building RIC Stub binary
   
    Follow the [Options](#Options) commands in [Section III.1](#1-Build-commands)
    ```bash
    make ric_stub NODE=... MACHINE=... MODE=...
    ```
3. Cleaning RIC Stub binary

    Follow the [Options](#Options) commands in [Section III.1](#1-Build-commands)
    ```bash
    make clean_ric
    ```

#### 5. Cleaning ODU, CU Stub and RIC Stub:
```bash
make clean_all
```

#### 6. Quick Build
Once you want to re-compile the code, just run the command below.
```bash=
cd l2/build/odu
make clean_all
make odu MACHINE=BIT64 MODE=FDD
make cu_stub MACHINE=BIT64 NODE=TEST_STUB MODE=FDD
make ric_stub MACHINE=BIT64 NODE=TEST_STUB MODE=FDD
```
## B. Execution
### 1. Assign virtual IP addresses as follows:
- **These commands should be run whenever rebooting**

```bash=
sudo ifconfig <interface name>:ODU "192.168.130.81"
sudo ifconfig <interface name>:CU_STUB "192.168.130.82"
sudo ifconfig <interface name>:RIC_STUB "192.168.130.80"



sudo ifconfig lo:ODU 192.168.130.81
sudo ifconfig lo:CU_STUB 192.168.130.82
sudo ifconfig lo:RIC_STUB 192.168.130.80
```
> You can assign the virtual IP in `lo` interface

### 2. Execute CU Stub:
```bash=
cd l2/bin/cu_stub
sudo ./cu_stub
```


### 3. Execute RIC Stub:
```bash=
cd l2/bin/ric_stub
sudo ./ric_stub
```

### 4.1 Execute DU:
```bash=
cd l2/bin/odu
sudo ./odu
```

### 4.2 After startup, save the log to the specified path
```bash=
sudo ./odu > ~/log/20230817_1741.log 
```

>
**PS**:
- CU stub and RIC stub must be run (in no particular sequence) before ODU
- If you encounter the problem, try to add the `sudo` to execute

### 5. Generate DL Traffic
Once the UE attach is finished, press `d` in CU Stub window to generate the DL traffic



## C. Adjust the slice configuration
### 1. Adjust the number of slices
- In `du_cfg.h`:
    ```c=300
    #define NUM_OF_SUPPORTED_SLICE  3
    ```

### 2. Adjust the slices which PLMN supports 
-  In `du_cfg.c`:
Adjust the S-NSSAI for each supported slices
```c=649
#ifndef O1_ENABLE
   /* Note: Added these below variable for local testing*/
   Snssai snssai[NUM_OF_SUPPORTED_SLICE] = {{1,{2,3,4}},{2,{3,3,4}},{3,{4,3,4}},{4,{5,3,4}}};
#endif 
```
> S-NSSAI consists of the SST and SD. This step is to configure the SST and SD of each slices. But it is just for local testing.


### 3. Adjust the default RRMPolicyRatio for each slice
- In `du_cfg.c`:
    - Adjust the RRMPolicyRatio based on the rule of RRMPolicyRatio
    -  SST and SD should be same as the step 2 you set
    -  Add / Remove the RRMPolicyRatio according to your requirement
```c=326
   /*Note: Static Configuration, when O1 is not configuring the RRM policy*/
   RrmPolicyList rrmPolicy[NUM_OF_SUPPORTED_SLICE];
   rrmPolicy[0].id[0] = 1;
   rrmPolicy[0].resourceType = PRB;
   rrmPolicy[0].rRMMemberNum = 1;
   memcpy(rrmPolicy[0].rRMPolicyMemberList[0].mcc,duCfgParam.macCellCfg.cellCfg.plmnInfoList[0].plmn.mcc, 3*sizeof(uint8_t));
   memcpy(rrmPolicy[0].rRMPolicyMemberList[0].mnc,duCfgParam.macCellCfg.cellCfg.plmnInfoList[0].plmn.mnc, 3*sizeof(uint8_t));
   rrmPolicy[0].rRMPolicyMemberList[0].sst = 1;
   rrmPolicy[0].rRMPolicyMemberList[0].sd[0] = 2;
   rrmPolicy[0].rRMPolicyMemberList[0].sd[1] = 3;
   rrmPolicy[0].rRMPolicyMemberList[0].sd[2] = 4;
   rrmPolicy[0].rRMPolicyMaxRatio = 100;
   rrmPolicy[0].rRMPolicyMinRatio = 50;
   rrmPolicy[0].rRMPolicyDedicatedRatio = 10;

   rrmPolicy[1].id[0] = 2;
   rrmPolicy[1].resourceType = PRB;
   rrmPolicy[1].rRMMemberNum = 1;
   memcpy(rrmPolicy[1].rRMPolicyMemberList[0].mcc,duCfgParam.macCellCfg.cellCfg.plmnInfoList[0].plmn.mcc, 3*sizeof(uint8_t));
   memcpy(rrmPolicy[1].rRMPolicyMemberList[0].mnc,duCfgParam.macCellCfg.cellCfg.plmnInfoList[0].plmn.mnc, 3*sizeof(uint8_t));
   rrmPolicy[1].rRMPolicyMemberList[0].sst = 2;
   rrmPolicy[1].rRMPolicyMemberList[0].sd[0] = 3;
   rrmPolicy[1].rRMPolicyMemberList[0].sd[1] = 3;
   rrmPolicy[1].rRMPolicyMemberList[0].sd[2] = 4;
   rrmPolicy[1].rRMPolicyMaxRatio = 100;
   rrmPolicy[1].rRMPolicyMinRatio = 50;
   rrmPolicy[1].rRMPolicyDedicatedRatio = 10;
```

### 4. Adjust the traffic of each slice
Because the slice is associated with the DRB, we should know how the DRB is associated with the slice in the code: 

- In `cu_f1ap_msg_hdl.c`:
    - `idx` is the cnt which is traversing the DRB
    - In this code, we can know that the slice which is associated with would be the `DRB ID % Number of Slices`
    - We don't need to change this rule.
```c=3012
      /*SNSSAI*/
      snssaiIdx = (idx% cuCb.numSnssaiSupported);
```

Adjust the number of DRB to generate the traffic of each slice

- In `cu_f1ap_msg_hdl.h`:
    - In this case, we create 3 DRBs for each UE. If we configure the 3 slices, then each DRB is associated with the distinct slice
```c=37
#define MAX_DRB_SET_UE_CONTEXT_SETUP_REQ 3  /*Number of DRBs to be added using UE CONTEXT SETUP procedure*/
```


## D. Adjust the traffic in CU STUB
![](https://hackmd.io/_uploads/B1Ej9Fch3.png)
- `Number of Transmission`: Specify the number of transmission in one traffic generating
- `Number of Packet`: Specify the number of packet in one transmission
- `Data Size`: Specify the data size of one packet
- `Generating Time Interval`: If CU STUB generates the infinite continuous data transfer, this parameter determines the time interval between traffic generating



In this guide, the **infinite continuous data transfer** is adopted. However, generating the infinite data makes the system unstable. We need to adjust the parameter **carefully**.
> [The problem we encounter while generating the infinite data](https://hackmd.io/jhWvRXOQSEiygElLGmxbdg?view#Problem-2-O-DU-is-crashed-when-generating-full-load-traffic)


Up to now, we observe some settings which is able to reduce the possibility of system crashed:
- The key is that **avoid to send a lot of packets at the same time**:
    - Set `Number of Transmission = 1` 
    - Increase the `Data Size`, reduce the `Number of Packet`
    - Reduce the `Generating Time Interval`

### 1. Generate continuous full load traffic
The default DL traffic is not full load. Hence, we should modify it to generate full load traffic first.

#### 1.1. Modify the traffic generating function
```c=
    while(cnt < NUM_DL_PACKETS)
    {
      ret =  cuEgtpDatReq(duId, teId);      
      if(ret != ROK)
      {
         DU_LOG("\nERROR --> EGTP: Issue with teid=%d\n",teId);
         break;
      }
      /* TODO : sleep(1) will be removed later once we will be able to
       * support the continuous data pack transfer */
      sleep(1);
      cnt++;
    }
```
    
### 2. Number of transmission

- File Name: `cu_stub.c`
- Function Name: `startDlData()`
```c=381
   int32_t totalNumOfTestFlow = 20; /* Default value is 20 */
```

### 3. Number of packet

### 4. Data size

### 5. Generating time interval

## E. Enable the multi-thread feature

- In `sch_slice_based.h`:
    - If we comment this line, odu runs with the single thread
    - If we don't comment this line, odu runs with the multi-thread
```c=19
#define SCH_MULTI_THREAD      /* Enable the multi-thread intra-slice scheduling feature */
```

> Note that enabling the multi-thread feature will cause the CPU usage higher, make sure your hardware is powerful enough (> 16 cores)
## F. Add a new scheduling algorithm