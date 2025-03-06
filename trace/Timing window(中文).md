---
title: Timing window(中文)
tags: [BMW Lab.]

---

[ETSI TS 103 859 V7.0.2](https://www.etsi.org/deliver/etsi_ts/103800_103899/103859/07.00.02_60/ts_103859v070002p.pdf)

# Reference Points



![](https://hackmd.io/_uploads/S1DsSGj0n.png)

- 下行傳輸延遲：T12min / T12max

- - T12min：從O-DU發射接口（R1）到O-RU接收接口（R2）的最小傳輸延遲，也就是傳輸過程中用時最少的一個或多個bit的傳輸時長。 

- - T12max：從O-DU發射接口（R1）到O-RU接收接口（R2）的最大傳輸延遲，也就是傳輸過程中用時最多的一個或多個bit的傳輸時長。 

- 上行傳輸延遲：T34min / T34max

- - T34min：從O-RU發射接口（R3）到O-DU接收接口（R4）的最小傳輸延遲，也就是傳輸過程中用時最少的一個或多個bit的傳輸時長。 

- - T34max：從O-RU發射接口（R3）到O-DU接收接口（R4）的最大傳輸延遲，也就是傳輸過程中用時最多的一個或多個bit的傳輸時長。





傳輸延遲僅包括從一個bit離開發送方（R1/R3）到在接收方（R2/R4）接收的時間。

在以太網傳輸網絡中，由於交換延遲（數據包延遲變化(Packet Delay Variation):PDV，傳輸路徑的延遲值），傳輸延遲可能不是恆定的。為了說明這一點，應使用一個範圍來考慮傳輸延遲，也就是上邊界和下邊界。


由於要保證air-interface傳輸時間，必須固定Ra處的定時。因此，在eCPRI模型中，Ra被用作延遲管理的參考點。其他參考點處的發送和接收都相對於Ra進行測量，並得到以下參數：


![](https://hackmd.io/_uploads/S1DsSGj0n.png)
![](https://hackmd.io/_uploads/SkQ6SGoRn.png)

- 在下行方向： 
- - T1a：測量從O-DU發射接口（R1）到通過空口發送（即O-RU天線接口Ra）的時延，R1到Ra的最短時間T1amin，R1到Ra的最長時間T1amax 

- - T2a：測量從O-RU接收接口（R2）到通過空口發送（即O-RU天線接口Ra）的時延，R2到Ra的最短時間T2amin，R2到Ra的最長時間T2amax，T2amin和T2amax反映了O-RU內部的下行處理時延 

- 在上行方向： 
- - Ta4：測量從O-RU天線接口（Ra）到O-DU接收接口（R4）的時延，Ra到R4的最短時間Ta4min，Ra到R4的最長時間Ta4max 
- - Ta3：測量從O-RU天線接口（Ra）到O-RU發射接口（R3）的時延，Ra到R3的最短時間Ta3min，Ra到R3的最長時間Ta3max，Ta3min和Ta3max反映了O-RU內部的上行處理時延


>在鏈路的兩端使用公共時間刻度。出於時延要求管理的目的，O-DU和O-RU之間的S平面測量信號的相對時間誤差應在3µs（±1.5微秒）的限制範圍內。
---
# O-DU timing window

U平面O-DU傳輸窗（T1amax-T1amin）是基於O-RU接收窗和最大傳輸變化，由以上延遲關係定義的。沒有定義從O-DU傳輸的確切時間。相反，定義的是U平面O-DU傳輸的上下邊界，O-DU必須在邊界內完成傳輸操作。時間窗僅表示由於O-RU和傳輸的限製而作用在O-DU上的數學邊界。

基於對O-DU，傳輸和O-RU中任意兩個的了解，可以定義對另一個的限制條件。然而，通常會基於設備來預定義O-RU的限制條件，並且傳輸是整個網絡目標的一部分。 根據延遲關係計算的時間窗，必須大於或等於O-DU需要的實際最大傳輸時間（），以便為一個symbol傳輸所有數據。

也就是說，窗口必須足夠大，即便在最壞的情況下，O-DU也能在窗口內完成傳輸。其中，在窗口內，O-DU傳輸（例如，開始，中間，結束），以及O-DU傳輸消耗的時間，則是O-DU的設計問題。
![](https://hackmd.io/_uploads/S1DsSGj0n.png)

![](https://hackmd.io/_uploads/HkUzWUi0h.png)
Reception Window ≥ (Transmission Window + Transport Variation)
接收窗 ≥ (發送窗 + 傳輸變化)



![](https://hackmd.io/_uploads/BJp2CHiA3.png)

- 在下行中，O-DU的發送時間，不應早於T1amax，如果T1amax大於(T2amax+T12min)，則O-DU發送的最早部分數據，O-RU可能接收不到，所以必須滿足T1amax ≤ ( T2amax + T12min) 
>假如(T2amax+T12min) = 210us
>如果你的接收時間大於210us的話(220us),那前10us進來的資料便不會收到,因此你會遺失最早進來的訊息


- 在下行中，O-DU的發送時間，不應晚於T1amin，如果T1amin小於(T2amin+T12max)，則O-DU發送的最晚部分數據，O-RU可能接收不到，所以必須滿足T1amin ≥ (T2amin + T12max) 
>假如(T2amin+T12max) = 210us
>如果你的接收時間小於210us的話(190us),那後20us進來的資料將不會收到,因此你會遺失最後進來的訊息


- 在上行中，O-DU的接收時間，不會早於Ta4min，(Ta3min+T34min)是O-RU發送數據最早到達O-DU的時間，如果Ta4min大於(Ta3min+T34min)，則O-RU發送的最早部分數據，O-DU可能接收不到，所以必須滿足Ta4min ≤ (Ta3min + T34min) 

- 在上行中，O-DU的接收時間，不會晚於Ta4max，(Ta3max+T34max)是O-RU發送數據最晚到達O-DU的時間，如果Ta4max小於(Ta3max+T34max)，則O-RU發送的最晚部分數據，O-DU可能接收不到，所以必須滿足Ta4max ≥ (Ta3max + T34max)

![](https://hackmd.io/_uploads/ByqkSOjAh.png)

- t_DL= 0表示天線端口（即空口）處的DL定時，也就是參考點Ra 

- T1a_max_cp_dl（綠色）表示O-DU最早發送下行C平面消息時，相對於Ra提前的時間，也就是O-DU下行C平面發送窗的開始 

- T1a_min_cp_dl（綠色）表示O-DU最晚發送下行C平面消息時，相對於Ra提前的時間，也就是O-DU下行C平面發送窗的結束 

- 所以T1a_max_cp_dl和T1a_min_cp_dl中間的時間就是O-DU下行C平面的發送窗。

- T1a_max_up（藍色）表示O-DU最早發送下行U平面消息時，相對於Ra提前的時間，也就是O-DU下行U平面發送窗的開始 

- T1a_min_up（藍色）表示O-DU最晚發送下行U平面消息時，相對於Ra提前的時間，也就是O-DU下行U平面發送窗的結束 

- 所以T1a_max_up和T1a_min_up中間的時間就是O-DU下行U平面的發送窗。 

- T2a_max_cp_dl（綠色）表示O-RU最早接收下行C平面消息時，相對於Ra提前的時間，也就是O-RU下行C平面接收窗的開始 

- T2a_min_cp_dl（綠色）表示O-RU最晚接收下行C平面消息時，相對於Ra提前的時間，也就是O-RU下行C平面接收窗的結束 

- 所以T2a_max_cp_dl和T2a_min_cp_dl中間的時間就是O-RU下行C平面的接收窗。