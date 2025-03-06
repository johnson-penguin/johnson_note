---
title: Metanoia JURA O-RU

---

Reference
:::success
[OAI 7.2 split(branch : fhidriver_E) & open FH(branch : e release) installation](https://hackmd.io/@Summer063/ryDBN4Qpn/https%3A%2F%2Fhackmd.io%2Fs2XCcxOjTfiZyB0vS2x1Xw%3Fview#3-System-Tools-and-Dependency)
:::




## Integration plan
|            Parameter                 |  Status              |
|:--------------------------------:    |:-------------------: |
|                                      |                      |
|     Settign between DU and RU        |                      |
|            IOP Check                 |                      |
|       Run PTP and OAI gNB	           |                      |
| Check Metanoia RU can detect OAI gNB |                      |
|                                      |                      |


## Rumanager.conf
:::spoiler
```c=


```
:::


## About Metanoia JURA O-RU
1. First install the ==Silicon Labs Virtual COM Port (VCP) driver== for windows on your PC, CP210xVCPInstaller (Provided by Metanoia)

2. Connect the PC's USB to the JURA's UART and confirm the 5 serial COM port numbers used (1 NP and 4 AP). PC software operating tools you can use such as Tera 



3. Term JURA’s 5 console: 1 NP (Network Processor), 4 AP (Antenna Processor) 
    Setup a serial port (Baud rate: ==115200==, Data: ==8 bit==, Parity: ==none==, Stop: ==1 bit==incharge)![image](https://hackmd.io/_uploads/BkuitK6ST.png)

5. JURA’s NP account/password is “root/root”

6. JURA general operation is usually in NP, which is set using Linux commands 
>    cat /etc/rumanager.conf
:::spoiler
![image](https://hackmd.io/_uploads/rJ9YTY6Bp.png)

![image](https://hackmd.io/_uploads/ryVtCF6r6.png)
:::

:::spoiler

root@localhost:~# cat /etc/rumanager.conf
{
    "ap_mac_all": ["00:0E:AD:33:44:00", "00:0E:AD:33:44:01", "00:0E:AD:33:44:02"                                                         , "00:0E:AD:33:44:03"],
    "ap_mac_ipc": ["00:0E:AD:00:00:20", "00:0E:AD:00:00:21", "00:0E:AD:00:00:22"                                                         , "00:0E:AD:00:00:23"],
    "ap_mac": ["00:0E:AD:33:44:00", "00:0E:AD:33:44:01", "00:0E:AD:33:44:02", "0                                                         0:0E:AD:33:44:03"],
    "ap_interface_all": ["fm1-mac6", "fm1-mac2", "fm1-mac10", "fm1-mac5"],
    "ap_interface": ["fm1-mac6", "fm1-mac2", "fm1-mac10", "fm1-mac5"],
    "np_interface": "fm1-mac9",
    "np_ecpri_forwarding": {"0xaaaa": "0100", "0x5555": "1000", "0x6666": "0001"                                                         , "0x2222": "0010", "0x0000": "0001", "0x0001": "0010", "0x0002": "0100", "0x000                                                         3": "1000", "0x0004": "0001", "0x0005": "0010", "0x0006": "0100", "0x0007": "100                                                         0"},
    "//": "ms_rf_ctl is a way that np control which ap is master/slave to contro                                                         l the rf card, a case that two rf card on-board then keep them like below, if we                                                          just have a card then it is decreaced like [\"master\", \"slave\"]",
    "ms_rf_ctl": ["master", "slave", "master", "slave"],
    "//": "diora interface support llcp/spi, 0 : spi, 1 : llcp, spi interface do                                                         esn't support rssi and slave calibration",
    "rf_interface": 1,
    "//": "rfic_num is a number to make np load port dpdcoef to ap, it also make                                                          ap-matser to update which mib rfic table",
    "rfic_num" : [0, 1],
    "//": "op_mode is state mode that user expecpt final state mode of ap, it su                                                         pport idle/sync/ready/oran/rate.",
    "op_mode": "oran",
    "lo_leakage_compensation_real": 0,
    "lo_leakage_compensation_image": 0,
    "adc_dc_compensation_real": 0,
    "adc_dc_compensation_image": 0,
    "//": "cali_cfg is a bitfiled settings about dpd calibration, Bit #0: TX BW.                                                          Bit #1: RX BW. Bit #2: TX LOL. Bit #3: RX DC. Bit #4: TX IQMC. Bit #5: RX IQMC.                                                          Bit #6: DPD. Bit #7: TX POWER NORMALIZATION. Bit #16: TX POWER ACTIVE.",
    "cali_cfg": ["0x3f", "0x3f", "0x3f", "0x3f"],
    "rf_cfg": 1,
    "//": "rf_pa have 3 types define value, 0: SKY66318_11, 1: SKY66320_11, 2: S                                                         KY66520_11",
    "rf_pa": 2,
    "//": "mt3812 TX gain. range in [0, 31]",
    "rf_tx_gain_manual": 0,
    "//": "mt3812 RX gain in non-hash mode. [6:4]: RF gain, [3:0]: baseband gain                                                         ",
    "rf_rx_gain_manual": 0,
    "//": "rx power auto gain control trigger threshold, -740 is equivalent to -                                                         74dBm.",
    "rx_power_agc_threshold": -740,
    "//": "rx power auto gain control update period, defalut per 4 sec",
    "rx_power_agc_update_period": 4,
    "//": "ap_pps is pulse generater switch, 0: disable, 1: pps(pulse per sec),                                                          2: ppframe(10ms - 50% duty)",
    "ap_pps": 0,
    "gps_time_delta": 0,
    "offset_alpha": 0,
    "offset_beta": 0,
    "processing_element/ru_mac_address": "00:E0:0C:00:AE:06",
    "processing_element/vlan_id": 3,
    "processing_element/du_mac_address": "00:11:22:33:44:55",
    "//": "n_ta_offset Timing Advance is a special command (notification) from e                                                         NB to UE that enable UE to adjust its uplink transmission as shown in [TS38.211                                                          Figure 5.3.1-1]",
    "//": "N_TA offset = 0 corresponding to trigger delay 9.999550ms is referenc                                                         e point. For example, N_TA offset = 25600, signal generator should have trigger                                                          delay = 9.986530ms ",
    "rx_array_carrier/n_ta_offset": 25600,
    "//": "support DYNAMIC/STATIC",
    "low_level_tx_endpoint/compression_type" : "DYNAMIC",
    "//": "9 bit for BFP, 16 bit for NO_COMP",
    "low_level_tx_endpoint/compression_iq_bitwidth": 9,
    "//": "tx compression format support BFP only",
    "low_level_tx_endpoint/compression_format": "BFP",
    "//": "The default value is 273 corresponding to 100 MHz BW with 30 kHz SCS.                                                          Currently, RU only supports 30 kHz",
    "low_level_tx_endpoint/num_prb_per_scs_30khz": 273,
    "low_level_tx_endpoint/compression/fs-offset": 0,
    "//": "support DYNAMIC/STATIC",
    "low_level_rx_endpoint/compression_type" : "DYNAMIC",
    "//": "9 bit for BFP, 16 bit for NO_COMP",
    "low_level_rx_endpoint/compression_iq_bitwidth": 9,
    "//": "rx compression format support BFP/NO_COMP",
    "low_level_rx_endpoint/compression_format": "BFP",
    "//": "The default value is 273 corresponding to 100 MHz BW with 30 kHz SCS.                                                          Currently, RU only supports 30 kHz",
    "low_level_rx_endpoint/num_prb_per_scs_30khz": 273,
    "low_level_rx_endpoint/compression/fs-offset": 0,
    "//": "This value is the RF central frequency. The default setting is 350025                                                         0000 Hz (3.50025 GHz) and support individual bandwidth each rf card",
    "center-of-channel-bandwidth": [3500250000, 3500250000],
    "//": "channel-bandwidth Carrier channel bandwidth compliance with O-RAN M-p                                                         lan definition and the unit are in Hz. This parameter would affect the baseband                                                          filter bandwidth. With smaller channel bandwidth, it's better to adjust the filt                                                         er to have better SNR. The default value would be 100,000,000 corresponds to 100                                                          MHz profile.",
    "tx-channel-bandwidth": 100000000,
    "rx-channel-bandwidth": 100000000,
    "//": "tx_carrier_gain  Default tx carrier gain 240000 is equivalent to 24 d                                                         B in decimial64: Q4. The current resolution of RF is 2 dB per step.  Please adju                                                         st the difference in 2 dB(20000). ",
    "tx_carrier_gain" : 240000,
    "//": "tx_gain_correction field is a signed integer in dB. This parameter is                                                          for debug/experiment. If different DUs have different interpretations of FS in                                                          U-plan, you can adjust this value to have a quick check.",
    "tx_gain_correction" : [0, 0, 0, 0],
    "//": "rx_gain_correction field is a signed integer in dB. This parameter is                                                          for debug/experiment. If different DUs have different interpretations of FS in                                                          U-plan, you can adjust this value to have a quick check.",
    "rx_gain_correction" : [0, 0, 0, 0],
    "//": "tx_loss is total RF path loss including all RF components like band p                                                         ass filters (BPFs), power detector, coupler, switch and so on. The 86 is equival                                                         ent to 8.6 dB decimial64: Q1.",
    "tx_loss": 86,
    "//": "Apply O-RU DL phase compensation feature: true, false",
    "DL_phase_comp_en": true,
    "//": "Apply O-RU UL phase compensation feature: true, false",
    "UL_phase_comp_en": true,
    "PA_settling_pre_kick": 20,
    "TRXSW_settling_pre_kick": 0,
    "DPD_settling_pre_kick": 0,
    "auxadc_pol_coeffs_manually": false,
    "//": "cal_auxadc_poly_coeffs_0 is slop for power detector voltage port 0/1/                                                         2/3",
    "cal_auxadc_poly_coeffs_0": [1.94261085987, 1.94261085987, 1.94261085987, 1.                                                         94261085987],
    "//": "cal_auxadc_poly_coeffs_1 is offset for power detector voltage port 0/                                                         1/2/3",
    "cal_auxadc_poly_coeffs_1": [-29.1785297394, -29.1785297394, -29.1785297394,                                                          -29.1785297394],
    "//": "Select AP console mode Bit #0: AP, Bit #1: NP, default NP/AP console                                                          mode with 0x3",
    "ap_console": "0x3",
    "//": "mtu size range support 1500 ~ 8000 bytes",
    "l2_mtu": 8000,
    "//": "lower bound delta of detect target tx power, 60000 is equivalent to 6                                                          dB",
    "tx_power_detect_floor_delta": 60000,
    "//": "allow maximum rf tx gain, 22 is equivalent to 22th step in rf card st                                                         ep of [0-31] gain range",
    "tx_rf_gain_max": [22, 22],
    "ptp_stack": "accutime",
    "ptp_debug": false,
    "skip_lock": false,
    "synce_enable": true,
    "accepted_clock_classes": [6, 7, 135, 140, 150, 160, 165, 248, 255],
    "acceptance_list_of_ssm": "QL-PRC",
    "holdover_interval": 60,
    "vdu_enable": false,
    "pcap_enable": false,
    "ap_recovery_delta": 5,
    "dummy": 0
}

:::
to view 
> vi /etc/rumanager.conf 
:::spoiler
```c=

root@localhost:~/bin# cat /etc/rumanager.conf
{
    "ap_mac_all": ["00:0E:AD:33:44:00", "00:0E:AD:33:44:01", "00:0E:AD:33:44:02", "00:0E:AD:33:44:03"],
    "ap_mac_ipc": ["00:0E:AD:00:00:20", "00:0E:AD:00:00:21", "00:0E:AD:00:00:22", "00:0E:AD:00:00:23"],
    "ap_mac": ["00:0E:AD:33:44:00", "00:0E:AD:33:44:01", "00:0E:AD:33:44:02", "00:0E:AD:33:44:03"],
    "ap_interface_all": ["fm1-mac6", "fm1-mac2", "fm1-mac10", "fm1-mac5"],
    "ap_interface": ["fm1-mac6", "fm1-mac2", "fm1-mac10", "fm1-mac5"],
    "np_interface": "fm1-mac9",
    "np_ecpri_forwarding": {"0xaaaa": "0100", "0x5555": "1000", "0x6666": "0001", "0x2222": "0010", "0x0000": "0001", "0x0001": "0010", "0x0002": "0100", "0x0003": "1000", "0x0004": "0001", "0x0005": "0010", "0x0006": "0100", "0x0007": "1000"},
    "//": "ms_rf_ctl is a way that np control which ap is master/slave to control the rf card, a case that two rf card on-board then keep them like below, if we just have a card then it is decreaced like [\"master\", \"slave\"]",
    "ms_rf_ctl": ["master", "slave", "master", "slave"],
    "//": "diora interface support llcp/spi, 0 : spi, 1 : llcp, spi interface doesn't support rssi and slave calibration",
    "rf_interface": 1,
    "//": "rfic_num is a number to make np load port dpdcoef to ap, it also make ap-matser to update which mib rfic table",
    "rfic_num" : [0, 1],
    "//": "op_mode is state mode that user expecpt final state mode of ap, it support idle/sync/ready/oran/rate.",
    "op_mode": "oran",
    "lo_leakage_compensation_real": 0,
    "lo_leakage_compensation_image": 0,
    "adc_dc_compensation_real": 0,
    "adc_dc_compensation_image": 0,
    "//": "cali_cfg is a bitfiled settings about dpd calibration, Bit #0: TX BW. Bit #1: RX BW. Bit #2: TX LOL. Bit #3: RX DC. Bit #4: TX IQMC. Bit #5: RX IQMC. Bit #6: DPD. Bit #7: TX POWER NORMALIZATION. Bit #16: TX POWER ACTIVE.",
    "cali_cfg": ["0x3f", "0x3f", "0x3f", "0x3f"],
    "rf_cfg": 1,
    "//": "rf_pa have 3 types define value, 0: SKY66318_11, 1: SKY66320_11, 2: SKY66520_11",
    "rf_pa": 2,
    "//": "mt3812 TX gain. range in [0, 31]",
    "rf_tx_gain_manual": 0,
    "//": "mt3812 RX gain in non-hash mode. [6:4]: RF gain, [3:0]: baseband gain",
    "rf_rx_gain_manual": 0,
    "//": "rx power auto gain control trigger threshold, -740 is equivalent to -74dBm.",
    "rx_power_agc_threshold": -740,
    "//": "rx power auto gain control update period, defalut per 4 sec",
    "rx_power_agc_update_period": 4,
    "//": "ap_pps is pulse generater switch, 0: disable, 1: pps(pulse per sec), 2: ppframe(10ms - 50% duty)",
    "ap_pps": 0,
    "gps_time_delta": 0,
    "offset_alpha": 0,
    "offset_beta": 0,
    "processing_element/ru_mac_address": "00:E0:0C:00:AE:06",
    "processing_element/vlan_id": 3,
    "processing_element/du_mac_address": "00:11:22:33:44:66",
    "//": "n_ta_offset Timing Advance is a special command (notification) from eNB to UE that enable UE to adjust its uplink transmission as shown in [TS38.211 Figure 5.3.1-1]",
    "//": "N_TA offset = 0 corresponding to trigger delay 9.999550ms is reference point. For example, N_TA offset = 25600, signal generator should have trigger delay = 9.986530ms ",
    "rx_array_carrier/n_ta_offset": 25600,
    "//": "support DYNAMIC/STATIC",
    "low_level_tx_endpoint/compression_type" : "DYNAMIC",
    "//": "9 bit for BFP, 16 bit for NO_COMP",
    "low_level_tx_endpoint/compression_iq_bitwidth": 9,
    "//": "tx compression format support BFP only",
    "low_level_tx_endpoint/compression_format": "BFP",
    "//": "The default value is 273 corresponding to 100 MHz BW with 30 kHz SCS. Currently, RU only supports 30 kHz",
    "low_level_tx_endpoint/num_prb_per_scs_30khz": 273,
    "low_level_tx_endpoint/compression/fs-offset": 0,
    "//": "support DYNAMIC/STATIC",
    "low_level_rx_endpoint/compression_type" : "DYNAMIC",
    "//": "9 bit for BFP, 16 bit for NO_COMP",
    "low_level_rx_endpoint/compression_iq_bitwidth": 9,
    "//": "rx compression format support BFP/NO_COMP",
    "low_level_rx_endpoint/compression_format": "BFP",
    "//": "The default value is 273 corresponding to 100 MHz BW with 30 kHz SCS. Currently, RU only supports 30 kHz",
    "low_level_rx_endpoint/num_prb_per_scs_30khz": 273,
    "low_level_rx_endpoint/compression/fs-offset": 0,
    "//": "This value is the RF central frequency. The default setting is 3500250000 Hz (3.50025 GHz) and support individual bandwidth each rf card",
    "center-of-channel-bandwidth": [3450720000, 3450720000],
    "//": "channel-bandwidth Carrier channel bandwidth compliance with O-RAN M-plan definition and the unit are in Hz. This parameter would affect the baseband filter bandwidth. With smaller channel bandwidth, it's better to adjust the filter to have better SNR. The default value would be 100,000,000 corresponds to 100 MHz profile.",
    "tx-channel-bandwidth": 100000000,
    "rx-channel-bandwidth": 100000000,
    "//": "tx_carrier_gain  Default tx carrier gain 240000 is equivalent to 24 dB in decimial64: Q4. The current resolution of RF is 2 dB per step.  Please adjust the difference in 2 dB(20000). ",
    "tx_carrier_gain" : 240000,
    "//": "tx_gain_correction field is a signed integer in dB. This parameter is for debug/experiment. If different DUs have different interpretations of FS in U-plan, you can adjust this value to have a quick check.",
    "tx_gain_correction" : [0, 0, 0, 0],
    "//": "rx_gain_correction field is a signed integer in dB. This parameter is for debug/experiment. If different DUs have different interpretations of FS in U-plan, you can adjust this value to have a quick check.",
    "rx_gain_correction" : [0, 0, 0, 0],
    "//": "tx_loss is total RF path loss including all RF components like band pass filters (BPFs), power detector, coupler, switch and so on. The 86 is equivalent to 8.6 dB decimial64: Q1.",
    "tx_loss": 86,
    "//": "Apply O-RU DL phase compensation feature: true, false",
    "DL_phase_comp_en": true,
    "//": "Apply O-RU UL phase compensation feature: true, false",
    "UL_phase_comp_en": true,
    "PA_settling_pre_kick": 20,
    "TRXSW_settling_pre_kick": 0,
    "DPD_settling_pre_kick": 0,
    "auxadc_pol_coeffs_manually": false,
    "//": "cal_auxadc_poly_coeffs_0 is slop for power detector voltage port 0/1/2/3",
    "cal_auxadc_poly_coeffs_0": [1.94261085987, 1.94261085987, 1.94261085987, 1.94261085987],
    "//": "cal_auxadc_poly_coeffs_1 is offset for power detector voltage port 0/1/2/3",
    "cal_auxadc_poly_coeffs_1": [-29.1785297394, -29.1785297394, -29.1785297394, -29.1785297394],
    "//": "Select AP console mode Bit #0: AP, Bit #1: NP, default NP/AP console mode with 0x3",
    "ap_console": "0x3",
    "//": "mtu size range support 1500 ~ 8000 bytes",
    "l2_mtu": 8000,
    "//": "lower bound delta of detect target tx power, 60000 is equivalent to 6 dB",
    "tx_power_detect_floor_delta": 60000,
    "//": "allow maximum rf tx gain, 22 is equivalent to 22th step in rf card step of [0-31] gain range",
    "tx_rf_gain_max": [22, 22],
    "ptp_stack": "accutime",
    "ptp_debug": false,
    "skip_lock": false,
    "synce_enable": true,
    "accepted_clock_classes": [6, 7, 135, 140, 150, 160, 165, 248, 255],
    "acceptance_list_of_ssm": "QL-PRC",
    "holdover_interval": 60,
    "vdu_enable": false,
    "pcap_enable": false,
    "ap_recovery_delta": 5,
}




```
:::
![image](https://hackmd.io/_uploads/S1DW1q6rT.png)

to edix and “ :wq ” to save and leave

6. After all settings of JURA are completed, you must reboot to enable it, the reboot command is "reboot"

## M-Plane Disable
Please disable all M-Plan related settings during the IOP test.
:::warning
Q:關於這方面並沒有著墨,這代表這部份的功能是預設關閉的嗎?倘若要使用的話需要怎麼開啟?
:::
## DU & RU Configure 


|            Parameter                 |  Metanoia Jura O-RU  |
|:--------------------------------:    |:-------------------: |
|                                     `一般RU設定`              |
|            `tx_carrier_gain`         |    `240000`          |
|            `gain-tx-correction`      |         `0`          |
|            `gain-rx-correction`      |         `0`          |

|            Parameter                 |  Metanoia Jura O-RU  |
|:--------------------------------:    |:-------------------: |
|          `FlexRAN IOP Special Settings on RU Side設定`      |
|            `fs-offset`               |    `8`               |
|            `gain-tx-correction`      |         `7224`       |
|            `gain-rx-correction`      |         `301`        |

---
## RU Configure 
| parameter          | BMW LAB        | OAI LAB       | LiteOn        | Metanoia(Manufacturer test) |
| ------------------ | -------------- | ------------- | ------------- | ------------- |
| product_name       | `FlexFi-RU`    | `FlexFi-RU`   | `FlexFi-RU`   |
| model id           | `FF-RFI078I4`  | `FF-RFI078I4` | `FF-RFI078I4` |
| firmware version   | `01.00.08`     | `01.00.08`    | `01.01.02`    |
| kernal             | `Linux FF-RFI078I4 5.4.0` | `Linux FF-RFI078I4 5.4.0` | `Linux FF-RFI078I4 5.4.0` |
| cmdline            |`earlycon console=ttyPS,115200 clk_ignore_unused root=/dev/mmcblk0p4 rw rootwait` | `earlycon console=ttyPS,115200 clk_ignore_unused root=/dev/mmcblk0p4 rw rootwait` | `earlycon console=ttyPS,115200 clk_ignore_unused root=/dev/mmcblk0p4 rw rootwait` |
| bandwidth          | `100000000`    | not provided  | `100000000`    | `100000000`   |
| c/u-plane-vlan     | `3`            |               | `564`          | `3`           |
| center-frequency   | `3450720000`   |               | `3450720000`   | `3750000000(配合DU 3450720000)`  |
| compression-bit    | `8`            |               | `8`            | `9`           |
| du-mac-address     | `001122334466` |               | `001122334466` | `001122334455(配合DU 001122334466)`|
| gain-rx            | `17`           |               | `17`           | `0`           |
| gain-tx            | `14`           |               | `14`           | `0`           |
| phasecomp-mode     | `Enable`       |               | `Enable`       |
| subcarrier-spacing | `1` (30kHz)    |               | `1` (30kHz)    |`1` (30kHz)    |
| sync-source        | `PTP`          |               | `PTP`          |`PTP`          |



---

:::success

**MTU** : 
Stands for Maximum Transmission Unit, which refers to the maximum size of data packets that can pass through the network in network communications. The size of the MTU is usually measured in bytes.
:::

:::success
**Compression_iq_bitwidth** : 
Is a parameter or setting that may be used to describe compression in signal processing or the bitwidth of a digital signal.

IQ (In-phase and Quadrature) signal usually refers to a complex signal composed of real part (In-phase) and imaginary part (Quadrature).

This parameter may represent the number of bits for each IQ component used in the process. For example, if compression_iq_bitwidth is 16, each real and imaginary part may be represented using 16 bits.part may be represented using 16 bits.

:::

### MAC Address & VLAN ID (1)
1. Enter to JURA’s NP ==/etc/rumanager.conf==
```c=
cd /etc/rumanger.conf
```
2. To find the 3 items and setting
![image](https://hackmd.io/_uploads/rkiO1p97p.png)

### Central Frequency and Bandwidth (2)
1. Enter to JURA’s NP ==/etc/rumanager.conf==
```c=
cd /etc
vim rumanger.conf
```
2. To find the frequency related configuration items and settings
![image](https://hackmd.io/_uploads/ry2JlTq7p.png)

### Compression Method(3)
1. Enter to JURA’s NP ==/etc/rumanager.conf==
```c=
cd /etc
vim rumanger.conf
```

2. To find the compression related settings
![image](https://hackmd.io/_uploads/SkNYzpc76.png)
![image](https://hackmd.io/_uploads/HkbqGp976.png)

### eAxCid（As message source and destination identifiers）
1. Enter to JURA’s NP ==/etc/rumanager.conf==
```c=
cd /etc/rumanger.conf
```
2. To find the item ==“np_ecpri_forwarding”==

**Ex1.** RU 4T2R in conducted mode: 
set DU 2 UL’s [0x0004]: [0001], [0x0005]: [0010], 
so UE’s 2 Tx path will connect to the corresponding antenna “0001”, “0010”.

![image](https://hackmd.io/_uploads/SJCtsa5Qa.png)

---

**Ex2.** RU 4T4R in conducted mode: 
set DU 4 UL’s [0x0004]: [0001], [0x0005]: [0010], [0x0006]:[0100], [0x0007]: [1000], 
so UE’s 4 Tx path will connect to the corresponding antenna “0001”,“0010”, “0100”, “1000”.

![image](https://hackmd.io/_uploads/S1YWaacmT.png)

---
:::info

Note1. DU controls all ==[eAxCid]: [antenna position]==. It is recommended to let DU correspond to JURA's default value, if DU can be set freely.

Note2. When UE uses conducted mode, the DU must first specify UL's

[eAxCid]: [antenna position]

which are the eAxCid for PRACH (RU’s Rx path) and (UE’s Tx path). If UE is OTA mode, it is not necessary.

Note3. In area2 or area3, eAxCid in the same area cannot be repeated.
:::

### Tx/Rx Gain on RU Side 

It is strongly recommended that if the UE in conductive mode, an attenuation value of 30dB must be placed between the RU and the UE initially. To avoid the default TxGain value 24dBm for RU too high for the UE.

:::warning
Q:How can I get TxGain value from test environment ?
:::

1. Enter to JURA’s NP /etc/rumanager.conf
```c=
cd /etc
vim rumanger.conf
```
2. Ti find 3 gain related settings
![image](https://hackmd.io/_uploads/Hy7GtMjQ6.png)


### C/U-Plane Timing on DU Side
T1a and T2a settings on ==DU side== For the value range, please refer to Metanoia specification.

Or you can just use the following DU settings used in Metanoia E2E as a reference

:::warning
Q1:
Why need two speciflaction in this table ?


Q2:
When the connection is possible, what is the difference between time windows in different groups?
:::
![image](https://hackmd.io/_uploads/H17njGima.png)


## Interoperability Check ( IOP Check )
:::spoiler
[Pcap 封包擷取格式詳解](https://blog.csdn.net/lu_embedded/article/details/124952413?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169984935216800197092782%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169984935216800197092782&biz_id=0&spm=1018.2226.3001.4187)

---
[Precision Time Protocol](https://en.wikipedia.org/wiki/Precision_Time_Protocol)
[PTP 時間同步原理](https://blog.csdn.net/kunkliu/article/details/127244116?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169984989916800213080988%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169984989916800213080988&biz_id=0&spm=1018.2226.3001.4187)

:::
![RU-IOP Check.drawio](https://hackmd.io/_uploads/HkfAX7k4p.png)
### PCAP Capture

First, please request a PCAP file capture from DU so that RD can check the entire link environment in advance.

Contains the following:
1. Broadcast: PBCH (type1)
2. Random access: PRACH (type3)
3. DL for data: PDSCH (type1)
4. UL for data: PUSCH (type1)

For JURA O-RU NP packet capturing PCAP feature. The eCPRI traffic between DU and AP can be captured to file for ==troubleshooting and analysis==.
:::success
**Step1.**
```c=
cd /etc
vim rumanger.conf
```

**Step2.**
To find pcap_enable, and change it ==from “false” to “ture”== first

**Step3.**
Start to capture packets with this command
```c=
vim /bin/pcap_start.sh
```

**Step4.**
Stop to capture packets with this command
```c=
vim /bin/pcap_stop.sh
```

**Step5.**
Captured packets are stored in these files
```c=
cd /root/pcap
```
Named ==ap(index)_date_time.pcap==. They can be loaded into wireshark for analysis.

![image](https://hackmd.io/_uploads/S1i0tefNT.png)

:::
### PTP Synchronize
When the PTP is synchronized, it can be seen from the JURA’s 4 AP console screen, and the C/U-Plane status will start to be updated periodically. Or you can use command to check status.
```c=
tail -f /var/log/rumanager.log
```
It will completely display the PTP process. If the PTP is successful, you will see several checkpoints.

- 'synchronized': 'YES'
- 4 AP 'SYNC success'
- 'M-Plan SYNC', 'S-Plan Ready', 'ORAN success' 
- And the last 'RU State: oranactive'

![image](https://hackmd.io/_uploads/ByffAxGNT.png)

![image](https://hackmd.io/_uploads/rkgO1ZMNp.png)

![image](https://hackmd.io/_uploads/B1SdyZfE6.png)

### C/U-Plane
Check JURA’s 4 AP console C-Plan and U-Plan of Data Direction DL/UL should all be ==on time==.

If the timing configuration between DU and RU is OK, ==you should not see the Late or Early counters continuously increasing== in the 4 AP console output.

:::warning

:::
![image](https://hackmd.io/_uploads/B1cKe-MVp.png)

### DL Quality on UE Side 

:::warning

Check DL Quality on UE Side from UE app(...)?
:::


### UL Quality on DU Side
:::warning
How should I check UL on DU side ?(waiting check)

:::

## Access method
:::success
**OAI gNB/open5GS**
- IP : 192.168.8.29
- user name : oai
- ssh/root password : oai
- Anydesk ID 419 592 001
- Anydesk password : bmwbmwbmwee809
- Root Password:bmwlab

**Metanoia RU**
- user name : root
- root password : root

**free5GC**
- IP : 192.168.8.21
- user name : Ubuntu
:::



## 6. Configuration
###  Configure O-RAN Fronthaul Interface C/U Plane
#### Set  DU/RU MAC and C/U plane vlan in config_o_du_3450_E.dat.
- original file : [config_o_du_3450_E.dat](https://hackmd.io/@Summer063/ryAKgzcWp#config_o_du_3450_Edat)
- Path : `/home/oai72/fhidriver_E/openairinterface5g/metanoia_conf_file/config_o_du_3450_E.dat`
```bash=
vim /home/oai72/fhidriver_E/openairinterface5g/meanoia_conf_file/config_o_du_3450_E.dat
```

>注意:預設是==conf_file==，但由於我們有多個RU，為避免設定上的混亂，因此另外開一個設定檔做使用==metanoia_conf_file==
```bash=54
# Eth 0
duMac0=00:11:22:33:44:66 # asigned MAC of O-DU VF 配合現有的OAI Envirment
ruMac0=00:00:E0:0C:AE:06 # O-RU VF for O-RU app   Metanoia RU_address
duMac0=00:11:22:33:44:66 # asigned MAC of O-DU VF 配合現有的OAI Envirment
ruMac0=00:00:E0:0C:AE:06 # O-RU VF for O-RU app   Metanoia RU_address

# Eth 1
# duMac2=00:11:22:33:44:77 # asigned MAC of O-DU VF
# duMac3=00:11:22:33:44:77 # asigned MAC of O-DU VF
```
```bash=64
c_plane_vlan_tag=3 #metanoia
u_plane_vlan_tag=3 #metanoia
```

#### Set RU MAC, VF bus, and config file info in usecase_du_3450.cfg.
- original file : [usecase_du_3450.cfg](https://hackmd.io/@Summer063/ryAKgzcWp#usecase_du_3450cfg)
- Path : `/home/oai72/fhidriver_E/openairinterface5g/metanoia_conf_file/usecase_du_3450.cfg`
```bash=
vim /home/oai72/fhidriver_E/openairinterface5g/metanoia_conf_file/usecase_du_3450.cfg
```
```bash=30
oXuCfgFile0=metanoia_config_o_du_3450_E.dat  #O-RU0

#O-XU 0
#PciBusAddoXu0Vf0=0000:3b:0a.0
#PciBusAddoXu0Vf1=0000:3b:0a.1
```

```bash=50
# remote O-XU 0 Eth Link 0
oXuRem0Mac0=00:aa:ff:bb:ff:cc
oXuRem0Mac1=00:aa:ff:bb:ff:cc
```
###  C/U-Plane Timing on DU Side (T1 & T4)
![image](https://hackmd.io/_uploads/BJ2BtrxIp.png)

```c=
##O-DU Settings
#C-plane
#Transmission Window Fast C-plane DL
T1a_min_cp_dl=385
T1a_max_cp_dl=435

##Transmission Window Fast C-plane UL
T1a_min_cp_ul=385   
T1a_max_cp_ul=435

#U-plane
##Transmission Window
T1a_min_up=300  #71 + 25 us
T1a_max_up=340  #71 + 25 us

#Reception Window
Ta4_min=0  # in us 
Ta4_max=70 # in us
```
###  Sitting compression-bit (default 8)
```c=
iqWidth=9 # 8-bit I/Q
```
## BBU reboot quick setting
:::success

- **Setup maximum performance, vf, and environment**
```c=
source /home/oai72/Script/setupvf.sh
```

```c=
# Run OAI gNB
cd /home/oai72/fhidriver_E/openairinterface5g/cmake_targets/ran_build/build

sudo ./nr-softmodem -O ../../../metanoia_conf_file/mwc_20899_newfhi_E_3450.conf --sa --reorder-thread-disable 1 --thread-pool 1,3,5,7,9,11,13,15 > /home/oai72/OAI_gNB_LOG/1005_gNB_1.log 2>&1
```
:::
## test_12/15

:::warning 
**If the following content appears in your logs, causing the base station to shut down**
>Received Time doesn't correspond to the time we think it is .......

please use it to 
```c=
sudo tuned-adm profile realtime
```
:::
### PCAP Capture

```c=
vi /etc/rumanager.conf
```
change =="pcap_enable": false== to =="pcap_enable": true==

---
```c=
reboot
```
Enable via reboot

---
```c=
cd bin
```
```c=
./pcap_start.sh
```
:::danger 
:::spoiler 錯誤訊息(DU_未啟動的情況下)
```c=
  File "<string>", line 1, in <module>
  File "/usr/lib/python3.8/json/__init__.py", line 293, in load
    return loads(fp.read(),
  File "/usr/lib/python3.8/json/__init__.py", line 357, in loads
    return _default_decoder.decode(s)
  File "/usr/lib/python3.8/json/decoder.py", line 337, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
  File "/usr/lib/python3.8/json/decoder.py", line 353, in raw_decode
    obj, end = self.scan_once(s, idx)
json.decoder.JSONDecodeError: Expecting property name enclosed in double quotes: line 106 column 1 (char 7329)
./pcap_start.sh: line 9: ((: i<: syntax error: operand expected (error token is "<")
```
:::