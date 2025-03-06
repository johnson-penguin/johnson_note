---
title: Unresolved Issues(10/2~10/5)
tags: [BMW Lab., code review]

---

:::success
https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/11653?tab=comments
:::

# General Questions


## src/5gnrsch/sch_utils.c

### 774. Can we have a single table having all the three required components i.e. 5QI, with three columns where first col = 5QiValue,Resource Type and Default Prior level?
:::success

Complete the table

:::










## src/5gnrsch/sch.c
### 1982. Can we call the scheduler specific api here cellCb->api->SchSliceRecfgReq(rrmPolicyOfSlices)?

:::success

Port the contents of 2034 else to 1982

:::
## src/5gnrsch/sch_slice_based.c 
### 211. Dont we need to dealloc and delete the LC information for this UE from sliceCbList?
:::success
coming soon
:::


### 307. since we are only changing the rrmPolicy here(which has been modified) , Can we only pass arg as rrmPolicy (SchRrmPolicyOfSlice) for optimization purpose?
:::success

![](https://hackmd.io/_uploads/BJJhYjGep.png)


We pass in the entire schcellcb, but in the end only the functions related to rrmpolicy are used

>SchCellCb *cellCb

The whole upload is too big, just upload the related content of storedSliceCfg.

:::



### 1432. Why we didnt pass schSliceBasedDlScheduling for retx case as well ?
:::success


comming soon
:::

### 3971. we dont need a separate function for slicebased algo, Can we reuse "calculateEstimateTBSize" which is defined in sch_utils.c?  Now only one difference which is seen is that special case for MAX_PRB = 1. We can introduce that in "calculateEstimateTBSize" because that check will be valid in any scheduling algo
:::success

Also change to "calculateEstimateTBSize" defined in sch_utils.c and add MAX_PRB = 1

:::


### 1631. As mentioned in review comments from sch_utils.c file, if we merge "fiveQiIdxTable" and "fiveQiTable" then 5Qi will be in 0th Index thus dont we need to change this if condition as well?
:::success



(The need trace has not yet been resolved) This part is related to the 5QI table, merging the two tables
![](https://hackmd.io/_uploads/rk0ftq21T.png)

:::

### (?)1737. please change the function name(x)

:::success

改名問題,原因不詳,等待對方回應

:::
### (?)1757. please change the func name(x)

:::success

改名問題,原因不詳,等待對方回應

:::




### 1900. Do we need to do allocation for PDSCH and PDCCH before scheduling?
:::success


延遲記憶體給值時機，最好是確定allocate的時間(待確認位置)


記憶體分配：在[Step3]中，程式碼分配了記憶體給 
cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId-1]，並用於儲存 DlMsgSchInfo 結構的資訊。這個結構包含了PDSCH和PDCCH的相關配置和分配資訊(如下)。這是為了準備儲存和傳輸下行資料和控制資訊的記憶體。

```c= 
typedef struct dlMsgSchedInfo
{
   uint16_t        crnti;
   uint8_t         dciFormatId;
   uint8_t         harqProcNum;
   bool            vrbPrbMapping;
   uint8_t         dlAssignIdx;
   uint8_t         pucchTpc; ///////
   uint8_t         pucchResInd;//////////
   uint8_t         harqFeedbackInd;
   uint16_t        dlMsgPduLen;
   uint8_t         *dlMsgPdu;
   FreqDomainAlloc freqAlloc;
   TimeDomainAlloc timeAlloc;
   uint8_t         numOfTbs;
   TransportBlock  transportBlock[2];
   BwpCfg          bwp;
   PdcchCfg        *dlMsgPdcchCfg;
   PdschCfg        *dlMsgPdschCfg;
}DlMsgSchInfo;
```

循環遍歷切片：在[Step5]中，程式碼透過循環遍歷各個切片來執行調度。每個切片可能具有不同的PDSCH和PDCCH資源分配演算法，因此需要在循環內對它們進行逐一處理。

:::


### 2186. portion after this line, is exactly same as "schSliceBasedDlIntraSliceScheduling". Why cant we directly call this function here?
:::success

If the repeated function is exactly the same as schSliceBasedDlIntraSliceScheduling, it is best to call the function directly for processing. Just change it to a calling function


:::
### 2350. Can we check if the below loop is required in case of retransmission?
:::success

We will write another judgment formula. If the newly transmitted data continues to be executed and the retransmission is continued, continue directly.

:::

### 2364.  Here we are only considering LC with no SNSSAI. What abt those LC's which are assoc with a snssai but has no rrm policy(default snssai's)? Please note that these kind of LCs will have a valid value of snssai value in its context.
:::success

We will add an or judgment. Those with snssai but no rrmpolicy will be treated as default slices.


:::


### 2399. Can we check if the below loop is required in case of retransmission?
:::success
Same as 2350, make a judgment. If the newly transmitted data continues to be executed, the retransmission will continue directly.
:::
### 2404. here instead of starting with algo check, Can we start with if(sliceCb->sharedPrb >= remainingPrb) check and then follow with algo check? because this will reduce the repetition of lines which are common for RR and WFQ.
:::success

Originally, the check started from the algorithm, but the other party wanted to do the remainingprb first, so that the content of remainingprb could be written less once.

We will put the selection of algorithm after prb calculation

:::

### 3979. instead of having this separate case, we can incorporate the case in line no 3996.please check the comment at line no 3996
:::success

Merged with 3996

:::
### 3996. Can we incorporate the special case for maxPrb = 1 here itself?if(maxPRB > MIN_PRB){*estPrb +=1;} AS per my comment above, we can place this check directly in "calculateEstimateTBSize" which is defined in sch_utils.c and use this function instead of "schSliceBasedcalculateEstimateTBSize"
:::success

Use the native function and do not need to add MAX_PRB = 1. Instead, use the method provided by the other party to make modifications, which can reduce the number of lines used.
:::
### 4120. Can we add cmLListDelFrm(casLcInfoList, lcNode) before SCH_FREE
:::success

no problem

:::
### 4247. Can we add cmLListDelFrm(casLcInfoList, lcNode) before SCH_FREE
:::success

no problem

same as 4120
:::

----


















# Multi Ue (Discuss with JOJO)

## src/5gnrsch/sch_slot_ind.c

### ( JOJO )45. not sure why we need to include algo specific header file in this file. Can you please specify the reason for this inclusion?
:::success

In line with the content of E2SM, just like multi ue, someone will push it in the future.

:::

## src/5gnrsch/sch_slice_based.h 


### ( JOJO )79. Can you elaborate on the usage of this and next three variables?
:::success

Ready for multi Ue
:::


## src/5gnrsch/sch_slice_based.c 

### ( JOJO ) 1875. Why do we need this List of UEs? because this function has only one ueId as input. Can you please elaborate for multi ue scenario how the input arguments of this function will change.?
:::success

It seems to be the same as the previous problem. The uploaded version does not have a multi ue version, which causes them to have doubts about the muti ue related framework. In the future, they will push the muti ue code. This part is currently suitable for cooperating with another person's experiment.


:::

### ( JOJO )1448. As mentioned in one of my comments in function schSliceBasedDlScheduling() Line 1875, this function has been called per UE(UE getting picked from cellCb->ueToBeScheduled List). How this function will cater multiple UE so that the input argument contain list of UEs so that further scheduling can be done for multiple UE?
:::success

Members of our laboratory are doing research on mutil ue. This part is to cooperate with the functions of mutil ue, but it has not been pushed up yet.



:::

### ( JOJO )2658. Can you elaborate the usage of this variable?
:::success

for multi ue
:::













