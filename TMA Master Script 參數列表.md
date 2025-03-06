---
title: TMA Master Script 參數列表

---

[TOC]

## 1. 設定模式
| 參數名稱 | 數值 | 描述 |
|----------|------|------|
| MTS_MODE | -    | 設定行動模式 |

## 2. 啟動行動設備
| 參數名稱 | 數值 | 描述 |
|----------|------|------|
| STRT     | -    | 啟動行動設備 |

## 3. 設定參數值
| 參數名稱                        | 數值 | 描述 |
|--------------------------------|------|------|
| L2_MAC_ENABLE_CR379_R2_094167 | 1    | 啟用 L2 MAC CR379 R2_094167 |
| L2_MAC_ENABLE_REL_9_CR409     | 1    | 啟用 L2 MAC Release 9 CR409 |
| RRC_ENABLE_RELEASE_15         | 1    | 啟用 RRC Release 15 |
| NAS_ENABLE_R15                | 1    | 啟用 NAS Release 15 |

## 4. 執行腳本
| 腳本名稱                        | 停止於錯誤 | 迭代次數 | 其他限制條件 |
|--------------------------------|----------|--------|------------|
| Init.txt                       | 1        | 1      | 無 |
| Cell Search and UE Configure.txt | 1        | 1      | 無 |
| UE conf Open5GS.txt            | 1        | 1      | 無 |
| Configure L3 L1 MTS.txt        | 1        | 1      | 無 |
| UE 0 UL UDP.txt                | 1        | 1      | 無 |
| UE 0 DL UDP.txt                | 1        | 1      | 無 |
| UE 0 ping.txt                  | 1        | 1      | 無 |
| Detach.txt                     | 1        | 1      | 無 |
| MUX_LOG.txt                    | 1        | 1      | 無 |


## Init.txt
:::warning
comming soon
:::
## Configure Radio Contexts
### 1. 設定無線電上下文 (Configure Radio Contexts)
| 參數名稱                       | 設定值  | 描述                |
| ------------------------------ | ------- |:------------------- |
| Radio context Id               | 0       | 無線電上下文 ID     |
| Cell Id                        | 0       | 小區 ID             |
| Downlink carrier frequency     | 34507.2 | 下行載波頻率        |
| System bandwidth               | 100     | 系統頻寬 (MHz)      |
| Uplink carrier frequency       | []      | 上行載波頻率        |
| Uplink Bandwidth               | []      | 上行頻寬            |
| Number of receive antennas     | []      | 接收天線數          |
| AbsoluteFrequencyPointA        | 34015.8 | 頻率點 A (絕對)     |
| AbsoluteFrequencySSB           | 34507.2 | SSB 絕對頻率        |
| gNB Downlink carrier frequency | 34507.2 | gNB 下行載波頻率    |
| SSB Subcarrier Spacing         | 1       | SSB 子載波間隔      |
| FrequencyShift7p5khz           | []      | 頻率偏移 7.5 kHz    |
| Minimum K1 Supported           | 2       | 最小 K1 支持        |
| Minimum K2 Supported           | 2       | 最小 K2 支持        |
| Offset To Carrier              | []      | 載波偏移            |
| Supplementary Downlink Cell    | []      | 輔助下行小區        |
| UL SRS-TxPortSwitch            | 0       | UL SRS 發送端口切換 |
| Acquire SIB1                   | []      | 獲取 SIB1           |
| gNB UL Carrier Frequency       | []      | gNB 上行載波頻率    |

### 2. 校準 NR 上行功率縮放 (Calibrate NR UL Power Scaling)
| 參數名稱                             | 設定值 | 描述              |
| ------------------------------------ |:------ | ----------------- |
| Radio Context                        | 0      | 無線電上下文      |
| Signalled SSB Reference Signal Power | []     | SSB 參考信號功率  |
| Delta UL DL Physical Attenuation     | []     | 上行/下行物理衰減 |
| RSRP Measurement Mode                | []     | RSRP 測量模式     |
### 3. 配置 NR 上行功率縮放共用參數 (Configure NR UL Power Scaling Common)
| 參數名稱                    | 設定值 | 描述                 |
| --------------------------- |:------ | -------------------- |
| Mode                        | 1      | 模式                 |
| Global Offset               | -100   | 全域偏移             |
| Radio Context               | []     | 無線電上下文         |
| Apply to Radio Context only | []     | 只應用於無線電上下文 |
| Global Offset for PUSCH     | []     | PUSCH 的全域偏移     |
| Global Offset for PUCCH     | []     | PUCCH 的全域偏移     |
| Global Offset for PRACH     | []     | PRACH 的全域偏移     |
| Global Offset for SRS       | 10     | SRS 的全域偏移       |
| UL Carrier Type             | []     | 上行載波類型         |

