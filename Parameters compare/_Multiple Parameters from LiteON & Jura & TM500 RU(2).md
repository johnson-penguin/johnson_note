---
title: ' Multiple Parameters from LiteON & Jura & TM500 RU(2)'

---

:::info
[ Multiple Parameters from TM500, TMA  & gNB(1)](https://hackmd.io/@Johnson-72/r1Nb-dkekg)
[ Multiple Parameters from LiteON & Jura & TM500 RU(2)](https://hackmd.io/@Johnson-72/Hk47ECoe1g)
:::


## LiteON

| Parameter          | Value                                                                           |
|:------------------ | ------------------------------------------------------------------------------- |
| product_name       | FlexFi-RU                                                                       |
| model id           | FF-RFI078I4                                                                     |
| firmware version   | 01.00.08                                                                        |
| kernal             | Linux FF-RFI078I4 5.4.0                                                         |
| cmdline            | earlycon console=ttyPS,115200 clk_ignore_unused root=/dev/mmcblk0p4 rw rootwait |
| bandwidth          | 100000000                                                                       |
| c/u-plane-vlan     | 3                                                                               |
| center-frequency   | 3450720000                                                                      |
| compression-bit    | 8                                                                               |
| du-mac-address     | 001122334466                                                                    |
| gain-rx            | 17                                                                              |
| gain-tx            | 14                                                                              |
| phasecomp-mode     | Disable                                                                         |
| subcarrier-spacing | 1 (30kHz)                                                                       |
| sync-source        | PTP                                                                             |

## Metanoia

| Parameter                                     | Value                                                                                                                                          |
|:--------------------------------------------- |:---------------------------------------------------------------------------------------------------------------------------------------------- |
| du_mac_address                                | 00:11:22:33:44:55     (or follow DU's MAC to make changes)                                                                                     |
| ru_mac_address                                | 00:E0:0C:00:AE:06                                                                                                                              |
| np_ecpri_forwarding                           | "0x0000": "0001", "0x0001": "0010", "0x0002": "0100", "0x0003": "1000", "0x0004": "0001", "0x0005": "0010", "0x0006": "0100", "0x0007": "1000" |
| low_level_tx_endpoint/compression_type        | DYNAMIC (also support "STATIC" mode)                                                                                                           |
| low_level_rx_endpoint/compression_type        | DYNAMIC (also support "STATIC" mode)                                                                                                           |
| low_level_tx_endpoint/compression_iq_bitwidth | 9 (support 9 bit only)                                                                                                                         |
| low_level_rx_endpoint/compression_iq_bitwidth | 9 (support 9 bit only)                                                                                                                         |
| low_level_tx_endpoint/num_prb_per_scs_30khz   | 273                                                                                                                                            |
| low_level_rx_endpoint/num_prb_per_scs_30khz   | 273                                                                                                                                            |
| vlan_id                                       | 3                                                                                                                                              |
| tx_carrier_gain                               | 240000 (e-4db)                                                                                                                                 |
| center-of-channel-bandwidth                   | 3750000000                                                                                                                                     |
| tx-channel-bandwidth                          | 100000000                                                                                                                                      |
| rx-channel-bandwidth                          | 100000000                                                                                                                                      |
| low_level_tx_endpoint/compression_format      | BFP (support BFP only)                                                                                                                         |
| low_level_rx_endpoint/compression_format      | BFP (support BFP only)                                                                                                                         |
| low_level_rx_endpoint/compression/fs-offset   | 0                                                                                                                                              |
| tx_gain_correction                            | 0                                                                                                                                              |
| rx_gain_correction                            | 0                                                                                                                                              |



## Compare


| Parameter                    | LiteON                         | Metanoia                                                                                                                                       | TM500 UE/RU SIM                                      | OAI                                                   |
|:---------------------------- |:------------------------------ |:---------------------------------------------------------------------------------------------------------------------------------------------- |:---------------------------------------------------- |:----------------------------------------------------- |
| DU MAC address               | du-mac-address: 1122334466     | du_mac_address: 00:11:22:33:44:66                                                                                                              | 00:11:22:33:44:66                                    | du_addr = ("00:11:22:33:44:66", "00:11:22:33:44:66"); |
| RU MAC address               | –                              | ru_mac_address: 00:E0:0C:00:AE:06                                                                                                              | ru_addr = ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10") | -                                                     |
| eAxC ID                      | –                              | np_ecpri_forwarding:                                                                                                                           |                                                      |                                                       |
|                              |                                | "0x0000": "0001", "0x0001": "0010", "0x0002": "0100", "0x0003": "1000", "0x0004": "0001", "0x0005": "0010", "0x0006": "0100", "0x0007": "1000" |                                                      |                                                       |
| Compress Type                | –                              | low_level_tx_endpoint/compression_type: DYNAMIC                                                                                                | format-of-iq-sample/dynamic-compression-supported    |                                                       |
|                              |                                | low_level_rx_endpoint/compression_type: DYNAMIC                                                                                                | format-of-iq-sample/dynamic-compression-supported    |                                                       |
| Bit length after compression | compression-bit: 8/9 (now : 9) | low_level_tx_endpoint/compression_iq_bitwidth: 9                                                                                               | iq_bitwidth: 9                                       | iq_width = 9                                          |
|                              |                                | low_level_rx_endpoint/compression_iq_bitwidth: 9                                                                                               | iq_bitwidth: 9                                       | iq_width = 9                                          |
| Maximum PRBs                 | –                              | low_level_tx_endpoint/num_prb_per_scs_30khz: 273                                                                                               | scs_30kh /  bandwith_100M : 273                      | scs_30kh /  bandwith_100M : 273                       |
|                              |                                | low_level_rx_endpoint/num_prb_per_scs_30khz: 273                                                                                               | scs_30kh /  bandwith_100M : 273                      | scs_30kh /  bandwith_100M : 273                       |
| VLAN id                      | c/u-plane-vlan: 3              | vlan_id: 3                                                                                                                                     | vlan_id: 3                                           | vlan_id: 3                                            |
| Tx-gain                      | gain-tx: 14                    | tx_carrier_gain: 240000 (e-4db)                                                                                                                | -                                                    |                                                       |
| Rx-gain                      | gain-rx: 17                    | –                                                                                                                                              | -                                                    |                                                       |
| Center Frequency             | center-frequency: 3450720000   | center-of-channel-bandwidth: 3750000000                                                                                                        | 3450720000                                           | 3450720000                                            |
| Bandwidth                    | bandwidth: 100000000           | tx-channel-bandwidth: 100000000                                                                                                                | tx-channel-bandwidth: 100000000                      | tx-channel-bandwidth: 100000000                       |
|                              |                                | rx-channel-bandwidth: 100000000                                                                                                                | rx-channel-bandwidth: 100000000                      | rx-channel-bandwidth: 100000000                       |
| phase_compensation           | Disable                        | "DL_phase_comp_en": false,                                                                                                                     | N/A   (Assume : Enable)                              | phase_compensation = 0 (Disable)                      |
|                              |                                | "UL_phase_comp_en": false,                                                                                                                     | N/A   (Assume : Enable)                              |                                                       |