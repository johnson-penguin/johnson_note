---
title: Record(1)

---

[TOC]
# Configuration file
:::success
[Configuration file](https://drive.google.com/drive/folders/15-YxSERhKWovXq9irwpyIH9rSWH8rBuO?usp=drive_link)
:::
# Record
## (11/25)

```c=
25/11/24 23:40:43:763	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
25/11/24 23:40:43:764	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
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
:::danger
- lte_tm500_192.168.8.67_0_1
- nr_tm500_192.168.8.67_0_1
- tm500_lte_192.168.8.67_0_1
- tm500_nr_192.168.8.67_0_1


- lte_tm500_192.168.8.67_0_0
- nr_tm500_192.168.8.67_0_0
- tm500_lte_192.168.8.67_0_0
- tm500_nr_192.168.8.67_0_0
:::

:::warning
- lte_tm500_192.168.8.67_0_1
- nr_tm500_192.168.8.67_0_1
- tm500_lte_192.168.8.67_0_1
- tm500_nr_192.168.8.67_0_1


- lte_tm500_192.168.8.67_0_0
- nr_tm500_192.168.8.67_0_0
- tm500_lte_192.168.8.67_0_0
- tm500_nr_192.168.8.67_0_0
:::
## (11/8) 

- o-ran.cfg
```c=
SysrepoInitFile=oran_oai_4x4_neu_johnson_9BFP_1500.csv
```
- C/U plane not receive successful
- 
![image](https://hackmd.io/_uploads/B1d04Nob1l.png)


## (11/7)
- OAI gNB 4x4
Base on [Compare Jura and TM500 OAI conf](https://hackmd.io/63kBPQWwQxGUEnhKGKDVTw#%E5%90%88%E4%BD%B5%E5%8F%83%E6%95%B8)

### Adjust OAI gNB
- Path : `FH_7.2_dev/openairinterface5g/targets/PROJECTS/GENERIC-NR-5GC/CONF/`
- File : `gnb.sa.band78.273prb.fhi72.4x4-TM500_johnson.conf`

Table for CC 4x4 OAI
:::success
| 參數                  | 調整後 | 調整前 |
|:--------------------- |:------ |:------ |
| nb_tx                 | 2      | 4      |
| nb_rx                 | 2      | 4      |
| pdsch_AntennaPorts_XP | 2      | 4      |
| pusch_AntennaPorts    | 2      | 4      |
| eAxC_offset           | 2      | 4      |
:::


### TMA
When connect you need to modify `Radio Cards Settings` 
![image](https://hackmd.io/_uploads/r1OKHY5Zye.png)


## (11/6) 
### Performancet testing 
- OAI + TM500

![image](https://hackmd.io/_uploads/SJ-JbHdZJl.png)

- [OAI + Jura RU](https://hackmd.io/@Summer063/Hyjno5GhA#Throughput-comparison-table)


| gNB         | RU     | CN      | DL Throughput  | UL Throughput      | TDD Pattern | Special Slot Config     |
|:----------- |:------ | ------- | -------------- |:------------------ | ----------- | ----------------------- |
| TY_gNB      | NKG    | free5gc | ~277 Mbits/sec | ~17 Mbits/sec      | DDDSU       | DDDDDDDDDDDDDGG(12:2:0) |
| TY_gNB      | JURA   | free5gc | ~278 Mbits/sec | ~17 Mbits/sec      | DDDSU       | DDDDDDDDDDDDDGG(12:2:0) |
| OAI (BBU)   | JURA   | free5gc | ~180 Mbits/sec | ~55 Mbits/sec      | DDDSU       | DDDDDDDGGGGUUUU(6:4:4)  |
| OAI (BBU)   | Liteon | free5gc | ~48 Mbits/sec  | ~49 Mbits/sec      | DDDSU       | DDDDDDDGGGGUUUU(6:4:4)  |
| OAI (BBU)   | TM500  | free5gc | 0.018768 Mbps  | 0.01672  Mbits/sec | DDDSU       | DDDDDDDGGGGUUUU(6:4:4)  |
| OAI (split) | TM500  | free5gc | Mbps           | Mbits/sec          | DDDSU       | DDDDDDDGGGGUUUU(6:4:4)  |


### Testing OAI conf ( for Jura )

Test the settings of gNB in the simulation environment of TM500 to see if there is any problem of Msg1/Msg3 instability.

- Result

Msg1 / Msg3 is stable in the simulator environment and does not require multiple receptions to succeed.
![image](https://hackmd.io/_uploads/r1EQyc_-ke.png)





## (11/1) 
Testing UE 0 DL UDP stuck at `nrPduSessionEstablishmentRequest`

![image](https://hackmd.io/_uploads/B1WrFbfWJl.png)


#### TMA log
```c=
WAIT FOR 0x00 OK
01/11/24 15:48:16:148	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
01/11/24 15:48:16:149	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
01/11/24 15:48:16:149	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
01/11/24 15:48:16:149	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0x4C68, TimingAdv: 0, PreambleTxCount: 1)
01/11/24 15:48:16:149	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0
01/11/24 15:48:32:442	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0
  Selected PLMN: 00101F
  Registration Result: 3GPP Access Only
```
#### CN log

```c=
2024-11-01T15:49:35.962816321+08:00 [ERRO][AMF][Gmm][amf_ue_ngap_id:RU:1,AU:2(3GPP)][supi:SUPI:imsi-001010000062653] Select SMF failed: DNN[oai] is not supported or not subscribed in the slice[Snssai: {Sst:1 Sd:010203}]
```

same as [10/30 No-PDU-session-accept](#No-PDU-session-accept)



## (10/30)

### NG-Setup failure
>free5GC log
```c=
2024-10-30T13:45:48.260867445+08:00 [WARN][AMF][Ngap][ran_addr:192.168.8.29:57955] NG-Setup failure: Cannot find Served TAI in AMF
```
#### error reason : Because TAI(tracking area identity) value in free5GC and OAI gNB is not match
#### Solution : Match TAI value in OAI gNB config and free5GC config
:::info
##### TAI(tracking area identity) field include
- plmnId
	- mcc(Mobile Country Code)
	- mnc(Mobile Network Code)
- TAC(tracing area code)
:::
>free5GC AMF config(path : `free5gc/config/amfcfg.yaml`)

| Item | value  |
| ---- | ------ |
| mcc  | 001    |
| mnc  | 001    |
| TAC  | 000001 |
```yaml=36
  supportTaiList:  # the TAI (Tracking Area Identifier) list supported by this AMF
    - plmnId: # Public Land Mobile Network ID, <PLMN ID> = <MCC><MNC>
    # Sheryl
        mcc: 001 # Mobile Country Code (3 digits string, digit: 0~9)
        mnc: 01 # Mobile Network Code (2 or 3 digits string, digit: 0~9)
        # mcc: 208 # Mobile Country Code (3 digits string, digit: 0~9)
        # mnc: 93 # Mobile Network Code (2 or 3 digits string, digit: 0~9)
      tac: 000001 # Tracking Area Code (3 bytes hex string, range: 000000~FFFFFF)
```
>OAI gNB config
- Before

| Item | value |
| ---- | ----- |
| mcc  | 001   |
| mnc  | 001   |
| TAC  | 100   |
```c=16
    tracking_area_code  =  100;
    plmn_list = ({ mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); });
```
- After : Adjust `tracking_area_code` to ==1== to match free5GC setting.

| Item | value |
| ---- | ----- |
| mcc  | 001   |
| mnc  | 001   |
| TAC  | 1     |

```c=16
    tracking_area_code  =  1;
    plmn_list = ({ mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); });
```
#### Result : NG-Setup response

```c=
"Send NG-Setup response" CAT="Ngap" NF="AMF" ran_addr="192.168.8.29:40702"
```

:::info
[Tracking Area](https://www.sharetechnote.com/html/Handbook_LTE_Tracking_Area.html)

Each eNobe broadcasts a special tracking area code (TAC) to indicate to which Tracking Area the eNodeB belongs to and the TAC is unique within a PLMN. 
:::

### Registration Reject
>free5GC log
```c=
"Send Registration Reject" CAT="Gmm" NF="AMF" amf_ue_ngap_id="RU:1,AU:1(3GPP)" supi="SUPI:"
```
#### Error reason : SIM information in TM500 is not match in free5GC SIM info database
- [3 SIM info record in core network](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2F%40Summer063%2FBkDNMq4bA)

| SIM  | IMSI            | Ki                               | OP                               |
| ---- | --------------- | -------------------------------- | -------------------------------- |
| SIM1 | 001010000062653 | 8baf473f2f8fd09487cccbd7097c6862 | 8e27b6af0e692e750f32667a3b14605d |
| SIM2 | 001010000062655 | 8baf473f2f8fd09487cccbd7097c6862 | 8e27b6af0e692e750f32667a3b14605d |
| SIM3 | 001010000062656 | 8baf473f2f8fd09487cccbd7097c6862 | 8e27b6af0e692e750f32667a3b14605d |

#### Solution : Modify TMA UECONFIG settings
>TMA script path : `Johnson_SA_2x2_AFORAN_1UE Max Traffic/Cell Search and UE Configure/Configure USIM`)

| Item                   | value                            |
|:---------------------- | -------------------------------- |
| IMSI                   | 001010000062653                  |
| Ki(Authentication Key) | 8baf473f2f8fd09487cccbd7097c6862 |
| OP                     | 8e27b6af0e692e750f32667a3b14605d |

![image](https://hackmd.io/_uploads/rJSt_w1-ke.png)

#### Result : Have Registration accept but ==stuck at nrPduSessionEstablishmentRequest==
>TMA log

![image](https://hackmd.io/_uploads/HkrgE81-1g.png)
![image](https://hackmd.io/_uploads/HyBbVUJWkl.png)

<!-- - 成功E2E後仍然會出現，可忽略以下Error
```c=
[ERRO][AMF][Gmm][amf_ue_ngap_id:RU:1,AU:1(3GPP)][supi:SUPI:imsi-001010000062653] Select SMF failed: DNN[ULUDP] is not supported or not subscribed in the slice[Snssai: {Sst:1 Sd:010203}]
``` -->

### No PDU session accept
#### Error reason : DNN is not supported or not subscribed in the slice
- TM500 UESIM is using APN/DNN `ULUDP` to send PDU session.
 
>free5GC log
```c=
[ERRO][AMF][Gmm][amf_ue_ngap_id:RU:1,AU:1(3GPP)][supi:SUPI:imsi-001010000062653] Select SMF failed: DNN[ULUDP] is not supported or not subscribed in the slice[Snssai: {Sst:1 Sd:010203}]
```
>PCAP

![image](https://hackmd.io/_uploads/rkebdheb1x.png)
- But in free5GC SMF config `dnn` is ==internet==.
>free5gc/config/smfcfg.yaml
```yaml=
  snssaiInfos: # the S-NSSAI (Single Network Slice Selection Assistance Information) list supported by this AMF
    - sNssai: # S-NSSAI (Single Network Slice Selection Assistance Information)
        sst: 1 # Slice/Service Type (uinteger, range: 0~255)
        sd: 010203 # Slice Differentiator (3 bytes hex string, range: 000000~FFFFFF)
      dnnInfos: # DNN information list
        - dnn: internet # Data Network Name
          dns: # the IP address of DNS
            ipv4: 8.8.8.8
            ipv6: 2001:4860:4860::8888