## UE conf Open5GS

### 1. 選擇 UE (Select UEs)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| UE Group Id | 0 | UE 群組 ID |
| UE Group Type | 0 | UE 群組類型 |
| Number of entries | 1 | 群組內 UE 數量 |
| UE Context Id | 0 | UE 上下文 ID |

### 2. 設定 UE 上下文 (Set the current UE context)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| UE Context Id | 0 | UE 上下文 ID |

### 3. 配置 USIM (Configure USIM)
| 參數名稱                            | 設定值                           | 描述                |
| ----------------------------------- | -------------------------------- |:------------------- |
| USIM Type (Conditional)             | 1                                | USIM 類型 (條件性)  |
| IMSI                                | 001010000062653                  | 國際行動用戶識別碼  |
| MNC length                          | 2                                | 行動網絡碼長度      |
| Last RPLMN use indication           | []                               | 最後 RPLMN 使用指示 |
| UAC Access Identities Configuration | [1]                              | 訪問控制 ID 配置    |
| Reset USIM                          | []                               | 是否重置 USIM       |
| Authentication Key                  | 8baf473f2f8fd09487cccbd7097c6862 | 鑑權金鑰            |
| OP                                  | 8e27b6af0e692e750f32667a3b14605d | 營運商金鑰          |
| C1                                  | 00000000000000000000000000000000 | C1 參數             |
| C2                                  | 00000000000000000000000000000001 | C2 參數             |
| C3                                  | 00000000000000000000000000000002 | C3 參數             |
| C4                                  | 00000000000000000000000000000004 | C4 參數             |
| C5                                  | 00000000000000000000000000000008 | C5 參數             |
| R1                                  | 64                               | R1 參數             |
| R2                                  | 0                                | R2 參數             |
| R3                                  | 32                               | R3 參數             |
| R4                                  | 64                               | R4 參數             |
| R5                                  | 96                               | R5 參數             |
| OPc Provided                        | [1]                              | OPc 是否提供        |


## Configure L3 L1 MTS
### 1. 配置 PHY 能力 (Configure PHY Capabilities)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| PHY System Capability | [] | 設定物理層能力 |
| PHY System Capability | [] | 設定物理層能力 |
| SRS-TxPortSwitch | 0 | SRS 發送端口切換 |
| Scaling Factor DL | 2 | 下行縮放因子 |
| Scaling Factor UL | 2 | 上行縮放因子 |
| Number of UE receive antennas | 0x7 | UE 接收天線數量 |
| Number of UE transmit antennas | 0 | UE 發送天線數量 |
| MaxUplinkDutyCycle-PC2-FR1 | 0 | FR1 的最大上行工作週期 |
| MaxUplinkDutyCycle-FR2 | 0 | FR2 的最大上行工作週期 |
| PUCCH Capability | 0 | PUCCH 能力 |
| UplinkTxSwitching Period | 0 | 上行發送切換週期 |
| Ul-FullPowerTransmission-r16 (Optional) | 0 | 上行滿功率傳輸 (可選) |
| Pusch-TransCoherence | 0 | PUSCH 傳輸一致性 |
| AS Release | 0 | AS 版本 |
| Pusch Different TB Per Slot | 0 | PUSCH 每個時隙不同 TB |
| MaxUplinkDutyCycle-interBandCA-PC2-17 | 0 | 跨頻載波聚合的最大上行工作週期 |







### 2. 配置 NR NAS 能力 (Configure NR NAS Capabilities)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| Nas Release Version | 0 | NAS 版本 |
| NAS Security options | [] | NAS 安全選項 |
| Supported Ciphering Algorithms | 0xe0 | 支援的加密算法 |
| Supported Integrity Algorithms | 0xe0 | 支援的完整性算法 |
| Preferred RAT | [] | 偏好接入技術 |
| Drx Parameters | [] | Drx 參數 |
| Reflective QoS | [] | 反射 QoS |
| Reserved | [] | 保留參數 |
| Uplink Integrity protection max data rate | 1 | 上行完整性保護最大速率 |
| Downlink Integrity protection max data rate | 1 | 下行完整性保護最大速率 |

