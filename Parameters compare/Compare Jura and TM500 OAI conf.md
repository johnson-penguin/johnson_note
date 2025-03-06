---
title: Compare Jura and TM500 OAI conf

---

## 合併參數

### 1. 識別參數
| 參數名稱           | JURA 值                                                                | TM500 值                                                               |
| ------------------ |:---------------------------------------------------------------------- | ---------------------------------------------------------------------- |
| Active_gNBs        | "gNB-OAI"                                                              | "gNB-OAI-2"                                                            |
| Asn1_verbosity     | "none"                                                                 | "none"                                                                 |
| gNB_ID             | 0xe00                                                                  | 0xe01                                                                  |
| gNB_name           | "gNB-OAI"                                                              | "gNB-OAI-2"                                                            |
| tracking_area_code | 1                                                                      | 1                                                                      |
| plmn_list          | { mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); } | { mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1; }); } |
| nr_cellid          | 1                                                                      | 1                                                                      |

### 2. 物理參數
| 參數名稱                                     | JURA 值                                               | TM500 值                                              |
| -------------------------------------------- |:----------------------------------------------------- | ----------------------------------------------------- |
| pdsch_AntennaPorts_XP                        | 2                                                     | 2                                                     |
| pusch_AntennaPorts                           | 4                                                     | 2                                                     |
| do_CSIRS                                     | 0                                                     | 1                                                     |
| do_SRS                                       | 0                                                     | 0                                                     |
| force_256qam_off                             | 0                                                     | 1                                                     |
| sib1_tda                                     | 15                                                    | 15                                                    |
| pdcch_ConfigSIB1                             | { controlResourceSetZero = 11; searchSpaceZero = 0; } | { controlResourceSetZero = 11; searchSpaceZero = 0; } |
| absoluteFrequencySSB                         | 630048                                                | 630048                                                |
| dl_frequencyBand                             | 78                                                    | 78                                                    |
| dl_absoluteFrequencyPointA                   | 626772                                                | 626772                                                |
| dl_offstToCarrier                            | 0                                                     | 0                                                     |
| dl_subcarrierSpacing                         | 1                                                     | 1                                                     |
| dl_carrierBandwidth                          | 273                                                   | 273                                                   |
| initialDLBWPlocationAndBandwidth             | 1099                                                  | 1099                                                  |
| initialDLBWPsubcarrierSpacing                | 1                                                     | 1                                                     |
| initialDLBWPcontrolResourceSetZero           | 11                                                    | 11                                                    |
| initialDLBWPsearchSpaceZero                  | 0                                                     | 0                                                     |
| ul_frequencyBand                             | 78                                                    | 78                                                    |
| ul_offstToCarrier                            | 0                                                     | 0                                                     |
| ul_subcarrierSpacing                         | 1                                                     | 1                                                     |
| ul_carrierBandwidth                          | 273                                                   | 273                                                   |
| pMax                                         | 23                                                    | 23                                                    |
| prach_ConfigurationIndex                     | 159                                                   | 151                                                   |
| preambleTransMax                             | 7                                                     | 7                                                     |
| powerRampingStep                             | 2                                                     | 3                                                     |
| ra_ResponseWindow                            | 5                                                     | 5                                                     |
| ssb_perRACH_OccasionAndCB_PreamblesPerSSB_PR | 3                                                     | 3                                                     |
| ssb_perRACH_OccasionAndCB_PreamblesPerSSB    | 15                                                    | 15                                                    |
| ra_ContentionResolutionTimer                 | 7                                                     | 7                                                     |
| rsrp_ThresholdSSB                            | 19                                                    | 19                                                    |
| prach_RootSequenceIndex_PR                   | 2                                                     | 2                                                     |
| prach_RootSequenceIndex                      | 1                                                     | 1                                                     |
| msg1_SubcarrierSpacing                       | 1                                                     | 1                                                     |
| restrictedSetConfig                          | 0                                                     | 0                                                     |
| msg3_DeltaPreamble                           | 2                                                     | 2                                                     |
| p0_NominalWithGrant                          | -96                                                   | -96                                                   |
| pucchGroupHopping                            | 0                                                     | 0                                                     |
| hoppingId                                    | 0                                                     | 0                                                     |
| p0_nominal                                   | -96                                                   | -96                                                   |
| ssb_PositionsInBurst_PR                      | 2                                                     | 2                                                     |
| ssb_PositionsInBurst_Bitmap                  | 0x1                                                   | 0x1                                                   |
| ssb_periodicityServingCell                   | 2                                                     | 2                                                     |
| dmrs_TypeA_Position                          | 0                                                     | 0                                                     |
| subcarrierSpacing                            | 1                                                     | 1                                                     |
| referenceSubcarrierSpacing                   | 1                                                     | 1                                                     |
| dl_UL_TransmissionPeriodicity                | 5                                                     | 6                                                     |
| nrofDownlinkSlots                            | 3                                                     | 7                                                     |
| nrofDownlinkSymbols                          | 6                                                     | 6                                                     |
| nrofUplinkSlots                              | 1                                                     | 2                                                     |
| nrofUplinkSymbols                            | 4                                                     | 4                                                     |
| ssPBCH_BlockPower                            | 0                                                     | 10                                                    |

