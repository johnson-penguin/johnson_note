---
title: UE Capability

---

- [【4G&5G專題-52】：物理層-移動終端能力等級分類](https://blog.csdn.net/HiWangWenBing/article/details/114277442?spm=1001.2101.3001.6650.2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-2-114277442-blog-123540416.235%5Ev43%5Epc_blog_bottom_relevance_base4&utm_relevant_index=5)
- [LTE UE 能力](https://blog.csdn.net/dxpqxb/article/details/106075598?spm=1001.2101.3001.6650.3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-3-106075598-blog-123540416.235%5Ev43%5Epc_blog_bottom_relevance_base4&utm_relevant_index=6)
- [LTE筆記: LTE 在下行訊號的 Transmission Modes (TM)](https://note-on-clouds.blogspot.com/2022/10/lte-transmission-modes.html)
- [TS 136 306 - V16.1.0](https://www.etsi.org/deliver/etsi_ts/136300_136399/136306/16.01.00_60/ts_136306v160100p.pdf)
- [TS 136 822 - V16.1.0]()
- [TS 136 331 - V16.1.0]()
## 基本說明
這個Cat等級是什麼？這個標識，指明了該款手機基帶物理層（空口無線通信）最高通信能力的指示，反應的是手機的無線接入能力，同時也是芯片公司產品設計、開發能力的標誌。

1. 3GPP的協議規範是一個逐漸發展的過程，終端發佈時，符合的3GPP標準不同。
2. 終端也是逐漸演進的過程，不同階段的終端，其==基頻晶片==的能力不同。
3. 終端的客戶群不同，低階、中階、高階手機，其價格不同，其實體層晶片的能力自然不同


## 能力類別
### gNB
主要是基地台RF層和物理層的能力，包括： 
1. 調變解調的能力：xPSK, xQAM 
2. 多天線MIMO的層數： 
3. 發送和接收分集 
4. 波束賦形的波束
5. 物理業務頻道最大的傳輸速率


### UE
| 類別              | 範例參數                                                                                             | 作用                             |
|:----------------- |:---------------------------------------------------------------------------------------------------- |:-------------------------------- |
| 基本能力          | UE Category（Cat.1, Cat.6, Cat.16, NR Rel-15/16）                                                    | 告知基地台 UE 的 LTE/NR 等級     |
| MIMO 支援         | 支援的天線數（2x2, 4x4, 8x8） 影響 MIMO 配置 Modulation 最大支援的調變方式（64QAM, 256QAM, 1024QAM） | 影響吞吐量                       |
| CA 載波聚合       | 支援的 Band 組合                                                                                     | 影響多載波配置                   |
| Dual Connectivity | 是否支援 LTE+NR 連線（EN-DC）                                                                        | 影響 NSA 群組網                  |
| 功率能力          | 最大發射功率                                                                                         | 影響 UE 在遠離基地台時的通訊能力 |
:::info
- 基地台必須知道 UE 能力，否則無法為其正確分配資源。 
    - ✅ 如果 UE 支援高階 MIMO（如 4×4 MIMO），基地台可以為其配置 4 個資料流，提高吞吐量。 
    - ❌ 如果 UE 僅支援 2×2 MIMO，但基地台錯誤地分配 4×4 MIMO，就會導致傳輸失敗或資源浪費。 

- 確保 UE 只接收其支援的空口傳輸模式（TM, Transmission Mode） LTE/NR 有不同的傳輸模式（TM1~TM10）： 
    - ✅ 若 UE 支援 TM9（多用戶 MIMO），基地台可配置 MU-MIMO，提高頻譜效率。 
    - ❌ 若 UE 不支援 TM9，基地台仍強行配置，則 UE 無法正確解碼數據，導致傳輸失敗。
    - Ex:![image](https://hackmd.io/_uploads/ry44ld5uJe.png)
:::
#### UE DL
![image](https://hackmd.io/_uploads/SJFlr_cdJg.png)
#### UE UL
![image](https://hackmd.io/_uploads/rJPXSu5_yl.png)




## 流程
==終端==與==基地台的L3 RRC層==進行

![image](https://hackmd.io/_uploads/HyMZId9u1g.png)

- 當基地台需要UE上報UE Capability時，基地台會為UE下發UECapabilityEnquiry指令。
- 當UE收到UECapabilityEnquiry指令後，UE根據指令回報UE能力UECapabilityInformation。


## UECapabilityEnquiry
### ASN.1
```c=
UECapabilityEnquiry ::= SEQUENCE {
   rrc-TransactionIdentifier RRC-TransactionIdentifier,
   criticalExtensions CHOICE {
      ueCapabilityEnquiry UECapabilityEnquiry-IEs,
      criticalExtensionsFuture SEQUENCE {}
      }
}

UECapabilityEnquiry-IEs ::= SEQUENCE {
   ue-CapabilityRAT-RequestList UE-CapabilityRAT-RequestList,
   lateNonCriticalExtension OCTET STRING OPTIONAL,
   ue-CapabilityEnquiryExt OCTET STRING (CONTAINING UECapabilityEnquiry-v1560-IEs) OPTIONAL -- Need N
}

UECapabilityEnquiry-v1560-IEs ::= SEQUENCE {
   capabilityRequestFilterCommon UE-CapabilityRequestFilterCommon OPTIONAL, -- Need N
   nonCriticalExtension SEQUENCE{} OPTIONAL
}
```
## UECapabilityInformation

```c=
UECapabilityInformation ::= SEQUENCE {
   rrc-TransactionIdentifier RRC-TransactionIdentifier,
   criticalExtensions CHOICE {
      ueCapabilityInformation UECapabilityInformation-IEs,
      criticalExtensionsFuture SEQUENCE {}
      }
}

UECapabilityInformation-IEs ::= SEQUENCE {
   ue-CapabilityRAT-ContainerList UE-CapabilityRAT-ContainerList OPTIONAL,
   
   lateNonCriticalExtension OCTET STRING OPTIONAL,
   nonCriticalExtension SEQUENCE{} OPTIONAL
}
```

## UE Capability
```c=
UE-NR-Capability ::= SEQUENCE {
   accessStratumRelease AccessStratumRelease,
   pdcp-Parameters PDCP-Parameters,
   rlc-Parameters RLC-Parameters OPTIONAL,
   mac-Parameters MAC-Parameters OPTIONAL,
   phy-Parameters Phy-Parameters,
   rf-Parameters RF-Parameters,
   measAndMobParameters MeasAndMobParameters OPTIONAL,
   fdd-Add-UE-NR-Capabilities UE-NR-CapabilityAddXDD-Mode OPTIONAL,
   tdd-Add-UE-NR-Capabilities UE-NR-CapabilityAddXDD-Mode OPTIONAL,
   fr1-Add-UE-NR-Capabilities UE-NR-CapabilityAddFRX-Mode OPTIONAL,
   fr2-Add-UE-NR-Capabilities UE-NR-CapabilityAddFRX-Mode OPTIONAL,
   featureSets FeatureSets OPTIONAL,
   featureSetCombinations SEQUENCE (SIZE (1..maxFeatureSetCombinations)) OF FeatureSetCombinationOPTIONAL,
   
   lateNonCriticalExtension OCTET STRING (CONTAINING UE-NR-Capability-v15c0)OPTIONAL,
   nonCriticalExtension UE-NR-Capability-v1530 OPTIONAL
}
```