```
#### Solution : Modify TMA APN/DNN settings
>TMA script path : `Johnson_SA_2x2_AFORAN_1UE Max Traffic/UE 0 UL UDP/Start MTS Scenario`)

![image](https://hackmd.io/_uploads/SyDtnLJ-Jg.png)
APN is the name used to connect to the internet or a specific data service. It tells the network how to handle the user's data traffic. In LTE, the APN can be a specific service name, such as internet or iot.

:::danger
- Wrong parameters path : 
    - `Johnson_SA_2x2_AFORAN_1UE Max Traffic/UE 0 UL UDP/Start MTS Scenario/MTSCONFIGRAFFICPROFILE : Number of APN list entries (Optional)`
    - This will cause RRC to disconnect and reconnect multiple times
    - Because it will make the registration request message malformed. 
	![image](https://hackmd.io/_uploads/rJk9iaxZJx.png)
:::
:::info
- Right parameters path : 
    - `Johnson_SA_2x2_AFORAN_1UE Max Traffic/UE 0 UL UDP/Start MTS Scenario/MTSCONFIGRAFFICPROFILE : APN-LTE-/DNN-NR-`
    - PDU session accept
:::

:::success
Parameter description : ==APN -LTE-/ / DNN -NR-==
Access Point Name/ Data Network Name. Can be "" for PDN/ PDU Session 0 - the default bearer created on Attach.

Note that if the APN/ DNN for a UE changes during scenario execution the UE will Detach and re-Attach. Note that the name must not include quotation marks unless an empty string (") is required.
:::

#### Result : DNN is internet and has PDU sessionestablishment accpt
>PCAP

![image](https://hackmd.io/_uploads/By_yq6lbke.png)
>TMA log

![image](https://hackmd.io/_uploads/SkWG8OJbJx.png)

## (10/29) NAS: Registration failure - Lower Layer Failure: Retrying to Attach

![image](https://hackmd.io/_uploads/HJWmCF0eyl.png)

```c=
30/10/24 01:08:06:668	I: CMPI MTE 0 ACTIVATE IND: RETURN CODE:0
30/10/24 01:08:06:670	C: WAIT FOR 0x00 OK
30/10/24 01:08:06:672	WAIT 20
30/10/24 01:08:16:783	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
30/10/24 01:08:16:783	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
30/10/24 01:08:16:783	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
30/10/24 01:08:16:783	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0xC472, TimingAdv: 0, PreambleTxCount: 2)
30/10/24 01:08:16:783	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0
30/10/24 01:08:26:684	C: WAIT 0x00 OK
30/10/24 01:08:26:685	forw mte MtsClearMts [1[10 100]]
30/10/24 01:08:26:685	C: FORW 0x00 Ok MTE MTSCLEARMTS: RETURN CODE:0 SUCCEEDED
30/10/24 01:08:31:679	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0
NAS: Registration failure - Lower Layer Failure: Retrying to Attach 
30/10/24 01:08:31:679	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
30/10/24 01:08:31:679	I: CMPI MTE 0 NR DISCONNECTION IND:UE Id:0
30/10/24 01:08:31:679	I: CMPI MTE 0 NR DEREGISTRATION IND:UE Id:0
    Detach Cause: Silent Detach 
30/10/24 01:08:31:709	I: CMPI MTE 0 MTSCLEARMTS COMPLETE IND
```
:::success

==RRC Cell Selection== and ==L2 Random Access Initiated== 
indicate that the user equipment is selecting a cell and initiating random access.

---
Successfully obtained connection identification ==(TC-RNTI)==
:::

:::danger
NAS: Registration failure - Lower Layer Failure: Retrying to Attach 
:::
>Possible reasons
>• UE Msg3 was not sent.
>• UE Msg3 was sent and the maximum number of retransmissions was reached.


![image](https://hackmd.io/_uploads/H18YSqReye.png)

![image](https://hackmd.io/_uploads/H14WEMyW1l.png)


## (10/28) 

### Using 4x4 CC to testing
- generator 4x4 file 
```c=
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
- testing 4x4 configuration