:::danger
| 參數名稱                      | JURA 值 | TM500 值 |
|:----------------------------- |:------- |:-------- |
| pusch_AntennaPorts            | 4       | 2        |
| prach_ConfigurationIndex      | 159     | 151      |
| powerRampingStep              | 2       | 3        |
| do_CSIRS                      | 0       | 1        |
| dl_UL_TransmissionPeriodicity | 5       | 6        |
| nrofDownlinkSlots             | 3       | 7        |
| nrofUplinkSlots               | 1       | 2        |
| ssPBCH_BlockPower             | 0       | 10       |

pusch_AntennaPorts 4>>2
:::


### 3. SCTP 定義
| 參數名稱                | JURA 值                     | TM500 值                     |
|----------------------|----------------------|----------------------|
| SCTP_INSTREAMS      | 2                    | 2                    |
| SCTP_OUTSTREAMS     | 2                    | 2                    |

### 4. AMF 參數
| 參數名稱                    | JURA 值                     | TM500 值                   |
| --------------------------- | --------------------------- |:-------------------------- |
| amf_ip_address              | { ipv4 = "192.168.8.108"; } | { ipv4 = "192.168.8.21"; } |
| GNB_IPV4_ADDRESS_FOR_NG_AMF | "192.168.8.29/24"           | "192.168.8.29"             |
| GNB_IPV4_ADDRESS_FOR_NGU    | "192.168.8.29/24"           | "192.168.8.29"             |
| GNB_PORT_FOR_S1U            | 2152                        | 2152                       |

### 5. MAC/RLC 參數
| 參數名稱             | JURA 值     | TM500 值    |
| -------------------- | ----------- |:----------- |
| num_cc               | 1           | 1           |
| tr_s_preference      | "local_L1"  | "local_L1"  |
| tr_n_preference      | "local_RRC" | "local_RRC" |
| pusch_TargetSNRx10   | 300         | 300         |
| pucch_TargetSNRx10   | 200         | 230         |
| ul_bler_target_upper | 0.35        | 0.35        |
| ul_bler_target_lower | 0.15        | 0.15        |
| pusch_FailureThres   | 100         | 100         |
| ul_max_mcs           | -           | 28          |


:::danger
| 參數名稱             | JURA 值     | TM500 值    |
| -------------------- | ----------- |:----------- |
| pucch_TargetSNRx10   | 200         | 230         |
| ul_max_mcs           | -           | 28          |
:::


### 6. L1 參數
| 參數名稱             | JURA 值     | TM500 值    |
| -------------------- | ----------- |:----------- |
| num_cc               | 1           | 1           |
| tr_n_preference      | "local_mac" | "local_mac" |
| prach_dtx_threshold  | 130         | 130         |
| pucch0_dtx_threshold | 80          | 80          |
| pusch_dtx_threshold  | 10          | 10          |
| tx_amp_backoff_dB    | 20          | 20          |
| L1_rx_thread_core    | 8           | 8           |
| L1_tx_thread_core    | 10          | 10          |
| phase_compensation   | 1           | 0           |
| max_ldpc_iterations  | -           | 10          |