### 3. 配置 NAS PLMN 選擇 (Configure NAS PLMN Selection)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| Force PLMN | 00101 | 強制選擇的 PLMN |
| Radio Access Type | [] | 無線接入技術 |

### 4. 激活配置 (Activate Configuration)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| Timing Type | -1 | 設定定時類型 |

### 5. 等待消息 (Wait for Messages)
| 參數名稱 | 設定值 | 描述 |
|----------|------|------|
| Message | ACTIVATE | 等待激活消息 |

### 6. 定義 MTS 小區 (Define MTS Cells)
| 參數名稱                         | 設定值  | 描述             |
| -------------------------------- | ------- | ---------------- |
| Number of entries                | 1       | 條目數           |
| gNB Id                           | 0       | gNB 識別碼       |
| gNB X                            | 0       | gNB X 座標       |
| gNB Y                            | 0       | gNB Y 座標       |
| Number of cells                  | 1       | 小區數量         |
| Cell ID                          | 0       | 小區 ID          |
| DL Frequency                     | 34507.2 | 下行頻率         |
| Cell Range                       | 1000    | 小區範圍         |
| Sector Start                     | 0       | 扇區起始角度     |
| Sector End                       | 360     | 扇區結束角度     |
| Beams Grid Model                 | 1       | 條件性束網格模型 |
| Number of elevation beams        | 0       | 垂直波束數量     |
| Override Default Beam Boundaries | 1       | 覆蓋預設波束邊界 |
| Number of azimuth beams          | 0       | 方位波束數量     |
| SSB Index                        | 0       | SSB 索引         |
| Narrow Beam Profile Id           | 0       | 窄波束配置 ID    |
| SSB Frequency Profile Id         | 0       | SSB 頻率配置 ID  |
| Antenna gain                     | 10      | 天線增益         |
| Reference Signal Power           | 0       | 參考信號功率     |
| Path Loss Frequency              | 0       | 路徑損耗頻率     |
| Radio Context                    | 1       | 無線電上下文     |

## UE ping

UE Group 設定
| 參數名稱               | 設定值  | 描述          |
|:---------------------- |:------- | ------------- |
| UE Group Id            | 0       | UE 群組識別碼 |
| UE Group Type          | 2       | UE 群組類型   |
| List Number of entries | 4       | UE 數量       |
| UE Context Id          | 0,1,2,3 | UE 識別碼清單 |


Traffic Profile 設定 

| 參數名稱                         | 設定值   | 描述                           |     |
|:-------------------------------- |:-------- | ------------------------------ | --- |
| Traffic Profile ID               | 0        | 流量設定檔 ID                  |     |
| Number of PDN/PDU Sessions       | 1        | 會話數量                       |     |
| APN/DNN                          | internet | APN (LTE) / DNN (NR) 名稱      |     |
| Number of services               | 1        | 服務數量                       |     |
| Service Name Type (Conditional)  | 0        | 服務名稱類型                   |     |
| Application name                 | cping    | 應用程式名稱                   |     |
| Load profile                     | -1       | 負載設定                       |     |
| Bearer allocation (Conditional)  | 0        | 承載配置                       |     |
| Service delay time               | 10       | 服務延遲時間                   |     |
| Service duration                 | 0        | 服務持續時間                   |     |
| Service repeat                   | 0        | 服務重複次數                   |     |
| PDN/PDU Session Type             | []       | PDN/PDU 會話類型               |     |
| PDN Options/PCO Extension        | []       | PDN 設定 (LTE) / PCO 擴展 (NR) |     |
| Emergency Call                   | []       | 是否為緊急通話                 |     |
| S-NSSAI                          | []       | 單一網絡切片選擇輔助資訊       |     |
| PDN/PDU session Delay            | []       | PDN/PDU 會話延遲               |     |
| PDN/PDU Session re-establishment | []       | 會話重新建立設定               |     |
| SMS Index                        | []       | 短訊索引                       |     |
| Preferred RRC Release State      | []       | 偏好的 RRC 釋放狀態            |     |
| Multiple PDU Sessions            | []       | 是否支援多個 PDU 會話          |     |



