---
title: TM500 & PPPoE Debug

---

[TOC]
## Reference
- [Record](https://hackmd.io/@Johnson-72/r1rv89qc0) 
- [Install pppoe for tm500 traffic test guide](https://hackmd.io/@Johnson-72/SJ40D_SXyx)

## Issue

### Issue 1 : VERR_NEM_NOT_AVAILABLE

進入 BIOS 設定：

開機時按下特定的鍵（通常是 F2、Delete、Esc 或 F10，具體取決於你的主機板）以進入 BIOS 設定。
啟用 VT-x：

在 BIOS 設定中，尋找與 CPU 或虛擬化技術相關的選項，通常在「Advanced」或「CPU Configuration」中。
找到「Intel Virtualization Technology」或「VT-x」選項，並將其設置為「Enabled」。


```c=
VM 名稱: Ubutu

Not in a hypervisor partition (HVP=0) (VERR_NEM_NOT_AVAILABLE).
VT-x is disabled in the BIOS for all CPU modes (VERR_VMX_MSR_ALL_VMX_DISABLED).

結果碼:
E_FAIL (0x80004005)
元件:
ConsoleWrap
介面:
IConsole {6ac83d89-6ee7-4e33-8ae6-b257b2e81be8}
```

![image](https://hackmd.io/_uploads/r1JigQ7Gye.png)
![image](https://hackmd.io/_uploads/S1VSZQQM1l.png)
![image](https://hackmd.io/_uploads/SyPm-mXM1e.png)

---
### Issue 2 : CPU Over Temperature Error

![image](https://hackmd.io/_uploads/B1LeRjQfJx.png)

- Please remove the mercury battery (cmos), discharge it and then reinsert it
- Please ==power off the device== before unplugging it

![image](https://hackmd.io/_uploads/HkIC6smzkx.png)

---

### Issue 3 : Ubuntu terminal no response or can't open

- Using tty terminal
`Ctrl + Alt + F3` : in tty terminal
`Ctrl + Alt + F2` : exit tty terminal

安裝 xterm 作為替代終端，嘗試能否開啟：
```c=
sudo apt update
sudo apt install xterm
```

- xterm 正常
![image](https://hackmd.io/_uploads/S1tmZhmG1g.png)

---

### Issue 4 : rp-pppoe source package download link Invalid

![image](https://hackmd.io/_uploads/r1vYg6QM1l.png)

- using new link
```c=
wget http://archive.ubuntu.com/ubuntu/pool/universe/r/rp-pppoe/rp-pppoe_3.12.orig.tar.gz
```

![image](https://hackmd.io/_uploads/Hk31XpmMkl.png)


### Issue 5 : no acceptable C compliler found in PATH

- 更新包列表：
```c=
sudo apt update
```
- 安裝 GCC，執行以下命令安裝 GCC 和相關的開發工具：
```c=
sudo apt install build-essential
```

- 確認安裝：
安裝完成後，可以使用以下命令確認 GCC 是否安裝成功：

```c=
gcc --version
```
- Result

![image](https://hackmd.io/_uploads/BkXc8TQG1x.png)

---
### Issue 6 : Compile rp-pppoe error


![image](https://hackmd.io/_uploads/S1pR267zJx.png)


Open rp-pppoe-3.12/src and check `plugin.c`

在 ==#include <linux/if_pppox.h>== 之前，添加以下兩行：
```c=
#define _LINUX_IN_H
#define _LINUX_IN6_H
```

- Before
![image](https://hackmd.io/_uploads/r1J7JC7Gyg.png)

After

![image](https://hackmd.io/_uploads/HyQOk0mGye.png)

- remake

![image](https://hackmd.io/_uploads/SJ8aJ0Qzyx.png)


### Issue 7 : ping connect network is unreachable
- before
![image](https://hackmd.io/_uploads/SkfvRsXXyg.png)

- change network card setting

![image](https://hackmd.io/_uploads/BJkTCsQX1e.png)

- After

![image](https://hackmd.io/_uploads/BJvly2Qm1e.png)

### Issue 8 : pppoe no receive PADO packet
![image](https://hackmd.io/_uploads/H16tMp4NJx.png)

![image](https://hackmd.io/_uploads/HyOGJL2Gyl.png)

:::success
Correct parameter `DTE_INTERFACE_NAME` of TM500 Clouede UE conf: `cuedock.yaml`

![image](https://hackmd.io/_uploads/H13ChaLEkl.png)

:::








### Issue 9 Network is unreachable

![image](https://hackmd.io/_uploads/HyiG2u6I1e.png)


- 如果 `DHCP` 服務未能自動分配 IP 地址，可以==手動請求 IP 地址==
```c=
sudo dhclient enp0s3
```

![image](https://hackmd.io/_uploads/SkmqidT8ke.png)


### Issue 10 One UE two PDU Session
:::danger
The expected result is ==multiple UE IDs==, but the actual result is ==single UE multiple PDU session==
:::
```bash=
31/12/24 01:55:56:327	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0 
31/12/24 01:55:56:767	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0 Selected PLMN: 00101F Pdu Session Id: 1 Data Network Name: internet IPv4 Address: 10.60.0.7 Registration Result: 3GPP Access Only 
31/12/24 01:55:57:055	I: CMPI MTE 0 NR PDU SESSION ESTABLISHMENT IND:UE Id:0 Pdu Session Id:2 Data Network Name: internet IPv4 Address: 10.60.0.8 QosRuleId:1 QosFlowId:1 FilterId:1 QosFlowId:1 5QI:9 UE Initiated Procedure 
31/12/24 01:56:00:663	I: CMPI DTE PPPOE CONNECTION IND: UE Id: 0 De Id: 1 PDU Session Id: 1 Default DRB Id: 1 PDN IP Address String: 10.60.0.7 
31/12/24 01:56:05:150	I: CMPI DTE PPPOE CONNECTION IND: UE Id: 0 De Id: 2 PDU Session Id: 2 Default DRB Id: 2 PDN IP Address String: 10.60.0.8
```

![image](https://hackmd.io/_uploads/BylbzPeIJg.png)

![image](https://hackmd.io/_uploads/S1G9URtI1e.png)

### Issue 11 PPPoE connect fail

Check whether `DTE_INTERFACE_NAME` in `cuedock.ymal` has been changed to the management port eth of this TM500. If not, please enter and down/up docker again.

![image](https://hackmd.io/_uploads/SkOf6AdHJg.png)


- TM500 ETH

![image](https://hackmd.io/_uploads/SJ5lC0_HJx.png)





###  Issue 12 SIB1 acquisition failure problem
```bash=
13/07/21 18:08:23:001    RRC: System Information acquisition failed
```
phase_compensation should be set to 0 to disable when it is performed in the RU and set to 1 when it should be performed on the DU side

```bash=
phase_compensation = 1 >>> phase_compensation = 0
```

![image](https://hackmd.io/_uploads/Sy3MPcaUye.png)
![image](https://hackmd.io/_uploads/BJWmw5pU1l.png)


### Issue 13 NG-Setup failure
>free5GC log
```c=
2024-10-30T13:45:48.260867445+08:00 [WARN][AMF][Ngap][ran_addr:192.168.8.29:57955] NG-Setup failure: Cannot find Served TAI in AMF
```
#### Error reason : 

Because TAI(tracking area identity) value in free5GC and OAI gNB is not match
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

###  Issue 14 Registration Reject
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

###  Issue 15 No PDU session accept
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

###  Issue 16 RRC UE Duplicate  PagingUeId (S-TMSI) or (IRNTI)
:::danger
![image](https://hackmd.io/_uploads/SyMS8JXvke.png)

- CN

```bash=
2024-1-6T13:17:37.401128177+08:00 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:12(3GPP)][ran_addr:192.168.8.43:53807] New RanUe [RanUeNgapID:1][AmfUeNgapID:12]
2024-1-6T13:17:37.401189864+08:00 [WARN][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:12(3GPP)][ran_addr:192.168.8.43:53807] Missing 5G-S-TMSI IE in InitialUEMessage; send ErrorIndication
2024-1-6T13:17:37.401229050+08:00 [INFO][AMF][Ngap][ran_addr:192.168.8.43:53807] Send Error Indication
2024-1-6T13:17:37.401755102+08:00 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:12(3GPP)][ran_addr:192.168.8.43:53807] Send UE Context Release Command
2024-1-6T13:17:37.402116069+08:00 [WARN][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:12(3GPP)][ran_addr:192.168.8.43:53807] AmfUe is nil
2024-1-6T13:17:37.603334470+08:00 [INFO][AMF][Ngap][ran_addr:192.168.8.43:53807] Handle UEContextReleaseComplete
2024-1-6T13:17:37.603478475+08:00 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:12(3GPP)][ran_addr:192.168.8.43:53807] Handle UEContextReleaseComplete (RAN UE NGAP ID: 1)
2024-1-6T13:17:37.603560633+08:00 [INFO][AMF][Ngap][ran_addr:192.168.8.43:53807] Release UE Context : RanUe[AmfUeNgapId: 12]
```

:::info
- A new RanUe (Radio Access Network User Equipment) is created and assigned a corresponding ID.
- When processing the Initial UE message, the 5G-S-TMSI information element is missing and the AMF shall send an error indication.
- The AMF has sent an error indication in response to a missing 5G-S-TMSI.
- The AMF sends a context release command to the UE, indicating that the UE context will be released.
- AMF issues a warning that AmfUe is nil, which may indicate that something went wrong during processing.
:::
:::danger
During the UE access process, the AMF may not correctly allocate a unique S-TMSI or IRNTI to each UE. To avoid duplicate Paging UE IDs (such as S-TMSI) in the 5G core network, may need to ensure that each UE is assigned a unique S-TMSI in the core network (CN).
::::::
#### Solution : Set different IMSI on different UEs
:::success
##### Make sure you have enough IMSI in CN
- Free5GC
[Set up additional IMSI on free5gc](https://hackmd.io/@Johnson-72/Byj-zpwL1l)

##### Setting different IMSI in script

![image](https://hackmd.io/_uploads/SkzsfJ7Dkl.png)
![image](https://hackmd.io/_uploads/BJic4J7vyl.png)

#### Result
- TMA
![image](https://hackmd.io/_uploads/H15Qry7wyg.png)

- CN
```bash=
2025-01-14T03:29:58.912472505+08:00 [INFO][AMF][Gmm][amf_ue_ngap_id:RU:2,AU:11(3GPP)][supi:SUPI:imsi-001010000000001] create smContext[pduSessionID: 1] Success
2025-01-14T03:30:01.893559444+08:00 [INFO][AMF][Gmm][amf_ue_ngap_id:RU:3,AU:12(3GPP)][supi:SUPI:imsi-001010000000002] create smContext[pduSessionID: 1] Success
2025-01-14T03:30:04.902394243+08:00 [INFO][AMF][Gmm][amf_ue_ngap_id:RU:4,AU:13(3GPP)][supi:SUPI:imsi-001010000000003] create smContext[pduSessionID: 1] Success
```
::::::

###  Issue 17 Only one UE can have PPPoE connect
:::danger
In this test, it is confirmed that ==4 UEs== have completed the ==PDU session Accept== and ==obtained the IP address==. However, only one UE can successfully connect during the PPPOE call.

- PDU Session Accept
```bash=
14/01/25 05:58:23:754	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0
  Selected PLMN: 00101F
  Pdu Session Id: 1
  Data Network Name: internet
  IPv4 Address: 10.60.0.10
  Registration Result: 3GPP Access Only


14/01/25 05:58:26:700	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:1
  Selected PLMN: 00101F
  Pdu Session Id: 1
  Data Network Name: internet
  IPv4 Address: 10.60.0.11
  Registration Result: 3GPP Access Only

14/01/25 05:58:29:716	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:2
  Selected PLMN: 00101F
  Pdu Session Id: 1
  Data Network Name: internet
  IPv4 Address: 10.60.0.12
  Registration Result: 3GPP Access Only

14/01/25 05:58:32:716	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:3
  Selected PLMN: 00101F
  Pdu Session Id: 1
  Data Network Name: internet
  IPv4 Address: 10.60.0.13
  Registration Result: 3GPP Access Only
```

![image](https://hackmd.io/_uploads/HJqgA-QwJe.png)

![image](https://hackmd.io/_uploads/SJQVRbQPyl.png)
::::::
#### Solution : Set different pppoe-service name
:::success
```c=
pppoe-service tm500_lte_192.168.8.67_0_${ue_num}
pppoe-service tm500_lte_192.168.8.67_1_${ue_num}
```
![image](https://hackmd.io/_uploads/SkPsg4Evkx.png)

![image](https://hackmd.io/_uploads/rJ9bgN4Pkx.png)

#### Result

![image](https://hackmd.io/_uploads/rkMKxV4wkl.png)
::::::

### (X)Issue 18 Can't do iperf through ETH'S IP
![image](https://hackmd.io/_uploads/SJyrsIiP1l.png)

![image](https://hackmd.io/_uploads/r1IhDpqPJx.png)

#### Soluation
```bash=
sudo ip route add 192.168.8.21 via 10.60.0.1 dev ppp0
```
- single
![image](https://hackmd.io/_uploads/HyLAT69D1x.png)
- multiple

If you want to access `192.168.8.21(CN)` through ==multiple PPP== interfaces (such as ppp0 and ppp1) at the same time, you need to use policy routing.

1. Add a new rule and select the routing table for the source address:

```bash=
sudo ip rule add from 10.60.0.1 table 100
sudo ip rule add from 10.60.0.2 table 101
```
2. Add new routes to groups:

```bash=
sudo ip route add 192.168.8.21 via 10.60.0.1 dev ppp0 table 100
sudo ip route add 192.168.8.21 via 10.60.0.2 dev ppp1 table 101
```
3. Verify policy rules:
```bash=
ip rule show
```
4. Verify the routing table:
```bash=
ip route show table 100
ip route show table 101
```

5. Delete all non-default routing rules
```bash=
sudo ip rule flush
```


![image](https://hackmd.io/_uploads/BJSK1R9DJl.png)

#### Single-UE testing

![image](https://hackmd.io/_uploads/SJNX_xjw1g.png)

```c=

20/01/25 06:07:58:760	I: CMPI L2 RLC maximum retransmissions reached (UE Id: 0, UL DRB Id: 1)
20/01/25 06:07:58:766	I: CMPI RRC RRC Connection Re-establishment Started: UE Id: 0
   Cause: RLC maximum UL retransmissions reached
20/01/25 06:07:58:766	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
20/01/25 06:07:58:793	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
20/01/25 06:07:58:796	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0x37E5, TimingAdv: 0, PreambleTxCount: 1)
20/01/25 06:07:58:796	I: CMPI RRC RRC Connection Re-establishment Success: UE Id: 0
   Old Cell Info:
      Cell Id: 0 DL Freq: 34507
   New Cell Info:
      Cell Id: 0 DL Freq: 34507
20/01/25 06:11:34:771	I: CMPI L2 RLC maximum retransmissions reached (UE Id: 0, UL DRB Id: 1)
20/01/25 06:11:34:773	I: CMPI RRC RRC Connection Re-establishment Started: UE Id: 0
   Cause: RLC maximum UL retransmissions reached
20/01/25 06:11:34:773	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
20/01/25 06:11:34:786	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
20/01/25 06:11:34:803	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0x3FBE, TimingAdv: 0, PreambleTxCount: 1)
20/01/25 06:11:34:803	I: CMPI RRC RRC Connection Re-establishment Success: UE Id: 0
   Old Cell Info:
      Cell Id: 0 DL Freq: 34507
   New Cell Info:
      Cell Id: 0 DL Freq: 34507

```
- RLC maximum retransmission times reached
```bash=
I: CMPI L2 RLC maximum retransmissions reached (UE Id: 0, UL DRB Id: 1)
```
The number of RLC retransmissions for uplink data has reached the maximum value, which may indicate poor network conditions or severe packet loss, resulting in the inability to successfully transmit data.

- RRC connection re-establishment starts
```bash=
I: CMPI RRC RRC Connection Re-establishment Started: UE Id: 0
 Cause: RLC maximum UL retransmissions reached
```
Because the maximum number of RLC retransmissions has been reached, RRC begins the process of reestablishing the connection.

#### Multi UE testing

![image](https://hackmd.io/_uploads/rkVZCxsv1g.png)

##### Client results:
UDP Bandwidth: The client communicates with the server, sending UDP packets of 1470 bytes with a target IPG of 224.30 microseconds (after Kalman adjustment). The test results show that the client stably transmits data at a bandwidth of 52.4 Mbit/s.

##### Server results:
- Jitter: The server reported a jitter of 131.073 ms, which is an unusually high value and usually indicates network instability.
- Packet loss: The server reports the number of lost packets as 4294964296/0 (inf%), which is incorrect. The packet loss ratio should be a normal number. This "inf%" is not valid data and may be an error in server statistics.
- The first report from the server shows a bandwidth of -nan (not a number), which indicates that the server is not processing the received data correctly under certain circumstances.


##### Observations and possible issues:
- High Jitter: The reported jitter is 131.073 ms, which is very high and indicates an unstable network connection. It could be a configuration problem with the router or switch, or it could be a problem with network congestion or latency.
- Packet loss/reporting errors: The server is showing a packet loss percentage of inf%, which indicates that there may be an error in the server's reporting tool. This may be because the server is not properly tracking or processing the received packets.
- UDP performance: Although the client's bandwidth was stable at 52.4 Mbit/s, the server did not seem to be able to process all packets correctly. This may indicate a problem with the server configuration, or that the server is running low on resources (e.g., processing power, memory, network interfaces, etc.).

:::info
##### Re-establishment Cause: **RLC maximum UL retransmissions reached**: 

The number of uplink data retransmissions at the RLC layer has reached the maximum value, which usually indicates a problem with the radio link quality, such as poor signal conditions or interference.

>Background: Radio Link Failure (RLF)
RRC connection re-establishment is usually associated with Radio Link Failure (RLF). When RLF occurs, the UE attempts to resume communication through RRC connection re-establishment.
---
##### Common triggering reasons for RLF:
- Uplink issues:
    - The uplink signal quality is poor (for example, the UE is too far away from the base station or the signal is blocked).
    - The uplink interference is too large, resulting in the inability to transmit data packets correctly.
    - The RLC layer reached the maximum number of uplink retransmissions (as shown in the log).
- Network configuration issues:
    - Improper RLC or RRC parameter configuration, for example, the number of retransmissions is set too low.
    - The timer setting is unreasonable, causing RLF to be triggered prematurely.
:::


### Issue 19 NAS: Registration failure - Lower Layer Failure: Retrying to Attach 
同樣適用`   Cause: NAS: There is no USIM present` 情況
```bash=
source $HOME/FH_7.2_dev/tm500vf.sh
cd $HOME/FH_7.2_dev/openairinterface5g/cmake_targets/ran_build/build
sudo ./nr-softmodem -O ../../../targets/PROJECTS/GENERIC-NR-5GC/CONF/gnb.sa.band78.273prb.fhi72.2x2-TM500_johnson.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72_su/OAI_gNB_LOG/johnson.log 2>&1
```
```bash=

12/02/25 23:24:43:124	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
12/02/25 23:24:43:125	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
12/02/25 23:24:43:137	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
12/02/25 23:24:43:168	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0x5282, TimingAdv: 0, PreambleTxCount: 1)
12/02/25 23:24:43:168	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0
12/02/25 23:24:46:123	I: CMPI MTE 0 EMM REGISTER IND:UE Id:1:RETURN CODE:6 FAILED
   Cause: NAS: There is no USIM present
12/02/25 23:24:49:123	I: CMPI MTE 0 EMM REGISTER IND:UE Id:2:RETURN CODE:6 FAILED
   Cause: NAS: There is no USIM present
12/02/25 23:24:52:124	I: CMPI MTE 0 EMM REGISTER IND:UE Id:3:RETURN CODE:6 FAILED
   Cause: NAS: There is no USIM present
```
:::success
#### 分析
- 1. 連接過程分析
    - UE 成功完成了 RRC 層的隨機接入與 NR 連接，代表物理層（PHY）、MAC、RLC、PDCP 和 RRC 層的交互正常。
    - 問題出現在 NAS 層的 EMM 註冊階段，UE 無法完成註冊，導致網路無法提供服務。
- 2. 問題與可能解決方案

| 問題   | 可能原因                               | 解決方案      |
| ------ | -------------------------------------- |:------------- |
| NAS 層 | EMM 註冊失敗(There is no USIM present) | UE 無 USIM 卡 |

- USIM 卡未正確插入
- USIM 卡損壞或未被網路識別	- 檢查 UE 是否有插入 USIM
- 更換 USIM 卡並測試
- 確保基站配置允許無 USIM 註冊（如支援緊急呼叫或測試模式）
---
:::info
#### Soluation
- 修改`NETWORK_INTERFACES`的設定
```bash=
    NETWORK_INTERFACES :
    {
        GNB_IPV4_ADDRESS_FOR_NG_AMF              = "192.168.8.82";
        GNB_IPV4_ADDRESS_FOR_NGU                 = "192.168.8.82";
        GNB_PORT_FOR_S1U                         = 2152; # Spec 2152
    };

```
:::