:::danger

| 參數名稱             | JURA 值     | TM500 值    |
| -------------------- | ----------- |:----------- |
| phase_compensation   | 1           | 0           |
| max_ldpc_iterations  | -           | 10          |

phase_compensation TM500 already done 1 >>>>0
:::




### 7. RU 參數
| 參數名稱                      | JURA 值                                  | TM500 值                                 |
| ----------------------------- | ---------------------------------------- |:---------------------------------------- |
| local_rf                      | "no"                                     | "no"                                     |
| nb_tx                         | 4                                        | 2                                        |
| nb_rx                         | 4                                        | 2                                        |
| att_tx                        | 0                                        | 0                                        |
| att_rx                        | 0                                        | 0                                        |
| bands                         | [78]                                     | [78]                                     |
| max_pdschReferenceSignalPower | -27                                      | -27                                      |
| max_rxgain                    | 75                                       | 75                                       |
| sf_extension                  | 0                                        | 0                                        |
| eNB_instances                 | [0]                                      | [0]                                      |
| ru_thread_core                | 6                                        | 6                                        |
| sl_ahead                      | 5                                        | 5                                        |
| bf_weights                    | [0x00007fff, 0x0000, 0x00007fff, 0x0000] | [0x00007fff, 0x0000, 0x00007fff, 0x0000] |
| tr_preference                 | "raw_if4p5"                              | "raw_if4p5"                              |
| do_precoding                  | 0                                        | 0                                        |

:::danger
| 參數名稱                      | JURA 值                                  | TM500 值                                 |
| ----------------------------- | ---------------------------------------- |:---------------------------------------- |
| nb_tx                         | 4 (testing 2)                                       | 2                                        |
| nb_rx                         | 4 (testing 2)                                       | 2                                        |

JURA  using 4x4 cc ,TM500 now testing 2x2 cc

nb_tx 4>>>2
nb_rx 4>>>2
:::
### 8. 安全性參數
| 參數名稱                | JURA 值                     | TM500 值                     |
|----------------------|----------------------|----------------------|
| ciphering_algorithms | ("nea0")             | ("nea0")             |
| integrity_algorithms  | ("nia2", "nia0")     | ("nia2", "nia0")     |
| drb_ciphering        | "yes"                | "yes"                |
| drb_integrity        | "no"                 | "no"                 |

### 9. 日誌配置
| 參數名稱                | JURA 值                     | TM500 值                     |
|----------------------|----------------------|----------------------|
| global_log_level     | "info"               | "info"               |
| hw_log_level         | "info"               | "info"               |
| phy_log_level        | "info"               | "info"               |
| mac_log_level        | "info"               | "info"               |
| rlc_log_level        | "info"               | "info"               |
| pdcp_log_level       | "info"               | "info"               |
| rrc_log_level        | "info"               | "info"               |
| ngap_log_level       | "info"               | "info"               |
| f1ap_log_level       | "info"               | "info"               |

### 10. FHI 72 配置
| 參數名稱     | JURA 值                                    | TM500 值                                   |
| ------------ |:------------------------------------------ | ------------------------------------------ |
| dpdk_devices | ("0000:3b:02.0", "0000:3b:02.1")           | ("0000:3b:02.0", "0000:3b:02.1")           |
| system_core  | 0                                          | 0                                          |
| io_core      | 4                                          | 4                                          |
| worker_cores | (2)                                        | (2)                                        |
| du_addr      | ("00:11:22:33:44:66", "00:11:22:33:44:66") | ("00:11:22:33:44:66", "00:11:22:33:44:66") |
| ru_addr      | ("00:e0:0c:00:ae:06", "00:e0:0c:00:ae:06") | ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10") |
| mtu          | 8000                                       | 1500                                       |

