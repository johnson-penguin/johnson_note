---
title: ' Multiple Parameters from TM500, TMA  & gNB(1)'

---

:::info
[ Multiple Parameters from TM500, TMA  & gNB(1)](https://hackmd.io/@Johnson-72/r1Nb-dkekg)
[ Multiple Parameters from LiteON & Jura & TM500 RU(2)](https://hackmd.io/@Johnson-72/Hk47ECoe1g)
:::

## TM500
### Hardware
#### `component_O-RAN-RADIO`

| 屬性                                      | 值                         | path                                                                                          |
| ----------------------------------------- |:-------------------------- |:--------------------------------------------------------------------------------------------- |
| **類別**                                  | o-ran-hardware:O-RAN-RADIO | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO']`                             |
| **名稱**                                  | component_O-RAN-RADIO      | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO']/class`                       |
| **管理狀態 (admin-state)**                | unlocked                   | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO']/state/admin-state`           |
| **產品代碼 (product-code)**               | ABCD-123456                | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO']/o-ran-hardware:product-code` |
                                                                                            |

#### `component_O-RAN-RADIO_0`

| 屬性                                      | 值                               | path                                                                                                           |
|:----------------------------------------- |:-------------------------------- |:-------------------------------------------------------------------------------------------------------------- |
| **類別**                                  | o-ran-hardware:O-RAN-RADIO       | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']`                                            |
| **名稱**                                  | component_O-RAN-RADIO_0          | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/class`                                      |
| **管理狀態 (admin-state)**                | unlocked                         | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/state/admin-state`                          |
| **產品代碼 (product-code)**               | ABCD-123456                      | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/o-ran-hardware:product-code`                |
| **硬體版本 (hardware-rev)**               | 001                              | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/hardware-rev`                               |
| **序號 (serial-num)**                     | V123456789abcd                   | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/serial-num`                                 |
| **製造商名稱 (mfg-name)**                 | VIAVI Solutions                  | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/mfg-name`                                   |
| **製造日期 (mfg-date)**                   | 2019-06-28                       | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/mfg-date`                                   |
| **狀態最後變更時間 (state-last-changed)** | 2019-10-16T08:14:43.058274+00:00 | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/state/state-last-changed`                   |
| **操作狀態 (oper-state)**                 | disabled                         | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/state/oper-state`                           |
| **使用狀態 (usage-state)**                | active                           | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/state/usage-state`                          |
| **可用性狀態 (availability-state)**       | NORMAL                           | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/state/o-ran-hardware:availability-state`    |
| **標籤內容 - 模型名稱 (model-name)**      | TRUE                             | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/o-ran-hardware:label-content/model-name`    |
| **標籤內容 - 序號 (serial-number)**       | TRUE                             | `/ietf-hardware:hardware/component[name='component_O-RAN-RADIO_0']/o-ran-hardware:label-content/serial-number` |


### O-RAN Interface information
#### Interface `LBM`

| 屬性                         | 值                          |                                                            |
| ---------------------------- |:--------------------------- |:---------------------------------------------------------- |
| **路徑**                     |                             | /ietf-interfaces:interfaces/interface[name='LBM']          |
| **類別**                     | SR_IDENTITYREF_T            | /enabled,SR_BOOL_T,TRUE                                    |
| **類型**                     | iana-if-type:ethernetCsmacd | enabled,SR_BOOL_T,TRUE                                     |
| **啟用狀態 (enabled)**       |                             | enabled,SR_BOOL_T,TRUE                                     |
| **L2 MTU**                   | 1500                        | o-ran-interfaces:l2-mtu,SR_UINT16_T,1500                   |
| **MAC 地址 (mac-address)**   | 01:23:45:67:89:AB           | o-ran-interfaces:mac-address,SR_STRING_T,01:23:45:67:89:AB |
| **VLAN 標記 (vlan-tagging)** | TRUE                        | o-ran-interfaces:vlan-tagging,SR_BOOL_T,TRUE               |



#### Interface `ORU1-P1`

| 屬性                         | 值                          |                                                            |
| ---------------------------- |:--------------------------- |:---------------------------------------------------------- |
| **路徑**                     |                             | /ietf-interfaces:interfaces/interface[name='ORU1-P1']     |
| **類別**                     | SR_IDENTITYREF_T            | /enabled,SR_BOOL_T,TRUE                                    |
| **類型**                     | iana-if-type:ethernetCsmacd | enabled,SR_BOOL_T,TRUE                                     |
| **啟用狀態 (enabled)**       |                             | enabled,SR_BOOL_T,TRUE                                     |
| **L2 MTU**                   | 1500                        | o-ran-interfaces:l2-mtu,SR_UINT16_T,1500                   |
| **MAC 地址 (mac-address)**   | 01:23:45:67:89:AB           | o-ran-interfaces:mac-address,SR_STRING_T,01:23:45:67:89:AB |
| **VLAN 標記 (vlan-tagging)** | TRUE                        | o-ran-interfaces:vlan-tagging,SR_BOOL_T,TRUE               |
| **操作狀態 (oper-status)**   | FALSE                       | /oper-status,SR_ENUM_T,up                                  |
| **物理地址 (phys-address)**  | FALSE                       | /phys-address,SR_STRING_T,01:23:45:67:89:AB               |
| **上層接口 (higher-layer-if)**| FALSE                       | /higher-layer-if,SR_STRING_T,Mplane_eth                    |
| **速度 (speed)**            | FALSE                       | /speed,SR_UINT64_T,25000000000                             |



#### Interface `ORU1-P2`

| 屬性                         | 值                          |                                                            |
| ---------------------------- |:--------------------------- |:---------------------------------------------------------- |
| **路徑**                     |                             | /ietf-interfaces:interfaces/interface[name='ORU1-P2']     |
| **類別**                     | SR_IDENTITYREF_T            | /enabled,SR_BOOL_T,TRUE                                    |
| **類型**                     | iana-if-type:ethernetCsmacd | enabled,SR_BOOL_T,TRUE                                     |
| **啟用狀態 (enabled)**       |                             | enabled,SR_BOOL_T,TRUE                                     |
| **L2 MTU**                   | 1500                        | o-ran-interfaces:l2-mtu,SR_UINT16_T,1500                   |
| **MAC 地址 (mac-address)**   | 23:45:67:89:ab:01           | o-ran-interfaces:mac-address,SR_STRING_T,23:45:67:89:ab:01 |
| **VLAN 標記 (vlan-tagging)** | TRUE                        | o-ran-interfaces:vlan-tagging,SR_BOOL_T,TRUE               |
| **操作狀態 (oper-status)**   | FALSE                       | /oper-status,SR_ENUM_T,up                                  |
| **物理地址 (phys-address)**  | FALSE                       | /phys-address,SR_STRING_T,01:23:45:67:89:AB               |
| **速度 (speed)**            | FALSE                       | /speed,SR_UINT64_T,25000000000                             |




#### Interface `Mplane_eth`

| 屬性                         | 值                          |                                                            |
| ---------------------------- |:--------------------------- |:---------------------------------------------------------- |
| **路徑**                     |                             | /ietf-interfaces:interfaces/interface[name='Mplane_eth']   |
| **類別**                     | SR_IDENTITYREF_T            | /enabled,SR_BOOL_T,TRUE                                    |
| **類型**                     | iana-if-type:l2vlan         | enabled,SR_BOOL_T,TRUE                                     |
| **啟用狀態 (enabled)**       |                             | enabled,SR_BOOL_T,TRUE                                     |
| **VLAN ID**                  | 20                          | o-ran-interfaces:vlan-id,SR_UINT16_T,20                    |
| **上層接口 (higher-layer-if)**| FALSE                       | /higher-layer-if,SR_STRING_T,Mplane_ip                     |
| **下層接口 (lower-layer-if)**| FALSE                       | /lower-layer-if,SR_STRING_T,ORU1-P1                        |


#### Interface `Mplane_ip`

| 屬性                         | 值                          |                                                            |
| ---------------------------- |:--------------------------- |:---------------------------------------------------------- |
| **路徑**                     |                             | /ietf-interfaces:interfaces/interface[name='Mplane_ip']    |
| **類別**                     | SR_IDENTITYREF_T            | /enabled,SR_BOOL_T,TRUE                                    |
| **類型**                     | iana-if-type:ip             | enabled,SR_BOOL_T,TRUE                                     |
| **啟用狀態 (enabled)**       |                             | enabled,SR_BOOL_T,TRUE                                     |
| **下層接口 (lower-layer-if)**| FALSE                       | /lower-layer-if,SR_STRING_T,Mplane_eth                     |
| **IPv4 啟用 (ipv4/enabled)** | TRUE                        | /ietf-ip:ipv4/enabled,SR_BOOL_T,TRUE                      |
| **IPv4 轉發 (ipv4/forwarding)**| FALSE                     | /ietf-ip:ipv4/forwarding,SR_BOOL_T,FALSE                  |




### Interface `operations`

| 屬性                      | 值                        |                                                                |
|:------------------------- |:------------------------- |:-------------------------------------------------------------- |
| **路徑**                  |                           | /o-ran-operations:operational-info/declarations/ru-instance-id |
| **RU 實例 ID**            | Viavi-ru-instance#1       | SR_STRING_T,Viavi-ru-instance#1                                |
| **支持的 M-Plane 版本**   | 2.0.0                     | SR_STRING_T,2.0.0                                              |
| **支持的 CUS Plane 版本** | 2.0.0                     | SR_STRING_T,2.0.0                                              |
| **ECPRI 標頭串聯支持**    | FALSE                     | SR_BOOL_T,FALSE                                                |
| **ECPRI 協議版本**        | 1.0                       | SR_STRING_T,1.0                                                |
| **重啟原因**              | POWER-ON                  | SR_ENUM_T,POWER-ON                                             |
| **重啟時間**              | 2019-09-30T23:59:59-00:00 | SR_STRING_T,2019-09-30T23:59:59-00:00                          |
| **時區名稱**              | Europe/London             | SR_STRING_T,Europe/London                                      |
| **時區 UTC 偏移**         | 0                         | SR_INT16_T,0                                                   |
| **回呼無 SSH 計時器**     | 60                        | SR_UINT16_T,60                                                 |





### Delay management (timing windows)

| Parameter              | RUSIM                | config parameter                                                                                                                                             |
|:---------------------- |:-------------------- |:------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Tadv_cp_dl             | 125000 ns            | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/tcp-adv-dl,SR_UINT32_T    |
| T2a_min_cp_dl          | 74000 ns             | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-dl,SR_UINT32_T |
| T2a_max_cp_dl          | 464000 ns            | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-dl,SR_UINT32_T |
| T2a_min_cp_ul          | 36000 ns             | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-cp-ul,SR_UINT32_T |
| T2a_max_cp_ul          | 75800 ns             | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-cp-ul,SR_UINT32_T |
| T2a_min_up             | 24000 ns             | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-min-up,SR_UINT32_T    |
| T2a_max_up             | 464000 ns            | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/t2a-max-up,SR_UINT32_T    |
| Ta3_min                | 84000 ns             | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-min,SR_UINT32_T       |
| Ta3_max                | 120000 ns            | o-ran-delay-management:delay-management/bandwidth-scs-delay-state[bandwidth='100000'][subcarrier-spacing='30000']/ru-delay-profile/ta3-max,SR_UINT32_T       |
| compression            | 9                    | o-ran-uplane-conf:user-plane-configuration/low-level-tx-endpoints[name='CC0_TxEndpoint0']/compression/iq-bitwidth,SR_UINT8_T,9                               |
|                        | 9                    | o-ran-uplane-conf:user-plane-configuration/low-level-rx-endpoints[name='CC0_RxEndpoint1']/compression/iq-bitwidth,SR_UINT8_T,9                               |
| number-of-prb-per-scs: | 66(!)                | o-ran-uplane-conf:user-plane-configuration/low-level-rx-endpoints[name='CC0_RxEndpoint0']/number-of-prb-per-scs[scs='KHZ_120']/number-of-prb,SR_UINT16_T,66  |
| DU_MAC                 | 01:AB:23:CD:45:EF(!) | o-ran-processing-element:processing-elements/ru-elements[name='CC0_PE.ORU1-P1.5']/transport-flow/eth-flow/o-du-mac-address,SR_STRING_T,01:AB:23:CD:45:EF     |
| bandwidth-dl           | 100000               | o-ran-uplane-conf:user-plane-configuration/tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/max-supported-bandwidth-dl,SR_DECIMAL64_T,100000                 |
| bandwidth-dl           | 100000               | o-ran-uplane-conf:user-plane-configuration/rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/max-supported-bandwidth-ul,SR_DECIMAL64_T,100000                 |








### DHCP Configuration

| 屬性                  | 值                         |                                                                |
|:--------------------- |:-------------------------- |:-------------------------------------------------------------- |
| **DHCP 客戶端 ID**    | genericDHCPclientID_string | client-id,SR_STRING_T,genericDHCPclientID_string               |
| **DHCP 伺服器識別符** | 192.168.253.195            | dhcp-server-identifier,SR_STRING_T,192.168.253.195             |
| **介面 MTU**          | 1500                       | dhcpv4/interface-mtu,SR_UINT32_T,1500                          |
| **可選端口**          | 4334                       | optional-port,SR_UINT16_T,4334                                 |
| **私有企業號**        | 53148                      | private-enterprise-num,SR_UINT32_T,53148                       |
| **廠商類別數據**      |                            | vendor-class-data,SR_STRING_T,o-ran-ru2/Viavi/ORUemulatorV0001 |



### Loop Back 配置

| 屬性           | 值        |                             |
| -------------- |:--------- |:--------------------------- |
| **維護域名稱** | Viavi_md0 | /name,SR_STRING_T,Viavi_md0 |
| **維護域級別** | 7         | /md-level,SR_INT32_T,7      |




### Module Capability 配置

| 屬性                                       | 值          |                                                                                          |
|:------------------------------------------ |:----------- |:---------------------------------------------------------------------------------------- |
| **RU 支持類別**                            | CAT_A       | /ru-supported-category,SR_ENUM_T,CAT_A                                                   |
| **RU 端口數量**                            | 4           | /number-of-ru-ports,SR_UINT8_T,4                                                         |
| **空間流數量**                             | 2           | /number-of-spatial-streams,SR_UINT8_T,2                                                  |
| **每個 PA 天線最大功率**                   | 40.0        | /max-power-per-pa-antenna,SR_DECIMAL64_T,40.0                                            |
| **每個 PA 天線最小功率**                   | 0.0         | /min-power-per-pa-antenna,SR_DECIMAL64_T,0.0                                             |
| **前傳分割選項**                           | 7           | /fronthaul-split-option,SR_UINT8_T,7                                                     |
| **傳輸空白節能**                           | false       | /energy-saving-by-transmission-blanks,SR_BOOL_T,false                                    |
| **動態壓縮支持**                           | FALSE       | /format-of-iq-sample/dynamic-compression-supported,SR_BOOL_T,FALSE                       |
| **實時變位元寬度支持**                     | FALSE       | /format-of-iq-sample/realtime-variable-bit-width-supported,SR_BOOL_T,FALSE               |
| **IQ 樣本壓縮方法支持 (1) - 位寬**         | 14          | /format-of-iq-sample/compression-method-supported[1]/iq-bitwidth,SR_UINT8_T,14           |
| **IQ 樣本壓縮方法支持 (1) - 壓縮類型**     | STATIC      | /format-of-iq-sample/compression-method-supported[1]/compression-type,SR_ENUM_T,STATIC   |
| **IQ 樣本壓縮方法支持 (1) - 位寬**         | 14          | /format-of-iq-sample/compression-method-supported[1]/bitwidth,SR_UINT8_T,14              |
| **IQ 樣本壓縮方法支持 (1) - 指數**         | 4           | /format-of-iq-sample/compression-method-supported[1]/exponent,SR_UINT8_T,4               |
| **IQ 樣本壓縮方法支持 (2) - 位寬**         | 9           | /format-of-iq-sample/compression-method-supported[2]/iq-bitwidth,SR_UINT8_T,9            |
| **IQ 樣本壓縮方法支持 (2) - 壓縮類型**     | STATIC      | /format-of-iq-sample/compression-method-supported[2]/compression-type,SR_ENUM_T,STATIC   |
| **IQ 樣本壓縮方法支持 (2) - 位寬**         | 9           | /format-of-iq-sample/compression-method-supported[2]/bitwidth,SR_UINT8_T,9               |
| **IQ 樣本壓縮方法支持 (2) - 指數**         | 4           | /format-of-iq-sample/compression-method-supported[2]/exponent,SR_UINT8_T,4               |
| **IQ 樣本壓縮方法支持 (3) - 位寬**         | 16          | /format-of-iq-sample/compression-method-supported[3]/iq-bitwidth,SR_UINT8_T,16           |
| **IQ 樣本壓縮方法支持 (3) - 壓縮類型**     | STATIC      | /format-of-iq-sample/compression-method-supported[3]/compression-type,SR_ENUM_T,STATIC   |
| **IQ 樣本壓縮方法支持 (3) - 位寬**         | 16          | /format-of-iq-sample/compression-method-supported[3]/bitwidth,SR_UINT8_T,16              |
| **IQ 樣本壓縮方法支持 (4) - 壓縮類型**     | STATIC      | /format-of-iq-sample/compression-method-supported[4]/compression-type,SR_ENUM_T,STATIC   |
| **IQ 樣本壓縮方法支持 (4) - 位寬**         | 4           | /format-of-iq-sample/compression-method-supported[4]/biq-bitwidth,SR_UINT8_T,4           |
| **IQ 樣本壓縮方法支持 (4) - CSF**          | 0           | /format-of-iq-sample/compression-method-supported[4]/csf,SR_UINT8_T,0                    |
| **IQ 樣本壓縮方法支持 (4) - 模組壓縮比例** | 15          | /format-of-iq-sample/compression-method-supported[4]/mod-comp-scaler,SR_UINT16_T,15      |
| **IQ 樣本符號增益支持**                    | FALSE       | /format-of-iq-sample/syminc-supported,SR_BOOL_T,FALSE                                    |
| **動態傳輸延遲管理支持**                   | FALSE       | /dynamic-transport-delay-management-supported,SR_BOOL_T,FALSE                            |
| **頻段能力 (260) - 最大下行頻率**          | 40000000000 | /band-capabilities[band-number='260']/max-supported-frequency-dl,SR_UINT64_T,40000000000 |
| **頻段能力 (260) - 最小下行頻率**          | 37000000000 | /band-capabilities[band-number='260']/min-supported-frequency-dl,SR_UINT64_T,37000000000 |
| **頻段能力 (260) - 最大下行帶寬**          | 100000      | /band-capabilities[band-number='260']/max-supported-bandwidth-dl,SR_UINT64_T,100000      |
| **頻段能力 (260) - 最大下行載波數量**      | 1           | /band-capabilities[band-number='260']/max-num-carriers-dl,SR_UINT32_T,1                  |
| **頻段能力 (260) - 最大載波帶寬**          | 100000      | /band-capabilities[band-number='260']/max-carrier-bandwidth-dl,SR_UINT64_T,100000        |
| **頻段能力 (260) - 最小載波帶寬**          | 10000       | /band-capabilities[band-number='260']/min-carrier-bandwidth-dl,SR_UINT64_T,10000         |
| **頻段能力 (260) - 最大上行頻率**          | 40000000000 | /band-capabilities[band-number='260']/max-supported-frequency-ul,SR_UINT64_T,40000000000 |
| **頻段能力 (260) - 最小上行頻率**          | 37000000000 | /band-capabilities[band-number='260']/min-supported-frequency-ul,SR_UINT64_T,37000000000 |
| **頻段能力 (260) - 最大上行帶寬**          | 1           | /band-capabilities[band-number='260']/max-supported-bandwidth-ul,SR_UINT64_T,1           |
| **頻段能力 (260) - 最大上行載波數量**      | 1           | /band-capabilities[band-number='260']/max-num-carriers-ul,SR_UINT32_T,1                  |
| **頻段能力 (260) - 最大上行載波帶寬**      | 100000      | /band-capabilities[band-number='260']/max-carrier-bandwidth-ul,SR_UINT64_T,100000        |
| **頻段能力 (260) - 最小上行載波帶寬**      | 10000       | /band-capabilities[band-number='260']/min-carrier-bandwidth-ul,SR_UINT64_T,10000         |
| **頻段能力 (260) - 最大組件載波數量**      | 1           | /band-capabilities[band-number='260']/max-num-component-carriers,SR_UINT8_T,1            |
| **頻段能力 (260) - 最大頻段數量**          | 1           | /band-capabilities[band-number='260']/max-num-bands,SR_UINT16_T,1                        |
| **頻段能力 (260) - 最大扇區數量**          | 1           | /band-capabilities[band-number='260']/max-num-sectors,SR_UINT8_T,1                       |
| **頻段能力 (260) - 每個天線最大功率**      | 40.0        | /band-capabilities[band-number='260']/max-power-per-antenna,SR_DECIMAL64_T,40.0          |
| **頻段能力 (260) - 每個天線最小功率**      | 0.0         | /band-capabilities[band-number='260']/min-power-per-antenna,SR_DECIMAL64_T,0.0           |


### Mplane 配置

| 屬性                                          | 值                 |                                                                                                                                |
| --------------------------------------------- |:------------------ |:------------------------------------------------------------------------------------------------------------------------------ |
| **可搜索的 Mplane 存取 VLANs 數量**           | 1                  | /searchable-access-vlans,SR_UINT16_T,1                                                                                         |
| **VLAN 範圍 - 最低 VLAN ID**                  | 1                  | /vlan-range/lowest-vlan-id,SR_UINT16_T,1                                                                                       |
| **VLAN 範圍 - 最高 VLAN ID**                  | 4094               | /vlan-range/highest-vlan-id,SR_UINT16_T,4094                                                                                   |
| **Mplane 介面 - 客戶端 FQDN (Mplane_eth, 2)** | myhost.example.com | /m-plane-sub-interfaces[interface-name='Mplane_eth'][sub-interface='2']/client-info/mplane-fqdn,SR_STRING_T,myhost.example.com |
| **Mplane 介面 - 呼叫主 SSH 端口**             | 4334               | /call-home-ssh-port,SR_UINT16_T,4334                                                                                           |
| **Mplane 介面 - 伺服器 SSH 端口**             | 830                | /server-ssh-port,SR_UINT16_T,830                                                                                               |



### Fan 配置

| 屬性                       | 值    |                                        |
| -------------------------- |:----- |:-------------------------------------- |
| **風扇狀態 (viavi_fan_1)** | false | /present-and-operating,SR_BOOL_T,false |


### Performance Management 配置

| 屬性                               | 值                      |                                                                                                          |
| ---------------------------------- |:----------------------- |:-------------------------------------------------------------------------------------------------------- |
| **啟用 SFTP 上傳**                 | FALSE                   | /enable-SFTP-upload,SR_BOOL_T,FALSE                                                                      |
| **啟用隨機文件上傳**               | FALSE                   | /enable-random-file-upload,SR_BOOL_T,FALSE                                                               |
| **收發器測量間隔**                 | 100                     | /transceiver-measurement-interval,SR_UINT16_T,100                                                        |
| **接收窗口測量間隔**               | 100                     | /rx-window-measurement-interval,SR_UINT16_T,100                                                          |
| **通知間隔**                       | 100                     | /notification-interval,SR_UINT16_T,100                                                                   |
| **文件上傳間隔**                   | 900                     | /file-upload-interval,SR_UINT16_T,900                                                                    |
| **最大計數**                       | 0                       | /max-bin-count,SR_UINT16_T,0                                                                             |
| **遠程 SFTP 上傳密碼**             | tm500                   | /remote-SFTP-uploads[remote-SFTP-upload-path='tm500@10.220.16.62']/password/password,SR_STRING_T,tm500   |
| **RX_ON_TIME 測量對象 - 活動狀態** | FALSE                   | /rx-window-measurement-objects[measurement-object='RX_ON_TIME']/active,SR_BOOL_T,FALSE                   |
| **RX_ON_TIME 測量對象 - 單位**     | RU                      | /rx-window-measurement-objects[measurement-object='RX_ON_TIME']/object-unit,SR_ENUM_T,RU                 |
| **RX_ON_TIME 測量對象 - 報告信息** | COUNT                   | /rx-window-measurement-objects[measurement-object='RX_ON_TIME']/report-info,SR_ENUM_T,COUNT              |
| **RX_ON_TIME 測量對象 - 名稱**     | component_O-RAN-RADIO_0 | /rx-window-measurement-objects[measurement-object='RX_ON_TIME']/name,SR_STRING_T,component_O-RAN-RADIO_0 |
| **RX_ON_TIME 測量對象 - 計數**     | 0                       | /rx-window-measurement-objects[measurement-object='RX_ON_TIME']/count,SR_UINT64_T,0                      |
| **RX_EARLY 測量對象 - 活動狀態**   | FALSE                   | /rx-window-measurement-objects[measurement-object='RX_EARLY']/active,SR_BOOL_T,FALSE                     |
| **RX_EARLY 測量對象 - 單位**       | RU                      | /rx-window-measurement-objects[measurement-object='RX_EARLY']/object-unit,SR_ENUM_T,RU                   |
| **RX_EARLY 測量對象 - 報告信息**   | COUNT                   | /rx-window-measurement-objects[measurement-object='RX_EARLY']/report-info,SR_ENUM_T,COUNT                |
| **RX_EARLY 測量對象 - 名稱**       | component_O-RAN-RADIO_0 | /rx-window-measurement-objects[measurement-object='RX_EARLY']/name,SR_STRING_T,component_O-RAN-RADIO_0   |
| **RX_EARLY 測量對象 - 計數**       | 0                       | /rx-window-measurement-objects[measurement-object='RX_EARLY']/count,SR_UINT64_T,0                        |
| **RX_LATE 測量對象 - 活動狀態**    | FALSE                   | /rx-window-measurement-objects[measurement-object='RX_LATE']/active,SR_BOOL_T,FALSE                      |
| **RX_LATE 測量對象 - 單位**        | RU                      | /rx-window-measurement-objects[measurement-object='RX_LATE']/object-unit,SR_ENUM_T,RU                    |
| **RX_LATE 測量對象 - 報告信息**    | COUNT                   | /rx-window-measurement-objects[measurement-object='RX_LATE']/report-info,SR_ENUM_T,COUNT                 |
| **RX_LATE 測量對象 - 名稱**        | component_O-RAN-RADIO_0 | /rx-window-measurement-objects[measurement-object='RX_LATE']/name,SR_STRING_T,component_O-RAN-RADIO_0    |
| **RX_LATE 測量對象 - 計數**        | 0                       | /rx-window-measurement-objects[measurement-object='RX_LATE']/count,SR_UINT64_T,0                         |
| **RX_CORRUPT 測量對象 - 活動狀態** | FALSE                   | /rx-window-measurement-objects[measurement-object='RX_CORRUPT']/active,SR_BOOL_T,FALSE                   |
| **RX_CORRUPT 測量對象 - 單位**     | RU                      | /rx-window-measurement-objects[measurement-object='RX_CORRUPT']/object-unit,SR_ENUM_T,RU                 |
| **RX_CORRUPT 測量對象 - 報告信息** | COUNT                   | /rx-window-measurement-objects[measurement-object='RX_CORRUPT']/report-info,SR_ENUM_T,COUNT              |
| **RX_CORRUPT 測量對象 - 名稱**     | component_O-RAN-RADIO_0 | /rx-window-measurement-objects[measurement-object='RX_CORRUPT']/name,SR_STRING_T,component_O-RAN-RADIO_0 |
| **RX_CORRUPT 測量對象 - 計數**     | 0                       | /rx-window-measurement-objects[measurement-object='RX_CORRUPT']/count,SR_UINT64_T,0                      |
| **RX_DUPL 測量對象 - 活動狀態**    | FALSE                   | /rx-window-measurement-objects[measurement-object='RX_DUPL']/active,SR_BOOL_T,FALSE                      |
| **RX_DUPL 測量對象 - 單位**        | RU                      | /rx-window-measurement-objects[measurement-object='RX_DUPL']/object-unit,SR_ENUM_T,RU                    |
| **RX_DUPL 測量對象 - 報告信息**    | COUNT                   | /rx-window-measurement-objects[measurement-object='RX_DUPL']/report-info,SR_ENUM_T,COUNT                 |
| **RX_DUPL 測量對象 - 名稱**        | component_O-RAN-RADIO_0 | /rx-window-measurement-objects[measurement-object='RX_DUPL']/name,SR_STRING_T,component_O-RAN-RADIO_0    |
| **RX_DUPL 測量對象 - 計數**        | 0                       | /rx-window-measurement-objects[measurement-object='RX_DUPL']/count,SR_UINT64_T,0                         |
| **RX_TOTAL 測量對象 - 活動狀態**   | FALSE                   | /rx-window-measurement-objects[measurement-object='RX_TOTAL']/active,SR_BOOL_T,FALSE                     |
| **RX_TOTAL 測量對象 - 單位**       | RU                      | /rx-window-measurement-objects[measurement-object='RX_TOTAL']/object-unit,SR_ENUM_T,RU                   |
| **RX_TOTAL 測量對象 - 報告信息**   | COUNT                   | /rx-window-measurement-objects[measurement-object='RX_TOTAL']/report-info,SR_ENUM_T,COUNT                |
| **RX_TOTAL 測量對象 - 名稱**       | component_O-RAN-RADIO_0 | /rx-window-measurement-objects[measurement-object='RX_TOTAL']/name,SR_STRING_T,component_O-RAN-RADIO_0   |
| **RX_TOTAL 測量對象 - 計數**       | 0                       | /rx-window-measurement-objects[measurement-object='RX_TOTAL']/count,SR_UINT64_T,0                        |




















### Processing Elements 配置

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **傳輸會話類型**                                           | ETH-INTERFACE            | /transport-session-type,SR_ENUM_T,ETH-INTERFACE |

### Sync 配置

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **支持的參考類型**                                       | PTP                      | /sync-status/supported-reference-types[item='PTP'] |
| **同步能力 - TSC 類別**                                   | CLASS_B                 | /sync-capability/sync-t-tsc,SR_ENUM_T,CLASS_B |
| **PTP 配置 - 域號**                                       | 24                       | /ptp-config/domain-number,SR_UINT8_T,24 |
| **PTP 配置 - 接受的時鐘類別**                             | 6                        | /ptp-config/accepted-clock-classes[clock-classes='6'] |
| **PTP 配置 - 接受的時鐘類別**                             | 7                        | /ptp-config/accepted-clock-classes[clock-classes='7'] |
| **PTP 配置 - PTP 配置文件**                               | G_8275_1                | /ptp-config/ptp-profile,SR_ENUM_T,G_8275_1 |
| **PTP 配置 - 多播 MAC 地址**                               | NONFORWARDABLE          | /g-8275-1-config/multicast-mac-address,SR_ENUM_T,NONFORWARDABLE |
| **PTP 配置 - 延遲非對稱性**                               | 0                        | /g-8275-1-config/delay-asymmetry,SR_INT16_T,0 |
| **PTP 狀態 - 報告周期**                                   | 10                       | /ptp-status/reporting-period,SR_UINT8_T,10 |

### Transceiver Ports 配置

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **ORU1-P1 端口 - 名稱**                                   | viavi_transceiver0_0     | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/name,SR_STRING_T,viavi_transceiver0_0 |
| **ORU1-P1 端口 - 存在性**                                 | TRUE                     | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/present,SR_BOOL_T,TRUE |
| **ORU1-P1 端口 - 廠商 ID**                                | Mellanox                | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/vendor-id,SR_STRING_T,Mellanox |
| **ORU1-P1 端口 - 廠商部件**                              | MCX512F-ACAT            | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/vendor-part,SR_STRING_T,MCX512F-ACAT |
| **ORU1-P1 端口 - 廠商版本**                              | 01                       | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/vendor-rev,SR_STRING_T,01 |
| **ORU1-P1 端口 - 序列號**                                | XXXXAAAABBBBCCCC        | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/serial-no,SR_STRING_T,XXXXAAAABBBBCCCC |
| **ORU1-P1 端口 - SFF8472 合規性代碼**                    | diagnostics-undefined    | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/SFF8472-compliance-code,SR_ENUM_T,diagnostics-undefined |
| **ORU1-P1 端口 - 連接器類型**                            | unknown                  | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/connector-type,SR_ENUM_T,unknown |
| **ORU1-P1 端口 - 標識符**                                | sfp                      | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/identifier,SR_ENUM_T,sfp |
| **ORU1-P1 端口 - 標稱比特率**                            | 25000                   | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/nominal-bitrate,SR_UINT32_T,25000 |
| **ORU1-P1 端口 - RX 功率類型**                           | avp                      | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/rx-power-type,SR_ENUM_T,avp |
| **ORU1-P1 端口 - RX 功率**                               | 1.234                   | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/rx-power,SR_DECIMAL64_T,1.234 |
| **ORU1-P1 端口 - TX 功率**                               | 0.987                   | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/tx-power,SR_DECIMAL64_T,0.987 |
| **ORU1-P1 端口 - TX 偏置電流**                           | 12.0                    | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/tx-bias-current,SR_DECIMAL64_T,12.0 |
| **ORU1-P1 端口 - 電壓**                                  | 12.0                    | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/voltage,SR_DECIMAL64_T,12.0 |
| **ORU1-P1 端口 - 溫度**                                  | 48.0                    | /port-transceiver-data[interface-name='ORU1-P1'][port-number='0']/temperature,SR_DECIMAL64_T,48.0 |
| **ORU1-P2 端口 - 名稱**                                   | viavi_transceiver0_1     | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/name,SR_STRING_T,viavi_transceiver0_1 |
| **ORU1-P2 端口 - 存在性**                                 | TRUE                     | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/present,SR_BOOL_T,TRUE |
| **ORU1-P2 端口 - 廠商 ID**                                | Mellanox                | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/vendor-id,SR_STRING_T,Mellanox |
| **ORU1-P2 端口 - 廠商部件**                              | MCX512F-ACAT            | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/vendor-part,SR_STRING_T,MCX512F-ACAT |
| **ORU1-P2 端口 - 廠商版本**                              | 01                       | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/vendor-rev,SR_STRING_T,01 |
| **ORU1-P2 端口 - 序列號**                                | XXXXAAAABBBBCCCC        | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/serial-no,SR_STRING_T,XXXXAAAABBBBCCCC |
| **ORU1-P2 端口 - SFF8472 合規性代碼**                    | diagnostics-undefined    | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/SFF8472-compliance-code,SR_ENUM_T,diagnostics-undefined |
| **ORU1-P2 端口 - 連接器類型**                            | unknown                  | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/connector-type,SR_ENUM_T,unknown |
| **ORU1-P2 端口 - 標識符**                                | sfp                      | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/identifier,SR_ENUM_T,sfp |
| **ORU1-P2 端口 - 標稱比特率**                            | 25000                   | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/nominal-bitrate,SR_UINT32_T,25000 |
| **ORU1-P2 端口 - RX 功率類型**                           | avp                      | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/rx-power-type,SR_ENUM_T,avp |
| **ORU1-P2 端口 - RX 功率**                               | 1.234                   | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/rx-power,SR_DECIMAL64_T,1.234 |
| **ORU1-P2 端口 - TX 功率**                               | 0.987                   | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/tx-power,SR_DECIMAL64_T,0.987 |
| **ORU1-P2 端口 - TX 偏置電流**                           | 12.0                    | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/tx-bias-current,SR_DECIMAL64_T,12.0 |
| **ORU1-P2 端口 - 電壓**                                  | 12.0                    | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/voltage,SR_DECIMAL64_T,12.0 |
| **ORU1-P2 端口 - 溫度**                                  | 49.3                    | /port-transceiver-data[interface-name='ORU1-P2'][port-number='1']/temperature,SR_DECIMAL64_T,49.3 |

### Antenna Line Devices Port Configuration

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **過電流支持**                                           | FALSE                    | /over-current-supported,SR_BOOL_T,FALSE |
| **ALD 端口 - 端口 ID**                                   | 0                        | /ald-port[name='viavi_ald_port']/port-id,SR_UINT8_T,0 |
| **ALD 端口 - DC 控制支持**                               | FALSE                    | /ald-port[name='viavi_ald_port']/dc-control-support,SR_BOOL_T,FALSE |
| **ALD 端口 - 支持的連接器**                             | ANTENNA_CONNECTOR       | /ald-port[name='viavi_ald_port']/supported-connector,SR_ENUM_T,ANTENNA_CONNECTOR |
| **ALD DC 控制 - DC 啟用**                               | TRUE                     | /ald-port-dc-control[name='viavi_ald_port']/dc-enabled,SR_BOOL_T,TRUE |




### User Plane Capabilities 配置

#### 通用端點類型 (eCPRI Streams)

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **端點類型 ID 0 - 支持的區段類型 (0)**                   | 0                        | /endpoint-types[id='0']/supported-section-types[section-type='0']/supported-section-extensions,SR_UINT8_T,0 |
| **端點類型 ID 0 - 支持的區段類型 (1)**                   | 0                        | /endpoint-types[id='0']/supported-section-types[section-type='1']/supported-section-extensions,SR_UINT8_T,0 |
| **端點類型 ID 0 - 支持的區段類型 (3)**                   | 0                        | /endpoint-types[id='0']/supported-section-types[section-type='3']/supported-section-extensions,SR_UINT8_T,0 |
| **端點類型 ID 0 - 支持的幀結構**                         | 193                      | /endpoint-types[id='0']/supported-frame-structures,SR_UINT8_T,193 |
| **端點類型 ID 0 - 管理延遲支持**                         | MANAGED                  | /endpoint-types[id='0']/managed-delay-support,SR_ENUM_T,MANAGED |
| **端點類型 ID 0 - 支持多重數字**                         | FALSE                    | /endpoint-types[id='0']/multiple-numerology-supported,SR_BOOL_T,FALSE |
| **端點類型 ID 0 - 最大數字變更持續時間**                 | 0                        | /endpoint-types[id='0']/max-numerology-change-duration,SR_UINT16_T,0 |
| **端點類型 ID 0 - 每個數據區段的最大控制區段數**         | 1                        | /endpoint-types[id='0']/max-control-sections-per-data-section,SR_UINT8_T,1 |
| **端點類型 ID 0 - 每個符號的最大區段數**                 | 1                        | /endpoint-types[id='0']/max-sections-per-symbol,SR_UINT16_T,1 |
| **端點類型 ID 0 - 每個時隙的最大區段數**                 | 14                       | /endpoint-types[id='0']/max-sections-per-slot,SR_UINT16_T,14 |
| **端點類型 ID 0 - 每個符號的最大波束數**                 | 1                        | /endpoint-types[id='0']/max-beams-per-symbol,SR_UINT16_T,1 |
| **端點類型 ID 0 - 每個時隙的最大波束數**                 | 1                        | /endpoint-types[id='0']/max-beams-per-slot,SR_UINT16_T,1 |
| **端點類型 ID 0 - 每個符號的最大 PRB 數**                | 66                       | /endpoint-types[id='0']/max-prb-per-symbol,SR_UINT16_T,66 |
| **端點類型 ID 0 - PRB 容量分配粒度**                     | 1                        | /endpoint-types[id='0']/prb-capacity-allocation-granularity,SR_UINT16_T,1 |
| **端點類型 ID 0 - 每個符號的最大數字數**                 | 1                        | /endpoint-types[id='0']/max-numerologies-per-symbol,SR_UINT16_T,1 |

#### SRS 端點類型

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **端點類型 ID 1 - 支持的區段類型 (1)**                   | 0                        | /endpoint-types[id='1']/supported-section-types[section-type='1']/supported-section-extensions,SR_UINT8_T,0 |
| **端點類型 ID 1 - 支持的區段類型 (3)**                   | 0                        | /endpoint-types[id='1']/supported-section-types[section-type='3']/supported-section-extensions,SR_UINT8_T,0 |
| **端點類型 ID 1 - 支持的幀結構**                         | 193                      | /endpoint-types[id='1']/supported-frame-structures,SR_UINT8_T,193 |
| **端點類型 ID 1 - 管理延遲支持**                         | NON_MANAGED              | /endpoint-types[id='1']/managed-delay-support,SR_ENUM_T,NON_MANAGED |
| **端點類型 ID 1 - 每個數據區段的最大控制區段數**         | 1                        | /endpoint-types[id='1']/max-control-sections-per-data-section,SR_UINT8_T,1 |
| **端點類型 ID 1 - 每個符號的最大區段數**                 | 64                       | /endpoint-types[id='1']/max-sections-per-symbol,SR_UINT16_T,64 |
| **端點類型 ID 1 - 每個時隙的最大區段數**                 | 192                      | /endpoint-types[id='1']/max-sections-per-slot,SR_UINT16_T,192 |
| **端點類型 ID 1 - 每個符號的最大波束數**                 | 1                        | /endpoint-types[id='1']/max-beams-per-symbol,SR_UINT16_T,1 |
| **端點類型 ID 1 - 每個時隙的最大波束數**                 | 1                        | /endpoint-types[id='1']/max-beams-per-slot,SR_UINT16_T,1 |
| **端點類型 ID 1 - 每個符號的最大 PRB 數**                | 273                      | /endpoint-types[id='1']/max-prb-per-symbol,SR_UINT16_T,273 |

#### 端點容量共享組

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **共享組 ID 0 - 每個數據區段的最大控制區段數**           | 1                        | /endpoint-capacity-sharing-groups[id='0']/max-control-sections-per-data-section,SR_UINT8_T,1 |
| **共享組 ID 0 - 每個符號的最大區段數**                   | 1                        | /endpoint-capacity-sharing-groups[id='0']/max-sections-per-symbol,SR_UINT16_T,1 |
| **共享組 ID 0 - 每個時隙的最大區段數**                   | 14                       | /endpoint-capacity-sharing-groups[id='0']/max-sections-per-slot,SR_UINT16_T,14 |
| **共享組 ID 0 - 每個符號的最大波束數**                   | 1                        | /endpoint-capacity-sharing-groups[id='0']/max-beams-per-symbol,SR_UINT16_T,1 |
| **共享組 ID 0 - 每個時隙的最大波束數**                   | 1                        | /endpoint-capacity-sharing-groups[id='0']/max-beams-per-slot,SR_UINT16_T,1 |
| **共享組 ID 0 - 每個符號的最大 PRB 數**                  | 132                      | /endpoint-capacity-sharing-groups[id='0']/max-prb-per-symbol,SR_UINT16_T,132 |
| **共享組 ID 0 - 每個符號的最大數字數**                   | 1                        | /endpoint-capacity-sharing-groups[id='0']/max-numerologies-per-symbol,SR_UINT16_T,1 |
| **共享組 ID 0 - 最大端點數**                              | 4                        | /endpoint-capacity-sharing-groups[id='0']/max-endpoints,SR_UINT16_T,4 |
| **共享組 ID 0 - 最大管理延遲端點數**                      | 4                        | /endpoint-capacity-sharing-groups[id='0']/max-managed-delay-endpoints,SR_UINT16_T,4 |
| **共享組 ID 0 - 最大非管理延遲端點數**                    | 0                        | /endpoint-capacity-sharing-groups[id='0']/max-non-managed-delay-endpoints,SR_UINT16_T,0 |

#### CC0 靜態低級 TX 端點

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **靜態 TX 端點 CC0_TxEndpoint0 - 限制接口**              | ORU1-P1                 | /static-low-level-tx-endpoints[name='CC0_TxEndpoint0']/restricted-interfaces,SR_STRING_T,ORU1-P1 |
| **靜態 TX 端點 CC0_TxEndpoint0 - 陣列**                  | CC0_TxArray0_H(CC0_TxArray1_H) | /static-low-level-tx-endpoints[name='CC0_TxEndpoint0']/array,SR_STRING_T,CC0_TxArray0_H(CC0_TxArray1_H) |
| **靜態 TX 端點 CC0_TxEndpoint0 - 端點類型**              | 0                        | /static-low-level-tx-endpoints[name='CC0_TxEndpoint0']/endpoint-type,SR_UINT16_T,0 |
| **靜態 TX 端點 CC0_TxEndpoint0 - 容量共享組**            | 0                        | /static-low-level-tx-endpoints[name='CC0_TxEndpoint0']/capacity-sharing-groups,SR_UINT16_T,0 |
| **靜態 TX 端點 CC0_TxEndpoint0 - 端口選擇**              | 0                        | /static-low-level-tx-endpoints[name='CC0_TxEndpoint0']/selection_on_port,SR_UINT16_T,0 |

#### CC0 靜態低級 RX 端點

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **靜態 RX 端點 CC0_RxEndpoint0 - 限制接口**              | ORU1-P1                 | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0']/restricted-interfaces,SR_STRING_T,ORU1-P1 |
| **靜態 RX 端點 CC0_RxEndpoint0 - 陣列**                  | CC0_RxArray0_H(CC0_RxArray1_H) | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0']/array,SR_STRING_T,CC0_RxArray0_H(CC0_RxArray1_H) |
| **靜態 RX 端點 CC0_RxEndpoint0 - 端點類型**              | 0                        | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0']/endpoint-type,SR_UINT16_T,0 |
| **靜態 RX 端點 CC0_RxEndpoint0 - 容量共享組**            | 0                        | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0']/capacity-sharing-groups,SR_UINT16_T,0 |
| **靜態 RX 端點 CC0_RxEndpoint0 - 端口選擇**              | 0                        | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0']/selection_on_port,SR_UINT16_T,0 |
| **靜態 RX 端點 CC0_RxEndpoint0 - 是否為 SRS 流**         | FALSE                    | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0']/is_srs_flow,SR_BOOL_T,False |

#### CC0 靜態低級 RX 端點 (RACH)

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **靜態 RX 端點 CC0_RxEndpoint0_RACH - 限制接口**         | ORU1-P1                 | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0_RACH']/restricted-interfaces,SR_STRING_T,ORU1-P1 |
| **靜態 RX 端點 CC0_RxEndpoint0_RACH - 陣列**             | CC0_RxArray0_H_RACH(CC0_RxArray1_V_RACH) | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0_RACH']/array,SR_STRING_T,CC0_RxArray0_H_RACH(CC0_RxArray1_V_RACH) |
| **靜態 RX 端點 CC0_RxEndpoint0_RACH - 端點類型**         | 0                        | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0_RACH']/endpoint-type,SR_UINT16_T,0 |
| **靜態 RX 端點 CC0_RxEndpoint0_RACH - 容量共享組**       | 0                        | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0_RACH']/capacity-sharing-groups,SR_UINT16_T,0 |
| **靜態 RX 端點 CC0_RxEndpoint0_RACH - 端口選擇**         | 0                        | /static-low-level-rx-endpoints[name='CC0_RxEndpoint0_RACH']/selection_on_port,SR_UINT16_T,0 |




### CC0 Tx 和 Rx Arrays 配置

#### Tx Arrays

##### tx-array : CC0_TxArray0_H

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **行數**                                                   | 1                        | /tx-arrays[name='CC0_TxArray0_H']/number-of-rows,SR_UINT16_T,1 |
| **列數**                                                   | 1                        | /tx-arrays[name='CC0_TxArray0_H']/number-of-columns,SR_UINT16_T,1 |
| **層數**                                                   | 1                        | /tx-arrays[name='CC0_TxArray0_H']/number-of-array-layers,SR_UINT8_T,1 |
| **水平間距**                                               | 0                        | /tx-arrays[name='CC0_TxArray0_H']/horizontal-spacing,SR_DECIMAL64_T,0 |
| **垂直間距**                                               | 0                        | /tx-arrays[name='CC0_TxArray0_H']/vertical-spacing,SR_DECIMAL64_T,0 |
| **方位角**                                               | 0                        | /tx-arrays[name='CC0_TxArray0_H']/normal-vector-direction/azimuth-angle,SR_DECIMAL64_T,0 |
| **天頂角**                                               | 0                        | /tx-arrays[name='CC0_TxArray0_H']/normal-vector-direction/zenith-angle,SR_DECIMAL64_T,0 |
| **最左下元素位置 (x)**                                   | 0                        | /tx-arrays[name='CC0_TxArray0_H']/leftmost-bottom-array-element-position/x,SR_DECIMAL64_T,0 |
| **最左下元素位置 (y)**                                   | 0                        | /tx-arrays[name='CC0_TxArray0_H']/leftmost-bottom-array-element-position/y,SR_DECIMAL64_T,0 |
| **最左下元素位置 (z)**                                   | 0                        | /tx-arrays[name='CC0_TxArray0_H']/leftmost-bottom-array-element-position/z,SR_DECIMAL64_T,0 |
| **極化**                                                  | ZERO                     | /tx-arrays[name='CC0_TxArray0_H']/polarisations[p='0']/polarisation,SR_ENUM_T,ZERO |
| **頻段號**                                               | 0                        | /tx-arrays[name='CC0_TxArray0_H']/band-number,SR_UINT16_T,0 |
| **最大增益**                                             | 0                        | /tx-arrays[name='CC0_TxArray0_H']/max-gain,SR_DECIMAL64_T,0 |
| **獨立功率預算**                                         | TRUE                     | /tx-arrays[name='CC0_TxArray0_H']/independent-power-budget,SR_BOOL_T,TRUE |
| **最大支持下行頻率**                                     | 1                        | /tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/max-supported-frequency-dl,SR_DECIMAL64_T,1 |
| **最小支持下行頻率**                                     | 0                        | /tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/min-supported-frequency-dl,SR_DECIMAL64_T,0 |
| **最大支持下行帶寬**                                     | 100000                   | /tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/max-supported-bandwidth-dl,SR_DECIMAL64_T,100000 |
| **最大載波數**                                           | 1                        | /tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/max-num-carriers-dl,SR_UINT32_T,1 |
| **最大載波帶寬**                                         | 100000                   | /tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/max-carrier-bandwidth-dl,SR_DECIMAL64_T,100000 |
| **最小載波帶寬**                                         | 10000                    | /tx-arrays[name='CC0_TxArray0_H']/capabilities[1]/min-carrier-bandwidth-dl,SR_DECIMAL64_T,10000 |

##### tx-array : CC0_TxArray1_V

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **行數**                                                   | 1                        | /tx-arrays[name='CC0_TxArray1_V']/number-of-rows,SR_UINT16_T,1 |
| **列數**                                                   | 1                        | /tx-arrays[name='CC0_TxArray1_V']/number-of-columns,SR_UINT16_T,1 |
| **層數**                                                   | 1                        | /tx-arrays[name='CC0_TxArray1_V']/number-of-array-layers,SR_UINT8_T,1 |
| **水平間距**                                               | 0                        | /tx-arrays[name='CC0_TxArray1_V']/horizontal-spacing,SR_DECIMAL64_T,0 |
| **垂直間距**                                               | 0                        | /tx-arrays[name='CC0_TxArray1_V']/vertical-spacing,SR_DECIMAL64_T,0 |
| **方位角**                                               | 0                        | /tx-arrays[name='CC0_TxArray1_V']/normal-vector-direction/azimuth-angle,SR_DECIMAL64_T,0 |
| **天頂角**                                               | 0                        | /tx-arrays[name='CC0_TxArray1_V']/normal-vector-direction/zenith-angle,SR_DECIMAL64_T,0 |
| **最左下元素位置 (x)**                                   | 0                        | /tx-arrays[name='CC0_TxArray1_V']/leftmost-bottom-array-element-position/x,SR_DECIMAL64_T,0 |
| **最左下元素位置 (y)**                                   | 0                        | /tx-arrays[name='CC0_TxArray1_V']/leftmost-bottom-array-element-position/y,SR_DECIMAL64_T,0 |
| **最左下元素位置 (z)**                                   | 0                        | /tx-arrays[name='CC0_TxArray1_V']/leftmost-bottom-array-element-position/z,SR_DECIMAL64_T,0 |
| **極化**                                                  | PLUS_90                 | /tx-arrays[name='CC0_TxArray1_V']/polarisations[p='0']/polarisation,SR_ENUM_T,PLUS_90 |
| **頻段號**                                               | 0                        | /tx-arrays[name='CC0_TxArray1_V']/band-number,SR_UINT16_T,0 |
| **最大增益**                                             | 0                        | /tx-arrays[name='CC0_TxArray1_V']/max-gain,SR_DECIMAL64_T,0 |
| **獨立功率預算**                                         | TRUE                     | /tx-arrays[name='CC0_TxArray1_V']/independent-power-budget,SR_BOOL_T,TRUE |
| **最大支持下行頻率**                                     | 1                        | /tx-arrays[name='CC0_TxArray1_V']/capabilities[1]/max-supported-frequency-dl,SR_DECIMAL64_T,1 |
| **最小支持下行頻率**                                     | 0                        | /tx-arrays[name='CC0_TxArray1_V']/capabilities[1]/min-supported-frequency-dl,SR_DECIMAL64_T,0 |
| **最大支持下行帶寬**                                     | 100000                   | /tx-arrays[name='CC0_TxArray1_V']/capabilities[1]/max-supported-bandwidth-dl,SR_DECIMAL64_T,100000 |
| **最大載波數**                                           | 1                        | /tx-arrays[name='CC0_TxArray1_V']/capabilities[1]/max-num-carriers-dl,SR_UINT32_T,1 |
| **最大載波帶寬**                                         | 100000                   | /tx-arrays[name='CC0_TxArray1_V']/capabilities[1]/max-carrier-bandwidth-dl,SR_DECIMAL64_T,100000 |
| **最小載波帶寬**                                         | 10000                    | /tx-arrays[name='CC0_TxArray1_V']/capabilities[1]/min-carrier-bandwidth-dl,SR_DECIMAL64_T,10000 |

#### Rx Arrays

##### rx-array : CC0_RxArray0_H

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **行數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray0_H']/number-of-rows,SR_UINT16_T,1 |
| **列數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray0_H']/number-of-columns,SR_UINT16_T,1 |
| **層數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray0_H']/number-of-array-layers,SR_UINT8_T,1 |
| **水平間距**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H']/horizontal-spacing,SR_DECIMAL64_T,0 |
| **垂直間距**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H']/vertical-spacing,SR_DECIMAL64_T,0 |
| **方位角**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H']/normal-vector-direction/azimuth-angle,SR_DECIMAL64_T,0 |
| **天頂角**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H']/normal-vector-direction/zenith-angle,SR_DECIMAL64_T,0 |
| **最左下元素位置 (x)**                                   | 0                        | /rx-arrays[name='CC0_RxArray0_H']/leftmost-bottom-array-element-position/x,SR_DECIMAL64_T,0 |
| **最左下元素位置 (y)**                                   | 0                        | /rx-arrays[name='CC0_RxArray0_H']/leftmost-bottom-array-element-position/y,SR_DECIMAL64_T,0 |
| **最左下元素位置 (z)**                                   | 0                        | /rx-arrays[name='CC0_RxArray0_H']/leftmost-bottom-array-element-position/z,SR_DECIMAL64_T,0 |
| **極化**                                                  | ZERO                     | /rx-arrays[name='CC0_RxArray0_H']/polarisations[p='0']/polarisation,SR_ENUM_T,ZERO |
| **頻段號**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H']/band-number,SR_UINT16_T,0 |
| **增益修正範圍 (最大)**                                  | 0                        | /rx-arrays[name='CC0_RxArray0_H']/gain-correction-range/max,SR_DECIMAL64_T,0 |
| **增益修正範圍 (最小)**                                  | 0                        | /rx-arrays[name='CC0_RxArray0_H']/gain-correction-range/min,SR_DECIMAL64_T,0 |
| **最大支持上行頻率**                                     | 0                        | /rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/max-supported-frequency-ul,SR_DECIMAL64_T,0 |
| **最小支持上行頻率**                                     | 0                        | /rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/min-supported-frequency-ul,SR_DECIMAL64_T,0 |
| **最大支持上行帶寬**                                     | 100000                   | /rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/max-supported-bandwidth-ul,SR_DECIMAL64_T,100000 |
| **最大載波數**                                           | 1                        | /rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/max-num-carriers-ul,SR_UINT32_T,1 |
| **最大載波帶寬**                                         | 100000                   | /rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/max-carrier-bandwidth-ul,SR_DECIMAL64_T,100000 |
| **最小載波帶寬**                                         | 10000                    | /rx-arrays[name='CC0_RxArray0_H']/capabilities[1]/min-carrier-bandwidth-ul,SR_DECIMAL64_T,10000 |

##### rx-array : CC0_RxArray1_V

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **行數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray1_V']/number-of-rows,SR_UINT16_T,1 |
| **列數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray1_V']/number-of-columns,SR_UINT16_T,1 |
| **層數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray1_V']/number-of-array-layers,SR_UINT8_T,1 |
| **水平間距**                                               | 0                        | /rx-arrays[name='CC0_RxArray1_V']/horizontal-spacing,SR_DECIMAL64_T,0 |
| **垂直間距**                                               | 0                        | /rx-arrays[name='CC0_RxArray1_V']/vertical-spacing,SR_DECIMAL64_T,0 |
| **方位角**                                               | 0                        | /rx-arrays[name='CC0_RxArray1_V']/normal-vector-direction/azimuth-angle,SR_DECIMAL64_T,0 |
| **天頂角**                                               | 0                        | /rx-arrays[name='CC0_RxArray1_V']/normal-vector-direction/zenith-angle,SR_DECIMAL64_T,0 |
| **最左下元素位置 (x)**                                   | 0                        | /rx-arrays[name='CC0_RxArray1_V']/leftmost-bottom-array-element-position/x,SR_DECIMAL64_T,0 |
| **最左下元素位置 (y)**                                   | 0                        | /rx-arrays[name='CC0_RxArray1_V']/leftmost-bottom-array-element-position/y,SR_DECIMAL64_T,0 |
| **最左下元素位置 (z)**                                   | 0                        | /rx-arrays[name='CC0_RxArray1_V']/leftmost-bottom-array-element-position/z,SR_DECIMAL64_T,0 |
| **極化**                                                  | PLUS_90                 | /rx-arrays[name='CC0_RxArray1_V']/polarisations[p='0']/polarisation,SR_ENUM_T,PLUS_90 |
| **頻段號**                                               | 0                        | /rx-arrays[name='CC0_RxArray1_V']/band-number,SR_UINT16_T,0 |
| **增益修正範圍 (最大)**                                  | 0                        | /rx-arrays[name='CC0_RxArray1_V']/gain-correction-range/max,SR_DECIMAL64_T,0 |
| **增益修正範圍 (最小)**                                  | 0                        | /rx-arrays[name='CC0_RxArray1_V']/gain-correction-range/min,SR_DECIMAL64_T,0 |
| **最大支持上行頻率**                                     | 0                        | /rx-arrays[name='CC0_RxArray1_V']/capabilities[1]/max-supported-frequency-ul,SR_DECIMAL64_T,0 |
| **最小支持上行頻率**                                     | 0                        | /rx-arrays[name='CC0_RxArray1_V']/capabilities[1]/min-supported-frequency-ul,SR_DECIMAL64_T,0 |
| **最大支持上行帶寬**                                     | 100000                   | /rx-arrays[name='CC0_RxArray1_V']/capabilities[1]/max-supported-bandwidth-ul,SR_DECIMAL64_T,100000 |
| **最大載波數**                                           | 1                        | /rx-arrays[name='CC0_RxArray1_V']/capabilities[1]/max-num-carriers-ul,SR_UINT32_T,1 |
| **最大載波帶寬**                                         | 100000                   | /rx-arrays[name='CC0_RxArray1_V']/capabilities[1]/max-carrier-bandwidth-ul,SR_DECIMAL64_T,100000 |
| **最小載波帶寬**                                         | 10000                    | /rx-arrays[name='CC0_RxArray1_V']/capabilities[1]/min-carrier-bandwidth-ul,SR_DECIMAL64_T,10000 |

##### rx-array : CC0_RxArray0_H_RACH

| 屬性                                                       | 值                       |                             |
| ---------------------------------------------------------- |:------------------------ |:--------------------------- |
| **行數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/number-of-rows,SR_UINT16_T,1 |
| **列數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/number-of-columns,SR_UINT16_T,1 |
| **層數**                                                   | 1                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/number-of-array-layers,SR_UINT8_T,1 |
| **水平間距**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/horizontal-spacing,SR_DECIMAL64_T,0 |
| **垂直間距**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/vertical-spacing,SR_DECIMAL64_T,0 |
| **方位角**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/normal-vector-direction/azimuth-angle,SR_DECIMAL64_T,0 |
| **天頂角**                                               | 0                        | /rx-arrays[name='CC0_RxArray0_H_RACH']/normal-vector-direction/zenith-angle,SR_DECIMAL64_T,0 |







## TMA
### TM500 UESIM

| Parameter                            | Value   |
| ------------------------------------ |:------- |
| Duplex                               | TDD     |
| Cell Id                              | 0       |
| Downlink carrier frequency (100 kHz) | 34507.2 |
| System bandwidth (MHz)               | 100     |
| AbsoluteFrequencyPointA (100 kHz)    | 34015.8 |
| AbsoluteFrequencySSB (100 kHz)       | 34507.2 |

### Configure Radio Contexts

| Parameter                          | Value   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|:---------------------------------- |:------- |:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Radio context Id                   | 0       | Radio context Identity (0~47)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Downlink carrier frequency         | 34507.2 | The frequency of the downlink carrier in units of 100 kHz.<br><br>Notes:<br><br>This can be configured as a string representing the decimall floating point value with a maximum of two decimal places allowed.<br><br>E.g.: 35485.20(100KHz) which means the specific value is 3548520KHz after conversion.<br><br>For RF operation, In case of FR2 (high band), the frequency provided for this parameter is the down converted frequency. <br><br>For ORAN operation, it is actual FR2 frequency.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Cell Id                            | 0       | Cell Identity                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| System bandwidth                   | 100     | 100 = 100 MHz (Valid for FR1, FR2), Defines the downlink and uplink bandwith. Use the 'Uplink Bandwidth' parameter if this differs from the downlink bandwith.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| AbsoluteFrequencyPointA (Optional) | True    | Must be called once only per Radio Context and must be used before any UE Context is set (SetUEContext). Associates a Cell with the Radio Context. The Cell must be connected to the Radio Context and be operational.<br><br>The command will attempt to perform a cell search and BCH decode to ensure that the specified cell is visible.<br><br>The command can be re-issued if it fails - the TM500 does not autonomously retry.<br><br>This command is mandatory in all configurations.<br><br><br>In NAS MODE, the command will automatically acquire system information.<br><br>If automatic SI acquisition fails the following error message is displaved: "RRC: System Information acquisition failed".<br><br>Note: At the E500 radio card, only 6 GHz frequencies are supported. To configure the actual qNB Tx freq, please see the parameter 'gNB Downlink carrier frequency' below.                                                                                                                                                                                                                                                                                                                     |
| AbsoluteFrequencyPointA            | 34015.8 | Absolute frequency position of the reference resource block (Common RB 0) in units of 100 kHz. Its lowest subcarrier is also known as Point A.<br><br>If no value is provided, TM500 shall default the value of this parameter to the frequency (in units of 100 kiz) of the lowest subcarmier of the resource block on the bottom edge of the carrier bandwidth (excluding the guard band frequencies).<br><br> Note:==Currently, this parameter is not used in cell search.== This can be configured as a string representing the decimall floating point value with a maximum of two decimal places allowed. E.g.: 35485.20(100KHz) which means the specific value is 3548520KHz after conversion.<br><br>For RF operation, In case of FR2 (high band), the frequency provided for this parameter is the down converted PointA frequency. . For ORAN operation, it is actual FR2 frequency.<br><br>For more details, please refer to Chapter 4 (Page 14, 15) of the document Ka_Band_Converter_User_Guide_47000_412 pdf<br><br>When using spectral inversion using the command NrSetKaBandConverterInfo, this frequency provided for this parameter must not be inverted and should be provided as described above. |
| AbsoluteFrequencySSB               | 34507.2 | Frequency of the SSB to be used for this serving cell in units of 100 ke The frequency provided in this field identfies the position of resource element RE=(subcamier #O) of resource block RB#10 of the SS block.<br><br>For RF operation:<br><br>If no value is provided, TM500 shall work in automatic cell detection mode to automatically detect and search SSB.<br><br>For O-RAN operation:<br><br>If no value is provided, TM500 shall default the value of this parameter to the value specified for 'Downink carrier frequency' parameter.<br><br>Notes:<br><br>This can be configured as a string representing the decimal/ loating point value with a maximum of two decimal places allowed.<br><br>E.g.: 35485.20(100KHz) which means the specific value is 3548520KHz affer conversion.                                                                                                                                                                                                                                                                                                                                                                                                                  |
| gNB Downlink carrier frequency     | 34507.2 | The downlink centre freguency at which the gNB transmits (in units of 100 klz). The phase compensation is performed at this frequency.<br><br>For TDD, If no value is provided, TM500 shall default the value of this parameter to the value specfied for 'Downlink carrier frequency' parameter.<br><br>This parameter is not required if gNB Downlink carrier frequency' is same as being specified in "Downinkcarrier frequency' parameter. Otherwise, this parameter must be provided.<br><br>Note:<br><br>A 'zero' value is ==not supported== for this parameter.<br><br>This can be configured as a string representing the decimal/ floating point value with a maximum of two decimal places allowed.<br><br>E.g.: 35485.20(100KHz) which means the specific value is 3548520KHz after conversion.                                                                                                                                                                                                                                                                                                                                                                                                             |
| Number of antennas                 | 2       | Number of receive antennas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |










## gNB

### Basic configuration

| 參數名稱           | 值                                                                    | NTUST 值                                                              |
| ------------------ | --------------------------------------------------------------------- | --------------------------------------------------------------------- |
| Active_gNBs        | "gNB-OAI"                                                             | "gNB-OAI-2"                                                           |
| Asn1_verbosity     | "none"                                                                | "none"                                                                |
| gNB_ID             | 0xe00                                                                 | 0xe01                                                                 |
| gNB_name           | "gNB-OAI"                                                             | "gNB-OAI-2"                                                           |
| tracking_area_code | 1                                                                     | 100                                                                   |
| plmn_list          | { mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1;}); } | { mcc = 001; mnc = 01; mnc_length = 2; snssaiList = ( { sst = 1;}); } |
| nr_cellid          | 1                                                                     | 1                                                                     |

### Physical parameters

| 參數名稱              | UNH | NTUST |
| --------------------- |:--- |:----- |
| pdsch_AntennaPorts_XP | 2   | 2     |
| pusch_AntennaPorts    | 2   | 2     |
| do_CSIRS              | 1   | 1     |
| do_SRS                | 0   | 0     |
| sib1_tda              | 15  | 15    |


### Service cell configuration

| 參數名稱                           | UNH    | NTUST  |
| ---------------------------------- | ------ |:------ |
| physCellId                         | 1      | 0      |
| n_TimingAdvanceOffset              | 1      | 0      |
| absoluteFrequencySSB               | 630048 | 630048 |
| dl_frequencyBand                   | 78     | 78     |
| dl_absoluteFrequencyPointA         | 626772 | 626772 |
| dl_subcarrierSpacing               | 1      | 1      |
| dl_carrierBandwidth                | 273    | 273    |
| initialDLBWPlocationAndBandwidth   | 1099   | 1099   |
| initialDLBWPsubcarrierSpacing      | 1      | 1      |
| initialDLBWPcontrolResourceSetZero | 11     | 11     |
| initialDLBWPsearchSpaceZero        | 0      | 0      |


### RACH configuration parameters

| 參數名稱                                     | UNH  | NTUST |
| -------------------------------------------- |:---- | ----- |
| prach_ConfigurationIndex                     | 159  | 151   |
| prach_msg1_FDM                               | 0    | 0     |
| preambleReceivedTargetPower                  | -104 | -100  |
| preambleTransMax                             | 7    | 7     |
| powerRampingStep                             | 2    | 3     |
| ra_ResponseWindow                            | 5    | 5     |
| ssb_perRACH_OccasionAndCB_PreamblesPerSSB_PR | 3    | 3     |
| ra_ContentionResolutionTimer                 | 7    | 7     |
| rsrp_ThresholdSSB                            | 19   | 19    |

### SCTP definition

| 參數名稱        | UNH | NTUST |
| --------------- |:--- |:----- |
| SCTP_INSTREAMS  | 2   | 2     |
| SCTP_OUTSTREAMS | 2   | 2     |

### Network interface configuration

| 參數名稱                    | UNH             | NTUST           |
| --------------------------- |:--------------- | --------------- |
| GNB_IPV4_ADDRESS_FOR_NG_AMF | "127.0.0.5"     | "127.0.0.5"     |
| GNB_IPV4_ADDRESS_FOR_NGU    | "127.0.0.18/24" | "127.0.0.18/24" |
| GNB_PORT_FOR_S1U            | 2152            | 2152            |

### MAC/RLC configuration

| 參數名稱                   | UNH         | NTUST       |
| -------------------------- |:----------- | ----------- |
| num_cc                     | 1           | 1           |
| tr_s_preference            | "local_L1"  | "local_L1"  |
| tr_n_preference            | "local_RRC" | "local_RRC" |
| pusch_TargetSNRx10         | 300         | 300         |
| pucch_TargetSNRx10         | 200         | 230         |
| ul_bler_target_upper       | .35         | .35         |
| ul_bler_target_lower       | .15         | .15         |
| pusch_FailureThres         | 100         | 100         |
| ulsch_max_frame_inactivity | 0           | 0           |
| ul_max_mcs                 | 25          | 28          |

### L1 configuration

| 參數名稱             | UNH         | NTUST       |
|:-------------------- | ----------- |:----------- |
| num_cc               | 1           | 1           |
| tr_n_preference      | "local_mac" | "local_mac" |
| prach_dtx_threshold  | 130         | 130         |
| pucch0_dtx_threshold | 80          | 80          |
| pusch_dtx_threshold  | 10          | 10          |
| max_ldpc_iterations  | 10          | 10          |
| tx_amp_backoff_dB    | 20          | 20          |
| L1_rx_thread_core    | 8           | 8           |
| L1_tx_thread_core    | 10          | 10          |
| phase_compensation   | 1           | 1           |

:::info
phase_compensation 在整合上需要注意，DU及RU擇一做即可
:::
### Security configuration

| 參數名稱             | UNH                | NTUST              |
| -------------------- | ------------------ |:------------------ |
| ciphering_algorithms | ( "nea0" )         | ( "nea0" )         |
| integrity_algorithms | ( "nia2", "nia0" ) | ( "nia2", "nia0" ) |
| drb_ciphering        | "yes"              | "yes"              |
| drb_integrity        | "no"               | "no"               |




### FHI 72 Configuration


| 參數名稱     | UNH                                        | NTUST                                      |
| ------------ | ------------------------------------------ |:------------------------------------------ |
| dpdk_devices | ("0000:3b:02.0", "0000:3b:02.1")           | ("0000:3b:02.0", "0000:3b:02.1")           |
| system_core  | 0                                          | 0                                          |
| io_core      | 4                                          | 4                                          |
| worker_cores | (2)                                        | (2)                                        |
| du_addr      | ("00:11:22:33:44:66", "00:11:22:33:44:66") | ("00:11:22:33:44:66", "00:11:22:33:44:66") |
| ru_addr      | ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10") | ("10:70:fd:14:1c:10", "10:70:fd:14:1c:10") |
| mtu          | 1500                                       | 1500                                       |
| Tadv_cp_dl   | 125                                        | 125                                        |
| T2a_cp_dl    | (74, 464)                                  | (259, 470)                                 |
| T2a_cp_ul    | (36, 758)                                  | (125, 1200)                                |