---
title: Compare Configuration(TY vs OAI in Jura_RU)

---

## Metanoia RU_configure
### Compare
| Parameter                                     | Metanoia (for TY)                                                                                                                                                          | Metanoia (for OAI)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|:--------------------------------------------- |:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| du_mac_address                                | 00:11:22:33:44:55 <br> (or follow DU's MAC to make changes)                                                                                                                | "00:11:22:33:44:66"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ru_mac_address                                | 00:E0:0C:00:AE:06                                                                                                                                                          | "00:E0:0C:00:AE:06"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| np_ecpri_forwarding                           | "0x0000": "0001" <br> "0x0001": "0010" <br> "0x0002": "0100" <br> "0x0003": "1000" <br> "0x0004": "0001" <br> "0x0005": "0010" <br> "0x0006": "0100" <br> "0x0007": "1000" | "ul": { <br>"0xaaaa": "0100", <br>"0x5555": "1000",<br>"0x6666": "0001",<br>"0x2222": "0010",<br>"0x0000": "0001",<br>"0x0001": "0010",<br>"0x0002": "0100",<br>"0x0003": "1000",<br> "0x0004": "0001",<br> "0x0005": "0010",<br>"0x0006": "0100",<br>"0x0007": "1000"<br>},<br> "dl": {<br>            "0xaaaa": "0100",<br>"0x5555": "1000",<br>"0x6666": "0001",<br> "0x2222": "0010",<br>"0x0000": "0001",<br>"0x0001": "0010",<br>"0x0002": "0100",<br>"0x0003": "1000",<br>"0x0004": "0001",<br>"0x0005": "0010",<br>"0x0006": "0100",<br>"0x0007": "1000"<br>}<br> |
| low_level_tx_endpoint/compression_type        | DYNAMIC (also support "STATIC" mode)                                                                                                                                       | "STATIC"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| low_level_rx_endpoint/compression_type        | DYNAMIC (also support "STATIC" mode)                                                                                                                                       | "STATIC"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| low_level_tx_endpoint/compression_iq_bitwidth | 9 (support 9 bit only)                                                                                                                                                     | 9                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| low_level_rx_endpoint/compression_iq_bitwidth | 9 (support 9 bit only)                                                                                                                                                     | 9                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| low_level_tx_endpoint/num_prb_per_scs_30khz   | 273                                                                                                                                                                        | 273                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| low_level_rx_endpoint/num_prb_per_scs_30khz   | 273                                                                                                                                                                        | 273                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| vlan_id                                       | 3                                                                                                                                                                          | 3(vlanid = 1 will error)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| tx_carrier_gain                               | 240000 (e-4db)                                                                                                                                                             | 240000 (e-4db)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| center-of-channel-bandwidth                   | 3750000000                                                                                                                                                                 | 3450720000,3450720000                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                                                                                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| low_level_tx_endpoint/compression_format      | BFP (support BFP only)                                                                                                                                                     |  BFP                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| low_level_rx_endpoint/compression_format      | BFP (support BFP only)                                                                                                                                                     |  BFP                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| low_level_rx_endpoint/compression/fs-offset   | 0                                                                                                                                                                          |  0                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| tx_gain_correction                            | 0                                                                                                                                                                          | 7117,7117,7117,7117                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| rx_gain_correction                            | 0                                                                                                                                                                          | 0                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |

### Difference
#### Timing windows

- O-RU Setting:

| Parameters    | value(TY) | value(OAI) | Description                                        |
| ------------- | --------- | ---------- |:-------------------------------------------------- |
| Tadv_cp_dl    | 125       | 125        | Delay needed for O-RU to translate control message |
| T2a_min_cp_dl | 285       | 255        | Reception Window C-plane DL (min)                  |
| T2a_max_cp_dl | 429       | 490        | Reception Window C-plane DL (max)                  |
| T2a_min_cp_ul | 285       | 90         | Reception Window C-plane UL (min)                  |
| T2a_max_cp_ul | 429       | 429        | Reception Window C-plane UL (max)                  |
| T2a_min_up    | 71        | 140        | Reception Window U-plane (min)                     |
| T2a_max_up    | 428       | 350        | vReception Window U-plane (max)                    |
| Ta3_min       | 20        | 50         | Transmission Window U-plane (min)                  |
| Ta3_max       | 32        | 170        | Transmission Window U-plane (max)                  |

- O-DU Setting:
 
| Parameters    | value(TY) | value(OAI) | Description                          |
| ------------- | --------- |:---------- |:------------------------------------ |
| MTU           | 9600      | 1500       | Maximum Transmission Unit (MTU) size |
| T1a_min_cp_dl | 258       | 385        | Transmission Window Fast C-plane DL  |
| T1a_max_cp_dl | 392       | 500        | Transmission Window Fast C-plane DL  |
| T1a_min_cp_ul | 155       | 300        | Transmission Window Fast C-plane DL  |
| T1a_max_cp_ul | 300       | 435        | Transmission Window Fast C-plane UL  |
| T1a_min_up    | 210       | 300        | Transmission Window U-plane          |
| T1a_max_up    | 230       | 355        | Transmission Window U-plane          |
| Ta4_min       | 0         | 60         | Reception Window U-Plane             |
| Ta4_max       | 70        | 200        | Reception Window U-Plane             |

- MTU
9600MTU will cause C/U plane connection failure
![image](https://hackmd.io/_uploads/r1PZVCamR.png)
- tx_gain_correction
tx_gain_correction=[0,0,0,0]will cause AP to fail to start fail. Using [7117,7117,7117,7117] instead
![image](https://hackmd.io/_uploads/r191KRa70.png)

Correction value to -8.7 => (-8.7)-(-34.7) x 100 = 2600. Using [9717,9717,9717,9717] instead
- Using TY+JURA_timing_widow result (Only SIB/MIB)

![image](https://hackmd.io/_uploads/BkVm2CTm0.png)

![image](https://hackmd.io/_uploads/B1oaWATQR.png)


- Using OAI+JURA_timing_widow result (RRC_SetupRequest)
![image](https://hackmd.io/_uploads/rJkba0aQR.png)



#### np_ecpri_forwarding
[Reference](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2F%40Summer063%2FH1l9KtAcp#Step-32-Configure-O-RAN-Fronthaul-Interface-CU-Plane) 
:::success
prach_config: PRACH-specific configuration eAxC_offset: PRACH antenna offset
- Layer mapping (eAxC offsets) happens as follows:
    - For PUSCH/PDSCH, the layers are mapped to [0,1,...,N-1] where N is the respective RX/TX number of antennas.
    - For PRACH, the layers are mapped to [No,No+1,...No+N-1] where No is the fhi_72.fh_config.[0].prach_config.eAxC_offset and N the number of receive antennas.
    
![image](https://hackmd.io/_uploads/B1ZHF5pXC.png)

Right now OAI have:
```bash=
nb_tx          = 4
nb_rx          = 4
```
[0,1,…,N-1] where N is the respective RX/TX number of antennas. (N=4 => [0,1,2,3])
According to `eAxC_offset = 4`, so [4,5,6,7] is for `PRACH` & [0,1,2,3] is for `PUSCH/PDSCH`

- `PUSCH/PDSCH`
![image](https://hackmd.io/_uploads/HJnf9cpXA.png)
- `PRACH`
![image](https://hackmd.io/_uploads/Sy51cqTmR.png)

:::

---
#### tx_gain_correction
Fellow Metanoia document to adjust digital Tx gain to make Tx power more ==reliable== for purpose. Follow the steps below or provide the PCAP file to your Metanoia window.
:::success
##### Step1. To find Tx gain related settings(In JURA’s NP /etc/rumanager.conf).

- a. Reference default TargetTxGain to ==24dBm==,"tx_carrier_gain": ==240000==, (e-4dB in unit)
![image](https://hackmd.io/_uploads/B19NzcdmC.png)

- b. Reference default analogy to ==0dBm== default"tx_gain_correction": 0, (e-2dB in unit)
![image](https://hackmd.io/_uploads/Hy1NM5OQA.png)

- c. Check the output "PwrAvg(dBFs)" of the 4 AP consoles on the downlink side. Based on the  target to make the value close to -8.7dBFs as an adjustment, set the deployment "tx_gain_Correction". Ex: If show -13.0dB, then the lack of 4.3dB will be made up by the digital gain.

##### Step3. Edit "tx_gain_correction" to correct the power scale.
Ex: made up 4.3dB "tx_gain_correction": 430, (e-2dB in unit), and reboot to save.
![image](https://hackmd.io/_uploads/SJVeG5uQC.png)

##### Step4. Check again whether the 4 AP console outputs can be close to "PwrAvg -8.7dBFs", and then 
system get a close value of "PwrAvg 24dBm"
![image](https://hackmd.io/_uploads/Sk3T-5OQ0.png)
:::

## Configuration 
### JURA_RU
:::spoiler Metanoia_jura+TY(configuration)
```c=
root@localhost:/etc# cat rumanager.conf
{
    "ap_mac_all": [
        "00:0E:AD:33:44:00",
        "00:0E:AD:33:44:01",
        "00:0E:AD:33:44:02",
        "00:0E:AD:33:44:03"
    ],
    "ap_mac_loopback": [
        "00:0E:AD:33:44:10",
        "00:0E:AD:33:44:11",
        "00:0E:AD:33:44:12",
        "00:0E:AD:33:44:13"
    ],
    "ap_mac_ipc": [
        "00:0E:AD:00:00:20",
        "00:0E:AD:00:00:21",
        "00:0E:AD:00:00:22",
        "00:0E:AD:00:00:23"
    ],
    "ap_mac": [
        "00:0E:AD:33:44:00",
        "00:0E:AD:33:44:01",
        "00:0E:AD:33:44:02",
        "00:0E:AD:33:44:03"
    ],
    "ap_interface_all": [
        "fm1-mac6",
        "fm1-mac2",
        "fm1-mac10",
        "fm1-mac5"
    ],
    "ap_interface": [
        "fm1-mac6",
        "fm1-mac2",
        "fm1-mac10",
        "fm1-mac5"
    ],
    "np_interface": "fm1-mac9",
    "np_ecpri_forwarding": {
        "ul": {
            "0xaaaa": "0100",
            "0x5555": "1000",
            "0x6666": "0001",
            "0x2222": "0010",
            "0x0000": "0001",
            "0x0001": "0010",
            "0x0002": "0100",
            "0x0003": "1000",
            "0x0004": "0001",
            "0x0005": "0010",
            "0x0006": "0100",
            "0x0007": "1000"
        },
        "dl": {
            "0xaaaa": "0100",
            "0x5555": "1000",
            "0x6666": "0001",
            "0x2222": "0010",
            "0x0000": "0001",
            "0x0001": "0010",
            "0x0002": "0100",
            "0x0003": "1000",
            "0x0004": "0001",
            "0x0005": "0010",
            "0x0006": "0100",
            "0x0007": "1000"
        }
    },
    "ms_rf_ctl": [
        "master",
        "slave",
        "master",
        "slave"
    ],
    "rf_interface": 1,
    "rfic_num": [
        0,
        1
    ],
    "op_mode": "oran",
    "lo_leakage_compensation_real": 0,
    "lo_leakage_compensation_image": 0,
    "adc_dc_compensation_real": 0,
    "adc_dc_compensation_image": 0,
    "cali_cfg": "0x300ff",
    "rf_cfg": 1,
    "rf_pa": 2,
    "rf_tx_gain_manual": 0,
    "rf_rx_gain_manual": 66,
    "rx_power_agc_threshold": -740,
    "rx_power_agc_update_period": 4,
    "ap_pps": 0,
    "gps_time_delta": 0,
    "offset_alpha": 0,
    "offset_beta": 0,
    "processing_element/pcp": 7,
    "processing_element/vlan_id": 3,
    "processing_element/du_mac_address": "00:11:22:33:44:66",
    "rx_array_carrier/n_ta_offset": 25600,
    "low_level_tx_endpoint/compression_type": "STATIC",
    "low_level_tx_endpoint/compression_iq_bitwidth": 9,
    "low_level_tx_endpoint/compression_format": "BFP",
    "low_level_tx_endpoint/num_prb_per_scs_30khz": 273,
    "low_level_tx_endpoint/compression/fs-offset": 0,
    "low_level_rx_endpoint/compression_type": "STATIC",
    "low_level_rx_endpoint/compression_iq_bitwidth": 9,
    "low_level_rx_endpoint/compression_format": "BFP",
    "low_level_rx_endpoint/num_prb_per_scs_30khz": 273,
    "low_level_rx_endpoint/compression/fs-offset": 0,
    "center-of-channel-bandwidth": [
        3450720000,
        3450720000
    ],
    "tx_carrier_gain": 240000,
    "tx_gain_correction": [
        7117,
        7117,
        7117,
        7117
    ],
    "rx_gain_correction": [
        0,
        0,
        0,
        0
    ],
    "tx_loss": 86,
    "DL_phase_comp_en": true,
    "UL_phase_comp_en": true,
    "PA_settling_pre_kick": 20,
    "TRXSW_settling_pre_kick": 0,
    "DPD_settling_pre_kick": 0,
    "auxadc_pol_coeffs_manually": false,
    "cal_auxadc_poly_coeffs_0": [
        1.94261085987,
        1.94261085987,
        1.94261085987,
        1.94261085987
    ],
    "cal_auxadc_poly_coeffs_1": [
        -29.1785297394,
        -29.1785297394,
        -29.1785297394,
        -29.1785297394
    ],
    "ap_console": "0x3",
    "l2_mtu": 1500,
    "tx_power_detect_floor_delta": 60000,
    "tx_rf_gain_max": [
        22,
        22
    ],
    "llcp_switch_fail_upper_limit": 2000,
    "llcp_switch_delay_ticks": 1,
    "ptp_stack": "accutime",
    "ptp_debug": false,
    "skip_lock": false,
    "synce_enable": false,
    "accepted_clock_classes": [
        6,
        7,
        135,
        140,
        150,
        160,
        165,
        248,
        255
    ],
    "acceptance_list_of_ssm": "QL-PRC",
    "holdover_interval": 60,
    "vdu_enable": false,
    "pcap_enable": true,
    "ap_recovery_delta": 0,
    "version": "2.0.1"
}
```

### TY_gNB(FlexRAN)
Reference : [FlexRAN Configuration provied by Heide](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2F%40H141319%2FrJ3FOE0da#B-phycfg_xranxml)
:::