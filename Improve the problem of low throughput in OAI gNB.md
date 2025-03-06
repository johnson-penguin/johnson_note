---
title: Improve the problem of low throughput in OAI gNB

---

## Test environment

![image](https://hackmd.io/_uploads/ry4HuGe91e.png)

---

## Test_1:
:::success
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=200Mbps，20sec
:::
---
### Result iperf:200M/20秒
:::success
```bash=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 192.168.8.21 -u -B 10.60.0.1 -b200M -t 20 -p 8889 -i 2 -R
Connecting to host 192.168.8.21, port 8889
Reverse mode, remote host 192.168.8.21 is sending
[  5] local 10.60.0.1 port 58859 connected to 192.168.8.21 port 8889
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  47.8 MBytes   201 Mbits/sec  0.059 ms  700/36526 (1.9%)  
[  5]   2.00-4.00   sec  47.5 MBytes   199 Mbits/sec  0.087 ms  105/35701 (0.29%)  
[  5]   4.00-6.00   sec  47.5 MBytes   199 Mbits/sec  0.074 ms  173/35726 (0.48%)  
[  5]   6.00-8.00   sec  47.7 MBytes   200 Mbits/sec  0.040 ms  11/35717 (0.031%)  
[  5]   8.00-10.00  sec  47.7 MBytes   200 Mbits/sec  0.055 ms  8/35713 (0.022%)  
[  5]  10.00-12.00  sec  47.5 MBytes   199 Mbits/sec  0.080 ms  166/35714 (0.46%)  
[  5]  12.00-14.00  sec  47.7 MBytes   200 Mbits/sec  0.060 ms  19/35712 (0.053%)  
[  5]  14.00-16.00  sec  47.6 MBytes   200 Mbits/sec  0.050 ms  100/35718 (0.28%)  
[  5]  16.00-18.00  sec  47.7 MBytes   200 Mbits/sec  0.051 ms  15/35713 (0.042%)  
[  5]  18.00-20.00  sec  47.3 MBytes   198 Mbits/sec  0.057 ms  306/35716 (0.86%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.06  sec   478 MBytes   200 Mbits/sec  0.000 ms  0/358205 (0%)  sender
[SUM]  0.0-20.1 sec  1856 datagrams received out-of-order
[  5]   0.00-20.00  sec   476 MBytes   200 Mbits/sec  0.057 ms  1603/357956 (0.45%)  receiver

iperf Done.
```
:::
## Test_2:
:::success
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=200Mbps，20sec
:::
---
###  Free5gc + OAI(R750) + TM500
:::warning
#### CN
```bash=
 [WARN][AMF][Ngap][ran_addr:192.168.8.82:53493] Cause RadioNetwork[21]
 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:1(3GPP)][ran_addr:192.168.8.82:53493] Ue Context in GMM-Registered
 [INFO][SMF][PduSess] Receive Update SM Context Request
 [WARN][SMF][PduSess][pdu_session_id:1][supi:imsi-001010000000001] Unexpected state, expect: [InActivePending], actual:[Active]
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] handleSessionModificationRequest
 [INFO][SMF][PduSess] Received PFCP Session Modification Accepted Response from AN UPF
 [INFO][SMF][GIN] | 200 |       127.0.0.1 | POST    | /nsmf-pdusession/v1/sm-contexts/urn:uuid:b843e31e-7bd0-4294-866f-ef534a4f0a86/modify |
 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:1(3GPP)][ran_addr:192.168.8.82:53493] Send UE Context Release Command
 [INFO][AMF][Ngap][ran_addr:192.168.8.82:53493] Handle UEContextReleaseComplete
 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:1(3GPP)][ran_addr:192.168.8.82:53493] Handle UEContextReleaseComplete (RAN UE NGAP ID: 1)
 [INFO][AMF][Ngap][amf_ue_ngap_id:RU:1,AU:1(3GPP)][ran_addr:192.168.8.82:53493] Release Ue Context in GMM-Registered
 [INFO][SMF][PduSess] Receive Update SM Context Request
 [WARN][SMF][PduSess][pdu_session_id:1][supi:imsi-001010000000001] Unexpected state, expect: [InActivePending], actual:[Active]
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] handleSessionModificationRequest
 [INFO][SMF][PduSess] Received PFCP Session Modification Accepted Response from AN UPF
 [INFO][SMF][GIN] | 200 |       127.0.0.1 | POST    | /nsmf-pdusession/v1/sm-contexts/urn:uuid:b843e31e-7bd0-4294-866f-ef534a4f0a86/modify |
 [INFO][AMF][Ngap][ran_addr:192.168.8.82:53493] Release UE[imsi-001010000000001] Context : N2 Connection Release
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] serveDLDReport
 [INFO][AMF][Producer] Handle N1N2 Message Transfer Request
 [INFO][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] Send Paging to TAI(&{Mcc:001 Mnc:01}, Tac:000001)
 [INFO][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] Start T3513 timer
 [INFO][AMF][GIN] | 202 |       127.0.0.1 | POST    | /namf-comm/v1/ue-contexts/imsi-001010000000001/n1-n2-messages |
 [INFO][SMF][PFCP] Receive ATTEMPTING_TO_REACH_UE, AMF is able to page the UE
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] handleSessionReportResponse
 [WARN][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] T3513 expires, retransmit Paging (retry: 1)
 [WARN][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] T3513 expires, retransmit Paging (retry: 2)
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] serveDLDReport
 [INFO][AMF][Producer] Handle N1N2 Message Transfer Request
 [INFO][AMF][GIN] | 409 |       127.0.0.1 | POST    | /namf-comm/v1/ue-contexts/imsi-001010000000001/n1-n2-messages |
 [WARN][SMF][PFCP] Send N1N2Transfer failed: 409 Conflict
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] handleSessionReportResponse
 [WARN][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] T3513 expires, retransmit Paging (retry: 3)
 [WARN][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] T3513 expires, retransmit Paging (retry: 4)
 [WARN][AMF][Gmm][amf_ue_ngap_id:RU:,AU:(3GPP)][supi:SUPI:imsi-001010000000001] T3513 expires 4 times, abort paging procedure
 [INFO][SMF][GIN] | 404 |       127.0.0.1 | POST    | / |
 [ERRO][AMF][Http] 404 is not a valid status code in N1N2TransferFailureNotification
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] serveDLDReport
 [INFO][AMF][Producer] Handle N1N2 Message Transfer Request
 [INFO][AMF][GIN] | 409 |       127.0.0.1 | POST    | /namf-comm/v1/ue-contexts/imsi-001010000000001/n1-n2-messages |
 [WARN][SMF][PFCP] Send N1N2Transfer failed: 409 Conflict
 [INFO][UPF][PFCP][LAddr:127.0.0.8:8805] handleSessionReportResponse
 ```
##### UE 在 iPerf 測試期間被 RAN 釋放
```css=
[WARN][AMF][Ngap][ran_addr:192.168.8.82:53493] Cause RadioNetwork[21]
```
1.「Cause RadioNetwork[21]」 [是 NGAP 錯誤碼](https://github.com/free5gc/free5gc/issues/176)。
```bash=
Authentication failure (5GMM cause #21 "synch failure").
The UE shall send an AUTHENTICATION FAILURE message, with 5GMM cause #21 "synch failure", to the
network and start the timer T3520 (see example in figure 5.4.1.3.7.1).
```

2. iPerf 負載導致 UE 或 gNB 超時 (例如 DRB (Data Radio Bearer) 超時)
    - 高流量 (High Throughput)：iPerf 測試可能讓 RLC 層 Buffers 滿載，導致 HARQ Retransmission 失敗。
    - DRB 超時：如果 iPerf 負載過大，gNB 可能會認為 UE 沒有回應，而觸發釋放。

##### SMF 狀態不同步

```css=
[WARN][SMF][PduSess][pdu_session_id:1][supi:imsi-001010000000001] Unexpected state, expect: [InActivePending], actual:[Active]
```

PDU Session 在 AMF 端被釋放，但 SMF 仍認為它是 Active。

推測:RAN (gNB) 直接觸發 UE 釋放，但核心網 (Core Network) 沒有正確同步此狀態。核心網可能沒有正確處理 RLF 事件，導致 AMF 仍然認為 UE 可用，進而觸發 Paging。這可能會導致 UPF (User Plane Function) 還在轉發數據，但 UE 其實已經離線。


:::





:::warning

#### TMA
```bash=
13/02/25 00:41:01:455	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
13/02/25 00:41:01:456	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
13/02/25 00:41:01:465	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
13/02/25 00:41:01:500	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0x5E9A, TimingAdv: 0, PreambleTxCount: 2)
13/02/25 00:41:01:500	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0
13/02/25 00:41:02:454	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0
  Selected PLMN: 00101F
  Pdu Session Id: 1
  Data Network Name: internet
  IPv4 Address: 10.60.0.1
  Registration Result: 3GPP Access Only
13/02/25 00:41:08:994	I: CMPI DTE PPPOE CONNECTION IND: UE Id: 0
  De Id: 1
  PDU Session Id: 1
  Default DRB Id: 1
  PDN IP Address String: 10.60.0.1
13/02/25 00:41:20:600	I: CMPI DTE PPPOE DISCONNECTION IND: UE Id: 0
  De Id: 1
  PDU Session Id: 1
  Default DRB Id: 1
  IP Address: 10.60.0.1
13/02/25 00:41:23:678	I: CMPI DTE PPPOE CONNECTION IND: UE Id: 0
  De Id: 1
  PDU Session Id: 1
  Default DRB Id: 1
  PDN IP Address String: 10.60.0.1
  
===================================================================================================================================================================================================
  
13/02/25 00:43:36:616	I: ERNO 0x00000000 CASS 0x00000008 NR_L1.DL_PHY.RX_MSG: "NR L1: Previous PDSCH CTRL message is not freed. Hwi: 0, Lfn: 879, Sfn: 244, ((Subframe << 5) | Slot): 224, PrevLfn: 878, PrevSfn: 243" at line 2153 of ../lte_fw_app_l1/code/nr5g_l1_dl_phy_handler.c
13/02/25 00:43:38:142	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "bHasContextCompleted" in ../nr_fw_app_l0/modules/pdsch_brp/sub/pre_processing/code/cb_parameters.cpp at line 514, Context not cleared for Lfn: 878, Sfn: 243, Subframe: 7, SlotNum: 1, Numerology: 1
13/02/25 00:43:38:265	I: TMAE 0x1 Warning - Logging is being stopped automatically. Please wait for logging stopped message.
13/02/25 00:43:38:265	#$$STOP_LOGGING
13/02/25 00:43:38:662	I: TMAE 0x1 Warning - Logging has been stopped automatically.
13/02/25 00:43:38:674	C: STOP_LOGGING 0x00 Ok. Logging stopped successfully after error. 0x00000000 0x00000000 Assert Fail: "bHasContextCompleted" in ../nr_fw_app_l0/modules/pdsch_brp/sub/pre_processing/code/cb_parameters.cpp at line 514, Context not cleared for Lfn: 878, Sfn: 243, Subframe: 7, SlotNum: 1, Numerology: 1
13/02/25 00:43:44:033	DTRC 2025_02_13_00_43_44_dump
13/02/25 00:43:44:037	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "(0)" in ../tm_system/framework/code/os_msg_frm.c at line 412, osMessageQSend failed -205
13/02/25 00:43:44:122	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
13/02/25 00:43:44:122	 >>>OranRxEth_FE_1_I0: m_RngBuf full." in ../tm_oran_app/oran_ru/oran_ru_fifos/interface/oran_uplane_fifo.hpp at line 238
13/02/25 00:43:44:172	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
13/02/25 00:43:44:172	 >>>OranRxEth_FE_0_I0: m_RngBuf full." in ../tm_oran_app/oran_ru/oran_ru_fifos/interface/oran_uplane_fifo.hpp at line 238
13/02/25 00:43:44:207	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
13/02/25 00:43:44:207	 >>>FhsTimer_Main_I0: CRuDlCuPlane::PushUPlaneTimerEvent: Failed to queue m_UplaneTimerEventQueue" in ../tm_oran_app/oran_ru/oran_ru_dl_cuplane/code/oran_ru_dl_cuplane.cpp at line 1319
13/02/25 00:43:44:242	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 1198087 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
13/02/25 00:43:44:282	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "(0)" in ../tm_system/framework/code/os_msg_frm.c at line 412, osMessageQSend failed -205
13/02/25 00:43:44:322	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 264027 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
13/02/25 00:43:44:360	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "false" in ../nr_fw_app_l0/modules/dl_srp/sub/radio/code/radio.cpp at line 322, Hwi: 0, DL FFT data ind message still in use, old msg RxStreamPktId: 7988288, CurrentRxStreamPktId: 7988800, RefCount: 1, Old BClk: 125309219, New BClk: 125664653
13/02/25 00:43:44:398	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "(0)" in ../tm_system/framework/code/os_msg_frm.c at line 412, osMessageQSend failed -205
13/02/25 00:43:44:435	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 455875 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
13/02/25 00:43:44:471	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 697743 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
13/02/25 00:43:44:524	I: TMAE 0x0 Information - Stopping Protocol logging. Status:-1 Error code:10054
13/02/25 00:43:44:524	I: TMAE 0x0 Information - Stopped Protocol logging.
13/02/25 00:44:26:602	C: DTRC 0x00 Ok
13/02/25 00:44:26:643	I: TMAE 0x1 Warning - Incident occurred (TM500 exception). Details are stored under C:\Users\bmwlab\Documents\VIAVI\TM500\5G NR\Test Mobile Application\Logged Data\250213_003726_session\2025_02_13_00_44_26_602_Incident
13/02/25 00:44:28:199	I: TMAE 0x1 Warning - Test Mobile not responding. Rebooting the Test Mobile.
13/02/25 00:44:28:199	RBOT
13/02/25 00:44:28:200	I: ERNO 0x00000000 CASS 0x00000009 31 repeated CAssert(s) suppressed
13/02/25 00:44:28:237	C: RBOT 0x00 Ok
13/02/25 00:44:44:821	I: REBOOT - The Test Mobile has rebooted. The TMA has re-connected automatically.
```

##### 物理層 (PHY) 與 MAC 層錯誤
###### PDSCH 控制訊息未釋放 (NR L1 層):
```css=
Previous PDSCH CTRL message is not freed.
```
可能影響：可能造成 L1 緩存積壓 或 記憶體泄漏。

---
###### cb_parameters.cpp (Lfn 878, Sfn 243) 發生錯誤
```css=
Assert Fail: "bHasContextCompleted" in cb_parameters.cpp
```
可能影響：該錯誤通常與 PDSCH 資源未正確釋放 相關，可能導致數據傳輸異常。

---
###### ORAN 接收 FIFO 滿溢
在 00:43:44：
```css=
OranRxEth_FE_1_I0: m_RngBuf full.
OranRxEth_FE_0_I0: m_RngBuf full.
```
這顯示 ORAN (Open RAN) 前傳 FIFO 滿溢，可能導致數據流中斷，影響傳輸。

---

###### 系統崩潰與設備重啟
在 00:44:28：
```css=
Test Mobile not responding. Rebooting the Test Mobile.
```
測試設備無回應，觸發自動重啟。
推測與 記憶體不足、FIFO 滿溢、L1 層未釋放資源 等問題相關。

---
:::
:::warning
#### gNB
```css=
Assertion (0) failed!
In ngap_gNB_decode_initiating_message() ../../../openair3/NGAP/ngap_gNB_decoder.c:94
Unknown procedure ID (1) for initiating message

Exiting execution
first_call set from phy cb first_call_set=0x7f33b4067bd1
```
:::

#### Speculate the crash sequence and analyze the cause of the crash
:::danger
- Normal situation
![image](https://hackmd.io/_uploads/rkZZZjJc1g.png)
---
- When iperf3 UDP DL_THP=200Mbps，20sec
- TMA Rebooting
```bash=
- Previous PDSCH CTRL message is not freed.
- Assert Fail: "bHasContextCompleted" in cb_parameters.cpp
- OranRxEth_FE_1_I0: m_RngBuf full.
- OranRxEth_FE_0_I0: m_RngBuf full.
- Test Mobile not responding. Rebooting the Test Mobile.
```
![image](https://hackmd.io/_uploads/ryc--s15Jx.png)

---
- CN 
```bash=
[WARN][AMF][Ngap][ran_addr:192.168.8.82:53493] Cause RadioNetwork[21]
[WARN][SMF][PduSess][pdu_session_id:1][supi:imsi-001010000000001] Unexpected state, expect: [InActivePending], actual:[Active]
```

[Cause RadioNetwork[21] from free5gc github](https://github.com/free5gc/free5gc/issues/176)
```bash=
Authentication failure (5GMM cause #21 "synch failure").
The UE shall send an AUTHENTICATION FAILURE message, with 5GMM cause #21 "synch failure", to the
network and start the timer T3520 (see example in figure 5.4.1.3.7.1).
```
![image](https://hackmd.io/_uploads/B1zGZo1qJx.png)

---

- gNB ngap_gNB_decode_initiating_message failed
```bash=
Assertion (0) failed!
In ngap_gNB_decode_initiating_message() ../../../openair3/NGAP/ngap_gNB_decoder.c:94
Unknown procedure ID (1) for initiating message

Exiting execution
first_call set from phy cb first_call_set=0x7f33b4067bd1
```

![image](https://hackmd.io/_uploads/BJTzZsJqJl.png)


:::


















## Test_3: Free5gc + OAI(R750) + TM500
- OAI(750) and OAI(supermicro) have same commit
- [PCAP]()
- [TM500 UE LOG]()
---
:::success
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=175Mbps，20sec
- iperf3 UDP DL_THP=200Mbps，20sec


MAX Throughput DL : ==174 Mbits/sec==
:::
---
### iperf result
- iperf3 UDP DL_THP=175Mbps，20sec
```bash=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 192.168.8.21 -u -B 10.60.0.1 -b175M -t 20 -p 8889 -i 2 -R
Connecting to host 192.168.8.21, port 8889
Reverse mode, remote host 192.168.8.21 is sending
[  5] local 10.60.0.1 port 49031 connected to 192.168.8.21 port 8889
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  41.8 MBytes   175 Mbits/sec  0.219 ms  580/31881 (1.8%)  
[  5]   2.00-4.00   sec  41.7 MBytes   175 Mbits/sec  0.046 ms  27/31235 (0.086%)  
[  5]   4.00-6.00   sec  41.1 MBytes   172 Mbits/sec  0.055 ms  447/31252 (1.4%)  
[  5]   6.00-8.00   sec  41.6 MBytes   174 Mbits/sec  0.050 ms  89/31246 (0.28%)  
[  5]   8.00-10.00  sec  41.7 MBytes   175 Mbits/sec  0.046 ms  39/31251 (0.12%)  
[  5]  10.00-12.00  sec  41.1 MBytes   172 Mbits/sec  0.243 ms  479/31266 (1.5%)  
[  5]  12.00-14.00  sec  41.6 MBytes   175 Mbits/sec  0.031 ms  85/31234 (0.27%)  
[  5]  14.00-16.00  sec  41.6 MBytes   175 Mbits/sec  0.063 ms  71/31250 (0.23%)  
[  5]  16.00-18.00  sec  41.7 MBytes   175 Mbits/sec  0.034 ms  49/31252 (0.16%)  
[  5]  18.00-20.00  sec  41.6 MBytes   175 Mbits/sec  0.064 ms  62/31250 (0.2%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.06  sec   418 MBytes   175 Mbits/sec  0.000 ms  0/313428 (0%)  sender
[SUM]  0.0-20.1 sec  236 datagrams received out-of-order
[  5]   0.00-20.00  sec   415 MBytes   174 Mbits/sec  0.064 ms  1928/313117 (0.62%)  receiver
```
- iperf3 UDP DL_THP=200Mbps，20sec

```bash=
- iperf3 UDP DL_THP=200Mbps，20sec
vboxuser@Ubunt:~/manageUE$ iperf3 -c 192.168.8.21 -u -B 10.60.0.1 -b200M -t 20 -p 8889 -i 2 -R
Connecting to host 192.168.8.21, port 8889
Reverse mode, remote host 192.168.8.21 is sending
[  5] local 10.60.0.1 port 57153 connected to 192.168.8.21 port 8889
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  13.0 MBytes  54.4 Mbits/sec  1.441 ms  539/10259 (5.3%)  
[  5]   2.00-4.00   sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]   4.00-6.00   sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]   6.00-8.00   sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]   8.00-10.00  sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]  10.00-12.00  sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]  12.00-14.00  sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]  14.00-16.04  sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
[  5]  16.04-18.00  sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
^C[  5]  20.00-28.25  sec  0.00 Bytes  0.00 bits/sec  1.441 ms  0/0 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-28.25  sec  0.00 Bytes  0.00 bits/sec  0.000 ms  0/0 (0%)  sender
[SUM]  0.0-28.3 sec  1984 datagrams received out-of-order
[  5]   0.00-28.25  sec  13.0 MBytes  3.85 Mbits/sec  1.441 ms  539/10259 (5.3%)  receiver
iperf3: interrupt - the client has terminated
```

## Test_4: Free5gc + OAI(SuperMicro) + TM500

- [PCAP]()
- [TM500 UE LOG]()
---
:::success
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP = 400Mbps，20sec
- iperf3 UDP DL_THP = 450Mbps，20sec
- iperf3 UDP DL_THP = 500Mbps，20sec

MAX Throughput DL : ==394 Mbits/sec==
:::
:::success
```bash=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 192.168.8.21 -u -B 10.60.0.1 -b400M -t 20 -p 8889 -i 2 -R
Connecting to host 192.168.8.21, port 8889
Reverse mode, remote host 192.168.8.21 is sending
[  5] local 10.60.0.1 port 32994 connected to 192.168.8.21 port 8889
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  91.3 MBytes   383 Mbits/sec  0.048 ms  1239/69619 (1.8%)  
[  5]   2.00-4.00   sec  97.4 MBytes   408 Mbits/sec  0.058 ms  207/73135 (0.28%)  
[  5]   4.00-6.00   sec  94.5 MBytes   396 Mbits/sec  0.108 ms  656/71432 (0.92%)  
[  5]   6.00-8.00   sec  94.5 MBytes   396 Mbits/sec  0.071 ms  684/71439 (0.96%)  
[  5]   8.00-10.00  sec  94.4 MBytes   396 Mbits/sec  0.038 ms  713/71412 (1%)  
[  5]  10.00-12.00  sec  94.5 MBytes   396 Mbits/sec  0.027 ms  681/71437 (0.95%)  
[  5]  12.00-14.00  sec  93.5 MBytes   392 Mbits/sec  0.052 ms  1394/71409 (2%)  
[  5]  14.00-16.00  sec  92.8 MBytes   389 Mbits/sec  0.029 ms  1922/71450 (2.7%)  
[  5]  16.00-18.00  sec  93.3 MBytes   391 Mbits/sec  0.033 ms  1525/71419 (2.1%)  
[  5]  18.00-20.00  sec  94.3 MBytes   396 Mbits/sec  0.043 ms  761/71418 (1.1%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.01  sec   954 MBytes   400 Mbits/sec  0.000 ms  0/714801 (0%)  sender
[SUM]  0.0-20.0 sec  18682 datagrams received out-of-order
[  5]   0.00-20.00  sec   940 MBytes   394 Mbits/sec  0.043 ms  9782/714170 (1.4%)  receiver

iperf Done.
```
```bash=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 192.168.8.21 -u -B 10.60.0.1 -b450M -t 20 -p 8889 -i 2 -R
Connecting to host 192.168.8.21, port 8889
Reverse mode, remote host 192.168.8.21 is sending
[  5] local 10.60.0.1 port 57559 connected to 192.168.8.21 port 8889
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  65.8 MBytes   276 Mbits/sec  0.016 ms  22332/71638 (31%)  
[  5]   2.00-4.03   sec  74.3 MBytes   306 Mbits/sec  0.049 ms  15420/71058 (22%)  
[  5]   4.03-6.00   sec   100 MBytes   427 Mbits/sec  0.040 ms  13708/88626 (15%)  
[  5]   6.00-8.00   sec  86.3 MBytes   362 Mbits/sec  0.048 ms  16765/81398 (21%)  
[  5]   8.00-10.01  sec  90.0 MBytes   377 Mbits/sec  21.751 ms  12978/80421 (16%)  
[  5]  10.01-12.00  sec  87.0 MBytes   366 Mbits/sec  0.364 ms  13562/78695 (17%)  
[  5]  12.00-14.00  sec  95.7 MBytes   401 Mbits/sec  0.067 ms  10891/82561 (13%)  
[  5]  14.00-16.00  sec  88.7 MBytes   372 Mbits/sec  25.484 ms  14631/81056 (18%)  
[  5]  16.00-18.00  sec  90.6 MBytes   380 Mbits/sec  0.034 ms  9593/77445 (12%)  
[  5]  18.00-20.01  sec  90.6 MBytes   379 Mbits/sec  0.029 ms  13543/81386 (17%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.02  sec  1.05 GBytes   450 Mbits/sec  0.000 ms  0/804532 (0%)  sender
[SUM]  0.0-20.0 sec  498742 datagrams received out-of-order
[  5]   0.00-20.01  sec   869 MBytes   364 Mbits/sec  0.029 ms  143423/794284 (18%)  receiver

iperf Done.
```


```bash=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 192.168.8.21 -u -B 10.60.0.1 -b500M -t 20 -p 8889 -i 2 -R
Connecting to host 192.168.8.21, port 8889
Reverse mode, remote host 192.168.8.21 is sending
[  5] local 10.60.0.1 port 43788 connected to 192.168.8.21 port 8889
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  45.9 MBytes   192 Mbits/sec  141.839 ms  36930/71309 (52%)  
[  5]   2.00-4.02   sec  90.2 MBytes   375 Mbits/sec  0.017 ms  33719/101302 (33%)  
[  5]   4.02-6.02   sec  87.5 MBytes   368 Mbits/sec  0.040 ms  23511/89037 (26%)  
[  5]   6.02-8.00   sec  87.9 MBytes   371 Mbits/sec  0.043 ms  26011/91826 (28%)  
[  5]   8.00-10.00  sec  79.6 MBytes   334 Mbits/sec  9.457 ms  27604/87187 (32%)  
[  5]  10.00-12.02  sec  87.3 MBytes   363 Mbits/sec  0.153 ms  23443/88858 (26%)  
[  5]  12.02-14.00  sec  76.3 MBytes   322 Mbits/sec  0.435 ms  31932/89050 (36%)  
[  5]  14.00-16.00  sec  88.7 MBytes   372 Mbits/sec  2.946 ms  23139/89578 (26%)  
[  5]  16.00-18.02  sec  90.2 MBytes   375 Mbits/sec  0.251 ms  21687/89233 (24%)  
[  5]  18.02-20.00  sec  92.8 MBytes   392 Mbits/sec  0.033 ms  22780/92291 (25%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.12  sec  1.17 GBytes   500 Mbits/sec  0.000 ms  0/898386 (0%)  sender
[SUM]  0.0-20.1 sec  467760 datagrams received out-of-order
[  5]   0.00-20.00  sec   826 MBytes   347 Mbits/sec  0.033 ms  270756/889671 (30%)  receiver

iperf Done.
```
:::