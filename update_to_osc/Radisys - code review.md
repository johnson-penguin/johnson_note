---
title: Radisys - code review
tags: [BMW Lab., code review]

---

:::success
https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/11653?tab=comments
:::
# Discuss (1)

## src/5gnrsch/sch_fcfs.c 
### (x)184. Cosmetic change: Function name starts with small letter alphabet. Can you please correct it to schFcfsSliceCfgReq
:::success

![](https://hackmd.io/_uploads/SJj_OywCh.png)

![](https://hackmd.io/_uploads/SygAIJxgT.png)

:::


### (x)203. same comment as 184
:::success

![](https://hackmd.io/_uploads/H1hlAJwR2.png)
![](https://hackmd.io/_uploads/ByVAUkglp.png)
:::
## src/5gnrsch/sch_slice_based.c 

### (x)83. what is the purpose of this variable because it is not used anywhere?
:::success

For experiment, we will remove.

![](https://hackmd.io/_uploads/SJHQPklgT.png)

:::

### (~)211. Dont we need to dealloc and delete the LC information for this UE from sliceCbList?
:::success
Change the order of step3 and step4

Leave resource allocation until after confirming whether resources are available
:::



### (x)212. Can we acquire the Algorithm method and algorithm in SliceCfgReq/RecfgReq from DUAPP?
:::success

Yes, but it is currently only used for testing to prove that we have designed a framework in which different slices can use different algorithms. We will develop it on DU.APP when we have the opportunity in the future.

:::

### (x)239. if and else case have no difference. what is the purpose of this condition?
:::success

This part of the content was originally just an experiment. Functionally, I gave it the RR algorithm for the 0th slice, and I gave it the WFQ algorithm for the 1th slice. This is only a temporary framework for processing the algorithm of each slice. When the algorithm is increased in the future, this framework can be expanded.


:::
### (x)260. since this counter will determine the size of threadArg array. Why dont we have a max size check of threadCounter to avoid any memory corruption?
:::success

We will limit a value. The main purpose is to avoid the situation where slice is larger than thread, which will lead to insufficient execution threads and cause errors.

Give schSpcCell->sliceCbList.count a numerical value to limit, and use conditional expressions to make judgments.
![](https://hackmd.io/_uploads/r1fswTDl6.png)

By specifying the maximum thread value, you can avoid using too many threads when creating a slice or exceeding the number of threads in the hardware itself.
![](https://hackmd.io/_uploads/HJo3vavxp.png)


:::

### (x)307. since we are only changing the rrmPolicy here(which has been modified) , Can we only pass arg as rrmPolicy (SchRrmPolicyOfSlice) for optimization purpose?
:::success

![](https://hackmd.io/_uploads/BJJhYjGep.png)


我們傳整個schcellcb進來，但最後只用到rrmpolicy相關的功能
>SchCellCb *cellCb

整個傳太肥大了，傳storedSliceCfg相關內容就好
::::::

### (x)310. we can rename it as sliceCbList?
:::success
before
![](https://hackmd.io/_uploads/Sk1IoWgga.png)
after
![](https://hackmd.io/_uploads/HyklhZxl6.png)


Done
:::
### (x)1344. Can you highlight where this variable is getting set?
:::success

Just for experiment,we will remove this
![](https://hackmd.io/_uploads/r1RWaeeg6.png)

:::
### 1432. Why we didnt pass schSliceBasedDlScheduling for retx case as well ?
:::success


comming soon
:::
### ( JOJO )1448. As mentioned in one of my comments in function schSliceBasedDlScheduling() Line 1875, this function has been called per UE(UE getting picked from cellCb->ueToBeScheduled List). How this function will cater multiple UE so that the input argument contain list of UEs so that further scheduling can be done for multiple UE?
:::success

Members of our laboratory are doing research on mutil ue. This part is to cooperate with the functions of mutil ue, but it has not been pushed up yet.



:::
### (x)1463. Is it correct that resetting of SliceCbList will happen only in case when a slot ind is received for a UE which doesnt have a BO ind(or a new DL data)? It means if theres a continuous pump of data for UE then allocatedPrb is never resetted?
:::success

The function of else here is due to our experiments. It was found that in the absence of else, the UE will automatically be assigned to the last assigned number of prbs, which will cause the prb to be assigned to the UE even if there is no traffic. Case


:::
### (x)1504. why this function is not enabled?
:::success

The original functional test of DL/UL was normal, but due to time issues, subsequent updates and changes were only made to the DL part, and the UL part has not yet been completed and is still being prepared.


:::
### (x)1580. why do we need this because in the above while loop we directly use cmLListDelFrm for all nodes for this list.?
:::success

A part already delete the data why we need B part do the same thing again?

A:
```c=
 while(lcInfoNode)
      {
         lcInfoNext = lcInfoNode->next;
         SCH_FREE(lcInfoNode->node, sizeof(SchSliceBasedLcInfo));
         cmLListDelFrm(&sliceCb->lcInfoList[ueId-1], lcInfoNode);
         SCH_FREE(lcInfoNode, sizeof(CmLList));
         
         lcInfoNode = lcInfoNext;
      }
```

B:
```c=
      cmLListDeleteLList(&sliceCb->lcInfoList[ueId-1]);
```

We will remove B part

:::
### (x)1588. Is it correct understandind that here we are only adding those LC(s) whose are associated with a snssai which has a rrm policy(i.e. dedicated snssai)? This means LC(s) which has association with SNSSAI without RRM Policy (default slice) or LC(s) with no snssai are not included in "sliceCbList"?
:::success


Q1 : Yes
Q2 : Yes

:::

### (x)1603. Why do we need to do sorting of LC based on priority here because we need sorting only in case of WFQ, therefore if we sort it here wont that sort will be applicable in RR case also? Also I can see that sorting is happening in "schSliceBasedWeightedFairQueueAlgo"?
:::success

We will remove it 
![](https://hackmd.io/_uploads/HkWrhWPea.png)
after
![](https://hackmd.io/_uploads/ryCHn-ve6.png)


:::
### (~~)1631. As mentioned in review comments from sch_utils.c file, if we merge "fiveQiIdxTable" and "fiveQiTable" then 5Qi will be in 0th Index thus dont we need to change this if condition as well?
:::success



(need trace 尚未解決) be fixed with 774 這部分與5QI的表格有關，將兩個table合併
![](https://hackmd.io/_uploads/rk0ftq21T.png)

:::
### (x)1655. we can name it startNode because outerNode is confusing?
:::success

OK, We will rename that
![](https://hackmd.io/_uploads/HJY37xxg6.png)

:::
### (x)1680. Is it correct that here LC's are sorted in ascending order of priority? If it is ascending order then while scheduling isn't the least priority LC are being picked first?
:::success



Q1:YES

Q2: Our priority list is based on the 5QI. 

According to the description of 3GPP TS 23.501 5.7.3.3 Priority Level (Priority Level related to 5G QoS features indicates the priority of scheduling resources in QoS Flows, and the lowest Priority Level value corresponds to the highest priority.), so we use ascending order



:::

### (?)1737. please change the function name(x)

:::success

改名問題,原因不詳,等待對方回應

:::
### (?)1757. please change the func name(x)

:::success

改名問題,原因不詳,等待對方回應

:::

----
# Discuss (2)

## src/5gnrsch/sch_slice_based.c 

### ( JOJO ) 1875. Why do we need this List of UEs? because this function has only one ueId as input. Can you please elaborate for multi ue scenario how the input arguments of this function will change.?
:::success

It seems to be the same as the previous problem. The uploaded version does not have a multi ue version, which causes them to have doubts about the muti ue related framework. In the future, they will push the muti ue code. This part is currently suitable for cooperating with another person's experiment.

:::
### 1900. Do we need to do allocation for PDSCH and PDCCH before scheduling?
:::success
與step4對調

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
### (x)1917. Can we have a validity check for maxFreePrb ?
:::success

Make an if loop to determine whether the condition is 0, and if not, return it directly. Memory needs to be cleared before returning. If there is a value, it means there are remaining resources, then execution continues.
![](https://hackmd.io/_uploads/rkH2-XPg6.png)



:::


### (x)2027. Are we using this variable?
:::success

No, we will remove it.

before

![](https://hackmd.io/_uploads/BJ3aKsIxa.png)

after
![](https://hackmd.io/_uploads/BJf15jLea.png)

在schSliceBasedDlIntraSliceScheduling當中似乎完全沒用到crnti，直接remove
:::
### (x)2043. Can we have a availability check for mininumPrb based on totalRemainingPrb?
:::success

It can be circumvented after improvements in 1917

:::
### (x)2186. portion after this line, is exactly same as "schSliceBasedDlIntraSliceScheduling". Why cant we directly call this function here?
:::success

If the repeated function is exactly the same as schSliceBasedDlIntraSliceScheduling, it is best to call the function directly for processing. Just change it to a calling function

:::
### (x)2350. Can we check if the below loop is required in case of retransmission?
:::success

We will write another judgment formula. If the newly transmitted data continues to be executed and the retransmission is continued, continue directly.
![](https://hackmd.io/_uploads/SJgL7REKga.png)
![](https://hackmd.io/_uploads/BkPE0VKlp.png)

:::

### 2364.  Here we are only considering LC with no SNSSAI. What abt those LC's which are assoc with a snssai but has no rrm policy(default snssai's)? Please note that these kind of LCs will have a valid value of snssai value in its context.
:::success

We will add an or judgment. Those with snssai but no rrmpolicy will be treated as default slices.

:::warning
關於有沒有rrmpolicy的部份我找不太到參數去做判斷,我有找過lccfg或者dlcfg之類的設定,但好像都只有提到snssai並沒有提到關於lc的設定,我不確定這部份我要怎麼去做條件判斷
:::success

沒有RRM policy卻有snssai的LC不需要額外做處理嗎。增加or判斷，有snssai但沒有rrmpolicy者當作default slice 處理
:::


### (x)2399. Can we check if the below loop is required in case of retransmission?
:::success

Same as 2350
![](https://hackmd.io/_uploads/BJKmlHYlT.png)

![](https://hackmd.io/_uploads/r1zVeHtx6.png)

同2350，做個判斷，假如新傳輸的資料繼續執行，重傳則直接continue

:::
### (x)2404. here instead of starting with algo check, Can we start with if(sliceCb->sharedPrb >= remainingPrb) check and then follow with algo check? because this will reduce the repetition of lines which are common for RR and WFQ.
:::success

Originally, the check started from the algorithm, but the other party wanted to do the remainingprb first, so that the content of remainingprb could be written less once.

We will put the selection of algorithm after prb calculation




原本是從演算法開始檢查，但對方希望remainingprb先做，這樣能少寫一次remainingprb的內容
:::

### ( JOJO )2658. Can you elaborate the usage of this variable?
:::success

for multi ue
:::
### (x)3346. as per my understanding, this is for defaultslice which will not have rrmPolicy. Can we rename it to SliceBasedPrbAllocDefaultSlice
:::success

Yes , we will rename.


:::
### (x)3971. we dont need a separate function for slicebased algo, Can we reuse "calculateEstimateTBSize" which is defined in sch_utils.c?  Now only one difference which is seen is that special case for MAX_PRB = 1. We can introduce that in "calculateEstimateTBSize" because that check will be valid in any scheduling algo
:::success

Also change to "calculateEstimateTBSize" defined in sch_utils.c and add MAX_PRB = 1


另外換成 sch_utils.c 中定義的「calculateEstimateTBSize」然後補上MAX_PRB = 1
:::
### (xx)3979. instead of having this separate case, we can incorporate the case in line no 3996.please check the comment at line no 3996
:::success

Merged with 3996


與3996合併處理
:::
### (xx)3996. Can we incorporate the special case for maxPrb = 1 here itself?if(maxPRB > MIN_PRB){*estPrb +=1;} AS per my comment above, we can place this check directly in "calculateEstimateTBSize" which is defined in sch_utils.c and use this function instead of "schSliceBasedcalculateEstimateTBSize"
:::success

使用原生function 並且不需要額外加MAX_PRB = 1 ，改採用對方提供的做法做修改，可以減少行數的使用
![](https://hackmd.io/_uploads/HJozBVFl6.png)

:::
### (x)4045. Can we check if this variable is used?
:::success
we will remove that.

before
![](https://hackmd.io/_uploads/HJsmMMxl6.png)

after
![](https://hackmd.io/_uploads/BJSwzMeg6.png)

在RR演算法內似乎沒有被使用，直接砍remove
:::
### (x)4109. why do we need to sort the LC for Roundrobin algo?
:::success
before
![](https://hackmd.io/_uploads/Bksyjj8gT.png)
after
![](https://hackmd.io/_uploads/HyJWssUgT.png)


we will remove that.



:::
### (x)4120. Can we add cmLListDelFrm(casLcInfoList, lcNode) before SCH_FREE
:::success

no problem
![](https://hackmd.io/_uploads/r1iovNFxp.png)

依照對方提供的方式就修改
:::
### (x)4170. Can we check if this variable is used?
:::success

we will remove that

:::
### (x)4247. Can we add cmLListDelFrm(casLcInfoList, lcNode) before SCH_FREE
:::success

no problem
![](https://hackmd.io/_uploads/S1yeOEFlT.png)

同4120，依照對方提供的方式做修改
:::
### (x)4254. Please check the function name
:::success

Change wrong text in log

:::
### (x)4273 Instead of modifying the rrm policy her, Can we pass Slice ReqCfgReq from DUAPP whenever required and put that under flag ifndef O1_ENABLE?
:::success

Just for experiment

This function was originally used to experiment with the effect of rrmplicy switching, and can be removed .


:::
### (x)4318 here only change i see is change in algo from RR to WFQ of second slice. neither slice's rrm policy is modified
:::success

This is just to test the algorithm, the rrmpolicy settings can be adjusted separately. As for the difference, the only difference is the algorithm part, which is for experimentation.


:::
## src/5gnrsch/sch_slice_based.h 

### (x)53. Do we need to maintain separate list for dedicated and default LC lists here? Are we using these in case of slice_based algo as we have separate sliceCb in cellCb?
:::success


Q1:NO

Q2:YES
:::

### ( JOJO )79. Can you elaborate on the usage of this and next three variables?
:::success

Ready for multi Ue
:::
## src/5gnrsch/sch_slot_ind.c

### ( JOJO )45. not sure why we need to include algo specific header file in this file. Can you please specify the reason for this inclusion?
:::success

In line with the content of E2SM, just like multi ue, someone will push it in the future.

配合E2SM的內容，同multi ue 一樣後續會有人push
:::
## src/5gnrsch/sch_utils.c

### (x)774. Can we have a single table having all the three required components i.e. 5QI, with three columns where first col = 5QiValue,Resource Type and Default Prior level?
:::success

Complete the table

窮舉完表格
:::


## src/5gnrsch/sch.c


### (x)1982. Can we call the scheduler specific api here cellCb->api->SchSliceRecfgReq(rrmPolicyOfSlices)?

:::success
將2034 else的內容移植到1982
:::

## src/du_app/du_cfg.c


### (x)653. Can we check the size of snssai array here?
:::success

The array size is currently directly rewritten by humans for testing, and will be supplemented after future automation.

:::





# Patch (3) Review (Status 7/23)
## src/5gnrsch/sch_slice_based.c
### 211 Dont we need to dealloc and delete the LC information for this UE from sliceCbList?
:::success
Iterate through sliceCbList: The slice control block associated with this UE needs to be found.
Delete and deallocate LC information: For each logical channel associated with this UE, memory should be freed and removed from the list.

- Before
```c=
   while(node)
   {
      next = node->next;
      ueId = *(uint8_t *)node->node;
      if(ueId == ueCb->ueId)
      {
        SCH_FREE(node->node, sizeof(uint8_t));
        cmLListDelFrm(&schSpcCellCb->ueToBeScheduled, node);
        SCH_FREE(node, sizeof(CmLList));
        break;
      }
      node = next;
   }
   
```
- after
```c=
while (node) {
    sliceCb = (SchSliceBasedCb *)node->node; 
    if (sliceCb->ueId == ueCb->ueId) {
        cmLListDeleteLList(&sliceCb->lcList); 
        SCH_FREE(sliceCb, sizeof(SchSliceBasedCb)); /块
        break; 
    }
    node = node->next;
}

```
:::
### (x)239 if and else case have no difference. what is the purpose of this condition?
:::success
This part of the content was originally just an experiment. Functionally, I gave it the RR algorithm for the 0th slice, and I gave it the WFQ algorithm for the 1th slice. This is only a temporary framework for processing the algorithm of each slice. When the algorithm is increased in the future, this framework can be expanded.
:::
### 307 since we are only changing the rrmPolicy here(which has been modified) , Can we only pass arg as rrmPolicy (SchRrmPolicyOfSlice) for optimization purpose?
### 323 as stated in my previous comment, if we directly pass rrmPolicy in this funtion, we dont need this loop. Please check
### 1432 Why we didnt pass schSliceBasedDlScheduling for retx case as well ?
---
### 1448 not answered appropriately. Either this function should be passing multiple UEs to make the sense of having List of UE stored inside this function or if this func is only processing one UE then that list purpose remain questionable
### (X)1680 please add your previous answer as comment in the code?
:::success
Already add answer as comment in the code and also specify the spec reference(~/docs/release-notes.rst)
![image](https://hackmd.io/_uploads/rJlQZxz90.png)
:::
### (X)1737 I meant that function description has wrong function name. This description is for "schSliceBasedSortUeByWeight" but line 1737 it is wrongly mentioned
:::success

![image](https://hackmd.io/_uploads/BkT9nRWqR.png)
:::
### (X)1757
:::success
![image](https://hackmd.io/_uploads/HkpAhA-5A.png)
:::

### 1875 My point is that this variable ueDlNewTransmission will be reset everytime this function is called and will always have one UE stored in this Linklist. Still confused why we need a List of UE to be maintained inside a funtion which is havng one UE processing at a time
### 1900
### 2186 portion after this line, is exactly same as "schSliceBasedDlIntraSliceScheduling". Why cant we directly call this function here?
### 2364 Here we are only considering LC with no SNSSAI. What abt those LC's which are assoc with a snssai but has no rrm policy(default snssai's)? Please note that these kind of LCs will have a valid value of snssai value in its context.
### (x)4429 any comments? Just to re-iterate, Function "schSliceBasedDlScheduling" includes all the functionality of "schSliceBasedScheduleDlLc". Why do we need this function in case of Slice based scheduing algo?
:::success
You are right, they are same. We'll remove this one
:::
### (x)4430 we can ignore this comment because "schSliceBasedUlScheduling" is not enabled yet.. when it will be enabled during UL data scheduling then we should consider this comment because we might not be using this function same as in the case of DL
:::success
okey,we ignore this comment
:::
## src/5gnrsch/sch_slice_based.h
### 53 Still not sure if we need separate list for Dedicate and default LC for this Slice based scheduling algo as for me it seems like this has been take care differently.
### 58 I dont think this Database is used for SliceBased Schedular. Can you recheck the ussage of this Database and remove if it is unused?
:::success
okey we will check it
:::

### 79 These variable are being used in SchScheduleSlot() as local variable. Please elaborate in what scenarios we might be needing these variable value stored or used in multiple places? Also in this gerrit they are not getting used from this structure. Any reason for that?

## src/5gnrsch/sch_slot_ind.c
### 45 Not understanding the explanation. My point is that sch_slot_ind.c file is a common file, which must not refer to anything from Algo-Specific file. I meant architecture says that this file should be anonymous of the scheduling algorithm thus nothing should be refereed from sch_slice_based.h even in future also. Please explain in detail?

## src/5gnrsch/sch_utils.c
### (x)770 I think we can remove this table as we have merged it in the below table i.e. "fiveQiTable"
:::success
okey
:::
## src/5gnrsch/sch.c
### 1982 The comment "Can we call the scheduler specific api here cellCb->api->SchSliceRecfgReq(rrmPolicyOfSlices)?" has not been implemented yet. Any reason why?
:::success
將2034 else的內容移植到1982
:::
## src/du_app/du_cfg.c
### 653 Here my point is NUM_OF_SUPPORTED_SLICE = 2 and we are adding 4 slices which will corrupt the array memory. Instead of using this variable we can use the MAX variable determining maximum slice which can be supported?
:::success
Instead of using this variable, we will add a MAX variable to determine the maximum number of slices that can be supported. Avoid memory errors
- Before
```c=
#ifndef O1_ENABLE
   /* Note: Added these below variable for local testing*/
   Snssai snssai[NUM_OF_SUPPORTED_SLICE] = {{1,{2,3,4}},{2,{3,3,4}},{3,{4,3,4}},{4,{5,3,4}}};
#endif
```

- After 
```c=

#define MAX_SUPPORTED_SLICE 4 // (It should defind in common.h)Define the maximum number of slices supported

#ifndef O1_ENABLE

   Snssai snssai[MAX_SUPPORTED_SLICE] = {
       {1, {2, 3, 4}},
       {2, {3, 3, 4}},
       {3, {4, 3, 4}},
       {4, {5, 3, 4}}
   };
#endif

```
:::
## (x)src/5gnrsch/sch_slice_based.c
### (x)1504 