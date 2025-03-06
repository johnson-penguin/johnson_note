---
title: Install pppoe for tm500 traffic test guide

---

![](https://i.imgur.com/JORnn3y.png =150x)@NTUST
# Install pppoe for tm500 traffic test guide
:::success
**🎯 Readme:** 
1. Since the TM500 UE sim cannot generate traffic on its own, the traffic currently being tested is presumed to be only C-plane. Viavi suggested that we set up pppoe to facilitate traffic testing of TM500 UE
2. If you encounter an error you can refer to the [Troubleshooting note](##Troubleshooting-note).
3. The maximum number of connectable UEs is currently 32
:bookmark: **Reference:**
- [pppoe撥號.pdf](https://drive.google.com/file/d/1Mb4x9-9UAF_kVYSKT0X-IgyBV5IaPkG9/view?usp=drive_link)
- [TM500LTE_PPPoE_Multi-UE_Data_47090_113.pdf](https://drive.google.com/file/d/1j8GDFW_kR0IvswlFJziNGeC_qqhaYtoY/view?usp=drive_link)
- [pppoe Install guide](https://hackmd.io/@Johnson-72/SJhDdM7GJx)
:::

:::info
#### :memo: **Outline:**
[TOC]
:::

## Topology

![image](https://hackmd.io/_uploads/HJt_dKBX1e.png)

![image](https://hackmd.io/_uploads/SJiSf0UFJl.png)



## Environment
### gNB
**Hardware:**
|     Item     | Info                                                               |
|:------------:|:------------------------------------------------------------------ |
|     CPU      | Intel® Xeon® Gold 6226R CPU @ 2.90GHz (16 cores, disable HT) x 2   |
|    Memory    | 128GB (64GB x 2)                                                   |
|     Disk     | 600GB                                                              |
|     NIC      | Intel X710-DA2 (i40e,8.30 0x8000a4db) (Bus-Info: 3b:00.0, 3b:00.1) |
| Server Model | Dell PowerEdge R740                                                |

**Software:**
|  **Item**  | **Info**                                         |
|:----------:|:------------------------------------------------ |
|     OS     | Red Hat Enterprise Linux release 9.2 (Plow)      |
|   Kernel   | Linux R740 5.14.0-284.18.1.rt14.303.el9_2.x86_64 |
|    DPDK    | 20.11.8                                          |
|  LinuxPTP  | 3.1.1                                            |
| gNB Branch | develop                                          |



## Current Status
> :heavy_check_mark: : Complete,  :Hourglass: : On-going,  :x: : Incomplete

- TM500 & OAI DU

| Item                                                                     | Status             |
| ------------------------------------------------------------------------ | ------------------ |
| [Bring up CN](#2-Core-Network)                                           | :heavy_check_mark: |
| [PTP sync of gNB](#Step-33-gNB-PTP-synchronization)                      | :heavy_check_mark: |
| [Bring up gNB](#Step-34-Run-gNB)                                         | :heavy_check_mark: |
| [NG Setup](#Step-35-Check-CN-connection-with-DU)                         | :heavy_check_mark: |
| [TM500 get on time packet from DU](#Step-41-Check-DU-connection-at-RU-side) | :heavy_check_mark: |
| [DU get FH packet from TM500](#Step-42-Check-RU-connection-at-DU-side)      | :heavy_check_mark: |
| [TM500 get mib and sib1 for DL sync](#UE-get-mib-and-sib1-for-DL-syn)       | :heavy_check_mark: |
| [TM500 send preamble](#UE-send-preamble)                                    | :heavy_check_mark: |
| [DU get preamble](#DU-get-preamble)                                      | :heavy_check_mark: |
| [DU send RACH response(msg2)](#DU-send-RACH-responsemsg2)                | :heavy_check_mark: |
| [UE get RACH response(msg2)](#UE-get-RACH-responsemsg2)                  | :heavy_check_mark: |
| [UE send RRC setup request(msg3)](#UE-send-RRC-setup-requestmsg3)        | :heavy_check_mark: |
| [DU get RRC setup request(msg3)](#DU-get-RRC-setup-requestmsg3)          | :heavy_check_mark: |
| [DU send RRC Set up(msg4)](#DU-send-RRC-Set-upmsg4)                      | :heavy_check_mark: |
| [TM500 get RRC Set up(msg4)](#UE-get-RRC-Set-upmsg4)                        | :heavy_check_mark: |
| [RRC setup complete](#RRC-setup-complete)                                | :heavy_check_mark: |
| [NAS registration](#NAS-registration)                                    | :heavy_check_mark: |
| [NAS identity request](#NAS-identity-request)                            | :heavy_check_mark: |
| [NAS identity response](#NAS-identity-response)                          | :heavy_check_mark: |
| [Authenication request](#Authenication-request)                          | :heavy_check_mark: |
| [Authenication response](#Authenication-response)                        | :heavy_check_mark: |
| [NAS Security mode command](#NAS-Security-mode-command)                  | :heavy_check_mark: |
| [NAS Security mode complete](#NAS-Security-mode-complete)                | :heavy_check_mark: |
| [UECapabilityEnquiry](#UECapabilityEnquiry)                              | :heavy_check_mark: |
| [UECapabilityInformation](#UECapabilityInformation)                      | :heavy_check_mark: |
| [AS SecurityModeCommand](#AS-SecurityModeCommand)                        | :heavy_check_mark: |
| [AS SecurityModeComplete](#AS-SecurityModeComplete)                      | :heavy_check_mark: |
| [RRCReconfiguration](#RRCReconfiguration)                                | :heavy_check_mark: |
| [Registration accept](#Registration-accept)                              | :heavy_check_mark: |
| [RRCReconfigurationComplete](#RRCReconfigurationComplete)                | :heavy_check_mark: |
| [Registration complete](#Registration-complete)                          | :heavy_check_mark: |
| [PDU session establish request](#PDU-session-establish-request)          | :heavy_check_mark: |
| [PDU session establish accept](#PDU-session-establish-accept)            | :heavy_check_mark: |


![image](https://hackmd.io/_uploads/Hk1kTFr7ye.png)

- PPPoe
![image](https://hackmd.io/_uploads/ByYAbNTmyl.png)



- Control PC & TM500 UE PPPoE 


| Item                                           | Status             |
|:---------------------------------------------- |:------------------ |
| Download the chosen Linux distribution ISO     | :heavy_check_mark: |
| Configuring a Linux PC as the Data Test Device | :heavy_check_mark: |
| Create conf                                    | :heavy_check_mark: |
| Create configuration file                      | :heavy_check_mark: |
| Test Connection                                | :heavy_check_mark: |
| Running Client Application                     | :heavy_check_mark: |
| Disconnect stopped                             | :heavy_check_mark: |

## Access Method
:::success
#### **TM500 Server (Supermicro) :**
- IP Address : 192.168.8.67
- Username : viavi
- Password : viavi

**TM500 Control PC :**
- IP Address : 192.168.8.71
	- User Password : bmwee809
- Anydesk ID : 1549083559
	- Anydesk Password : bmwbmwbmwee809

**OAI gNB (Dell R740) :**
- IP Address : 192.168.8.29
	- Username : oai72
	- Password : bmwlab
	- Root Password : bmwlab
<!-- - IDRAC IP : 192.168.10.124
	- IDRAC user name : root
	- IDRAC Password : calvin -->
    
**Free5GC :**
- Host free5GC
    - HostName 192.168.8.21
    - User free5gc
:::

## Configuration
:::info

### TM500
Before using TM500 to connect your gNB (MTS mode), you should prepare the following documents in the specified path

PATH :
`\Desktop\viavi cloudue\NLA_7_4_0\TM500_NR_5G_EXT-MUE_Release_NLA_7_4_0_CloudUE\ppc_pq\public\ftp_root`

1. [o-ran.cfg](https://drive.google.com/file/d/11iyAWnskRLqBp0Qwsr3UEqtYoMP_D4Jx/view?usp=drive_link)
2. [oran_oai_tm500.csv](https://drive.google.com/file/d/1hckCWINHcz8vn7BlRtF1ZBhjrLbfPKHP/view?usp=drive_link)

### TMA script
1. [Johnson_TM500_OAI_ Max Traffic.txt](https://drive.google.com/file/d/19oOTdb0FHCyO4E-LiqkPbyj9m8m4Ys6E/view?usp=drive_link)

### gNB conf
#### split version
1. [OAI_CU_conf](https://drive.google.com/file/d/1uZcEDSFEvbAUPHmk4E4YPeMwC2Vg0aOB/view?usp=drive_link)
2. [OAI_DU_conf](https://drive.google.com/file/d/1K153GHiCObzNbpTBC4CeKzL8M4LOu38Q/view?usp=drive_link)
#### BBU version
1. [11_14_gnb.sa.band78.273prb.fhi72.2x2-TM500_johnson.conf](https://drive.google.com/file/d/1DYyo7z6O_r7uVcbEUGvJzsmCjW8JCF2E/view?usp=drive_link)

:::

## Run Application

## 1. Use TMA to control TM500
### Step1.1 
![image](https://hackmd.io/_uploads/r1xQP39r7ye.png)


### Step1.2 Check C/U plane packet on time
![image](https://hackmd.io/_uploads/S1FI39SQJg.png)



## 2. Bring up CN
```c=
cd ~/free5gc/
sudo ./run.sh
```
## 3. Bring up gNB
- Put command here
```c=
cd /home/oai72/FH_7.2_dev/openairinterface5g/cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.2x2-TM500_johnson.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/test_johnson.log 2>&1 
```
![image](https://hackmd.io/_uploads/SkAe69HXJx.png)

## 4. Run TM500 script


### Protocol result
![image](https://hackmd.io/_uploads/By_PpcBQ1l.png)
### Throughout result
![image](https://hackmd.io/_uploads/rkwd65rX1e.png)
### UE information

| Parameter      | Value     |
| -------------- |:--------- |
| UE Id          | 0         |
| Pdu Session Id | 1         |
| IPv4 Address   | 10.60.0.3 |

```c=
25/11/24 23:40:43:763	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
25/11/24 23:40:43:764	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info: Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
25/11/24 23:40:43:764	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
25/11/24 23:40:43:822	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0xBF8F, TimingAdv: 0, PreambleTxCount: 2)
25/11/24 23:40:43:822	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0
25/11/24 23:41:00:196	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0
  Selected PLMN: 00101F
  Registration Result: 3GPP Access Only
25/11/24 23:41:16:271	I: CMPI MTE 0 NR PDU SESSION ESTABLISHMENT IND:UE Id:0
   Pdu Session Id:1 

  Data Network Name: internet
  IPv4 Address: 10.60.0.3
  QosRuleId:1 QosFlowId:1 FilterId:1
  QosFlowId:1 5QI:9
 UE Initiated Procedure 
25/11/24 23:41:24:928	I: TMAE 0x0 Information - Time Drift between TM500 clock & Network clock identified, Drift(ms): 11 total drift:11calcualted at time: 1732549284870
25/11/24 23:42:55:303	1
```



## 5. Run PPPoe through control pc vm
### 5.1 Install and Environment compilation
please check
- [Install Oracle VirtualBox.](https://hackmd.io/wJ910QIQRC6G36zjD5A5bg?view#Install-Oracle-VirtualBox)
- [Configuring a Linux PC as the Data Test Device](https://hackmd.io/wJ910QIQRC6G36zjD5A5bg?view#Install-Oracle-VirtualBox)
### Option(1)
### 5.2.1 Create configuration file

```c=
sudo cp /etc/ppp/pppoe.conf /etc/ppp/pppoe-ue0pdn0.conf
```

```c=
sudo gedit /etc/ppp/pppoe-ue0pdn0.conf
```
識別 `TM500 IP`、`UE ID` 和 `PDN ID`：

例如，如果您的 TM500 IP 是 ==192.168.10.67==，UE ID 是 ==0==，PDN ID 是 ==0==，則服務名稱為<br>tm500_lte_192.168.10.70_0_0



| 參數     | 數值         |
|:-------- |:------------ |
| TM500 IP | 192.168.8.67 |
| UE ID    | 0            |
| PDN ID   | 0            |

- 將 SERVICENAME 更改為您的連接 (SERVICENAME=`tm500_lte_192.168.8.67_0_0`)

![image](https://hackmd.io/_uploads/HkKuFdNzJe.png)

- 設置 PIDFILE = `"/var/run/$CF_BASE-pppoe-ue0pdn0.pid"`

![image](https://hackmd.io/_uploads/HkwXjONGJx.png)


- 確保 LINUX_PLUGIN =`/etc/ppp/plugins/rp-pppoe.so`

![image](https://hackmd.io/_uploads/ByUQn_4M1l.png)


- ==可選地==，調整 MTU 和 MRU 設置：
    - PPPD_EXTRA="mtu 1452 mru 1452"
### 5.2.2 Start PPPoE connect：
```c=
cd /rp-pppoe-3.12/src
```
```c=
sudo pppoe-start /etc/ppp/pppoe-ue0pdn0.conf
```
### Option(2) ---- Recommend
### 5.3 Using manage_UEs.sh to establish pppoe connection (After PDU session accept)
```
sudo apt update
```

```
sudo apt install ppp pppoeconf
```

Create a .sh doc and using the code below, and adjust several specific parameters according to your machine, as shown in the table below



| Parameter                | Value                  |
|:------------------------ |:---------------------- |
| pppoe_ethernet_interface | enp0s3                 |
| rp_pppoe_service         | tm500_lte_192.168.10.2 |

```c=
#!/bin/bash

pppoe_ethernet_interface="enp0s3"
test_route_targets=(
	"10.16.1.222/32"
	"10.16.1.223/32"
	"10.16.1.224/32"
	"10.16.1.225/32"
)

# Check our input arguments
if [ -z ${1} ]; then
	echo "Usage ${0} [Number of UEs to start]";
	exit 2;
fi

# Check our routing targets
if [ "${#test_route_targets[@]}" -lt "${1}" ]; then
	echo "Too few test route targets to setup ${1} UEs!";
	exit 2;
fi

first_ue=0;
last_ue=$((first_ue + ${1} - 1));

for ue_num in $(seq ${first_ue} ${last_ue});
do

	# Create the config file for the UE
	cat > "/etc/ppp/peers/tm500-ue${ue_num}" << __EOF
user tm500
#plugin rp-pppoe.so
plugin pppoe.so
${pppoe_ethernet_interface}
noipdefault
lcp-echo-interval 20
lcp-echo-failure 3
noauth
persist
maxfail 0
mtu 1500
mru 1500
noaccomp
default-asyncmap
linkname pppoe-ue${ue_num}
#rp_pppoe_service tm500_lte_192.168.10.2_0_${ue_num}
pppoe-service tm500_lte_192.168.10.2_0_${ue_num}
#rp_pppoe_ac "<server_name>"
__EOF

	echo "Starting up UE${ue_num}...";
	/usr/sbin/pppd call "tm500-ue${ue_num}";
	sleep 5;
	if ip link show dev ppp${ue_num} >/dev/null 2>&1; then
		echo "Adding route for test target [${test_route_targets[$ue_num]}]...";
		ip route add ${test_route_targets[$ue_num]} dev "ppp${ue_num}";
	else
		echo "PPPoE connection failed!";
		echo "Bailing out!";
		exit 1;

	fi
done
```
```c=
sudo ./manage_UEs.sh 32
```

### 5.4 PPPoE Result
![image](https://hackmd.io/_uploads/S1OqogRVyg.png)

![image](https://hackmd.io/_uploads/ry2pog04ke.png)




### 5.5 Using Iperf3 to testing performance

#### Testing OAI BBU with TM500 (Single UE)

- CN terminal
free5gc@free5gc-virtual-machine:~$ iperf3 -s -p 8889
```c=
iperf3 -s -p 8889
```

- Control terminal
vboxuser@Ubunt:~$ iperf3 -u -c 192.168.8.21 -p 8889 -b 800M -t 30
```c=
iperf3 -u -c 192.168.8.21 -p 8889 -b <target> -t 30 -l 1300
```
- Test direction: Uplink (sent from ==client== to ==server==)
```c=
iperf3 -u -c 192.168.8.21 -p 8889 -b 100M -t 30 -l 1300
```

- Test direction: Downlink (sent from ==server== to ==client==)
```c=
iperf3 -u -c 192.168.8.21 -p 8889 -b 100M -t 30 -l 1300 -R
```



:::info
- u: Use UDP protocol for testing.
- c 192.168.8.21: specifies the IP address of the server (192.168.8.21).
- p 8889: Specifies the server port number as 8889.
- b 100M: Set the target rate to 100 Mbps.
- t 30: The test duration is 30 seconds.
- l 1300: Set the size of each UDP packet to 1300 bytes.
:::

- Stop
```c=
sudo killall pppd
```




## 6. Quick flow
### Connect TM500 and DU

![image](https://hackmd.io/_uploads/By0pCsHXJg.png)

### Bring up CN
- In Free5gc
```c=
cd ~/free5gc/
sudo ./run.sh
```
### Bring up DU
- In OAI
```c=
cd /home/oai72/FH_7.2_dev/openairinterface5g/cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.2x2-TM500_johnson.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/test_johnson.log 2>&1 
```
### Run TM500 scrept (After C/U plane on time)
- In TMA

![image](https://hackmd.io/_uploads/r1luCjH7Jx.png)

### Running PPPoe (After PDU Session accept)
![image](https://hackmd.io/_uploads/Sy0Qk3HXye.png)

- VM terminal
```c=
sudo ./manage_UEs.sh <ue-number>
```
```c=
sudo ./manage_UEs.sh 32
```
- Iperf testing

```c=
iperf3 -s -p 8889 #Master
```

```c=
iperf3 -u -c 192.168.8.21 -p 8889 -b <target> -t 30 -l 1300 #Client
````


## 7. Result
### The maximum number of connectable UEs is currently 32


If the number of connected UEs exceeds ==32==, it will cause ==licensed problems==.
```c=
17/12/24 01:57:29:671	forw mte MtsConfigUeGroup 0 0 1{0-31}
17/12/24 01:57:29:696	C: FORW 0x00 Ok MTE MTSCONFIGUEGROUP: RETURN CODE:0 SUCCEEDED
17/12/24 01:57:34:013	forw mte MtsConfigUeGroup 0 0 1{0-32}
17/12/24 01:57:34:027	C: FORW 0x04 Resource_Unavailable MTE MTSCONFIGUEGROUP RETURN CODE:4 FAILED  - Not licensed for User Id in the group
17/12/24 01:57:37:056	forw mte MtsConfigUeGroup 0 0 1{0-64}
17/12/24 01:57:37:066	C: FORW 0x04 Resource_Unavailable MTE MTSCONFIGUEGROUP RETURN CODE:4 FAILED  - Not licensed for User Id in the group
```
### Free5GC + BBU + TM500_RU + Cloud UE(Single)

- Uplink testing ==482== Mbits/sec
```c=
[  5]  27.00-28.00  sec  59.1 MBytes   496 Mbits/sec  47686  
[  5]  28.00-29.00  sec  58.6 MBytes   491 Mbits/sec  47239  
[  5]  29.00-30.00  sec  58.9 MBytes   494 Mbits/sec  47520  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-30.00  sec  1.69 GBytes   483 Mbits/sec  0.000 ms  0/1393733 (0%)  sender
[  5]   0.00-30.04  sec  1.69 GBytes   482 Mbits/sec  0.014 ms  1020/1393731 (0.073%)  receiv
```
- Downlink testing ==738== Mbits/sec
```c=
[  5]  26.00-27.00  sec  87.3 MBytes   732 Mbits/sec  0.016 ms  3615/74036 (4.9%)  
[  5]  27.00-28.00  sec  91.3 MBytes   766 Mbits/sec  0.028 ms  2368/76032 (3.1%)  
[  5]  28.00-29.00  sec  90.8 MBytes   762 Mbits/sec  0.019 ms  3650/76916 (4.7%)  
[  5]  29.00-30.00  sec  90.0 MBytes   755 Mbits/sec  0.016 ms  5664/78287 (7.2%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-30.06  sec  2.71 GBytes   775 Mbits/sec  0.000 ms  0/2240891 (0%)  sender
[  5]   0.00-30.00  sec  2.58 GBytes   738 Mbits/sec  0.016 ms  113111/2240835 (5%)  receiver
```


### Free5GC + OAI_CU + OAI_DU + TM500_RU + Cloud UE(Single)


- Uplink testing ==473== Mbits/sec
```c=
[  5]  27.00-28.00  sec  52.1 MBytes   437 Mbits/sec  41985  
[  5]  28.00-29.00  sec  53.7 MBytes   451 Mbits/sec  43353  
[  5]  29.00-30.00  sec  51.0 MBytes   428 Mbits/sec  41164  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-30.00  sec  1.65 GBytes   474 Mbits/sec  0.000 ms  0/1366532 (0%)  sender
[  5]   0.00-30.04  sec  1.65 GBytes   473 Mbits/sec  0.021 ms  1007/1366532 (0.074%)  receiver
```


- Downlink testing ==727== Mbits/sec
```c=
[  5]  26.00-27.00  sec  83.9 MBytes   704 Mbits/sec  0.028 ms  2926/70602 (4.1%)  
[  5]  27.00-28.00  sec  91.8 MBytes   770 Mbits/sec  0.022 ms  1734/75787 (2.3%)  
[  5]  28.00-29.00  sec  90.3 MBytes   757 Mbits/sec  0.028 ms  3160/75957 (4.2%)  
[  5]  29.00-30.00  sec  95.6 MBytes   802 Mbits/sec  0.037 ms  2298/79380 (2.9%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-30.04  sec  2.64 GBytes   756 Mbits/sec  0.000 ms  0/2182584 (0%)  sender
[  5]   0.00-30.00  sec  2.54 GBytes   727 Mbits/sec  0.037 ms  85574/2182523 (3.9%)  receive
```





## 8. Debug
- [TM500 & PPPoE Debug](https://hackmd.io/@Johnson-72/S1lMYF6LJl)