DU receive C/U plane packet 
![image](https://hackmd.io/_uploads/Hys4AI6x1l.png)

C/U plane lost lot of packet
![image](https://hackmd.io/_uploads/H132TITeye.png)

![image](https://hackmd.io/_uploads/rJrMqcheye.png)


![image](https://hackmd.io/_uploads/Byp6Yq3e1l.png)



```c=
28/10/24 16:05:12:058	FORW MTE NRSETMUERADIOCONTEXTCELL 0 0 34507.2 100 [] [] [] [34015.8 34507.2] [34507.2] [1] [] [] [2 2] [] [] [] [0] [] []
28/10/24 16:05:12:709	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "false" in ../nr_fw_app_l0/modules/oran_rad_proc/sub/searcher_spoof/code/fsm_searcher_spoof.cpp at line 249, Invalid Event: 1 in Current State: 0
28/10/24 16:05:17:774	DTRC 2024_10_28_16_05_17_dump
```
>check .csv

### phase_compensation configuration testing
phase_compensation should be set to 0 to disable when it is performed in the RU and set to 1 when it should be performed on the DU side

```c=
phase_compensation = 1 >>> phase_compensation = 0
```
![image](https://hackmd.io/_uploads/Hkmeu86xke.png)


![image](https://hackmd.io/_uploads/ByBZ_Upekg.png)


###  DTE RDA ERROR: Unable to connect to RDA 

```c=
28/10/24 19:28:34:234	C: FORW 0x00 Ok MTE DECONFIGRDASTARTTESTCASE: RETURN CODE:0 SUCCEEDED
28/10/24 19:28:34:234	I: CMPI DTE RDA ERROR IND: Unable to connect to RDA platform at 192.168.8.71
28/10/24 19:28:34:234	I: ERNO 0x00000001 DTE 0x00000007 DTE RDA ERROR: Unable to connect to RDA platform at 192.168.8.71
```
![image](https://hackmd.io/_uploads/BygFIbAeyg.png)


## (10/23) Unable to connect for unknown reasons

![image](https://hackmd.io/_uploads/SJxBt7Neke.png)

![image](https://hackmd.io/_uploads/BkDTd7Nx1x.png)

```c=
╭─oai72@inoue ~ 
╰─$ ping  192.168.8.67                                                                                          1 ↵
PING 192.168.8.67 (192.168.8.67) 56(84) bytes of data.
From 192.168.8.29 icmp_seq=1 Destination Host Unreachable
From 192.168.8.29 icmp_seq=2 Destination Host Unreachable
From 192.168.8.29 icmp_seq=3 Destination Host Unreachable
```
- 嘗試接上外網進行測試

- ping的到，但從非指定的IP打回
![image](https://hackmd.io/_uploads/H1KH57VlJx.png)

ARP table issues: Computer may have logged the wrong MAC address. You can try clearing the ARP table and try again

> The ARP (Address Resolution Protocol) table is a data structure used to map IP addresses to physical addresses (MAC addresses). When a device needs to send a packet to a certain IP address, it first queries the ARP table to obtain the corresponding MAC address. If the address is not in the ARP table, the device issues an ARP request for help from other devices on the network.

- Solution

Clear the ARP table:
```c=
arp -d *
```
Use the arp -d * command to clear all ARP entries and force the system to re-establish the ARP table.

![image](https://hackmd.io/_uploads/r1UGRQNeyx.png)

---
- FTP ERROR
```c=
Startup with CAP_SYS_ADMIN
Startup with CAP_SYS_RAWIO
cd: Fatal error: max-retries exceeded (No route to host)
get: init_infrastructure_host_container.sh: Fatal error: max-retries exceeded (No route to host)
Failed to ftp init_infrastructure_host_container.sh
```

IP 變更導致，將FTP_ADDR修改即可
```c=
cd installcue/
vim cuedock.yaml
FTP_ADDR = 192.168.8.68 // control PC IP
ETH0_ADDR = 192.168.8.67 // cloudUE ETH0 IP
```


## (10/18)
### mac_address
oran_oai_2x2_neu_johnson_9BFP_1500.csv

![image](https://hackmd.io/_uploads/B1fO7xQe1l.png)

```c=
TRUE,/o-ran-processing-element:processing-elements/ru-elements[name='CC0_PE.ORU1-P1.5']/transport-flow/eth-flow/o-du-mac-address,SR_STRING_T,01:AB:23:CD:45:EF
```
>01:AB:23:CD:45:EF modify 00:11:22:33:44:66

### scs error
low_level_tx_endpoint/num_prb_per_scs_120khz: 66

![image](https://hackmd.io/_uploads/rJlXByQekx.png)

### no support M-plane vlan-id
```c=
# Set the M-plane vlan-id.
# Valid values are 1 to 4094. Values outside this range imply no vlan.
MplaneVlanId=0
```
> trying modify MplaneVlanId=0 to MplaneVlanId=1~4094
![image](https://hackmd.io/_uploads/B1Y5ZlXlyg.png)



## (10/09) compare control plane message between Jura and TM500
![image](https://hackmd.io/_uploads/SJpJpQ7y1g.png)
|                     | JURA                                                          | TM500                                                         |
| ------------------- |:------------------------------------------------------------- |:------------------------------------------------------------- |
| Comparison Item     | Packet 16                                                     | Packet 52                                                     |
| Packet Size         | Larger than Packet 52                                         | Smaller than Packet 16                                        |
| Arrival Time        | October 8, 2024 (about 8 days later than Packet 52)           | September 30, 2024                                            |
| Encapsulation Type  | Linux cooked-mode                                             | Ethernet II                                                   |
| Source Address      | CIMSYS_33:44:66                                               | CIMSYS_33:44:66                                               |
| Destination Address | Unicast to another host                                       | MellanoxTech_14:1c:10                                         |
| VLAN ID             | 3                                                             | 5                                                             |
| Payload             | `00:00:a9:80:10:9c:40:0a:01:01:91:00:00:00:00:00:ff:f4:00:00` | `00:00:0e:80:90:00:60:40:01:01:91:00:00:00:00:00:ff:fe:00:00` |

![image](https://hackmd.io/_uploads/ByOmz4mJJg.png)

| Comparison Item    | JURA                                                   | TM500                                                  |
| ------------------ |:------------------------------------------------------ |:------------------------------------------------------ |
| DU Port ID         | 0                                                      | 0                                                      |
| BandSector ID      | 0                                                      | 0                                                      |
| CC ID              | 0                                                      | 0                                                      |
| RU Port ID         | 0                                                      | 1                                                      |
| c_eAxC_ID          | 0:0:0:0                                                | 0:0:0:1                                                |
| Sequence ID        | 170                                                    | 14                                                     |
| Subsequence ID     | 0                                                      | 0                                                      |
| E Bit              | Last fragment (1)                                      | Last fragment (1)                                      |
| Frame ID           | 156                                                    | 0                                                      |
| Subframe ID        | 4                                                      | 6                                                      |
| Slot ID            | 0                                                      | 1                                                      |
| Start Symbol ID    | 0                                                      | 0                                                      |
| Number of Sections | 1                                                      | 1                                                      |
| Section Type       | Most DL/UL radio channels (1)                          | Most DL/UL radio channels (1)                          |
| udCompHdr          | IqWidth=9, udCompMeth=Block floating point compression | IqWidth=9, udCompMeth=Block floating point compression |
| Reserved           | 0x00                                                   | 0x00                                                   |
| Section ID         | 0 (all PRBs)                                           | 0 (all PRBs)                                           |
| Symbols            | 6                                                      | 14                                                     |
| symInc             | Use the current symbol number (0)                      | Use the current symbol number (0)                      |
| startPrbc          | 0                                                      | 0                                                      |
| numPrbc            | 0 (all PRBs - configured as 273)                       | 0 (all PRBs - configured as 273)                       |
| RE Mask            | 0xfff                                                  | 0xfff                                                  |
| Extension Flag     | False                                                  | False                                                  |
| Beam ID            | 0                                                      | 0                                                      |

----

- IQ Data
![image](https://hackmd.io/_uploads/S1mzGDmyJe.png)

## (9/30) tracing log information

[9/30 log file](https://drive.google.com/drive/folders/18Hx4srTyj6AzAN_WEzzaygPqypPE0n4h?usp=drive_link)

```c=
[0m[34m[MAC]   numDlDci: 1
[0m[34m[MAC]   Frame 59 slot 9: Getting PRACH info from index 151 (col 6 1) absoluteFrequencyPointA 626772 mu 1 frame_type 1 start_symbol 2 N_t_slot 1 N_dur 12 N_RA_slot 1 RA_sfn_index 0 
[0m[34m[MAC]   get_NCS: indx 0,format0 180, restriced_set_config 0
[0m[34m[MAC]   Frame 59 slot 19: Getting PRACH info from index 151 (col 6 1) absoluteFrequencyPointA 626772 mu 1 frame_type 1 start_symbol 2 N_t_slot 1 N_dur 12 N_RA_slot 1 RA_sfn_index 1 
[0m[34m[MAC]   get_NCS: indx 0,format0 180, restriced_set_config 0
[0m[34m[MAC]   Scheduling ssb 0 at frame 60 and slot 0
[0m[34m[MAC]   NrofSymbols:4, startSymbol:8, mappingtype:1, dmrs_TypeA_Position:0
[0m[34m[MAC]   l0:1, ld:4,row:2, column:6, addpos:2, maxlen:1
[0m[34m[MAC]    PDSCH DMRS MASK in HEX:100
[0m[34m[MAC]   dlDmrsSymbPos 100
[0m[34m[MAC]   Coreset : BWPstart 112, BWPsize 48, SCS 1, freq ff, , duration 1
[0m[34m[MAC]   BWPSize: 48
[0m[34m[MAC]   BWPStart: 112
[0m[34m[MAC]   SubcarrierSpacing: 1
[0m[34m[MAC]   CyclicPrefix: 0
[0m[34m[MAC]   StartSymbolIndex: 0
[0m[34m[MAC]   DurationSymbols: 1
[0m[34m[MAC]   FreqDomainResource[0]: ff
[0m[34m[MAC]   FreqDomainResource[1]: 0
[0m[34m[MAC]   FreqDomainResource[2]: 0
[0m[34m[MAC]   FreqDomainResource[3]: 0
[0m[34m[MAC]   FreqDomainResource[4]: 0
[0m[34m[MAC]   FreqDomainResource[5]: 0
[0m[34m[MAC]   CceRegMappingType: 1
[0m[34m[MAC]   RegBundleSize: 6
[0m[34m[MAC]   InterleaverSize: 2
[0m[34m[MAC]   CoreSetType: 0
[0m[34m[MAC]   ShiftIndex: 0
[0m[34m[MAC]   precoderGranularity: 0
```
| **Log Entry**                       | **Description**                                                                                           |
| ----------------------------------- |:--------------------------------------------------------------------------------------------------------- |
| numDlDci: 1                         | Indicates that there is one Downlink Control Information (DCI) currently available.                       |
| get_NCS                             | Displays network configuration parameters that have been retrieved, including index and format.           |
| Scheduling ssb 0 at frame 60 slot 0 | Indicates that Synchronization Signal Block (SSB) 0 is being scheduled in frame 60 during slot 0.         |
| PDSCH DMRS MASK in HEX: 100         | Shows the Downlink Shared Channel (PDSCH) Demodulation Reference Signal (DMRS) mask.                      |
| Coreset                             | Represents the configuration of the core set, including bandwidth start point, size, SCS, frequency, etc. |
| BWPSize and BWPStart                | Displays the size and starting point of the Bandwidth Part (BWP).                                         |
| FreqDomainResource                  | Shows the configuration of frequency domain resources, with multiple resource values listed.              |
| CceRegMappingType and RegBundleSize | Displays the mapping type of Control Channel Elements (CCE) and the size of the register bundle.          |
| CoreSetType and ShiftIndex          | Shows the type of core set and the shift index.                                                           |
| precoderGranularity                 | Indicates the setting for precoder granularity.                                                           |

- PRACH information
- 
| **Log Entry**                  | **Description**                                                                                 |
| ------------------------------ |:----------------------------------------------------------------------------------------------- |
| Frame 59 slot 9                | Indicates that Random Access (PRACH) information is being retrieved in frame 59 during slot 9.  |
| Frame 59 slot 19               | Indicates that Random Access (PRACH) information is being retrieved in frame 59 during slot 19. |
| absoluteFrequencyPointA 626772 | Represents the value of the Absolute Frequency Point A.                                         |
| mu 1                           | Indicates the Multi-User (MU) configuration for the spectrum.                                   |
| frame_type 1                   | Specifies the type of frame.                                                                    |
| start_symbol 2                 | Indicates the index of the starting symbol.                                                     |
| N_t_slot 1                     | Relates to the timing settings for random access.                                               |
| N_dur 12                       | Relates to the duration settings for random access.                                             |
| N_RA_slot 1                    | Relates to the random access slot configuration.                                                |
| RA_sfn_index 0/1               | Indicates the Random Access System Frame Number index.                                          |

## (9/27) Check the sib1_tda & Confirm in which FN/SFN/Slot/Symbol does DU send SSB

:::success
### Check the sib1_tda used for what ? 

https://gitlab.eurecom.fr/oai/openairinterface5g/-/merge_requests/1522

It is related to RRC
![image](https://hackmd.io/_uploads/ByrHdvERA.png)

Check the [asn.1 spec](https://www.sqimway.com/rrc_nr.html#PUSCH-TimeDomainResourceAllocation). 
![image](https://hackmd.io/_uploads/rJWOuD4RR.png)

Guess the full name of tda should be timeDomainAllocation, the explanation given in the specification is as follows (38.321)

![image](https://hackmd.io/_uploads/HkzNKPERC.png)
- Robert explain
![image](https://hackmd.io/_uploads/BkbjwTvRC.png)
:::
## (9/26) Too many zero data in IQ.
:::info
From the FH pcap, can see there are many zero data in IQ. 
![image](https://hackmd.io/_uploads/BJ2NvvNCA.png)

---
![image](https://hackmd.io/_uploads/ryP5GjwCR.png)
---


### Confirm in which FN/SFN/Slot/Symbol does DU send DCI for SIB1 on pdcch and SIB1 on pdsch
- Using debug logo check
```c=
log_config :
{
  global_log_level                      ="info";
  hw_log_level                          ="info";
  phy_log_level                         ="info";
  mac_log_level                         ="debug";
  rlc_log_level                         ="info";
  pdcp_log_level                        ="info";
  rrc_log_level                         ="info";
  ngap_log_level                        ="info";
  f1ap_log_level                        ="info";
};

```
- from DU log 

SSB 傳輸時機：
| Frame | Slot | Symbol     |
| ----- | ---- |:---------- |
| 8     | 0    | 8（start） |


```c=
[0m[34m[MAC]   numDlDci: 1
[0m[34m[MAC]   Frame 7 slot 9: Getting PRACH info from index 151 (col 6 1) absoluteFrequencyPointA 626772 mu 1 frame_type 1 start_symbol 2 N_t_slot 1 N_dur 12 N_RA_slot 1 RA_sfn_index 0 
[0m[34m[MAC]   get_NCS: indx 0,format0 180, restriced_set_config 0
[0m[34m[MAC]   Frame 7 slot 19: Getting PRACH info from index 151 (col 6 1) absoluteFrequencyPointA 626772 mu 1 frame_type 1 start_symbol 2 N_t_slot 1 N_dur 12 N_RA_slot 1 RA_sfn_index 1 
[0m[34m[MAC]   get_NCS: indx 0,format0 180, restriced_set_config 0
[0m[34m[MAC]   [gNB 0] Frame 8 : MIB->BCH  CC_id 0, Received 3 bytes
[0m[34m[MAC]   Scheduling ssb 0 at frame 8 and slot 0
[0m[34m[MAC]   NrofSymbols:4, startSymbol:8, mappingtype:1, dmrs_TypeA_Position:0
[0m[34m[MAC]   l0:1, ld:4,row:2, column:6, addpos:2, maxlen:1
[0m[34m[MAC]    PDSCH DMRS MASK in HEX:100
[0m[34m[MAC]   dlDmrsSymbPos 100
[0m[34m[MAC]   Coreset : BWPstart 112, BWPsize 48, SCS 1, freq ff, , duration 1
[0m[34m[MAC]   BWPSize: 48
[0m[34m[MAC]   BWPStart: 112
[0m[34m[MAC]   SubcarrierSpacing: 1
[0m[34m[MAC]   CyclicPrefix: 0
[0m[34m[MAC]   StartSymbolIndex: 0
[0m[34m[MAC]   DurationSymbols: 1
[0m[34m[MAC]   FreqDomainResource[0]: ff
[0m[34m[MAC]   FreqDomainResource[1]: 0
[0m[34m[MAC]   FreqDomainResource[2]: 0
[0m[34m[MAC]   FreqDomainResource[3]: 0
[0m[34m[MAC]   FreqDomainResource[4]: 0
[0m[34m[MAC]   FreqDomainResource[5]: 0
[0m[34m[MAC]   CceRegMappingType: 1
[0m[34m[MAC]   RegBundleSize: 6
[0m[34m[MAC]   InterleaverSize: 2
[0m[34m[MAC]   CoreSetType: 0
[0m[34m[MAC]   ShiftIndex: 0
[0m[34m[MAC]   precoderGranularity: 0
```












### Ask DU owner to check if the IQ data is normal or not?

:::
## (9/24) Status : EQ data seems abnormal and Verifiy point A
[Calculation method](https://www.sharetechnote.com/html/5G/5G_ResourceBlockIndexing.html#google_vignette)
##### SIB
- SIB
```c=
 servingCellConfigCommon
                                downlinkConfigCommon
                                    frequencyInfoDL
                                        frequencyBandList: 1 item
                                            Item 0
                                                NR-MultiBandInfo
                                                    freqBandIndicatorNR: 78
                                        offsetToPointA: 252 PRBs
                                        scs-SpecificCarrierList: 1 item
                                            Item 0
                                                SCS-SpecificCarrier
                                                    offsetToCarrier: 0
                                                    subcarrierSpacing: kHz30 (1)
                                                    carrierBandwidth: 273
```

##### MIB
- MIB
```c=
BCCH-BCH-Message
                message: mib (0)
                    mib
                        systemFrameNumber: 60 [bit length 6, 2 LSB pad bits, 0110 00.. decimal value 24]
                        subCarrierSpacingCommon: scs30or120 (1)
                        ssb-SubcarrierOffset: 12
                        dmrs-TypeA-Position: pos2 (0)
                        pdcch-ConfigSIB1
                            controlResourceSetZero: 11
                            searchSpaceZero: 0
                        cellBarred: notBarred (1)
                        intraFreqReselection: notAllowed (1)
                        spare: 00 [bit length 1, 7 LSB pad bits, 0... .... decimal value 0]

```
### [Calcuate](https://www.sharetechnote.com/html/5G/5G_ResourceBlockIndexing.html#SSB_Frequency_Location_in_SA_Ex01) 
![image](https://hackmd.io/_uploads/r1LgbJZCA.png)

:::success
![image](https://hackmd.io/_uploads/ByCby21CA.png)

- 1 ARFCN =  630048 (from gNB `absoluteFrequencySSB`)
- 2 k_SSB = ssb-subcarrierOffset = 12 [(from MIB ssb-subcarrierOffset)](#MIB)
- 3 OffsetToPointA = 252 [(from SIB offsetToPointA)](#SIB)
#### Step 1 : ARFCN turn to Frequency

>ARFCN =  629952 ==> 3450.720 Mhz [(5G NR ARFCN calculator)](https://5g-tools.com/5g-nr-arfcn-calculator/)

---

#### Step 2 :  Calculate the SSB Reference Location (SSB_REF)
$SSB_{REF}= absoluteFrequencySSB - (10 * 12 * 0.03)= 3450.72 - (10 * 12 * 0.03)= 3447.12$

---

#### Step 3 :  Calculate Point A in the form of frequency in Mhz using following equation and assign it to (C)

$Point_{A} = SSB_{REF} - (k_{SSB} * 0.015) - ( OffsetToPointA * 12 * 0.015) = 3447.12 - (12 * 0.015) - (252 * 12 * 0.015)=3401.58$
:::
### EQ data seems abnormal
![image](https://hackmd.io/_uploads/rkriykWCR.png)

## (9/23) Supplement MIB to VIAVI

### Liteon_SIB
:::spoiler
```c=
16695, 2154, 45185016, 09:39:58:696 2024/09/20, MOD_NRRC_SI, , MOD_NRRC_SI_OTA_UH, [NW->MS] Dedicated SIB1 (DL_FREQ_BAND[78], SSB_ARFCN[[630048], PCI[0], SCS[NR_SCS_30])
Peer Message
    global_peer_msg_id = PROTOCOL_SECTION__NR_RRC_BCCH_DL_SCH (enum 1310)
    raw data = Buffer
        0000 - 000F 64 80 0a 02 08 00 80 40   00 00 40 00 00 00 06 90
        0010 - 001F 84 d0 fc 00 00 31 06 04   4b 35 fd 85 b8 20 10 00
        0020 - 002F 00 40 41 b8 40 10 00 00   40 41 b8 60 10 00 00 40
        0030 - 003F 40 00 61 03 48 50 36 1c   87 4c c0 00 20 00 03 10
        0040 - 004F d5 c0 89 66 54 9f 00 00   c4 f5 5f c9 c0 49 39 c2
        0050 - 005F a9 85 6f 0a 9a 15 33 6a   b8 00 03 57 40 20 a8 0d
        0060 - 006F 80 28 f0 de c4 30 00 00   00 00
    Message Content
        NR Radio Resource Control (RRC) protocol
            BCCH-DL-SCH-Message
                message: c1 (0)
                    c1: systemInformationBlockType1 (1)
                        systemInformationBlockType1
                            cellSelectionInfo
                                q-RxLevMin: -130dBm (-65)
                            cellAccessRelatedInfo
                                plmn-IdentityInfoList: 1 item
                                    Item 0
                                        PLMN-IdentityInfo
                                            plmn-IdentityList: 1 item
                                                Item 0
                                                    PLMN-Identity
                                                        mcc: 3 items
                                                            Item 0
                                                                MCC-MNC-Digit: 0
                                                            Item 1
                                                                MCC-MNC-Digit: 0
                                                            Item 2
                                                                MCC-MNC-Digit: 1
                                                        mnc: 2 items
                                                            Item 0
                                                                MCC-MNC-Digit: 0
                                                            Item 1
                                                                MCC-MNC-Digit: 1
                                            trackingAreaCode: 000001 [bit length 24, 0000 0000  0000 0000  0000 0001 decimal value 1]
                                            cellIdentity: 0000000010 [bit length 36, 4 LSB pad bits, 0000 0000  0000 0000  0000 0000  0000 0000  0001 .... decimal value 1]
                                            cellReservedForOperatorUse: notReserved (1)
                            servingCellConfigCommon
                                downlinkConfigCommon
                                    frequencyInfoDL
                                        frequencyBandList: 1 item
                                            Item 0
                                                NR-MultiBandInfo
                                                    freqBandIndicatorNR: 78
                                        offsetToPointA: 252 PRBs
                                        scs-SpecificCarrierList: 1 item
                                            Item 0
                                                SCS-SpecificCarrier
                                                    offsetToCarrier: 0
                                                    subcarrierSpacing: kHz30 (1)
                                                    carrierBandwidth: 273
                                    initialDownlinkBWP
                                        genericParameters
                                            locationAndBandwidth: 1099
                                            subcarrierSpacing: kHz30 (1)
                                        pdcch-ConfigCommon: setup (1)
                                            setup
                                                controlResourceSetZero: 11
                                                searchSpaceZero: 0
                                                commonSearchSpaceList: 3 items
                                                    Item 0
                                                        SearchSpace
                                                            searchSpaceId: 1
                                                            controlResourceSetId: 0
                                                            monitoringSlotPeriodicityAndOffset: sl1 (0)
                                                                sl1: NULL
                                                            monitoringSymbolsWithinSlot: 8000 [bit length 14, 2 LSB pad bits, 1000 0000  0000 00.. decimal value 8192]
                                                            nrofCandidates
                                                                aggregationLevel1: n0 (0)
                                                                aggregationLevel2: n0 (0)
                                                                aggregationLevel4: n1 (1)
                                                                aggregationLevel8: n0 (0)
                                                                aggregationLevel16: n0 (0)
                                                            searchSpaceType: common (0)
                                                                common
                                                                    dci-Format0-0-AndFormat1-0
                                                    Item 1
                                                        SearchSpace
                                                            searchSpaceId: 2
                                                            controlResourceSetId: 0
                                                            monitoringSlotPeriodicityAndOffset: sl1 (0)
                                                                sl1: NULL
                                                            monitoringSymbolsWithinSlot: 8000 [bit length 14, 2 LSB pad bits, 1000 0000  0000 00.. decimal value 8192]
                                                            nrofCandidates
                                                                aggregationLevel1: n0 (0)
                                                                aggregationLevel2: n0 (0)
                                                                aggregationLevel4: n1 (1)
                                                                aggregationLevel8: n0 (0)
                                                                aggregationLevel16: n0 (0)
                                                            searchSpaceType: common (0)
                                                                common
                                                                    dci-Format0-0-AndFormat1-0
                                                    Item 2
                                                        SearchSpace
                                                            searchSpaceId: 3
                                                            controlResourceSetId: 0
                                                            monitoringSlotPeriodicityAndOffset: sl1 (0)
                                                                sl1: NULL
                                                            monitoringSymbolsWithinSlot: 8000 [bit length 14, 2 LSB pad bits, 1000 0000  0000 00.. decimal value 8192]
                                                            nrofCandidates
                                                                aggregationLevel1: n0 (0)
                                                                aggregationLevel2: n0 (0)
                                                                aggregationLevel4: n1 (1)
                                                                aggregationLevel8: n0 (0)
                                                                aggregationLevel16: n0 (0)
                                                            searchSpaceType: common (0)
                                                                common
                                                                    dci-Format0-0-AndFormat1-0
                                                searchSpaceSIB1: 0
                                                searchSpaceOtherSystemInformation: 3
                                                pagingSearchSpace: 2
                                                ra-SearchSpace: 1
                                        pdsch-ConfigCommon: setup (1)
                                            setup
                                                pdsch-TimeDomainAllocationList: 3 items
                                                    Item 0
                                                        PDSCH-TimeDomainResourceAllocation
                                                            mappingType: typeA (0)
                                                            startSymbolAndLength: 40
                                                    Item 1
                                                        PDSCH-TimeDomainResourceAllocation
                                                            mappingType: typeA (0)
                                                            startSymbolAndLength: 54
                                                    Item 2
                                                        PDSCH-TimeDomainResourceAllocation
                                                            mappingType: typeA (0)
                                                            startSymbolAndLength: 57
                                    bcch-Config
                                        modificationPeriodCoeff: n2 (0)
                                    pcch-Config
                                        defaultPagingCycle: rf256 (3)
                                        nAndPagingFrameOffset: quarterT (2)
                                            quarterT: 1
                                        ns: one (2)
                                        firstPDCCH-MonitoringOccasionOfPO: sCS120KHZoneT-SCS60KHZhalfT-SCS30KHZquarterT-SCS15KHZoneEighthT (3)
                                            sCS120KHZoneT-SCS60KHZhalfT-SCS30KHZquarterT-SCS15KHZoneEighthT: 1 item
                                                Item 0
                                                    sCS120KHZoneT-SCS60KHZhalfT-SCS30KHZquarterT-SCS15KHZoneEighthT item: 0
                                uplinkConfigCommon
                                    frequencyInfoUL
                                        scs-SpecificCarrierList: 1 item
                                            Item 0
                                                SCS-SpecificCarrier
                                                    offsetToCarrier: 0
                                                    subcarrierSpacing: kHz30 (1)
                                                    carrierBandwidth: 273
                                        p-Max: 23 dBm
                                    initialUplinkBWP
                                        genericParameters
                                            locationAndBandwidth: 1099
                                            subcarrierSpacing: kHz30 (1)
                                        rach-ConfigCommon: setup (1)
                                            setup
                                                rach-ConfigGeneric
                                                    prach-ConfigurationIndex: 159
                                                    msg1-FDM: one (0)
                                                    msg1-FrequencyStart: 0
                                                    zeroCorrelationZoneConfig: 0
                                                    preambleReceivedTargetPower: -104 dBm
                                                    preambleTransMax: n20 (7)
                                                    powerRampingStep: dB4 (2)
                                                    ra-ResponseWindow: sl20 (5)
                                                ssb-perRACH-OccasionAndCB-PreamblesPerSSB: oneHalf (2)
                                                    oneHalf: n64 (15)
                                                ra-ContentionResolutionTimer: sf64 (7)
                                                rsrp-ThresholdSSB: -138dBm <= SS-RSRP < -137dBm (19)
                                                prach-RootSequenceIndex: l139 (1)
                                                    l139: 1
                                                msg1-SubcarrierSpacing: kHz30 (1)
                                                restrictedSetConfig: unrestrictedSet (0)
                                        pusch-ConfigCommon: setup (1)
                                            setup
                                                pusch-TimeDomainAllocationList: 4 items
                                                    Item 0
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 41
                                                    Item 1
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 55
                                                    Item 2
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 38
                                                    Item 3
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 38
                                                msg3-DeltaPreamble: 4dB (2)
                                                p0-NominalWithGrant: -96 dBm
                                        pucch-ConfigCommon: setup (1)
                                            setup
                                                pucch-ResourceCommon: 0
                                                pucch-GroupHopping: neither (0)
                                                hoppingId: 0
                                                p0-nominal: -96 dBm
                                    timeAlignmentTimerCommon: infinity (7)
                                ssb-PositionsInBurst
                                    inOneGroup: 80 [bit length 8, 1000 0000 decimal value 128]
                                ssb-PeriodicityServingCell: ms20 (2)
                                tdd-UL-DL-ConfigurationCommon
                                    referenceSubcarrierSpacing: kHz30 (1)
                                    pattern1
                                        dl-UL-TransmissionPeriodicity: ms2p5 (5)
                                        nrofDownlinkSlots: 3
                                        nrofDownlinkSymbols: 6
                                        nrofUplinkSlots: 1
                                        nrofUplinkSymbols: 4
                                ss-PBCH-BlockPower: 0 dBm
                            ue-TimersAndConstants
                                t300: ms400 (3)
                                t301: ms400 (3)
                                t310: ms2000 (6)
                                n310: n10 (6)
                                t311: ms3000 (1)
                                n311: n1 (0)
                                t319: ms400 (3)

TRACE_NAME, NRRC_SI_TRACE_PEER_SIB1_EXT
```
:::
### Metanoia_SIB
:::spoiler
```c=
22220, 1961, 38065328, 09:32:22:828 2024/09/20, MOD_NRRC_SI, , MOD_NRRC_SI_OTA_UH, [NW->MS] Dedicated SIB1 (DL_FREQ_BAND[78], SSB_ARFCN[[630048], PCI[3], SCS[NR_SCS_30])
Peer Message
    global_peer_msg_id = PROTOCOL_SECTION__NR_RRC_BCCH_DL_SCH (enum 1310)
    raw data = Buffer
        0000 - 000F 64 80 0a 02 08 00 80 40   00 00 40 00 00 00 06 b0
        0010 - 001F 84 d0 fc 00 00 31 06 04   4b 35 fd 85 b8 20 10 00
        0020 - 002F 00 40 41 b8 40 10 00 00   40 41 b8 60 10 00 00 40
        0030 - 003F 40 00 61 03 48 50 36 1c   87 4c c0 00 20 00 03 10
        0040 - 004F d5 c0 89 66 54 9f 00 00   c4 f5 5f c9 c0 49 39 c2
        0050 - 005F a9 85 6f 0a 9a 15 33 6a   b8 00 03 57 10 08 2a 03
        0060 - 006F 60 0a 3c 37 b1 0c 00 00   00 00
    Message Content
        NR Radio Resource Control (RRC) protocol
            BCCH-DL-SCH-Message
                message: c1 (0)
                    c1: systemInformationBlockType1 (1)
                        systemInformationBlockType1
                            cellSelectionInfo
                                q-RxLevMin: -130dBm (-65)
                            cellAccessRelatedInfo
                                plmn-IdentityInfoList: 1 item
                                    Item 0
                                        PLMN-IdentityInfo
                                            plmn-IdentityList: 1 item
                                                Item 0
                                                    PLMN-Identity
                                                        mcc: 3 items
                                                            Item 0
                                                                MCC-MNC-Digit: 0
                                                            Item 1
                                                                MCC-MNC-Digit: 0
                                                            Item 2
                                                                MCC-MNC-Digit: 1
                                                        mnc: 2 items
                                                            Item 0
                                                                MCC-MNC-Digit: 0
                                                            Item 1
                                                                MCC-MNC-Digit: 1
                                            trackingAreaCode: 000001 [bit length 24, 0000 0000  0000 0000  0000 0001 decimal value 1]
                                            cellIdentity: 0000000010 [bit length 36, 4 LSB pad bits, 0000 0000  0000 0000  0000 0000  0000 0000  0001 .... decimal value 1]
                                            cellReservedForOperatorUse: notReserved (1)
                            servingCellConfigCommon
                                downlinkConfigCommon
                                    frequencyInfoDL
                                        frequencyBandList: 1 item
                                            Item 0
                                                NR-MultiBandInfo
                                                    freqBandIndicatorNR: 78
                                        offsetToPointA: 252 PRBs
                                        scs-SpecificCarrierList: 1 item
                                            Item 0
                                                SCS-SpecificCarrier
                                                    offsetToCarrier: 0
                                                    subcarrierSpacing: kHz30 (1)
                                                    carrierBandwidth: 273
                                    initialDownlinkBWP
                                        genericParameters
                                            locationAndBandwidth: 1099
                                            subcarrierSpacing: kHz30 (1)
                                        pdcch-ConfigCommon: setup (1)
                                            setup
                                                controlResourceSetZero: 11
                                                searchSpaceZero: 0
                                                commonSearchSpaceList: 3 items
                                                    Item 0
                                                        SearchSpace
                                                            searchSpaceId: 1
                                                            controlResourceSetId: 0
                                                            monitoringSlotPeriodicityAndOffset: sl1 (0)
                                                                sl1: NULL
                                                            monitoringSymbolsWithinSlot: 8000 [bit length 14, 2 LSB pad bits, 1000 0000  0000 00.. decimal value 8192]
                                                            nrofCandidates
                                                                aggregationLevel1: n0 (0)
                                                                aggregationLevel2: n0 (0)
                                                                aggregationLevel4: n1 (1)
                                                                aggregationLevel8: n0 (0)
                                                                aggregationLevel16: n0 (0)
                                                            searchSpaceType: common (0)
                                                                common
                                                                    dci-Format0-0-AndFormat1-0
                                                    Item 1
                                                        SearchSpace
                                                            searchSpaceId: 2
                                                            controlResourceSetId: 0
                                                            monitoringSlotPeriodicityAndOffset: sl1 (0)
                                                                sl1: NULL
                                                            monitoringSymbolsWithinSlot: 8000 [bit length 14, 2 LSB pad bits, 1000 0000  0000 00.. decimal value 8192]
                                                            nrofCandidates
                                                                aggregationLevel1: n0 (0)
                                                                aggregationLevel2: n0 (0)
                                                                aggregationLevel4: n1 (1)
                                                                aggregationLevel8: n0 (0)
                                                                aggregationLevel16: n0 (0)
                                                            searchSpaceType: common (0)
                                                                common
                                                                    dci-Format0-0-AndFormat1-0
                                                    Item 2
                                                        SearchSpace
                                                            searchSpaceId: 3
                                                            controlResourceSetId: 0
                                                            monitoringSlotPeriodicityAndOffset: sl1 (0)
                                                                sl1: NULL
                                                            monitoringSymbolsWithinSlot: 8000 [bit length 14, 2 LSB pad bits, 1000 0000  0000 00.. decimal value 8192]
                                                            nrofCandidates
                                                                aggregationLevel1: n0 (0)
                                                                aggregationLevel2: n0 (0)
                                                                aggregationLevel4: n1 (1)
                                                                aggregationLevel8: n0 (0)
                                                                aggregationLevel16: n0 (0)
                                                            searchSpaceType: common (0)
                                                                common
                                                                    dci-Format0-0-AndFormat1-0
                                                searchSpaceSIB1: 0
                                                searchSpaceOtherSystemInformation: 3
                                                pagingSearchSpace: 2
                                                ra-SearchSpace: 1
                                        pdsch-ConfigCommon: setup (1)
                                            setup
                                                pdsch-TimeDomainAllocationList: 3 items
                                                    Item 0
                                                        PDSCH-TimeDomainResourceAllocation
                                                            mappingType: typeA (0)
                                                            startSymbolAndLength: 40
                                                    Item 1
                                                        PDSCH-TimeDomainResourceAllocation
                                                            mappingType: typeA (0)
                                                            startSymbolAndLength: 54
                                                    Item 2
                                                        PDSCH-TimeDomainResourceAllocation
                                                            mappingType: typeA (0)
                                                            startSymbolAndLength: 57
                                    bcch-Config
                                        modificationPeriodCoeff: n2 (0)
                                    pcch-Config
                                        defaultPagingCycle: rf256 (3)
                                        nAndPagingFrameOffset: quarterT (2)
                                            quarterT: 1
                                        ns: one (2)
                                        firstPDCCH-MonitoringOccasionOfPO: sCS120KHZoneT-SCS60KHZhalfT-SCS30KHZquarterT-SCS15KHZoneEighthT (3)
                                            sCS120KHZoneT-SCS60KHZhalfT-SCS30KHZquarterT-SCS15KHZoneEighthT: 1 item
                                                Item 0
                                                    sCS120KHZoneT-SCS60KHZhalfT-SCS30KHZquarterT-SCS15KHZoneEighthT item: 0
                                uplinkConfigCommon
                                    frequencyInfoUL
                                        scs-SpecificCarrierList: 1 item
                                            Item 0
                                                SCS-SpecificCarrier
                                                    offsetToCarrier: 0
                                                    subcarrierSpacing: kHz30 (1)
                                                    carrierBandwidth: 273
                                        p-Max: 23 dBm
                                    initialUplinkBWP
                                        genericParameters
                                            locationAndBandwidth: 1099
                                            subcarrierSpacing: kHz30 (1)
                                        rach-ConfigCommon: setup (1)
                                            setup
                                                rach-ConfigGeneric
                                                    prach-ConfigurationIndex: 159
                                                    msg1-FDM: one (0)
                                                    msg1-FrequencyStart: 0
                                                    zeroCorrelationZoneConfig: 0
                                                    preambleReceivedTargetPower: -104 dBm
                                                    preambleTransMax: n20 (7)
                                                    powerRampingStep: dB4 (2)
                                                    ra-ResponseWindow: sl20 (5)
                                                ssb-perRACH-OccasionAndCB-PreamblesPerSSB: oneHalf (2)
                                                    oneHalf: n64 (15)
                                                ra-ContentionResolutionTimer: sf64 (7)
                                                rsrp-ThresholdSSB: -138dBm <= SS-RSRP < -137dBm (19)
                                                prach-RootSequenceIndex: l139 (1)
                                                    l139: 1
                                                msg1-SubcarrierSpacing: kHz30 (1)
                                                restrictedSetConfig: unrestrictedSet (0)
                                        pusch-ConfigCommon: setup (1)
                                            setup
                                                pusch-TimeDomainAllocationList: 4 items
                                                    Item 0
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 41
                                                    Item 1
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 55
                                                    Item 2
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 38
                                                    Item 3
                                                        PUSCH-TimeDomainResourceAllocation
                                                            k2: 2
                                                            mappingType: typeB (1)
                                                            startSymbolAndLength: 38
                                                msg3-DeltaPreamble: 4dB (2)
                                                p0-NominalWithGrant: -96 dBm
                                        pucch-ConfigCommon: setup (1)
                                            setup
                                                pucch-ResourceCommon: 0
                                                pucch-GroupHopping: neither (0)
                                                hoppingId: 0
                                                p0-nominal: -96 dBm
                                    timeAlignmentTimerCommon: infinity (7)
                                n-TimingAdvanceOffset: n0 (0)
                                ssb-PositionsInBurst
                                    inOneGroup: 80 [bit length 8, 1000 0000 decimal value 128]
                                ssb-PeriodicityServingCell: ms20 (2)
                                tdd-UL-DL-ConfigurationCommon
                                    referenceSubcarrierSpacing: kHz30 (1)
                                    pattern1
                                        dl-UL-TransmissionPeriodicity: ms2p5 (5)
                                        nrofDownlinkSlots: 3
                                        nrofDownlinkSymbols: 6
                                        nrofUplinkSlots: 1
                                        nrofUplinkSymbols: 4
                                ss-PBCH-BlockPower: 0 dBm
                            ue-TimersAndConstants
                                t300: ms400 (3)
                                t301: ms400 (3)
                                t310: ms2000 (6)
                                n310: n10 (6)
                                t311: ms3000 (1)
                                n311: n1 (0)
                                t319: ms400 (3)

TRACE_NAME, NRRC_SI_TRACE_PEER_SIB1_EXT
```
:::
### provide ETL doc and SIB/MIB to VIAVI

![image](https://hackmd.io/_uploads/rkeVyZFR0.png)

### Liteon_MIB
```c=
16663, 2151, 45185013, 09:39:58:696 2024/09/20, MOD_NRRC_SI, , MOD_NRRC_SI_OTA_UH, [NW->MS] SIB1 (DL_FREQ_BAND[78], SSB_ARFCN[[630048], PCI[0], SCS[NR_SCS_30])
Peer Message
    global_peer_msg_id = PROTOCOL_SECTION__NR_START (enum 1300)
    raw data = Buffer
        0000 - 000F 31 c5 86
    Message Content
        NR Radio Resource Control (RRC) protocol
            BCCH-BCH-Message
                message: mib (0)
                    mib
                        systemFrameNumber: 60 [bit length 6, 2 LSB pad bits, 0110 00.. decimal value 24]
                        subCarrierSpacingCommon: scs30or120 (1)
                        ssb-SubcarrierOffset: 12
                        dmrs-TypeA-Position: pos2 (0)
                        pdcch-ConfigSIB1
                            controlResourceSetZero: 11
                            searchSpaceZero: 0
                        cellBarred: notBarred (1)
                        intraFreqReselection: notAllowed (1)
                        spare: 00 [bit length 1, 7 LSB pad bits, 0... .... decimal value 0]

TRACE_NAME, NRRC_SI_TRACE_PEER_SIB1
```
:::
### Metanoia_MIB
```c=
22188, 1957, 38065324, 09:32:22:828 2024/09/20, MOD_NRRC_SI, , MOD_NRRC_SI_OTA_UH, [NW->MS] SIB1 (DL_FREQ_BAND[78], SSB_ARFCN[[630048], PCI[3], SCS[NR_SCS_30])
Peer Message
    global_peer_msg_id = PROTOCOL_SECTION__NR_START (enum 1300)
    raw data = Buffer
        0000 - 000F 71 c5 86
    Message Content
        NR Radio Resource Control (RRC) protocol
            BCCH-BCH-Message
                message: mib (0)
                    mib
                        systemFrameNumber: e0 [bit length 6, 2 LSB pad bits, 1110 00.. decimal value 56]
                        subCarrierSpacingCommon: scs30or120 (1)
                        ssb-SubcarrierOffset: 12
                        dmrs-TypeA-Position: pos2 (0)
                        pdcch-ConfigSIB1
                            controlResourceSetZero: 11
                            searchSpaceZero: 0
                        cellBarred: notBarred (1)
                        intraFreqReselection: notAllowed (1)
                        spare: 00 [bit length 1, 7 LSB pad bits, 0... .... decimal value 0]

TRACE_NAME, NRRC_SI_TRACE_PEER_SIB1
```


## (9/19) Unsupported Mplane standard
:::success
[log & script](https://drive.google.com/drive/folders/19seRwJ8eX_TNZN2lIGL-0dxX7RtmbloK?usp=drive_link)
:::
### no configuration mode testing
```c=
# RadioScriptStart_0

###############################
# TEST STEP: Raw Text Command #
###############################

#--
# Configure Licensing
MULT STANDALONE
SCXT 0 LTE
SCXT 1 NR
LSST
LSCI
#--

###############################
# TEST STEP: Raw Text Command #
###############################

#--
# Configure Radio Cards
MULT STANDALONE
SCXT 0 LTE
SCXT 1 NR
SELR 0 FES-0 ORU1-P1 1 ORAN RU1
CFGR 0 SCS 1 1
EREF 0 0 0
ADDR 0 FES-0 ORU1-P1 ORAN RU1
GETR
LGPC
#--

###############################
# TEST STEP: Raw Text Command #
###############################

#--
CONF ORU ORU1 SysrepoInitFile UNH_demo_PRACH.csv
CONF ORU ORU1 MtuOverride 9216
CONF ORU ORU1 DuMacAddress 00:11:22:33:44:66
#--

###############################
# TEST STEP: Raw Text Command #
###############################

#--
SCFG MTS_MODE
#--

```
![image](https://hackmd.io/_uploads/S1O687dpC.png)
- Error log
```c=
19/09/24 13:37:29:052	SCFG MTS_MODE
19/09/24 13:38:01:119	C: SCFG 0x06 Failure - Master Initialise failed.
19/09/24 13:38:01:187	I: CMPI 0x00000014 Failed to create RU emulator: Unsupported Mplane standard: 10 Check that there is an "MPlaneStandard=<value>" entry in the header section of the configuration file: UNH_demo_PRACH.csv
```


## (9/16) Asking VIAVI for help

![image](https://hackmd.io/_uploads/r1l5_sDTA.png)
![image](https://hackmd.io/_uploads/rkAaOjP6R.png)



## (9/12) Status 
sib1搜不到, 試試看init add
- (1). Add “ SETP L0_DL_SRP_NR_SET_MAX_PDCCH_DMRS_QUALITY_IND 1”
- (2). Remove “SETP L0_DL_SRP_NR_SET_MAX_PDCCH_DMRS_QUALITY_IND 1” and add “SETP L0_ORP_NR_DL_GAIN 70”




![image](https://hackmd.io/_uploads/HyfmrGWp0.png)

| Item                       | ARFCN  | Frquency |
|:-------------------------- | ------ |:-------- |
| absoluteFrequencySSB       | 630048 | 3450.720 |
| dl_absoluteFrequencyPointA | 626772 | 3401.580 |

>[5G Frequency Сalculator](https://5g-tools.com/5g-nr-arfcn-calculator/)

- Next Step 
![image](https://hackmd.io/_uploads/SynerM-aR.png)




## (9/10) Status RRC: System Information acquisition failed

- CMPI L1 NR L1: The detected Cell information shows the Cell Id and RSRP (Received Signal Strength Index) values. Here Cell Id is 0 and RSRP is 0.5 dBm.
- The ==error code 8030== was returned when running NRSETMUERADIOCONTEXTCELL.
![image](https://hackmd.io/_uploads/H12mOERnR.png)

```c=
11/09/24 05:20:07:544	FORW MTE NRSETMUERADIOCONTEXTCELL 0 0 34507.2 100 [] [] [] [] [] [1] [] [] [2 2] [] [] [] [0] [] []
11/09/24 05:20:07:583	I: CMPI L1 NR L1: Detected Cell info on Hwi 0 is: Cell Id 0 RSRP 0.5 dBm, Cell Id - RSRP - dBm, Cell Id - RSRP - dBm
11/09/24 05:20:08:788	C: FORW 0x06 Failure MTE NRSETMUERADIOCONTEXTCELL: RETURN CODE:8030 FAILED
11/09/24 05:20:08:788	   RRC: System Information acquisition failed
```

![image](https://hackmd.io/_uploads/SJgX9jAnC.png)

---



### Configure PHY Capabilities
![image](https://hackmd.io/_uploads/rkFadM0hC.png)
![image](https://hackmd.io/_uploads/Hyb0OzRhR.png)

---
### Defind MTS Cells
![image](https://hackmd.io/_uploads/HyY1oGC2C.png)
![image](https://hackmd.io/_uploads/rJBAqzR2A.png)
![image](https://hackmd.io/_uploads/rkcgoz02C.png)

---

### MTS Scenario
#### MtsConfigPath 
forw mte MtsConfigPath 0 2{32 43 [],110 55 []}
![image](https://hackmd.io/_uploads/BJU8mSChC.png)


#### MtsConfigMobility 
```c=
forw mte MtsConfigMobility 0 0 0 1 0 0 0 -80 [] [] [] [] [1(-120 [0] [] [])]
```
![image](https://hackmd.io/_uploads/SJh_XrCnR.png)
![image](https://hackmd.io/_uploads/SyMhQH020.png)

#### MtsConfigTrafficProfile 
```c=
forw mte MtsConfigTrafficProfile 0 1{oai 0 [] [] [] [] [] []}
```
![image](https://hackmd.io/_uploads/rkD77rRh0.png)

#### MtsConfigTraffic 
```c=
forw mte MtsConfigTraffic 0 0 0 [10 0 0 -1(2000)] [] [] [] [] [5G:mnc001.mcc001.3gppnetwork.org]
```
![image](https://hackmd.io/_uploads/BkBNNHC3C.png)

#### MtsConfigScenario 
```c=
forw mte MtsConfigScenario 1{900 1{0 0} 1{0 0} []}
```
![image](https://hackmd.io/_uploads/HyqWErCh0.png)


## (9/9) Status Invalid NumUeDlRxAnts
### Configure MTS Cells modify the number of antennas
![image](https://hackmd.io/_uploads/BJbzUNC2C.png)
```c=
11/09/24 05:10:54:690	FORW MTE NRSETMUERADIOCONTEXTCELL 0 0 34507.2 100 [] [] [4] [] [] [1] [] [] [2 2] [] [] [] [0] [] []
11/09/24 05:10:54:729	I: CMPI L1 NR L1: Detected Cell info on Hwi 0 is: Cell Id 0 RSRP 0.5 dBm, Cell Id - RSRP - dBm, Cell Id - RSRP - dBm
11/09/24 05:10:54:994	I: ERNO 0x00000000 CASS 0x00000008 NR_L0_DL_SRP.CTRL_UE.DLDCI: "Hwi: 0, Invalid NumUeDlRxAnts: 2, Sfn: 1016, Subframe: 0, Slot: 0" at line 331 of ../nr_fw_app_l0/modules/dl_srp/code/dl_srp_handler_pdsch_info.cpp
11/09/24 05:10:55:944	C: FORW 0x06 Failure MTE NRSETMUERADIOCONTEXTCELL: RETURN CODE:8030 FAILED
11/09/24 05:10:55:944	   RRC: System Information acquisition failed
11/09/24 05:11:05:015	I: ERNO 0x00000000 CASS 0x00000009 47 repeated CAssert(s) suppressed
```
:::success
Reaseon
"Hwi: 0, Invalid NumUeDlRxAnts: 2, Sfn: 1016, Subframe: 0, Slot: 0"

The Invalid NumUeDlRxAnts: 2 mentioned here means that the ==number of downlink antennas== is invalid, which may mean that the number of antennas expected by the system does not match the actual configuration.

> Defind MTS Cells modify the number of antennas to 2
:::
### Configure USIM
![image](https://hackmd.io/_uploads/rkR_OG0hR.png)
![image](https://hackmd.io/_uploads/rklu_fR2R.png)
![image](https://hackmd.io/_uploads/SJ79dGRnA.png)

---








## (9/6) Status : Connection settings conflict with current script
The current script is connected with mts mode, and when running mts_radiocontext.txt, the MTS mode cmd will be repeated in front of it, causing TMA error.

---

### Suggested modifications

Try to change the setradiocontext information from the script you gave you earlier, change it to load the ==oran.cfg== and ==.csv== you got, and ==add the conf ORU== in it
![image](https://hackmd.io/_uploads/SyQTsRohC.png)
![image](https://hackmd.io/_uploads/Sy9Gh0onR.png)

---

### Configure Radio Contexts
![image](https://hackmd.io/_uploads/rJoRQfRhA.png)
![image](https://hackmd.io/_uploads/Sk1xVz0n0.png)



---



## (9/3) Status : Raise question and get response
- ==dpdk cap== is for ==cu-plane pcap==
- ==ptp== To get in from root use the ==tcpdump eth== method.

:::info
另外可以試試看這版本TMA(7.4.0)是否已經支持如下的 cmd line lcap 指令
- Example Command/CNF/IND to capture 100MB size file with the CU-plane traffic on ORU Port1
```bash=
LCAP ORU RU1 DPDK 100MB ORU-P1
```
Result
![image](https://hackmd.io/_uploads/BkqZtwS2A.png)

---
- Example Command/CNF/IND to capture 10MB size file with the S-plane traffic on ORU Port1
```bash=
LCAP ORU RU1 TCP 10MB ORU-P1 
```

Result
![image](https://hackmd.io/_uploads/HJxzAYPrhA.png)

---
- ptp status
- [tm500_root_0904.pcap](https://drive.google.com/file/d/11cl3PfjlajcDjYOrPKrjDwGVoVumyHet/view?usp=drive_link)

![image](https://hackmd.io/_uploads/SJdF3SBn0.png)

![image](https://hackmd.io/_uploads/Skiv2HH3A.png)

:::

## (9/2) Status : MULT 0x06 Failure - command invalid in this state.
:::success
### TMA part
![TestCaseScript.txt](https://hackmd.io/_uploads/BJdcjeQnR.png)
    - testing
        - [TestCaseScript.txt](https://drive.google.com/file/d/1vxKpd-591MCBPpvI9oGY41fIeZz4BK_2/view?usp=drive_link)
            - [MTS_RadioConfig](https://drive.google.com/file/d/1AVsQTZs192p_MB8qt8ZF2168ebF8Q81K/view?usp=drive_link)
            - [TestScript.txt](https://drive.google.com/file/d/1CirnLSexAwDBOEN0xv0VP6IXtRjI4U4I/view?usp=drive_link)
### OAI gNB part
[gnb.sa.band78.273prb.fhi72.2x2-TM500_johnson.conf](https://drive.google.com/file/d/1HhugOQAQevHLyWD6Id1gqEzOlQvnuQFJ/view?usp=drive_link)

![image](https://hackmd.io/_uploads/BJbvolQhA.png)

### Status
- TMA
Status : (C/U PLANE ACTIVE)
![image](https://hackmd.io/_uploads/ByvhJ-7hA.png)

- OAI gNB
Status : (Can get C/U PLANE packet)
![image](https://hackmd.io/_uploads/ryNdx-7hA.png)
:::
## (8/27) Status : TMA can get C/U plane packet on time
:::success

Adjust timing of RUSIM Timing window

- File location : `\VIAVI\TM500\5G NR - NLC 1.4.0\ppc_pq\public\ftp_root\oran_oai_2x2_neu_johnson_9BFP_1500.csv`

![image](https://hackmd.io/_uploads/SyW3kfiiR.png)

---
- U plane late 
    1. [RX_LATEST_UL]() : 1453.061 us
    2. Adjust t2a-min-up
        - Before : 7000 ns
        - After : 7000 - 1453 = 5547
 
- C plane UL early
    1. [RX_EARLIEST_C_UL]() : 6.071 us
    2. Adjust t2a-max-cp-ul
        - Before : 770000 ns
        - After : 770000 + 6071 = 776071
- C plane DL late
    1. [RX_LATEST_C_DL]() : 1453.765 us
    2. Adjust t2a-max-cp-dl
        - Before : 470000
        - After : 470000 + 1453765 = 1923765

---
![image](https://hackmd.io/_uploads/HyjlRGijR.png)
:::
---
## (8/26) Status : OAI can get C/U plane packet 
:::success
### Testing OAI gNB with NTUST conf in New TMA
![image](https://hackmd.io/_uploads/SJnPmnqjC.png)

### Testing OAI gNB with NEU conf in New TMA

26/08/24 18:15:21:162	C: SCFG 0x06 [Failure]() - Architecture Selection failed.
![image](https://hackmd.io/_uploads/r1jhpJcsC.png)

Check whether the system resources (such as memory and processor) are sufficient to support the OAI configuration provided by the NEU

檢查基站中CPU相關的配置
- L1 設定：
`L1_rx_thread_core`：
Specifies the core of the L1 thread used for ==receiving==.
`L1_tx_thread_core`：
Specifies the core of the L1 thread used for ==sending==.

| Item              | NTUST | NEU |
|:----------------- | ----- |:--- |
| L1_rx_thread_core | 8     | 0   |
| L1_tx_thread_core | 10    | 1   |

- RU 配置：
`ru_thread_core`：
Specify thread cores to use for RU

| Item           | NTUST | NEU |
|:-------------- | ----- |:--- |
| ru_thread_core | 6     | 2   |


- fhi_72 設定：
`system_core`：
Specify the core used by the system
`io_core`：
Specify the core to use for I/O operations
`worker_cores`：
Specify the cores used by the worker threads

| Item         | NTUST | NEU    |
|:------------ | ----- |:------ |
| system_core  | 0     | 3      |
| io_core      | 4     | 8      |
| worker_cores | 2     | (9,10) |

- Result : Can get C/U plane packet 

![image](https://hackmd.io/_uploads/B19L5zciR.png)

:::
---

![image](https://hackmd.io/_uploads/Syn-Teco0.png)

:::spoiler
```c=
26/08/24 20:27:45:596	FORW MTE GETRUSTATS
26/08/24 20:27:45:615	C: FORW 0x00 Ok MTE GETRUSTATS [ALL]
26/08/24 20:27:45:615	RU Id: 0
26/08/24 20:27:45:615	RU Name: ORU1
26/08/24 20:27:45:615	ELAPSED_TIME:    47016.0 s
26/08/24 20:27:45:615	[EAXCID]: ALL 
26/08/24 20:27:45:615	RX-WINDOW-STATS 
26/08/24 20:27:45:615	[RX_TOTAL]: 78494218 
26/08/24 20:27:45:615	[RX_ERR_DROP]: 1036 
26/08/24 20:27:45:615	[RX_EARLY(_U)]: 0 
26/08/24 20:27:45:615	[RX_ON_TIME(_U)]: 77007186 
26/08/24 20:27:45:615	[RX_LATE(_U)]: 10 
26/08/24 20:27:45:615	[RX_SEQID_ERR(_U)]: 0 
26/08/24 20:27:45:615	[RX_EARLY_C_DL]: 0 
26/08/24 20:27:45:615	[RX_ON_TIME_C_DL]: 1009416 
26/08/24 20:27:45:615	[RX_LATE_C_DL]: 6 
26/08/24 20:27:45:615	[RX_EARLY_C_UL]: 526 
26/08/24 20:27:45:615	[RX_ON_TIME_C_UL]: 477066 
26/08/24 20:27:45:615	[RX_LATE_C_UL]: 0 
26/08/24 20:27:45:615	[RX_SEQID_ERR_C]: 0 
26/08/24 20:27:45:615	[RX_CORRUPT]: 0 
26/08/24 20:27:45:615	[RX_INVALID_PRB]: 486 
26/08/24 20:27:45:615	[RX_INCONS_SYM_COUNT]: 178 
26/08/24 20:27:45:615	RX-WINDOW-TIMING 
26/08/24 20:27:45:615	[RX_EARLIEST(_U)]: -77.122 us
26/08/24 20:27:45:615	[RX_LATEST(_U)]: 1453.061 us
26/08/24 20:27:45:615	[RX_EARLIEST_C_UL]: 6.071 us
26/08/24 20:27:45:615	[RX_LATEST_C_UL]: -94.032 us
26/08/24 20:27:45:615	[RX_EARLIEST_C_DL]: -39.963 us
26/08/24 20:27:45:615	[RX_LATEST_C_DL]: 1453.765 us
```
:::
**Conclusion : Packet is not ontime.**


## (8/20) IA notebook Disk broken
:::success
- Status
    - TMA can't install by us
    - Prepare other device for install TMA
- Other
    - Confirm whether the computer is in need of repair
        - It seen can enter BIOS,maybe we can use another hard drive in the lab, or buy a new one.
:::


## (8/16) Testing with conf generate by ==NEU OAI_gNB yaml==
- Receive no C/U plane packet

![image](https://hackmd.io/_uploads/rJxeD55qR.png)

- TMA result 
![image](https://hackmd.io/_uploads/SkojUcc9R.png)

```c=
15/08/24 04:37:32:868	C: SDLI 0x00 Ok LTE_LA_RACH_STATS 0x00000001 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000 0x00000000
15/08/24 04:38:14:731	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
15/08/24 04:38:14:731	 >>>FhsUl_0_NR_I0: Same section Id 0 in a symbol with same/non complementary ReMask fff and fff" in ../tm_oran_app/oran_ru/oran_ru_ul_cuplane/code/oran_ru_ul_cuplane.cpp at line 4896
15/08/24 04:38:19:859	DTRC 2024_08_15_04_38_19_dump
15/08/24 04:38:19:877	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
15/08/24 04:38:19:877	 >>>FhsTimer_Main_I0: CRuUlCuPlane::HandleEthernetTx: Failed to queue m_EthernetTxQueue" in ../tm_oran_app/oran_ru/oran_ru_ul_cuplane/code/oran_ru_ul_cuplane.cpp at line 4930
15/08/24 04:38:20:006	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
15/08/24 04:38:20:006	 >>>FhsUl_1_NR_I0: Same section Id 0 in a symbol with same/non complementary ReMask fff and fff" in ../tm_oran_app/oran_ru/oran_ru_ul_cuplane/code/oran_ru_ul_cuplane.cpp at line 4896
15/08/24 04:38:20:155	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
15/08/24 04:38:20:155	 >>>OranRxEth_FE_0_I0: m_RngBuf full." in ../tm_oran_app/oran_ru/oran_ru_fifos/interface/oran_cplane_fifo.hpp at line 223
15/08/24 04:38:20:301	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
15/08/24 04:38:20:301	 >>>OranRxEth_FE_1_I0: m_RngBuf full." in ../tm_oran_app/oran_ru/oran_ru_fifos/interface/oran_cplane_fifo.hpp at line 223
15/08/24 04:38:58:333	C: DTRC 0x00 Ok
15/08/24 04:38:58:545	I: TMAE 0x1 Warning - Incident occurred (TM500 exception). Details are stored under C:\Users\bmwla\Documents\VIAVI\TM500\5G NR\Test Mobile Application\Logged Data\240815_042701_session\2024_08_15_04_38_58_371_Incident
```

- telnet command (can't catch any pcap)

![image](https://hackmd.io/_uploads/BJgjRq5qC.png)

:::danger
The parser does not understand the structure of the data correctly.
Verify that all relevant settings are correct, especially the dimension and format of the data.
:::
## (8/16) Testing with conf generate by ==BMW OAI_gNB yaml==
- Receive C/U plane packet

![image](https://hackmd.io/_uploads/H1MMCcc5A.png)

![image](https://hackmd.io/_uploads/HJY8nic5R.png)

# Troubleshooting
## Issue 1 SCFG 0x06 Failure - Architecture Selection failed.
:::info
檢查基站中CPU相關的配置
- L1 設定：
`L1_rx_thread_core`：指定用於==接收==的 L1 線程的核心。
`L1_tx_thread_core`：指定用於==發送==的 L1 線程的核心。

| Item              | NTUST | NEU |
|:----------------- | ----- |:--- |
| L1_rx_thread_core | 8     | 0   |
| L1_tx_thread_core | 10    | 1   |

- RU 配置：
`ru_thread_core`：指定用於 RU 的線程核心

| Item           | NTUST | NEU |
|:-------------- | ----- |:--- |
| ru_thread_core | 6     | 2   |


- fhi_72 設定：
`system_core`：指定系統使用的核心
`io_core`：指定 I/O 操作使用的核心
`worker_cores`：指定工作線程使用的核心

| Item         | NTUST | NEU    |
|:------------ | ----- |:------ |
| system_core  | 0     | 3      |
| io_core      | 4     | 8      |
| worker_cores | 2     | (9,10) |

:::
