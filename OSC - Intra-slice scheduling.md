---
title: OSC - Intra-slice scheduling
tags: [code交接, Trace]

---

| Item                 | Step             | Status               |
| -------------------- | ---------------- | ---------------------|
| [OSC - schSliceBasedDlScheduling - (high level)](https://hackmd.io/@Johnson-72/SyA9K85q3)| 1    | :heavy_check_mark:|
| [OSC - Intra-slice scheduling - (high level)](https://hackmd.io/@Johnson-72/ryE14SIih)| 2    | :heavy_check_mark: |
| [Final scheduling - (high level)]() | 3    |:heavy_check_mark: |
| [Multi-threaded version]()                         | 4    |  |
| [Run , Compile , Performance]()                    | 5    |  |
| [Muti UE]()                                        | 6    |  |
| [OSC - schSliceBasedDlScheduling - (in detail)]()  | 7    |  |
| [OSC - Intra-slice scheduling - (in detail)]()     | 8    |  |
| [Final scheduling - (in detail)]()                 | 9    |  |



### Flow Chart



### Key Parameter

- Pointer to Cell Control Block
- Current Slot timing info
- PDCCH Slot timing info
- Number of PDSCH Symbols
- Max FreePRB Block
- Pointer to Slice Control Block
- UE DL New Transmission LL

      
***


:::success
```c=

uint8_t schSliceBasedDlIntraSliceScheduling
        (
                            SchCellCb *cellCb, 
                            SlotTimingInfo pdcchTime,
                            uint8_t pdschNumSymbols, 
                            uint16_t *totalRemainingPrb, 
                            uint16_t maxFreePRB, 
                            SchSliceBasedSliceCb *sliceCb,
                            CmLListCp *ueDlNewTransmission
                                                        )
{   
    ...
    ...
    }

```
:::
***

## 依照3-GPP對於rrmpolicy定義進行設定

:::info

```c=

   /* Calculate the slice PRB quota according to RRMPolicyRatio and MaxFreePRB */
   sliceCb->dedicatedPrb = (uint16_t)(
       (
           (
            sliceCb->rrmPolicyRatioInfo.dedicatedRatio)*(maxFreePRB)
           )/100);

   sliceCb->prioritizedPrb = (uint16_t)(
       (
           (
            sliceCb->rrmPolicyRatioInfo.minRatio - 
            sliceCb->rrmPolicyRatioInfo.dedicatedRatio
            )*(maxFreePRB))/100);


   sliceCb->sharedPrb = (uint16_t)(
       (
           (
            sliceCb->rrmPolicyRatioInfo.maxRatio - 
            sliceCb->rrmPolicyRatioInfo.minRatio
            )*(maxFreePRB))/100);
  

   minimumPrb = sliceCb->dedicatedPrb + sliceCb->prioritizedPrb;


```
:::


:::warning
```c=

   while(ueNode)
   {
      ueId = *(uint8_t *)(ueNode->node);
      ueCb = &cellCb->ueCb[ueId-1];
      ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;

      /* Sum the weight of each UE */
      totalUeWeight += ueSliceBasedCb->weight;
   
      /* Update the requested BO of each LC in current slice */
      schSliceBasedUpdateLcListReqBo(&sliceCb->lcInfoList[ueId-1], ueCb, DIR_DL);
      ueNode = ueNode->next;
   }

```
:::
>Q1 : what is weight mean
>加權

>Q2 : node router

***

## 演算法選擇


### RR演算法與其報錯訊息反應（high level）

:::danger

```c=
if(sliceCb->algorithm == RR)
   {
      if(minimumPrb != 0)
      {
         remainingPrb = minimumPrb;            
         schSliceBasedRoundRobinAlgo(cellCb, ueDlNewTransmission, 
                                     sliceCb->lcInfoList, 
                                    pdschNumSymbols, 
                                     &remainingPrb, 
                                     sliceCb->algoMethod, 
                                     NULLP);
      }

      sliceCb->allocatedPrb = minimumPrb - remainingPrb;

#ifdef SLICE_BASED_DEBUG_LOG
      DU_LOG("\nDennis  -->  SCH Intra-Slice Result : [SST: %d, Allocated PRB: %d, Unallocated PRB: %d,Algo: RR]", 
             sliceCb->snssai.sst, 
             sliceCb->allocatedPrb, remainingPrb);
#endif
   }

```
:::

**Schematic**

Three strokes P1, P2 and P3, 
which take 5, 2 and 3 milliseconds respectively to complete their work.

![](https://hackmd.io/_uploads/H1qIzf9in.png)
![](https://hackmd.io/_uploads/ByqPMGqsh.png)


>Q1 : minimumPrb how to get value

### RR演算法實現（low level）- schSliceBasedRoundRobinAlgo()

to be continue
***


### WFQ演算法與其報錯訊息反應（high level）

:::success

```c=

else if(sliceCb->algorithm == WFQ)
         {
            /* Sort the UE list in terms of the weight */
            /* This function should be moved to schSliceBasedDlScheduling() when go through the UE list (for Jojo)*/
            schSliceBasedSortUeByWeight(
                cellCb, 
                ueDlNewTransmission, 
                totalUeWeight);

            if(minimumPrb != 0)
            {
               remainingPrb = minimumPrb;
                
               schSliceBasedWeightedFairQueueAlgo(
                   cellCb, 
                   ueDlNewTransmission, 
                   sliceCb->lcInfoList, 
                   pdschNumSymbols, 
                   &remainingPrb, 
                   sliceCb->algoMethod, NULLP);
            }

            sliceCb->allocatedPrb = minimumPrb - remainingPrb;

   #ifdef SLICE_BASED_DEBUG_LOG
            DU_LOG("\nDennis  -->  SCH Intra-Slice Result : \
            [SST: %d, Allocated PRB: %d, Unallocated PRB: %d, Algo: WFQ]"\
        , sliceCb->snssai.sst, sliceCb->allocatedPrb, remainingPrb);      
```
:::
**Schematic**
EX1:
Assume that there are three users, each with weights 
w1, w2 and w3 respectively. 
>w1 = 1
>w2 = 2
>w3 = 3

the total bandwidth is B.
>B = 30

Calculate the weight ratio for each user: 
r1 = w1 / (w1 + w2 + w3) = 1/6
r2 = w2 / (w1 + w2 + w3) = 2/6
r3 = w3 / (w1 + w2 + w3) = 3/6

Calculate the amount of bandwidth each user receives: 

b1 = r1 * B = 1/6 * 30 = 5
b2 = r2 * B = 2/6 * 30 = 10
b3 = r3 * B = 3/6 * 30 = 15

---

EX2:
>w1 = 1/2
>w2 = 1/4
>w3 = 1/4
>>w1 : w2 : w3 = 2 : 1 : 1

>D1 = 4 bit
>D2 = 6 bit
>D3 = 8 bit

First  Cycle  =>    D3D2D1D1
Second Cycle  =>    D3D2D1D1 
>D1 already been assigned, and D2,D3 remain 4,8 bit

Third  Cycle   =>    D3D2
Fourth  Cycle  =>    D3D2
..
..
..
Sixth  Cycle  =>    D3D2

>D2 already been assigned, and D3 remain 2 bit

Final D3D3


![](https://hackmd.io/_uploads/ByCpAyTsh.png)
計算每個用戶的權重比率：
r1 = w1 / (w1 + w2 + w3) = 1 / (1 + 2 + 3) = 1/6 ≈ 0.1667
r2 = w2 / (w1 + w2 + w3) = 2 / (1 + 2 + 3) = 2/6 ≈ 0.3333
r3 = w3 / (w1 + w2 + w3) = 3 / (1 + 2 + 3) = 3/6 = 0.5

計算每個用戶獲得的帶寬：
b1 = r1 * 總帶寬 = 0.1667 * 30 ≈ 5
b2 = r2 * 總帶寬 = 0.3333 * 30 ≈ 10
b3 = r3 * 總帶寬 = 0.5 * 30 = 15

確保總帶寬不超過30，並根據權重比率來分配帶寬：
b1 = 5
b2 = 10
b3 = 15

### WFQ演算法實現（low level）- schSliceBasedWeightedFairQueueAlgo()

to be continue


### 演算法不支持情況

```c=
   else
   {
      DU_LOG("\nDennis  -->  In schSliceBasedDlIntraSliceScheduling() : Invalid Scheduling Algorithm");
      return;
   }
```

## Rioritized Resource and Dedicated Resource


:::info


```c=
   /* Follow the rules of prioritized resource and dedicated resource */
   if(sliceCb->allocatedPrb > sliceCb->dedicatedPrb)
   {
      *totalRemainingPrb = *totalRemainingPrb - sliceCb->allocatedPrb;
   }
   else
   {
      *totalRemainingPrb = *totalRemainingPrb - sliceCb->dedicatedPrb;
   }

```

>   if(sliceCb->allocatedPrb > sliceCb->dedicatedPrb)

It means that the slice has been allocated additional resources, possibly based on ==its priority or other factors==. In this case, the program subtracts the amount of resources already allocated to the slice from the total Remaining Available Resources (totalRemainingPrb) to reflect the allocated resources.

>   if(sliceCb->allocatedPrb < sliceCb->dedicatedPrb)

If a slice has not been given more than the amount of dedicated resources it needs, the slice's resource allocation may ==be constrained and it has been given only the amount of dedicated resources== it needs. 

In this case, the program subtracts the amount of dedicated resources required for the slice from the total amount of remaining available resources to reflect the resources received by the slice.

This process ensures that resources are allocated appropriately and that each slice is allocated according to its priority and dedicated resource requirements. In the end, totalRemainingPrb will reflect the total number of remaining resources available to other slices.


***




如果某個切片（sliceCb）已經獲得的資源量（allocatedPrb）大於該切片所需的專用資源量（dedicatedPrb），則表示該切片獲得了額外的資源，可能是根據其優先級或其他因素。在這種情況下，程式會將剩餘的可用資源總數（totalRemainingPrb）減去該切片已獲得的資源量，以反映已分配的資源。

如果某個切片獲得的資源量沒有超過其所需的專用資源量，則該切片的資源分配可能是受限的，只獲得了其所需的專用資源量。在這種情況下，程式會將剩餘的可用資源總數減去該切片所需的專用資源量，以反映該切片獲得的資源。

這樣的處理確保了資源的合理分配，確保每個切片都按照其優先級和專用資源的要求進行資源分配。最終，totalRemainingPrb將反映剩餘可用於其他切片的資源總數。
:::
