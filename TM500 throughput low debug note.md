---
title: TM500 throughput low debug note

---

## Test environment
![image](https://hackmd.io/_uploads/rypCdBYqye.png)

## Adjust static routing
### Adjust the server's routing to ensure packets are forwarded in the 5G stack
- CN originally had a static route (`192.168.8.0/24 dev enp0s3`), which resulted in:
    - CN will mistakenly believe that `10.45.0.2` belongs to the `192.168.8.x` intranet.
    - When the CN responds to `10.45.0.2`, it is mistakenly sent to the `192.168.8.x` network instead of going through PPPoE.
    - This way the packets never get back to `10.45.0.2`, causing `iperf3 -R` to not respond.
- Delete rule in CN
```css=
192.168.8.0/24 dev enp0s3
10.45.0.2 via 192.168.8.100 dev ens160 
```
- Adjust rule in PPPoE server 
```css=
10.45.0.0/24 dev ppp0 scope link
```
- Iperf3 testing
    - `10.45.0.1`: open5GS_AMF
    - `10.45.0.2`: TM500_sim_UE

```css=
iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 1000M -t 20 -R
```
### Version Alignment
Mapping `CN` and `PPPoE` server `Iperf3` version
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 --version
iperf 3.7 (cJSON 1.5.2)
Linux Ubunt 6.8.0-52-generic #53~22.04.1-Ubuntu SMP PREEMPT_DYNAMIC Wed Jan 15 19:18:46 UTC 2 x86_64
Optional features available: CPU affinity setting, IPv6 flow label, TCP congestion algorithm setting, sendfile / zerocopy, socket pacing

```

## Open-5GS + OAI(Supermirco) + TM500
### Test_1:
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=180Mbps，20sec
:::info
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 180M -t 20 -i 2 -R
Connecting to host 10.45.0.1, port 5201
Reverse mode, remote host 10.45.0.1 is sending
[  5] local 10.45.0.2 port 38358 connected to 10.45.0.1 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-2.00   sec  43.0 MBytes   180 Mbits/sec  0.083 ms  556/32741 (1.7%)  
[  5]   2.00-4.00   sec  42.9 MBytes   180 Mbits/sec  0.123 ms  0/32148 (0%)  
[  5]   4.00-6.00   sec  42.6 MBytes   179 Mbits/sec  0.130 ms  214/32127 (0.67%)  
[  5]   6.00-8.00   sec  42.9 MBytes   180 Mbits/sec  0.324 ms  7/32149 (0.022%)  
[  5]   8.00-10.00  sec  42.9 MBytes   180 Mbits/sec  0.076 ms  -3/32144 (-0.0093%)  
[  5]  10.00-12.00  sec  42.7 MBytes   179 Mbits/sec  0.048 ms  191/32164 (0.59%)  
[  5]  12.00-14.00  sec  42.7 MBytes   179 Mbits/sec  0.151 ms  104/32092 (0.32%)  
[  5]  14.00-16.00  sec  42.8 MBytes   180 Mbits/sec  0.055 ms  105/32189 (0.33%)  
[  5]  16.00-18.00  sec  42.9 MBytes   180 Mbits/sec  0.045 ms  0/32164 (0%)  
[  5]  18.00-20.00  sec  42.9 MBytes   180 Mbits/sec  0.080 ms  0/32127 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.06  sec   430 MBytes   180 Mbits/sec  0.000 ms  0/322381 (0%)  sender
[SUM]  0.0-20.1 sec  3409 datagrams received out-of-order
[  5]   0.00-20.00  sec   428 MBytes   180 Mbits/sec  0.080 ms  1174/322045 (0.36%)  receiver

iperf Done.
```
:::

---
### Test_2:
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=185Mbps，20sec

:::info
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 185M -t 20 -i 1 -R
Connecting to host 10.45.0.1, port 5201
Reverse mode, remote host 10.45.0.1 is sending
[  5] local 10.45.0.2 port 35772 connected to 10.45.0.1 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.01   sec  8.53 MBytes  71.1 Mbits/sec  0.090 ms  1857/8247 (23%)  
[  5]   1.01-2.00   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   2.00-3.01   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   3.01-4.01   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   4.01-5.00   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   5.00-6.01   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   6.01-7.01   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   7.01-8.00   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
[  5]   8.00-9.00   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
^C[  5]   9.00-9.52   sec  0.00 Bytes  0.00 bits/sec  0.090 ms  0/0 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-9.52   sec  0.00 Bytes  0.00 bits/sec  0.000 ms  0/0 (0%)  sender
[SUM]  0.0- 9.5 sec  1168 datagrams received out-of-order
[  5]   0.00-9.52   sec  8.53 MBytes  7.52 Mbits/sec  0.090 ms  1857/8247 (23%)  receiver
iperf3: interrupt - the client has terminated
```
:::

### Test_3: 
- Disable the error on data overload option

![image](https://hackmd.io/_uploads/SyZAXcKcJl.png)

- Set UE scaling to 32

![image](https://hackmd.io/_uploads/rkvRm5Fcyg.png)
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 250M -t 20 -R
Connecting to host 10.45.0.1, port 5201
Reverse mode, remote host 10.45.0.1 is sending
[  5] local 10.45.0.2 port 54216 connected to 10.45.0.1 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec  29.7 MBytes   249 Mbits/sec  0.103 ms  842/23103 (3.6%)  
[  5]   1.00-2.00   sec  29.8 MBytes   250 Mbits/sec  0.094 ms  26/22322 (0.12%)  
[  5]   2.00-3.00   sec  29.4 MBytes   247 Mbits/sec  0.100 ms  293/22320 (1.3%)  
[  5]   3.00-4.00   sec  29.8 MBytes   250 Mbits/sec  0.107 ms  17/22332 (0.076%)  
[  5]   4.00-5.00   sec  29.6 MBytes   248 Mbits/sec  0.162 ms  21/22184 (0.095%)  
[  5]   5.00-6.00   sec  29.9 MBytes   251 Mbits/sec  0.118 ms  28/22450 (0.12%)  
[  5]   6.00-7.00   sec  29.7 MBytes   249 Mbits/sec  0.164 ms  89/22318 (0.4%)  
[  5]   7.00-8.00   sec  29.8 MBytes   250 Mbits/sec  0.091 ms  0/22326 (0%)  
[  5]   8.00-9.00   sec  29.7 MBytes   249 Mbits/sec  0.179 ms  41/22311 (0.18%)  
[  5]   9.00-10.00  sec  29.5 MBytes   248 Mbits/sec  0.059 ms  226/22354 (1%)  
[  5]  10.00-11.00  sec  29.1 MBytes   244 Mbits/sec  0.115 ms  482/22296 (2.2%)  
[  5]  11.00-12.00  sec  29.8 MBytes   250 Mbits/sec  0.101 ms  9/22329 (0.04%)  
[  5]  12.00-13.00  sec  29.5 MBytes   248 Mbits/sec  0.074 ms  231/22345 (1%)  
[  5]  13.00-14.00  sec  29.7 MBytes   249 Mbits/sec  0.131 ms  45/22291 (0.2%)  
[  5]  14.00-15.00  sec  29.7 MBytes   249 Mbits/sec  0.146 ms  94/22323 (0.42%)  
[  5]  15.00-16.00  sec  29.3 MBytes   246 Mbits/sec  0.286 ms  337/22309 (1.5%)  
[  5]  16.00-17.00  sec  28.9 MBytes   243 Mbits/sec  0.105 ms  672/22340 (3%)  
[  5]  17.00-18.00  sec  28.8 MBytes   242 Mbits/sec  0.191 ms  720/22299 (3.2%)  
[  5]  18.00-19.00  sec  29.7 MBytes   249 Mbits/sec  0.071 ms  121/22341 (0.54%)  
[  5]  19.00-20.00  sec  29.6 MBytes   248 Mbits/sec  0.127 ms  173/22325 (0.77%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.06  sec   598 MBytes   250 Mbits/sec  0.000 ms  0/447754 (0%)  sender
[SUM]  0.0-20.1 sec  8378 datagrams received out-of-order
[  5]   0.00-20.00  sec   591 MBytes   248 Mbits/sec  0.127 ms  4467/447218 (1%)  receiver

iperf Done.
```
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 280M -t 20 -R
Connecting to host 10.45.0.1, port 5201
Reverse mode, remote host 10.45.0.1 is sending
[  5] local 10.45.0.2 port 58130 connected to 10.45.0.1 port 5201
^C[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-476225.68 sec  0.00 Bytes  0.00 bits/sec  0.000 ms  0/0 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-476225.68 sec  0.00 Bytes  0.00 bits/sec  0.000 ms  0/0 (0%)  sender
[  5]   0.00-476225.68 sec  0.00 Bytes  0.00 bits/sec  0.000 ms  0/0 (0%)  receiver
iperf3: interrupt - the client has terminated
```
















## Free-5GC + OAI(Supermirco) + TM500
### Test_1:
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=185Mbps，20sec
:::info
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 180M -t 20 -R
Connecting to host 10.45.0.1, port 5201
Reverse mode, remote host 10.45.0.1 is sending
[  5] local 10.45.0.2 port 48729 connected to 10.45.0.1 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec  20.7 MBytes   173 Mbits/sec  0.073 ms  571/16060 (3.6%)  
[  5]   1.00-2.00   sec  21.5 MBytes   180 Mbits/sec  0.064 ms  0/16068 (0%)  
[  5]   2.00-3.00   sec  21.4 MBytes   180 Mbits/sec  0.063 ms  19/16070 (0.12%)  
[  5]   3.00-4.00   sec  21.4 MBytes   179 Mbits/sec  0.055 ms  62/16074 (0.39%)  
[  5]   4.00-5.00   sec  21.4 MBytes   180 Mbits/sec  0.115 ms  0/16058 (0%)  
[  5]   5.00-6.00   sec  20.9 MBytes   175 Mbits/sec  0.063 ms  467/16087 (2.9%)  
[  5]   6.00-7.00   sec  21.4 MBytes   180 Mbits/sec  0.053 ms  44/16072 (0.27%)  
[  5]   7.00-8.00   sec  21.5 MBytes   180 Mbits/sec  0.051 ms  5/16072 (0.031%)  
[  5]   8.00-9.00   sec  20.9 MBytes   176 Mbits/sec  0.091 ms  375/16061 (2.3%)  
[  5]   9.00-10.00  sec  20.9 MBytes   175 Mbits/sec  0.173 ms  449/16066 (2.8%)  
[  5]  10.00-11.00  sec  21.5 MBytes   180 Mbits/sec  0.047 ms  0/16086 (0%)  
[  5]  11.00-12.00  sec  21.2 MBytes   178 Mbits/sec  0.052 ms  157/16069 (0.98%)  
[  5]  12.00-13.00  sec  21.4 MBytes   180 Mbits/sec  0.049 ms  37/16072 (0.23%)  
[  5]  13.00-14.00  sec  21.3 MBytes   179 Mbits/sec  0.072 ms  115/16069 (0.72%)  
[  5]  14.00-15.00  sec  21.4 MBytes   180 Mbits/sec  0.049 ms  11/16075 (0.068%)  
[  5]  15.00-16.00  sec  21.4 MBytes   180 Mbits/sec  0.034 ms  10/16070 (0.062%)  
[  5]  16.00-17.00  sec  21.4 MBytes   179 Mbits/sec  0.060 ms  78/16073 (0.49%)  
[  5]  17.00-18.00  sec  21.5 MBytes   180 Mbits/sec  0.066 ms  0/16068 (0%)  
[  5]  18.00-19.00  sec  21.4 MBytes   180 Mbits/sec  0.105 ms  20/16060 (0.12%)  
[  5]  19.00-20.00  sec  21.3 MBytes   179 Mbits/sec  0.042 ms  154/16085 (0.96%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-20.01  sec   429 MBytes   180 Mbits/sec  0.000 ms  0/321659 (0%)  sender
[SUM]  0.0-20.0 sec  5554 datagrams received out-of-order
[  5]   0.00-20.00  sec   426 MBytes   179 Mbits/sec  0.042 ms  2574/321415 (0.8%)  receiver

iperf Done.
```
:::
### Test_2:
DUT:
- slot format: DDDSU
- MIMO: 2T2R 2 layer
- iperf3 UDP DL_THP=300Mbps，20sec
:::info
```css=
vboxuser@Ubunt:~/manageUE$ iperf3 -c 10.45.0.1 -u -B 10.45.0.2 -b 250M -t 20 -R
Connecting to host 10.45.0.1, port 5201
Reverse mode, remote host 10.45.0.1 is sending
[  5] local 10.45.0.2 port 35515 connected to 10.45.0.1 port 5201
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-1.00   sec  3.04 MBytes  25.5 Mbits/sec  0.105 ms  615/2892 (21%)  
[  5]   1.00-2.00   sec  0.00 Bytes  0.00 bits/sec  0.105 ms  0/0 (0%)  
[  5]   2.00-3.00   sec  0.00 Bytes  0.00 bits/sec  0.105 ms  0/0 (0%)  
[  5]   3.00-4.00   sec  0.00 Bytes  0.00 bits/sec  0.105 ms  0/0 (0%)  
[  5]   4.00-5.00   sec  0.00 Bytes  0.00 bits/sec  0.105 ms  0/0 (0%)  
[  5]   5.00-6.00   sec  0.00 Bytes  0.00 bits/sec  0.105 ms  0/0 (0%)  
^C[  5]   6.00-6.44   sec  0.00 Bytes  0.00 bits/sec  0.105 ms  0/0 (0%)  
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate         Jitter    Lost/Total Datagrams
[  5]   0.00-6.44   sec  0.00 Bytes  0.00 bits/sec  0.000 ms  0/0 (0%)  sender
[SUM]  0.0- 6.4 sec  516 datagrams received out-of-order
[  5]   0.00-6.44   sec  3.04 MBytes  3.96 Mbits/sec  0.105 ms  615/2892 (21%)  receiver
iperf3: interrupt - the client has terminated
```
:::


## Result




| CoreNetwork | DL_THP        | DL_THP<br>(1.)Disable the error on data overload option<br>(2.)Set UE scaling to 32 |
|:----------- |:------------- | ----------------------------------------------------------------------------------- |
| Open5GS     | 180 Mbits/sec | 248 Mbits/sec                                                                       |
| Free5GC     | 180 Mbits/sec | Text                                                                                |



[Error_log_open5gs_tma](https://drive.google.com/file/d/15qjL6w8za3D78cB9KY17A-j7dM2Wp8Hd/view?usp=drive_link)
[Error_log_free5gc_tma](https://drive.google.com/file/d/18W8h0D19YAGuRY34IvIToou8I5nNXt9L/view?usp=drive_link)
- TMA
```css
24/02/25 01:38:27:153	I: CMPI MTE 0 NAS ACTIVE RAT IND:UE Id:0
   N1 MODE ACTIVE
24/02/25 01:38:27:154	I: CMPI RRC Cell Selection: UE Id: 0
   Cell Info:
      Cell Id: 0 SSB Freq: 34507.20 Cell Type: Suitable
24/02/25 01:38:27:170	I: CMPI L2 Random Access Initiated :UE Id:0 (Connection Establish: Cell Id 0, Dl Freq 34507, SSB Id 0)
24/02/25 01:38:27:282	I: CMPI L2 Random Access Complete :UE Id:0 (TC-RNTI: 0xA1BD, TimingAdv: 0, PreambleTxCount: 1)
24/02/25 01:38:27:282	I: CMPI MTE 0 NR CONNECTION IND:UE Id:0
24/02/25 01:38:27:567	I: CMPI MTE 0 NR REGISTRATION IND:UE Id:0
  Selected PLMN: 00101F
  Pdu Session Id: 1
  Data Network Name: internet
  IPv4 Address: 10.45.0.2
  Registration Result: 3GPP Access Only
24/02/25 01:38:47:325	I: CMPI DTE PPPOE CONNECTION IND: UE Id: 0
  De Id: 1
  PDU Session Id: 1
  Default DRB Id: 1
  PDN IP Address String: 10.45.0.2
24/02/25 01:39:21:361	I: CMPI DTE PPPOE DISCONNECTION IND: UE Id: 0
  De Id: 1
  PDU Session Id: 1
  Default DRB Id: 1
  IP Address: 10.45.0.2
24/02/25 01:39:49:656	I: CMPI DTE PPPOE CONNECTION IND: UE Id: 0
  De Id: 1
  PDU Session Id: 1
  Default DRB Id: 1
  PDN IP Address String: 10.45.0.2
24/02/25 01:44:05:581	I: ERNO 0x00000000 CASS 0x00000008 NR_L1.DL_PHY.RX_MSG: "NR L1: Previous PDSCH CTRL message is not freed. Hwi: 0, Lfn: 152, Sfn: 140, ((Subframe << 5) | Slot): 224, PrevLfn: 151, PrevSfn: 139" at line 2153 of ../lte_fw_app_l1/code/nr5g_l1_dl_phy_handler.c
24/02/25 01:44:06:797	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "bHasContextCompleted" in ../nr_fw_app_l0/modules/pdsch_brp/sub/pre_processing/code/cb_parameters.cpp at line 514, Context not cleared for Lfn: 151, Sfn: 139, Subframe: 7, SlotNum: 0, Numerology: 1
24/02/25 01:44:11:825	DTRC 2025_02_24_01_44_11_dump
24/02/25 01:44:11:826	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "(0)" in ../tm_system/framework/code/os_msg_frm.c at line 412, osMessageQSend failed -205
24/02/25 01:44:11:857	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
24/02/25 01:44:11:857	 >>>OranRxEth_FE_0_I0: m_RngBuf full." in ../tm_oran_app/oran_ru/oran_ru_fifos/interface/oran_uplane_fifo.hpp at line 238
24/02/25 01:44:11:888	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
24/02/25 01:44:11:888	 >>>OranRxEth_FE_1_I0: m_RngBuf full." in ../tm_oran_app/oran_ru/oran_ru_fifos/interface/oran_uplane_fifo.hpp at line 238
24/02/25 01:44:11:913	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "
24/02/25 01:44:11:913	 >>>FhsTimer_Main_I0: CRuDlCuPlane::PushUPlaneTimerEvent: Failed to queue m_UplaneTimerEventQueue" in ../tm_oran_app/oran_ru/oran_ru_dl_cuplane/code/oran_ru_dl_cuplane.cpp at line 1319
24/02/25 01:44:11:938	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 1198087 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
24/02/25 01:44:11:967	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "(0)" in ../tm_system/framework/code/os_msg_frm.c at line 412, osMessageQSend failed -205
24/02/25 01:44:11:997	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "false" in ../nr_fw_app_l0/modules/dl_srp/sub/radio/code/radio.cpp at line 322, Hwi: 0, DL FFT data ind message still in use, old msg RxStreamPktId: 12085500, CurrentRxStreamPktId: 12086012, RefCount: 1, Old BClk: 411636624, New BClk: 411992076
24/02/25 01:44:12:026	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "(0)" in ../tm_system/framework/code/os_msg_frm.c at line 412, osMessageQSend failed -205
24/02/25 01:44:12:055	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 264027 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
24/02/25 01:44:12:083	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 455875 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
24/02/25 01:44:12:111	I: ERNO 0x00000002 0x00000000 0x00000000 Assert Fail: "Alloc of 697743 bytes failed (out of mem/too big)" in ../tm_system/uri/riport/code/rimem_lifo.c at line 1578
24/02/25 01:44:51:550	C: DTRC 0x00 Ok
24/02/25 01:44:51:665	I: TMAE 0x1 Warning - Incident occurred (TM500 exception). Details are stored under C:\Users\bmwlab\Documents\VIAVI\TM500\5G NR\Test Mobile Application\Logged Data\250218_124517_session\2025_02_24_01_44_51_566_Incident
24/02/25 01:44:54:315	I: TMAE 0x1 Warning - Test Mobile not responding. Rebooting the Test Mobile.
24/02/25 01:44:54:324	RBOT
24/02/25 01:44:54:328	I: ERNO 0x00000000 CASS 0x00000009 29 repeated CAssert(s) suppressed
24/02/25 01:44:54:359	C: RBOT 0x00 Ok
24/02/25 01:44:58:960	I: TMAE 0x0 Information - Could not close socket connection; Terminating receive thread.
24/02/25 01:45:10:846	I: REBOOT - The Test Mobile has rebooted. The TMA has re-connected automatically.
24/02/25 01:46:25:229	#$$DISCONNECT
24/02/25 01:46:25:422	C: DISCONNECT 0x00 OK
```