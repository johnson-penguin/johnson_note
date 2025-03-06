---
title: TMA-DL

---


## MTSCONFIGUEGROUP

:::spoiler DESCRIPTION
Defines a set of UEs to be referenced insubsequent commands. Multiple groups can be defined and any specific UE may be a member of more than one group of the same type. All members of a group must have the a consistent SYSCAP capabability (LTE, NSA, SA).

When executing an MTS scenario a UE can only be a member of one Mobility group and one Traffic group, or a single group for both Mobility and Traffic.

A group may be redefined once activation has been completed and providing the group is not used in an active MTS scenario.

The MDT group is used to indicate up to 250 UEs> which are to be used in Minimisation of Drive Tes been completed and providing the group is not used in an active MTS scenario.

The MDT group is used to indicate up to 250 UEs which are to be used in Minimisation of Drive Test (MDT). UEs belonging to both an MDT group and an active Mobility group will report location information to the network.

MDT is an additional attribute to a UE and therefore UEs in this group may also belong to Mobility and Traffic group types.Multiple MDT groups may be specified but the maximum number of UEs configured across these groups is 250.A specific UE may only be a member of one MDT group. UEs will remain in the defined MDT groups for the duration of the scenario.
:::

:::spoiler 描述
定義一組UE（用戶設備），可在後續命令中參考。可以定義多組，且任何特定的UE可以是同類型多個群組的成員。群組中的所有成員必須具有一致的SYSCAP能力（如LTE、NSA、SA）。

在執行MTS場景時，UE只能是單一移動性（Mobility）群組和單一流量（Traffic）群組的成員，或是同時擁有移動性和流量屬性的單一群組成員。

一旦群組被激活並完成後，且未在活動的MTS場景中使用的情況下，可以重新定義該群組。

MDT（最小化駕駛測試）群組用於標示最多250個UE。這些UE將用於MDT功能。屬於MDT群組和活動移動性群組的UE將向網路報告位置信息。

MDT是一個UE的附加屬性，因此該群組中的UE也可以屬於移動性和流量群組類型。可以指定多個MDT群組，但這些群組中的UE總數最多為250。特定UE只能屬於一個MDT群組。在場景期間，UE將保留在定義的MDT群組中。
:::

