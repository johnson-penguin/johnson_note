---
title: 2x2 4x4 YAML 比較

---

| 參數名稱                             | 2x2 配置值        | 4x4 配置值        |
| ------------------------------------ |:----------------- | ----------------- |
| ORAN YANG model version              | 2                 | 2                 |
| MTU size                             | 1500              | 1500              |
| O-RU configuration mode              | 0                 | 0                 |
| O-DU MAC address                     | 00:11:22:33:44:66 | 00:11:22:33:44:66 |
| M-plane VLAN ID                      | 20                | 20                |
| M-plane DHCP state                   | on                | on                |
| M-plane IP version                   | IPV4              | IPV4              |
| M-plane IP address                   | 10.10.10.2/24     | 10.10.10.2/24     |
| M-plane CallHome Ip address          | 10.10.10.1        | 10.10.10.1        |
| PTP domain number                    | 24                | 24                |
| PTP- G_8275_1- multicast-mac-address | NONFORWARDABLE    | NONFORWARDABLE    |

| CCid                                          | 1       | 1       |
|:--------------------------------------------- |:------- | ------- |
| Ethernet O-RU port                            | ORU1-P1 | ORU1-P1 |
| VLAN ID                                       | 5       | 5       |
| ==CC configuration==                          | 2x2     | 4x4     |
| Traffic type                                  | NR      | NR      |
| Numerology                                    | 1       | 1       |
| Bandwidth                                     | 100     | 100     |
| Alpha value                                   | 0       | 0       |
| Beta value                                    | 0       | 0       |
| DL Carrier Frequency                          | 0       | 0       |
| UL Carrier Frequency                          | 0       | 0       |
| DL compression                                | 9bitBFP | 9bitBFP |
| ==DL (PDSCH) eaxcid list==                    | 0,1     | 0,1,2,3 |
| DL mixed numerology?                          | no      | no      |
| Additional SSB eaxcid list                    | -       | -       |
| UL compression                                | 9bitBFP | 9bitBFP |
| ==UL (PUSCH) eaxcid list==                    | 0,1     | 0,1,2,3 |
| Is PUSCH and RACH traffic on separate eaxcid? | yes     | yes     |
| ==Additional RACH eaxcid list==               | 2,3     | 4,5,6,7 |
| Number of UL streams dedicated to SRS         | -       | -       |
| Eaxcid of first SRS dedicated stream          | -       | -       |
