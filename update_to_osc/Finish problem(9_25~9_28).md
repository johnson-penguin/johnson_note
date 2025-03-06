---
title: Finish problem(9/25~9/28)
tags: [BMW Lab., code review]

---

:::success
https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/11653?tab=comments
:::


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
![](https://hackmd.io/_uploads/BJlIwiMxa.png)

By specifying the maximum thread value, you can avoid using too many threads when creating a slice or exceeding the number of threads in the hardware itself.
![](https://hackmd.io/_uploads/HyMdviMe6.png)


:::


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

----

## src/5gnrsch/sch_slice_based.c 

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
### (x)3346. as per my understanding, this is for defaultslice which will not have rrmPolicy. Can we rename it to SliceBasedPrbAllocDefaultSlice
:::success

Yes , we will rename.


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

### (x)4170. Can we check if this variable is used?
:::success

we will remove that

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



### (x)653. Can we check the size of snssai array here?
:::success

NO,the array size is currently directly rewritten by humans for testing, and will be supplemented after future automation.

:::