| Parameter              | Value | DESCRIPTION                            |
|:---------------------- |:----- |:-------------------------------------- |
| UE Group Id            | 0     | UE Group Id                            |
| UE Group Type          | 0     | [DESCRIPTION](#UE-Group-Type)          |
| List Number of entries | Text  | [DESCRIPTION](#List-Number-of-entries) |
| UE Context Id          |       | [DESCRIPTION](#UE-Context-Id)          |

---

### UE Group Type

- 0 = Both (Mobility and Traffic)
- 1 = Mobility Only
- 2 = Traffic Only
- 3 = MDT Group

Defines how the group will be used. Note when the MTS scenario is activated a UE can only be in one Mobility group and one Traffic group. 

---

### List Number of entries	
Defines a set of UEs to be referenced in subsequent commands.Multiple groups can be defined and any specific UE may be a member of more than one group of the same type. All members of a group must have the a consistent SYSCAP capabability (LTE. NSA, SA).

When executing an MTS scenario a UE can only be a member of one Mobility group and one Traffic group, or a single group for both Mobility and Traffic.A group may be redefined once activation has been completed and providing the group is not used in an active MTS scenario.The MDT group is used to indicate up to 250 UEs which are to be used in Minimisation of Drive Test (MDT).

UEs belonging to both an MDT group and an active Mobility group will report location information to the network.MDT is an additional attribute to a UE and Multiple MDT groups may be specified but the maximum number of UEs configured across these groups is 250.A specific UE may only be a member of one MDT group. UEs will remain in the defined MDT groups for the duration of the scenario.

---

### UE Context Id
A range of UE IDs may be defined using a dash - to designate the ==first== and ==last== UE IDs required, e.g. "32-63".

The actual maximum UE Context ID value ==depends on the number of UEs supported in the specific EXT- MUE or E500 release==, the number of cell units and the configured licenses both at the cell and system

Note: UE Id 18000 to 39999 is supported only for NBloT UEs level.

---

## MTSCONFIGTRAFFICPROFILE (only list Required items/optional items are not added)

Defines the traffic profles to be applied to previously defined UE groups. This defines the time variant data application behaviour and is used to directly control an external device (typically the diversifEye D500 or 8400) to emulate real data applications used by UE.




| Parameter                                   | Value    | DESCRIPTION                                                |
|:------------------------------------------- |:-------- |:---------------------------------------------------------- |
| Traffic Profile ID*                         | 0        | Define the id for the profile                              |
| List Number of PDN -LTE-/PDU Sessions -NR-* |          | [DESCRIPTION](#List-Number-of-PDN--LTE--PDU-Sessions--NR-) |
| APN -LTE- / DNN -NR-*                       | internet |                                                            |
| Service Name Type (Conditional)*            | 0        |                                                            |
| Service Name Type_EQUAL_TO_0*               | NA       |                                                            |
| Application name*                           | DLUDP    |                                                            |
| Load profile*                               | -1       |                                                            |
| Bearer allocation (Conditional)*            | 0        |                                                            |
| Bearer allocation_EQUAL_TO_0..1*            | NA       |                                                            |
| Service delay time (Optional)               | Enable   |                                                            |
| Service delay time*                         | 10       |                                                            |
| Service duration*                           | 0        |                                                            |
| Service repeat*                             | Disable  |                                                            |
| PDN/ PDU Session Type                       | 0        |                                                            |
| PDN Options -LTE-/ PCO Extension -NR-       | NA       |                                                            |
| Emergency Call                              | 0        |                                                            |
| S-NSSAI                                     | NA       |                                                            |
| PDN/PDU session Delay                       | 0        |                                                            |
| PDN/ PDU Session re-establishment           | Disable  |                                                            |

### List Number of PDN -LTE-/ PDU Sessions -NR-*
Defines the traffic profiles to be applied to previously defined UE groups. This defines the time variant data application behaviour and is used to directly control an external device (typically the diversifEye D500 or 8400) to emulate real data applications used by UE.

---

### APN-LTE-/DNN-NR-
Access Point Name/ Data Network Name. Can be "" for PDN/ PDU Session 0- the default bearer created on Attach.

Note that if the ==APN/ DNN== for a UE ==changes== during scenario ==execution the UE will Detach and re-Attach==. 
Note that the name must not include quotation marks unless an empty string ("") is required.

---

### Service Name Type (Conditional)
- 0 = MTS provisioned (links into the predefined diversifEye applications) 
- 1 = User defined. Links into a user pre-defined named diversifEye application type.
- 2 = Custom RDA device. 

>不明註記
>Only applicable if' RDA Client Device' = 'Custom Device' in the MTSCONFIGRDA command. NOT SUPPORTED.

---

### Service Name Type_EQUAL_TO_0

Defines the traffic profiles to be applied to previously defined UE groups. This defines the time variant data application behaviour and is used to directly control an external device (typically the diversifEye D500 or 8400) to emulate real data applications used by UE. 

### Application name

已配置應用的根名稱：
例如："cftp_get"、"cftp_put"、"chttp"、"cvoip"。最終的應用名稱將包含啟動該應用的 UE ID 和 PDN ID//PDU Session ID。如果配置了載入配置檔，則還會附加表示載入配置檔的字串。例如："cftp_get_lp0_0001_0"。最終的完整應用名稱必須存在於已加載的 diversifEye 測試組中，並可用於與 MTS 情境一起運行。引號可以選擇性地用來將（應用名稱）括起來，但當 "應用名稱" 字串中有空格分隔的參數（如 sping、cping 應用程式）時，必須將其括在引號中。引號不計算字符數限制。

因此，字串中的應用名稱部分不能包含引號。

請注意，允許的字串長度為 3 到 20 個字符。對於作為引號包裹的應用名稱字串，長度不得超過 32 個字符（出於歷史原因，cping/sping 允許長度為 42 個字符，但輸入字串會在第一個參數後或 32 個字符處截斷，以先達到為準）。

---
- **MO（或客戶端）Ping**: "cping *dest*"，其中 *dest* 是目標主機名或 IP 地址（以標準格式），用於移動端發起的 Ping。
- **MT（或伺服器端）Ping**: "sping *id*"，其中 *id* 是移動端終止 Ping 的源 ping 應用 ID（即主機名）。這是一個 2 字符的欄位，第一個字符為 's'，第二個字符為 0 至 9 範圍內的數字。這支持最多 10 個伺服器端主機作為 Ping 的源來 Ping UE。
---
    
載入配置檔也可以應用於 cping 和 sping（如果已配置），允許每個 UE/PDN/PDU Session 支持最多 10 個不同配置的 cping/sping 應用。

注意，該命令要求適當命名的應用已在 diversifEye 500 中配置。例如："cping_lp0_0001_0"、"cping_0004_1"、"sping_s0_lp0_0001_0"、"sping_s1_0004_1"。

對於 cping，*dest* 可以包含一條規則，用於動態確定被 Ping 的 UE。

---
    
支持的規則如下：

- **[ODD]**：如果當前 UE ID 是偶數，則計算 UE ID+1 並應用到 *dest* 值。如果當前 UE ID 是奇數，則不執行 Ping 操作。即偶數編號的 UE 將會 Ping 奇數編號的 UE。
- **[EVEN]**：如果當前 UE ID 是奇數，則計算 UE ID-1 並應用到 *dest* 值。如果當前 UE ID 是偶數，則不執行 Ping 操作
- **[THIS+ (or-) N]**：地址被評估為 UE X+-N 並應用到 *dest*。

例子：
![image](https://hackmd.io/_uploads/rJy6X6JfJl.png)


    - `cping pppoe_[ODD]_1`    UE 4 cping UE 5.        UE 7 would not ping.
    - `cping pppoe_[THIS+5]_1  UE 4 cping UE 9.


- `cping pppoe_[ODD]_1`  
  UE 4 會 Ping UE 5，UE 7 不會 Ping。
- `cping pppoe_[THIS+5]_1`  
  UE 4 會 Ping UE 9。

    
---
注意：以下預定義名稱可用作 VoIP UA 的特殊別名，支持僅在 VoLTE/VONR/VOIMS 中可用的附加功能：
- **語音**: "volte"、"vonr"、"voims"
- **視頻**: "vilte"、"vinr"、"viims"
- **短信/IP**: sms
- **IMS 註冊**: "imsreg"

以上任何 VoIP UA 應用實例所支持的功能：
- **P-CSCF 發現**: 如果網絡在附著過程中為相應的 UE/PDN/PDU Session 提供了 P-CSCF IP 地址，那麼它將在運行時分配給給定的應用實例並設置為服務。
- **統一接入控制（5G）**: 僅適用於 5G (SA) 操作，當初始化任何移動端數據以後，這些名稱將用來指示相應的移動端 mmTel 服務類型（例如 "vinr" 對應 "moMmtelVideo" 等），用於後續的 NAS 信令和程序。"imsreg" 是一個僅用於 IMS 註冊的應用類型，並將觸發訪問類別 9 的嘗試。注意：對於 EPC (LTE/NSA) 操作，UAC 不適用。

---
    
### Load profile
The loading factor to apply to to the service.
- 0..9 identifies the Load Profile to use and adds the identification string to the application name as described above.
- -1 = do not include the Load Profile in the name.

==Use of Load Profiles is deprecated in favour of using application aliases (alternate names).==

---

### Bearer allocation (Conditional)
- 0 = Not required (Default bearer will be used) 
- 1 = NW allocated. NW (e.g. IMS server) will allocate bearer resources as required.
- 2 = UE requested. UE wil perform a bearer resource request.

---

### Bearer allocation_EQUAL_TO_0..1 /Service delay time (Optional)

DESCRIPTION: ==Defines the traffic profiles to be applied to previously defined UE groups==. This defines the time variant data application behaviour and is used to directly control an external device (typically the diversifEye D500 or 8400) to emulate real data applications used by UE.

---

### Service delay time
Delay time in seconds to apply before starting the service Note that SETP MTS_DISABLE_SERVICE_DELAY_AFTER_REATTACH can be used to prevent this delay time applying after non-MTS initiated detach and reattach.

---

### Service duration
Duration in seconds for the service to run.==(0 = continuous)==
When the duration time elapses the associated data application is taken out of service and any associated dedicated bearer is released.

---

### Service repeat
- 0 = No
- 1 = Yes. Service will restart after the ==specified delay time==.

---

### PDN/ PDU Session Type
- 0 IPv4 (default)
- 1= IPv6
- 2 = IPv4v6 (both)

---

### Parameter: PDN Options -LTE-/PCO Extension -NR-
- Bit 0 = P-CSCF IPv4 Address Request (NR Only)
- Bit 1-IM CN Subsytem Signaling Flag (NR Only) Bit 2 - P-CSCF IPv6 Address Request (NR Only)
- Bit 3-Ipv4 Link MTU Request (NR Only)
- Bit 4 - NON IP Link MTU Request (NR Only)
- Bit 5 - APN Rate ControlAs defined in 10.5.6.3, will be used for future enhancement of support of PCO not defined thus far. Sent un-ciphered in the PDN Connectivity Request or sent ciphered using the ESM information request procedure. (NR Only)
- Bit 6- DNS Server IPv4 Address Request
- Bit 7-DNS Server IPv6 Address Request (NR Only)
- Bit 8 - IP address allocation via NAS signaling (NR Only)

For NR: 
If not provided or empty brackets ([]) provided, this parameter is not configured/ used.

For LTE: 
Should be left blank. Currently the defaults are: DNS Server IPv4 Address Request = OFFIPv4 Address Allocation via DHCP = OFF

---

### Emergency Call
Notes:
In case of IRAT operation, setting this parameter to 1 will trigger an EPS fallback procedure in the UE on a 5G cell.

For NR SA, the value 1 is not supported and if set to 1 will not have any effect. Limited service UE is not supported.

If the emergency call is for the first PDN/ PDU Session it will result in an emergency attach if the UE is in limited service.To ensure the UE is in limited service all the PLMNs the UE could camp on should be put in the forbidden PLNMN list, or no USIM be provisioned.If the emergency call is for the second PDN/PDU Session it will result in an emergency PDN connection if the UE is in normal service.

No UE initiated bearers or additional PDN/PDU Session connections are allowed if an initial attach is performed.

No UE initiated bearers are allowed if the emergency call is for the second PDN/PDU Session.

Examples:
- Emergency Attach
```bash=
FORW MTE MTSCONFIGTRAFFICPROFILE 01(www.open-ims.test 1(0(voims_emergency 0)0[1]]
```
- Emergency PDN/PDU Session
```bash=
FORW MTE MTSCONFIGTRAFFICPROFILE 02(www.open-ims.test 1(0(cftp_put 0) 0 [][.mmm.test 1(0(voims_emergency 1) 0[1]}
```

---
### S-NSSAI
Single Network Slice Selection Assistance Information.
- Bits 0-23 = Slice Differentiator (SD)
- Bits 24-31 = Slice/service type (SST)

Example: 0x12345678
- SD = 0x345678
- SST = 0x12

If not provided or empty brackets ([]) provided, this parameter is not configured/ used.

---

### PDN/PDU session Delay
Delay time in seconds to apply before starting the PDN/PDU Session

---


### PDN/ PDU Session re-establishment
- 0 = False
- 1 = True

PDN/ PDU Session will be re-established after PDU session Delay if configured else, it will be re-established immediately.
==Note: This is currently applicable for NR only.==

---