Traffic 設定
| 參數名稱                  | 設定值                           | 描述                      |
| ------------------------- | -------------------------------- |:------------------------- |
| Traffic ID                | 0                                | 流量設定 ID               |
| Traffic Profile ID        | 0                                | 流量設定檔 ID             |
| UE Group ID               | 0                                | UE 群組識別碼             |
| Traffic Parameters        | [5 0 0 -1(3000)]                 | 主要流量參數，3000ms 延遲 |
| Additional Parameters     | []                               | 其他參數                  |
| Traffic Type              | 0                                | 流量類型                  |
| Extra Settings            | []                               | 額外設定                  |
| 5G Network Selection      | 5G:mnc001.mcc001.3gppnetwork.org | 5G 網路選擇               |
| Additional Configurations | []                               | |額外的網絡配置            |

Scenario 設定
| 參數名稱                 | 設定值 | 描述               |
| ------------------------ | ------ |:------------------ |
| Number of MTS sequences  | 1      | 測試序列數量       |
| Duration                 | 0      | 測試持續時間       |
| Number of MM             | 1      | MM 設定數量        |
| Number of TM             | 0      | TM 設定數量        |
| TM ID                    | 0      | TM 識別碼          |
| TM Action                | 0      | TM 動作            |
| Random Number Seed       | []     | 隨機種子           |
| Action mismatch handling | []     | 操作不匹配處理方式 |
| UE mismatch handling     | []     | UE 不匹配處理方式  |
| Error level              | []     | 錯誤等級           |
| Status level             | []     | 狀態等級           |


測試激活
| 參數名稱    | 設定值 | 描述         |
| ----------- | ------ |:------------ |
| Timing Type | -1     | 激活時間類型 |
等待測試結果
| 參數名稱 | 設定值 | 描述       |                                    |
| -------- | ------ | ---------- |:---------------------------------- |
| Message  |        | "ACTIVATE" | 等待 "ACTIVATE" 訊息以確認啟動成功 |

## OAI
- 7D1S2U
```css=
# pattern1
# dl_UL_TransmissionPeriodicity
# 0=ms0p5, 1=ms0p625, 2=ms1, 3=ms1p25, 4=ms2, 5=ms2p5, 6=ms5, 7=ms10
dl_UL_TransmissionPeriodicity                                 = 6;
nrofDownlinkSlots                                             = 7;
nrofDownlinkSymbols                                           = 6;
nrofUplinkSlots                                               = 2;
nrofUplinkSymbols                                             = 4;
```

## Router
- CN_5GS(192.168.8.108)

```css=
╭─oai-cn@oaicn-virtual-machine ~ 
╰─$ ip route                                                                                                        130 ↵

default via 192.168.8.9 dev ens160 proto dhcp metric 100 
default via 10.0.2.1 dev ens192 proto dhcp metric 20101 
default via 10.0.3.1 dev ens224 proto dhcp metric 20102 
10.0.2.0/24 dev ens192 proto kernel scope link src 10.0.2.6 metric 101 
10.0.3.0/24 dev ens224 proto kernel scope link src 10.0.3.15 metric 102 
10.45.0.0/16 dev ogstun proto kernel scope link src 10.45.0.1 
10.45.0.2 via 192.168.8.100 dev ens160 
169.254.0.0/16 dev ens160 scope link metric 1000 
192.168.8.0/24 dev ens160 proto kernel scope link src 192.168.8.108 metric 100 
```

- Control PC (VM)

```css=
vboxuser@Ubunt:~/manageUE$ ip route 
default via 192.168.8.9 dev enp0s3 
10.45.0.2 dev ppp0 scope link 
169.254.203.1 dev ppp0 proto kernel scope link src 10.45.0.2 
192.168.8.0/24 dev enp0s3 proto kernel scope link src 192.168.8.100 
```

![image](https://hackmd.io/_uploads/ryA2FAm5yl.png)

```css=
vboxuser@Ubunt:~/manageUE$ ip route get 192.168.8.108
192.168.8.108 dev enp0s3 src 192.168.8.100 uid 1000 
```

![image](https://hackmd.io/_uploads/SyQP8zVq1e.png)