### 11. FHI 72 fh_config 配置
| 參數名稱         | JURA       | TM500       |
| ---------------- | ---------- |:----------- |
| Tadv_cp_dl       | 125        | 125         |
| T2a_cp_dl        | (250, 429) | (259, 470)  |
| T2a_cp_ul        | (100, 429) | (125, 1200) |
| T2a_up           | (134, 360) | (70, 345)   |
| Ta3              | (10, 200)  | (50, 171)   |
| T1a_cp_dl        | (251, 429) | (258, 392)  |
| T1a_cp_ul        | (101, 429) | (285, 300)  |
| T1a_up           | (135, 357) | (155, 300)  |
| Ta4              | (20, 200)  | (0, 200)    |
| ru_config        |            |             |
| - iq_width       | 9          | 9           |
| - iq_width_prach | 9          | 9           |
| - fft_size       | 12         | 12          |
| prach_config     |            |             |
| - eAxC_offset    | 4          | 2           |
| - kbar           | 0          |             |


:::danger
Timing window different but can active C/U plane so So gNB does not need to be modified.

4x4 cc ----> 2x2 cc
eAxC_offset 4>>>2

:::



:::danger
| 參數名稱 | JURA 值 | TM500 值 |
| -------- | ------- | -------- |
| mtu      | 8000    | 1500     |
| ru_addr      | ("00:e0:0c:00:ae:06", "00:e0:0c:00:ae:06") | ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10") |


ru_addr >>> ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10")
MTU does not affect RACH
:::

### Record

### Testing OAI conf (for Jura)
After mapping the following parameters

#### Table for adjusting parameters
:::success
| 參數               | 調整後                                     | 調整前                                     |
|:------------------ |:------------------------------------------ |:------------------------------------------ |
| nb_tx              | 2                                          | 4                                          |
| nb_rx              | 2                                          | 4                                          |
| ru_addr            | ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10") | ("00:e0:0c:00:ae:06", "00:e0:0c:00:ae:06") |
| pusch_AntennaPorts | 2                                          | 4                                          |
| eAxC_offset        | 2                                          | 4                                          |
| phase_compensation | 0                                          | 1                                          |
:::

![image](https://hackmd.io/_uploads/rJ1hp1LbJx.png)



After mapping `eAxC_offset`
![image](https://hackmd.io/_uploads/r1dEggUWke.png)

#### Fail Cell Search FreqLockInd not received (Fixed after remapping parameters)

Please refer to this [table](#Table-for-adjusting-parameters) for details
:::warning
```c=
04/11/24 14:58:22:987	C: FORW 0x06 Failure MTE NRSETMUERADIOCONTEXTCELL: RETURN CODE:15014 FAILED NR L1: Cell Search FreqLockInd not received, time-out in waiting for Cell Frame Boundary detection
04/11/24 14:58:23:082	I: TMAE 0x1 Warning - Logging is being stopped automatically. Please wait for logging stopped message.
```
![image](https://hackmd.io/_uploads/B1ng9x8ZJg.png)




修改`ssPBCH_BlockPower`(無效)
| 參數                          | 值  |
|:----------------------------- |:--- |
| ssPBCH_BlockPower             | 10  |
| nrofDownlinkSlots             | 7   |
| nrofUplinkSlots               | 2   |
| dl_UL_TransmissionPeriodicity | 6   |


| 參數                 | 值  |
|:-------------------- |:--- |
| do_CSIRS             | 1   |
| do_SRS               | 0   |
| force_256qam_off     | 1   |
| dl_bler_target_upper | .35 |
| dl_bler_target_lower | .15 |
  
  
修改 `Timing windows` (失敗)
```c=
    Tadv_cp_dl = 125;
    T2a_cp_dl = (259, 470);
    T2a_cp_ul = (125, 1200);
    T2a_up = (70, 345);
    Ta3 = (50, 171);
    T1a_cp_dl = (258, 392);
    T1a_cp_ul = (285, 300);
    T1a_up = (155, 300);
    Ta4 = (0, 200);
```

:::