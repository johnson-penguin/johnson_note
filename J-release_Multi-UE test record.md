---
title: J-release_Multi-UE test record

---

[TOC]
# Summary
:::info
- [x] Install DU (J-release)
- [x] [Modify CU STUB TRAFFIC](#Step-1-Generate-continuous-full-load-traffic)
    - Number of Transmission: 1
    - Number of Packet: 2
    - Data Size: 1215
    - Generating Time Interval: 200ms
        - 1215 bytes x 2 x 1 x (1s/200ms) = 12150 bytes
- [x] [PRACH enable](#Step-2-Enable-RACH)(For Per TTI test)
- [x] [Verify DL sch per TTI](#DL-per-TTI)
- [x] [Verify UL sch per TTI](#UL-per-TTI)
:::
# Environment & Access
:::success
- Core number : 8
- RAM : 8G
- Storage : 150G
- OS version : Ubuntu 20.04

---

- IP : 192.168.8.38
- Name : o-du
- Password : bmwlab
```c=
ssh o-du@192.168.8.38
```
---
- I - smo
- IP : 192.168.8.6
- Name : ubuntu
- Password : bmwlab
```c=
ssh o-du@192.168.8.6
```
:::
# Environmental preparation
## Step 1. Clone O-DU
:::success
Clone form [Gerrit website](https://gerrit.o-ran-sc.org/r/admin/repos/o-du/l2,general)
```c=
git clone "https://gerrit.o-ran-sc.org/r/o-du/l2"
```
:::

---

## Step 2. Switch version to J-release
:::info
This command is a command in the Git version control system that is used to ==view all branches==, including local branches and remote branches.
```c=
git branch -a
```
![image](https://hackmd.io/_uploads/SJehSMKA6.png)

Switch to the version you need, in this note we need to test **Multi-UE** so we use ==J-release==
```c=
git checkout j-release
```
:::
## Step 3. Verify whether the version is successful
:::warning
You can use the following command to verify whether you have switched to the version you specified
```c=
git branch //verify
```

![image](https://hackmd.io/_uploads/BJ_ldftRT.png)

---

Now that you have the J-release environment of OSC DU, you can start testing Multi-UE or other updates of this version.
:::
## Step 4. Update to latest commit
```c=
git pull origin master
```
**ID:** [ JIRA Id - ODUHIGH-584 ] Fixing the error number issue CmInetSctpConnectx function

![image](https://hackmd.io/_uploads/SyF_LB7bC.png)



# Multi-UE test
## Step 1. Setting CU_STUB produces infinite traffic
- Modified File:`~/l2/src/cu_stub/cu_stub.c`
- Modified Function: `startDlData()`
```c=
while(1)
{
    startDlData();
    usleep(100000); /* Generating Time Interval */
}
```

## Step 2. Enable RACH
0 turn to 1 to enabel second UE RACH

- Modified File: `~/l2/src/phy_stub/phy_stub_msg_hdl.c`
- Modified Function: `l1HdlUlTtiReq()`
```c=
#if 1 
         /* Send RACH Ind to L2 for second UE */
         if(phyDb.ueDb.ueCb[UE_IDX_1].rachIndSent == false && phyDb.ueDb.ueCb[UE_IDX_0].msgRrcReconfigComp == true)
         {
            phyDb.ueDb.ueCb[UE_IDX_0].isCFRA = false;
            phyDb.ueDb.ueCb[UE_IDX_1].rachIndSent = true;
            l1BuildAndSendRachInd(ulTtiReq->slot, ulTtiReq->sfn, CB_RA_PREAMBLE_IDX);
            phyDb.ueDb.numActvUe++;
         }

         /* Send RACH Ind to L2 for third UE */
         if(phyDb.ueDb.ueCb[UE_IDX_2].rachIndSent == false && phyDb.ueDb.ueCb[UE_IDX_1].msgRrcReconfigComp == true)
         {
            phyDb.ueDb.ueCb[UE_IDX_0].isCFRA = false;
            phyDb.ueDb.ueCb[UE_IDX_2].rachIndSent = true;
            l1BuildAndSendRachInd(ulTtiReq->slot, ulTtiReq->sfn, CB_RA_PREAMBLE_IDX);
            phyDb.ueDb.numActvUe++;
```
## Step 3. DL per TTI
### Enable DL traffic
Modified File: ~/build/odu/makefile
```c=
PLTFRM_FLAGS=-UMSPD -DODU -DINTEL_FAPI -UODU_MEMORY_DEBUG_LOG -DDEBUG_ASN_PRINT -UDEBUG_PRINT -DERROR_PRINT -USTART_DL_UL_DATA -UNR_DRX -UCALL_FLOW_DEBUG_LOG -UODU_SLOT_IND_DEBUG_LOG -UNFAPI_ENABLED -UTHREAD_AFFINITY -UMEM_SIZE_CHECK 
```
### Adjust the totalNumOfTestFlow
- File Name: `cu_stub.c`
- Function Name: `startDlData()`
- To reduce the possibility of system crash, we set the Number of transmission to 1 to decrease the number of transmitting packet at the same time.
```c=
- int32_t totalNumOfTestFlow = 200; // before
+ int32_t totalNumOfTestFlow = 1;   // after
```
### Comment the the default generating time interval
- Modified File: cu_stub.c
- Modified Function: startDlData()
```c=
- sleep(1); before
+ // sleep(1); // after
```

![image](https://hackmd.io/_uploads/HJ7IFHmW0.png)
### Verify & Log result
Add this command to display which UEs are currently in the current slot.
- Modified File: `~/src/5gnrsch/sch_fcfs.c`
- Modified Function: `schFcfsScheduleSlot()`
```c=
printf("\nwilfrid johnson INFO   -->  SCH: retx UE ID = %d scheduled on sfn = %d slot = %d", ueId, slotInd->sfn, slotInd->slot);
```
![image](https://hackmd.io/_uploads/By5OjhQZA.png)


---

## Step 4. UL per TTI
### Enable UL traffic
Modified File: ~/build/odu/makefile

Command ==USTART_DL_UL_DATA== modified ==DSTART_DL_UL_DATA==
```c=
- PLTFRM_FLAGS=-UMSPD -DODU -DINTEL_FAPI -UODU_MEMORY_DEBUG_LOG -DDEBUG_ASN_PRINT -UDEBUG_PRINT -DERROR_PRINT -USTART_DL_UL_DATA -UNR_DRX -UCALL_FLOW_DEBUG_LOG -UODU_SLOT_IND_DEBUG_LOG -UNFAPI_ENABLED -UTHREAD_AFFINITY -UMEM_SIZE_CHECK 
+ PLTFRM_FLAGS=-UMSPD -DODU -DINTEL_FAPI -UODU_MEMORY_DEBUG_LOG -DDEBUG_ASN_PRINT -UDEBUG_PRINT -DERROR_PRINT -DSTART_DL_UL_DATA -UNR_DRX -UCALL_FLOW_DEBUG_LOG -UODU_SLOT_IND_DEBUG_LOG -UNFAPI_ENABLED -UTHREAD_AFFINITY -UMEM_SIZE_CHECK 
```
### UL traffic parameter
The below variable is taken for sending specific number of UL Packets.For sendind 4500 Ul packets for three UEs the calculation of `counter * NUM_DRB_TO_PUMP_DATA * MAX_NUM_UE * NUM_UL_PACKETS` must be equal to ==4500==

### Adjust the MAX_NUM_UE
- Modified File: `~/l2/src/cm/common_def.h`
- Maximum number of user equipment.
```c=
define MAX_NUM_UE = 3
```

### Adjust the counter
- Modified File: `~/l2/src/phy_stub/phy_stub_thread_hdl.c`
- Modified Function: `l1ConsoleHandler()`
- Counter used to count the number of packets to be sent.
```c=
uint32_t counter=75; //default 500
```
### Adjust the NUM_UL_PACKETS
- Modified File: `~/l2/src/phy_stub/phy_stub.h`
- The number of uplink data packets to be sent by each user equipment.
```c=
#define NUM_UL_PACKETS 5 //default 1
```
### Adjust the NUM_DRB_TO_PUMP_DATA
- Modified File: `~/l2/src/phy_stub/phy_stub.h`
- The number of data bearers that can be used by each user equipment (UE).
```c=
#define NUM_DRB_TO_PUMP_DATA  4 //default 2
```


---
### Verify & Log result

- CU
![image](https://hackmd.io/_uploads/SJORVUCFC.png)

- DU
![image](https://hackmd.io/_uploads/H17xrIAtC.png)
![image](https://hackmd.io/_uploads/Skbs3qli0.png)


## Compile
- These commands should be run whenever rebooting
```c=
sudo ifconfig lo:ODU "192.168.130.81"
sudo ifconfig lo:CU_STUB "192.168.130.82"
sudo ifconfig lo:RIC_STUB "192.168.130.80"
```

###  Quick Build
Once you want to re-compile the code, just run the command below.
- Compilation (O1 enable)
```bash=
cd l2/build/odu
make clean_all
make odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make ric_stub MACHINE=BIT64 NODE=TEST_STUB MODE=FDD
```
- Compilation
```bash=
cd l2/build/odu
make clean_all
make odu MACHINE=BIT64 MODE=FDD
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD
make ric_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD
```
### Execute CU Stub:
```bash=
cd l2/bin/cu_stub
sudo ./cu_stub
```


### Execute DU:
```bash=
cd l2/bin/odu
sudo ./odu
```

# issue list

:::danger
### Issue 1 : Tool version didn't match
:::
:::success
#### Soluation 1 : Go to this [github](https://github.com/CESNET/netopeer2/tags) check your netopeer2 version and whether it match other tool(libyang、libnetconf2、sysrepo) or not
![image](https://hackmd.io/_uploads/r1fAjt8uR.png)
- PATH : `~/l2/build/scripts`

![image](https://hackmd.io/_uploads/rymO2Y8OR.png)

![image](https://hackmd.io/_uploads/ByVi3FIOA.png)
:::

:::danger
### Issue 2 : 
![image](https://hackmd.io/_uploads/rylZ6Y8OR.png)
:::
:::success
#### Soluation 2
Use ubuntu 20.04 (18 & 22 will cause library or compile failed )
:::
:::danger
### Issue 3 : GPG Error
![image](https://hackmd.io/_uploads/S1SmEn6K0.png)
:::
:::success
#### Soluation 3
The update may fail due to insufficient capacity. Please try deleting unnecessary files.

```c=
sudo apt update
```
![image](https://hackmd.io/_uploads/r1J6XhpYC.png)

:::
:::danger
### Issue 4 :Connect to VM failed in vscode but it can be done manually
```c=
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: http://tw.archive.ubuntu.com/ubuntu focal InRelease: Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal_InRelease into data and signature failed
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: http://tw.archive.ubuntu.com/ubuntu focal-backports InRelease: Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal-backports_InRelease into data and signature failed      
W: Failed to fetch http://tw.archive.ubuntu.com/ubuntu/dists/focal/InRelease  Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal_InRelease into 
data and signature failed
W: Failed to fetch http://tw.archive.ubuntu.com/ubuntu/dists/focal-updates/InRelease  Error writing to output file - write (28: No space left on device) [IP: 140.110.240.80 80]
W: Failed to fetch http://tw.archive.ubuntu.com/ubuntu/dists/focal-backports/InRelease  Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal-backports_InRelease into data and signature failed
W: Failed to fetch http://security.ubuntu.com/ubuntu/dists/focal-security/InRelease  Error writing to output file - write (28: No space left on device) [IP: 185.125.190.83 80]
W: Some index files failed to download. They have been ignored, or old ones used instead.
```
:::
:::success
#### Soluation 4
![image](https://hackmd.io/_uploads/SyhVH2aFR.png)
>When you running this command get GPG Error please check [Issue 3](#Issue-3--GPG-Error)
:::

---
:::danger
### Issue5 : failed to load external entity "../build/config/fdd_odu_config.xml"41
:::
:::success
#### Soluation 5
- Modified File: `~/l2/src/du_app/du_cfg.c`
- Modified Function: `duReadCfg()`
DU High uses ==relative paths== for profile searches, so this issue will occur when there are other DUs on your path. Use ==absolute path== to solve the problem
```c=
- const char *filename = "../build/config/fdd_odu_config.xml";
+ const char *filename = "/home/johnson/Desktop/J-release/l2/build/config/fdd_odu_config.xml";
```
:::
---
:::danger
### Issue 6 : ERROR no harq stored in ul hq map at slot 8 ue id 2
It will crash after a short period of execution.

- After crash
![image](https://hackmd.io/_uploads/Byb5BOKA6.png)

[log link](https://drive.google.com/file/d/1T0sd6HW0cJBYQ6ffNsD2fR7KpSF9aLBc/view?usp=sharing)

:::

:::success
#### Soluation 6
[Updating to a new commit](https://hackmd.io/2mdwL25SS7mBHB3AHaComw?both#Step-4-Update-to-latest-commit) can resolve this issue
:::

:::danger
### Issue 7 : RACH for second didn't work during UL traffic testing
- RACH indication for UE_IDX_1 has not been sent yet
- Reconfiguration message for UE_IDX_0 ==not completed==

UE2 will be added to the multi_UE_per_TTI test environment when the RACH indication for UE_IDX_1 has not yet been sent and the reconfiguration message for UE_IDX_0 ==has completed==.

The initial value( phyDb.ueDb.ueCb[UE_IDX_0].msgRrcReconfigComp ) may be accidentally modified before the test starts and may result in behavior that is not as expected ( reconfiguration message for UE_IDX_0 completed ).

![image](https://hackmd.io/_uploads/S1_58_n5R.png)

:::
#### Soluation 7
:::success
[Adjust the totalNumOfTestFlow](#Adjust-the-totalNumOfTestFlow)  to the smaller point.
>TotalNumOfTestFlow : Specify the number of transmissions for one traffic generation

When DL totalNumOfTestFlow decreases from 200 to 1, the system releases a large amount of resources so that the UL flow can receive more resources. In this way, the RACH indication can be sent normally to observe that both UEs can be scheduled at the same time.
:::















--------