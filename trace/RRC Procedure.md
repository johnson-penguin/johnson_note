---
title: RRC Procedure

---

![image](https://hackmd.io/_uploads/rJFy2mB-yl.png)

---
38.311
![image](https://hackmd.io/_uploads/Bygi0QHWkx.png)

---
![image](https://hackmd.io/_uploads/HyvagtLZJg.png)

---
![image](https://hackmd.io/_uploads/rkF8ytk_1l.png)


## 基本描述
UE分為三種狀態：
1. 空閒態(RRC_IDLE)
2. 連線態(RRC_CONNECTED)
3. 非活動態(RRC_INACTIVE)
 


處於空閒態的UE需要發起業務時，首先需要發起[RRC建立請求](#Setup-request)。
- 觸發空閒態到連接態的過程，就是==RRC建立過程==。
- 非活動態到連接態的過程，就是==RRC恢復過程==。

## 為什麼需要建立RRC connect
概述：RRC，全名：Radio Resource Control（無線資源控制）訊息。 RRC連線建立是在LTE和5G NR等行動通訊系統中，用於建立通訊會話的過程。在這個過程中，設備和基地台之間交換RRC訊息以建立連接，其中RRC訊息包括RRC連接請求、RRC連接設定等。總之，RRC連線建立的成功與否，直接影響用戶設備能否正常接取網路並進行資料通訊。


## RRC Setup request
- 名稱：RRC連線建立請求 
- 方向：UE->gNB 
- 解析：UE傳送RRCSetupRequest訊息給gNodeB
    - 在初始的RA中，==Msg3==傳送的是RRCSetupRequest訊息，RRCSetupRequest訊息攜帶[RRC建立原因](#EstablishmentCause-建立原因)和[UE標識](#ue-Identity-UE標識)，請求建立RRC連線。其中UE標識可以是S-TMSI或隨機數，如果上層提供了S-TMSI，則攜帶S-TMSI資訊給gNodeB；如果沒有S-TMSI訊息，則產生一個隨機數。

### gNB 行為

根據==接納控制==(Admission Control)來決定是否允許UE接入。
若允許，發送RRC Connection Setup；否則，發送RRC Connection Reject，並啟動計時器拒絕時間窗口內的請求。
### ASN.1

```c=
RRCSetupRequest ::= SEQUENCE {
   rrcSetupRequest RRCSetupRequest-IEs
}

RRCSetupRequest-IEs ::= SEQUENCE {
   ue-Identity InitialUE-Identity,
   establishmentCause EstablishmentCause,
   spare BIT STRING (SIZE (1))
}
```

### EstablishmentCause (建立原因)
```c=
EstablishmentCause ::= ENUMERATED {
   emergency, highPriorityAccess, mt-Access, mo-Signalling,
   mo-Data, mo-VoiceCall, mo-VideoCall, mo-SMS, mps-PriorityAccess, mcs-PriorityAccess,
spare6, spare5, spare4, spare3, spare2, spare1}
```
| 建立原因                                       | 描述                                       |
|:---------------------------------------------- |:------------------------------------------ |
| emergency                                      | 表示緊急情況下的連接請求。                 |
| highPriorityAccess                             | 表示高優先級的接入請求。                   |
| mt-Access                                      | 表示移動終端（MT）接入的請求。             |
| mo-Signalling                                  | 表示移動終端發起的信令請求。               |
| mo-Data                                        | 表示移動終端發起的數據連接請求。           |
| mo-VoiceCall                                   | 表示移動終端發起的語音通話請求。           |
| mo-VideoCall                                   | 表示移動終端發起的視頻通話請求。           |
| mo-SMS                                         | 表示移動終端發起的簡訊請求。               |
| mps-PriorityAccess                             | 表示多媒體優先接入請求。                   |
| mcs-PriorityAccess                             | 表示多媒體服務優先接入請求。               |
| spare6, spare5, spare4, spare3, spare2, spare1 | 這些是保留的成員，用於未來擴展或特定需求。 |

:::info
- [mo-Data](https://baike.c114.com.cn/view.php?id=25321-0E342DF2)
    - "mo-Data" 是一個通訊領域中的術語，是"Mobile Originated Data" 的縮寫。它指的是由行動終端（例如手機）發起的資料傳輸，即從行動裝置到網路的資料傳輸。舉個例子，當您發送簡訊、撥打電話、使用行動應用程式或在網路上瀏覽網站時，都可以產生 mo-Data。在LTE網路中，mo-Data 可以是音訊、視訊、圖像、文字等多種類型的數據，這些數據可以透過行動終端上傳到網絡，並透過基地台和其他設備進行傳輸和處理。行動終端機發起的資料傳輸是LTE網路中的重要功能，它為使用者提供了更好的體驗和更廣泛的應用場景。
- [mt-Access / TSGT#5(99)203](https://www.3gpp.org/ftp/tsg_t/tsg_t/tsgt_05/docs/pdfs/TP-99203.PDF)
    - MT - Mobile Terminated 是行動通訊領域中的術語，指的是行動終端存取網路的過程。在GSM和UMTS網路中，mt-Access通常指的是行動終端與基地台之間的存取過程，包括尋呼、存取請求、認證、授權等步驟。在LTE網路中，mt-Access通常指的是行動終端與eNodeB之間的存取過程，包括隨機存取、安全存取等步驟。 mt-Access的過程是行動通訊網路中的重要組成部分，直接影響行動終端的接取成功率和網路效能。
- [mps / 22.153](https://www.tech-invite.com/3m22/tinv-3gpp-22-153.html)
    - MPS： Multimedia Priority Service 多媒體優先服務
- [mcs](https://simnovus.com/unified-access-control-uac-the-gatekeeper-of-5g-networks/)
    - ==mcs==-PriorityAccess: Indicating ==Mission Critical Service== (MCS) traffic.
:::




### ue-Identity (UE標識)

```c=
InitialUE-Identity ::= CHOICE {
   ng-5G-S-TMSI-Part1 BIT STRING (SIZE (39)),
   randomValue BIT STRING (SIZE (39))
}
```
>TMSI : 5G S-Temporary Mobile Subscription Identifier (23.003)

ue-Identity：分不同的場景使用==randomValue==還是==ng-5G-S-TMSI-Part1== 
- randomValue
    - 當NAS沒有提供5G-S-TMSI時使用randomValue，說明UE當時已經清除了NAS上下文，常見的場景有開關飛行模式、重啟、第一次開機等。
- ng-5G-S-TMSI-Part1
    - 當NAS提供了5G-S-TMSI時候使用ng-5G-S-TMSI-Part1，ng-5G-S-TMSI-Part1取值為5G-S-TMSI最右邊39bit，說明UE當時儲存了NAS的上下文訊息，常見的場景有UE進入到IDEL態重新連線等。

完整的ng-5G-S-TMSI有兩個部分
- Part1 在 RRC Setup Request (右邊 39bit)
- Part2 在 RRC Setup Complete (左邊 9bit)
    
![image](https://hackmd.io/_uploads/r1iSgcUb1l.png)

如果gNB在RRCSetupComplete訊息中的ng-5G-S-TMSI-Value欄位得到的不是完整的NG-5G-S-TMSI，則利用RRCSetupRequest訊息中提供右側39bit和RRCSetupComplete訊息中提供最左面9bit，合併成一完整的NG-5G-S-TMSI。

## RRC Setup
- 名稱：RRC設定
- 方向：gNB->UE
- 解析：gNB將攜帶SRB1資源配置的詳細信息傳送給UE。

### UE 行為
- 利用==Msg2==分配的==C-RNTI==接收來自基地台的RRC Connection Setup訊息
- 建立==SRB1==
- 停止定時器T300、T302、T303、T305、T320（若這些定時器正在運作）
- 停止小區重選流程
- UE進入RRC連接態
- 設定==RRC Connection Setup Complete==內容


### ASN.1
```c=
RRCSetup ::= SEQUENCE {
   rrc-TransactionIdentifier RRC-TransactionIdentifier,
   criticalExtensions CHOICE {
      rrcSetup RRCSetup-IEs,
      criticalExtensionsFuture SEQUENCE {}
      }
}

RRCSetup-IEs ::= SEQUENCE {
   radioBearerConfig RadioBearerConfig,
   masterCellGroup OCTET STRING (CONTAINING CellGroupConfig),
   
   lateNonCriticalExtension OCTET STRING OPTIONAL,
   nonCriticalExtension SEQUENCE{} OPTIONAL
}
```
### RadioBearerConfig (在RRC Setup中，==僅攜帶建立SRB1==的配置)
- 38.311 6.3.2
```c=
RadioBearerConfig ::= SEQUENCE {
   srb-ToAddModList SRB-ToAddModList OPTIONAL, -- Cond HO-Conn
   srb3-ToRelease ENUMERATED{true} OPTIONAL, -- Need N
   drb-ToAddModList DRB-ToAddModList OPTIONAL, -- Cond HO-toNR
   drb-ToReleaseList DRB-ToReleaseList OPTIONAL, -- Need N
   securityConfig SecurityConfig OPTIONAL, -- Need M
   ...
}
```
### masterCellGroup
```c=
CellGroupConfig ::= SEQUENCE {
   cellGroupId CellGroupId,
   rlc-BearerToAddModList SEQUENCE (SIZE(1..maxLC-ID)) OF RLC-BearerConfig OPTIONAL, -- Need N
   rlc-BearerToReleaseList SEQUENCE (SIZE(1..maxLC-ID)) OF LogicalChannelIdentity OPTIONAL, -- Need N
   mac-CellGroupConfig MAC-CellGroupConfig OPTIONAL, -- Need M
   physicalCellGroupConfig PhysicalCellGroupConfig OPTIONAL, -- Need M
   spCellConfig SpCellConfig OPTIONAL, -- Need M
   sCellToAddModList SEQUENCE (SIZE (1..maxNrofSCells)) OF SCellConfig OPTIONAL, -- Need N
   sCellToReleaseList SEQUENCE (SIZE (1..maxNrofSCells)) OF SCellIndex OPTIONAL, -- Need N
   ...,
   [[
   reportUplinkTxDirectCurrent ENUMERATED {true} OPTIONAL -- Cond BWP-Reconfig
   ]]
}
```
- mac-CellGroupConfig
    - BSR
    - TAG
    - PHR
    - SR
    - DRX
- physicalCellGroupConfig
- spCellConfig


## RRC Setup Complete
- 名稱：RRC設定完成
- 方向：UE->gNB
- 解析：通知網路側，RRC連線建立完成。UE完成SRB1配置後，傳送RRC Connection Setup Complete到gNB。
### UE傳送的重要訊息
- selectedPLMN-Identity：UE從SIB1中攜帶的plmn-IdentityList裡所選PLMN的索引值。
- registeredAMF：可選。 plmn-Identity + amf-Identifier。
- guami-Type ：GUAMI類型，native or mapped。
- s-NSSAI-List ：可選。切片資訊。
- ==**dedicatedNAS-Message**==：UE帶給核心網路的第一個NAS訊息，可能是Registration Request，也可能是Service Request 
- ng-5G-S-TMSI-Value：可選。


:::info
#### GUAMI（Globally Unique AMF Identifier）
    - `native`：UE 直接使用 5G 核心網（5GC）提供的 GUAMI。
    - `mapped`：UE 來自 4G（EPC）核心網，將 4G GUTI 映射為 5G GUAMI。
:::
:::info
#### AMF-Identifier 用來標識 UE 之前所屬的 AMF
    - 如果 UE 先前註冊過，網絡可用這個資訊來決定是否沿用相同 AMF 處理 UE。
:::
:::info
####  s-NSSAI-List（Network Slice Selection Assistance Information, NSSAI）
    - 如果 UE 需要特定網絡切片（S-NSSAI），則在這裡攜帶
        - s-NSSAI-List = {SST: 1, SD: 0x123456}（ex:SST:1 -> 增強型移動寬頻 eMBB）
        - s-NSSAI-List = {SST: 2, SD: 0x654321}（ex:SST:2 -> 低延遲 URLLC）
        - 如果 UE 沒有攜帶，則表示它使用默認的切片。
:::
:::info
#### dedicatedNAS-Message
該訊息中的DedicatedNAS-Message只用於傳輸==initial NAS message==。在3GPP中，初始NAS訊息共定義了4種，分別是：
- Registration Request
- Deregistration Request
- Service Request
- Control Plane Service Request

註冊流程中的第一個Registration Request訊息就包含在該欄位中傳送給gNB。


UE 傳給核心網的第一個 NAS 訊息：
1. 可能是 Registration Request → 新註冊 / 重新附著
2. 可能是 Service Request → 恢復 RRC 連線
- 這是 RRCSetupComplete 訊息的核心！

這個訊息由 NAS 層產生，RRC 只是負責承載它並傳送給 gNB，然後 gNB 會轉交給 AMF。
- 如果 UE 是「開機後的第一次註冊」，通常會攜帶 Registration Request：
```c=
NAS: Registration Request
- UE Security Capabilities
- 5G-GUTI or SUCI (Subscription Concealed Identifier)
- S-NSSAI List (if required)
```
如果 UE 之前有連接過 5G 網絡，則可能攜帶 Service Request：
```c=
NAS: Service Request
- ng-5G-S-TMSI
- DRX Parameters (if available)
```
:::

:::info
#### ng-5G-S-TMSI-Value
`S-TMSI`是`SAE Temporary Mobile Station Identifier`的簡稱，而SAE的全稱是`System Architecture Evolution`

gNB在`RRCSetupComplete`訊息中的`ng-5G-S-TMSI-Value`欄位得到的不是完整的`NG-5G-S-TMSI`，則利用
- `RRCSetupRequest`訊息中提供右側39bit
- `RRCSetupComplete`訊息中提供最左面9bit
- 合併成一個完整的NG-5G-S-TMSI

使用 5G-S-TMSI，而不是完整的 5G-GUTI 或 IMSI，以減少信令開銷並保護隱私。

---
##### 比較 vs 5G-GUTI(5G Globally Unique Temporary UE Identity )
其中
```bash=
<5G-GUTI> = <GUAMI><5G-TMSI>  
    <GUAMI> = 
        <MCC><MNC><AMF Identifier>
    <AMF Identifier> = 
        <AMF Region ID><AMF Set ID><AMF Pointer>

<5G-S-TMSI> = 
    <AMF Set ID><AMF Pointer><5G-TMSI>
```

| 參數          | 長度  | 描述      |
| ------------- |:----- |:--------- |
| 5G-TMSI       | 32bit | AMF内唯一 |
| AMF Region ID | 8bit  |           |
| AMF Set ID    | 10bit |           |
| AMF Pointer   | 6bit  |           |

```bash=
<5G-S-TMSI> = <AMF Set ID><AMF Pointer><5G-TMSI>
```

:::
### ASN.1
```c=
RRCSetupComplete-IEs ::= SEQUENCE {
   selectedPLMN-Identity INTEGER (1..maxPLMN),
   registeredAMF RegisteredAMF OPTIONAL,
   guami-Type ENUMERATED {native, mapped} OPTIONAL,
   s-NSSAI-List SEQUENCE (SIZE (1..maxNrofS-NSSAI)) OF S-NSSAI OPTIONAL,
   dedicatedNAS-Message DedicatedNAS-Message,
   ng-5G-S-TMSI-Value CHOICE {
      ng-5G-S-TMSI NG-5G-S-TMSI,
      ng-5G-S-TMSI-Part2 BIT STRING (SIZE (9))
      } OPTIONAL,
   lateNonCriticalExtension OCTET STRING OPTIONAL,
   nonCriticalExtension SEQUENCE{} OPTIONAL
}
```









## 參考
:::success
[【5G NR】UE註冊流程](https://blog.csdn.net/NoBack7/article/details/125573613?ops_request_misc=&request_id=&biz_id=102&spm=1018.2226.3001.4187)
[LTE學習筆記 ——RRC連線建立](https://blog.csdn.net/u012800825/article/details/84451057)
[5G學習筆記之UE接入訊息詳解](https://blog.csdn.net/u012800825/article/details/105930850)
:::
[38.311](https://www.etsi.org/deliver/etsi_ts/138300_138399/138331/15.03.00_60/ts_138331v150300p.pdf)
[39.514 / 5G System Policy Authorization Service](https://www.etsi.org/deliver/etsi_ts/129500_129599/129514/16.07.00_60/ts_129514v160700p.pdf)
[通信百科](https://baike.c114.com.cn)
[NR RRC協定(2)---RRC連線建立流程](https://blog.csdn.net/crg2046/article/details/134629148?ops_request_misc=%257B%2522request%255Fid%2522%253A%252281F1FF0C-3655-4DD3-91FB-0BA23EFE5B6C%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=81F1FF0C-3655-4DD3-91FB-0BA23EFE5B6C&biz_id=0&spm=1018.2226.3001.4187)
[5G Standalone Access Registration Signaling Messages](https://www.eventhelix.com/5G/standalone-access-registration/details/5g-standalone-access-registration.html)\
[從手機開機流程介紹5G通信](https://blog.csdn.net/z754721/article/details/137705663?ops_request_misc=&request_id=&biz_id=102&spm=1018.2226.3001.4187)