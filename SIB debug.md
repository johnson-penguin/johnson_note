---
title: SIB debug

---

[toc]
## Topology
:::danger
NO air interface just only uses SFP+
:::

![image](https://hackmd.io/_uploads/S1_e-R2Jyg.png)

- TM500 server
![image](https://hackmd.io/_uploads/r1vWb0ny1g.png)

## Status

DU can send MIB/SIB but TM500 can't decode
![image](https://hackmd.io/_uploads/r1JY6v5kyg.png)

```bash=
message mib : {
    systemFrameNumber '110011'B,
    subCarrierSpacingCommon scs30or120,
    ssb-SubcarrierOffset 12,
    dmrs-TypeA-Position pos2,
    pdcch-ConfigSIB1 {
        controlResourceSetZero 11,
        searchSpaceZero 0
    },
    cellBarred notBarred,
    intraFreqReselection notAllowed,
    spare '0'B
}
```
The parameters ==ControlResourceSetZero== and ==SearchSpaceZero== determine the search space of PDCCH

**controlResourceSetZero = 11 :** Indicates the control resource set CORESRT used by SIB1, including the ==number of continuous RBs== in the **frequency domain** and the ==number of continuous symbol symbols== in the time domain.



![image](https://hackmd.io/_uploads/BJjxMr211x.png)

![image](https://hackmd.io/_uploads/ryW-8B3yJl.png)

**searchSpaceZero = 0 :** Indicates the PDCCH monitoring position (Monitoring Occasion) in the SIB1 public search space
![image](https://hackmd.io/_uploads/HJVBPL3yye.png)

---

## MIB decod successful
![image](https://hackmd.io/_uploads/SJx3uH3JJe.png)

![image](https://hackmd.io/_uploads/SklBFH2k1x.png)
## DCI already send
[OAI gNB log](https://drive.google.com/file/d/1Mq3mAr2wqqPEkzto4ZKNqgFz0tKleJYF/view?usp=sharing)
```c=
[0m [34m[MAC]   precoderGranularity: 0
[0m [34m[MAC]   numDlDci: 1
[0m [32m[PHY]   Command line parameters for the UE: -C 3450720000 -r 273 --numerology 1 --ssb 1518
```
## TMA reveive SIB configuration is same as gNB
- TMA
![image](https://hackmd.io/_uploads/B1tw7I2y1l.png)

- OAI gNB
![image](https://hackmd.io/_uploads/SJnLQ8n1Je.png)




## Parameters for the UE
```c=
 [0m [32m[PHY]   Command line parameters for the UE: -C 3450720000 -r 273 --numerology 1 --ssb 1518
```


| Parameters   | value      |                      |
| ------------ |:---------- |:-------------------- |
| -C           | 3450720000 | dl_CarrierFreq       |
| -r           | 273:       | N_RB_DL              |
| --numerology | 1          | scs                  |
| --ssb        | 1518       | ssb_start_subcarrier |


### ARFCN turn to Frequency

>ARFCN =  629952 ==> 3450.720 Mhz [(5G NR ARFCN calculator)](https://5g-tools.com/5g-nr-arfcn-calculator/)


## Compare different log
- OAI(BBU) + TM500
```c=
[0m [34m[MAC]   numDlDci: 1
```
- OAI(BBU) + Jura
```c=
[0m[32m[NR_MAC]   UE 6d35: 177.8 Generating RA-Msg2 DCI, RA RNTI 0x10b, state 1, CoreSetType 0, RAPID 0
```
- OAI(BBU) + Jura
```c=
[0m[32m[NR_MAC]   UE 3bf5: 492.8 Generating RA-Msg2 DCI, RA RNTI 0x10b, state 1, CoreSetType 0, RAPID 23
```
- OAI(split) + Liteon
```c=
[0m[32m[NR_MAC]   [gNB 0][RAPROC] CC_id 0 Frame 744, slotP 7: Generating RA-Msg2 DCI, rnti 0x10b, state 1, CoreSetType 0
```

## Coreset 
```c=
 [0m [34m[MAC]   Coreset : BWPstart 112, BWPsize 48, SCS 1, freq ff, , duration 1
 [0m [34m[MAC]   BWPSize: 48
 [0m [34m[MAC]   BWPStart: 112
 [0m [34m[MAC]   SubcarrierSpacing: 1
 [0m [34m[MAC]   CyclicPrefix: 0
 [0m [34m[MAC]   StartSymbolIndex: 0
 [0m [34m[MAC]   DurationSymbols: 1
```
![image](https://hackmd.io/_uploads/B1sblvn11x.png)



| Parameters         |                                                                |
|:------------------ | -------------------------------------------------------------- |
| CceRegMappingType: | Control Resource Element (CCE) mapping type.                   |
| RegBundleSize:     | Register bundle size, which affects PDCCH resource allocation. |
| InterleaverSize:   | Interleaver size, used for PDCCH encoding.                     |
| ShiftIndex:        | offset index, used to adjust the resource mapping of PDCCH.    |
