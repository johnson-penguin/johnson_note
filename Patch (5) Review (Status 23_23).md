---
title: Patch (5) Review (Status 23/23)

---


# Patch (3) Review (Status 23/23)
## src/5gnrsch/sch_slice_based.c
### (x)211 Dont we need to dealloc and delete the LC information for this UE from sliceCbList?
:::success
OK we will add this section to ensure that all resources related to this UE are released correctly

- Before
```c=
void SchSliceBasedUeDeleteReq(SchUeCb *ueCb)
{
   SchSliceBasedCellCb *schSpcCellCb = NULLP;
   SchSliceBasedUeCb *ueSliceBasedCb = NULLP;
   CmLList *node = NULL, *next = NULL;
   uint8_t ueId = 0;

   schSpcCellCb = (SchSliceBasedCellCb *)ueCb->cellCb->schSpcCell;
   /* Remove all UE from ueToBeScheduled list and deallocate */
   node = schSpcCellCb->ueToBeScheduled.first;
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
   
   ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;
   cmLListDeleteLList(&ueSliceBasedCb->hqRetxCb.ulRetxHqList);
   cmLListDeleteLList(&ueSliceBasedCb->hqRetxCb.dlRetxHqList);

   SCH_FREE(ueSliceBasedCb, sizeof(SchSliceBasedUeCb));
   ueCb->schSpcUeCb = NULLP;
   return;
}
```

- After
```c=
void SchSliceBasedUeDeleteReq(CmLListCp *sliceCbList, SchUeCb *ueCb)
{
   SchSliceBasedCellCb *schSpcCellCb = NULLP;
   SchSliceBasedUeCb *ueSliceBasedCb = NULLP;
   SchSliceBasedSliceCb *sliceCb;
   CmLList *node = NULL, *next = NULL;
   CmLList *lcInfoNode, *lcInfoNext;
   CmLList *sliceCbNode;
   uint8_t ueId = 0;

   schSpcCellCb = (SchSliceBasedCellCb *)ueCb->cellCb->schSpcCell;
   /* Remove all UE from ueToBeScheduled list and deallocate */
   node = schSpcCellCb->ueToBeScheduled.first;
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

   /*Remove all UE LC info*/
   sliceCbNode = sliceCbList->first;
   while(sliceCbNode)
   {
      sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node; 

      /* Rebuild the lcInfoList */
      lcInfoNode = sliceCb->lcInfoList[ueId-1].first;
      while(lcInfoNode)
      {
         lcInfoNext = lcInfoNode->next;
         SCH_FREE(lcInfoNode->node, sizeof(SchSliceBasedLcInfo));
         cmLListDelFrm(&sliceCb->lcInfoList[ueId-1], lcInfoNode);
         SCH_FREE(lcInfoNode, sizeof(CmLList));
         
         lcInfoNode = lcInfoNext;
      }
      
      cmLListInit(&sliceCb->lcInfoList[ueId-1]);

   ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;
   cmLListDeleteLList(&ueSliceBasedCb->hqRetxCb.ulRetxHqList);
   cmLListDeleteLList(&ueSliceBasedCb->hqRetxCb.dlRetxHqList);

   SCH_FREE(ueSliceBasedCb, sizeof(SchSliceBasedUeCb));
   ueCb->schSpcUeCb = NULLP;
   return;
   }
}
```
- compare
![image](https://hackmd.io/_uploads/BJuL2OloR.png)

:::
### (x)239 if and else case have no difference. what is the purpose of this condition?
:::success
This part of the content was originally just an experiment. Functionally, I gave it the RR algorithm for the 0th slice, and I gave it the WFQ algorithm for the 1th slice. This is only a temporary framework for processing the algorithm of each slice. When the algorithm is increased in the future, this framework can be expanded.
:::
### (x)307 since we are only changing the rrmPolicy here(which has been modified) , Can we only pass arg as rrmPolicy (SchRrmPolicyOfSlice) for optimization purpose?
:::success

- Before
- 原始程式碼： 在外層循環中遍歷 sliceCfg，並在內層循環中遍歷 sliceCbList。
```c=
void SchSliceBasedSliceRecfgReq(SchCellCb *cellCb)
{
   CmLList *sliceCfg = NULLP;
   CmLList *sliceCbList = NULLP;
   CmLListCp *storedSliceCfg;
   SchSliceBasedCellCb  *schSpcCell;
   SchSliceBasedSliceCb *sliceCbNode;
   SchRrmPolicyOfSlice *rrmPolicyNode;

   schSpcCell = (SchSliceBasedCellCb *)cellCb->schSpcCell;
   storedSliceCfg = &schCb[cellCb->instIdx].sliceCfg;
   sliceCfg = storedSliceCfg->first;
   sliceCbList = schSpcCell->sliceCbList.first;
   
   while(sliceCfg)
   {
      rrmPolicyNode = (SchRrmPolicyOfSlice *)sliceCfg->node;

      while(sliceCbList)
      {
         sliceCbNode = (SchSliceBasedSliceCb *)sliceCbList->node;

         if(memcmp(&rrmPolicyNode->snssai, &sliceCbNode->snssai, sizeof(Snssai)) == 0)
         {
            memcpy(&sliceCbNode->rrmPolicyRatioInfo, &rrmPolicyNode->rrmPolicyRatioInfo, sizeof(SchRrmPolicyRatio));
            DU_LOG("\nDennis --> SCH: Process Slice Config Request: SST:%d, SD:[%d, %d, %d] RRMMaxRatio:%d, RRMMinRatio:%d, RRMDedicatedRatio:%d",\
            rrmPolicyNode->snssai.sst, rrmPolicyNode->snssai.sd[0], rrmPolicyNode->snssai.sd[1], \
            rrmPolicyNode->snssai.sd[2], rrmPolicyNode->rrmPolicyRatioInfo.maxRatio, \
            rrmPolicyNode->rrmPolicyRatioInfo.minRatio, rrmPolicyNode->rrmPolicyRatioInfo.dedicatedRatio);
            
            // DU_LOG("\nDennis --> SCH: Calculate PRB quota: Total PRB of Bandwidth:%d, Shared PRB Quota:%d, Prioritized PRB Quota:%d, Dedicated PRB Quota:%d",\
            // MAX_NUM_RB, sliceCbNode->sharedPrb, sliceCbNode->prioritizedPrb, sliceCbNode->dedicatedPrb);
            
            break;
         }

         sliceCbList = sliceCbList->next;
      }

      sliceCfg = sliceCfg->next;
   }
   return;
}
```
- After
- 修改後： 仍然保留了外層循環，但不再需要額外的循環來存取 storedSliceCfg，直接使用 sliceCfg 進行處理。
- 減少了對 storedSliceCfg 的遍歷，降低了時間複雜度
```c=
void SchSliceBasedSliceRecfgReq(SchCellCb *cellCb, SchRrmPolicyOfSlice *rrmPolicy)
{
   CmLList *sliceCfg = NULLP;
   CmLList *sliceCbList = NULLP;
   CmLListCp *storedSliceCfg;
   SchSliceBasedCellCb  *schSpcCell;
   SchSliceBasedSliceCb *sliceCbNode;

   
   schSpcCell = (SchSliceBasedCellCb *)cellCb->schSpcCell;
   storedSliceCfg = &schCb[cellCb->instIdx].sliceCfg;
   sliceCfg = storedSliceCfg->first;
   sliceCbList = schSpcCell->sliceCbList.first;
   
   while(sliceCfg)
   {
      if(memcmp(&rrmPolicy->snssai, &sliceCbNode->snssai, sizeof(Snssai)) == 0)
      {
         memcpy(&sliceCbNode->rrmPolicyRatioInfo, &rrmPolicy->rrmPolicyRatioInfo, sizeof(SchRrmPolicyRatio));
         DU_LOG("\nDennis --> SCH: Process Slice Config Request: SST:%d, SD:[%d, %d, %d] RRMMaxRatio:%d, RRMMinRatio:%d, RRMDedicatedRatio:%d",\
         rrmPolicy->snssai.sst, rrmPolicy->snssai.sd[0], rrmPolicy->snssai.sd[1], \
         rrmPolicy->snssai.sd[2], rrmPolicy->rrmPolicyRatioInfo.maxRatio, \
         rrmPolicy->rrmPolicyRatioInfo.minRatio, rrmPolicy->rrmPolicyRatioInfo.dedicatedRatio);
                    
         // DU_LOG("\nDennis --> SCH: Calculate PRB quota: Total PRB of Bandwidth:%d, Shared PRB Quota:%d, Prioritized PRB Quota:%d, Dedicated PRB Quota:%d",\
         // MAX_NUM_RB, sliceCbNode->sharedPrb, sliceCbNode->prioritizedPrb, sliceCbNode->dedicatedPrb);
        
         break;
      }

      sliceCbList = sliceCbList->next;
      sliceCfg = sliceCfg->next;
   }
   return;
}
```
- Compare
![image](https://hackmd.io/_uploads/Byiu9UloR.png)

:::
### (x)323 as stated in my previous comment, if we directly pass rrmPolicy in this funtion, we dont need this loop. Please check
:::success

- Before
- 原始程式碼： 在外層循環中遍歷 sliceCfg，並在內層循環中遍歷 sliceCbList。
```c=
void SchSliceBasedSliceRecfgReq(SchCellCb *cellCb)
{
   CmLList *sliceCfg = NULLP;
   CmLList *sliceCbList = NULLP;
   CmLListCp *storedSliceCfg;
   SchSliceBasedCellCb  *schSpcCell;
   SchSliceBasedSliceCb *sliceCbNode;
   SchRrmPolicyOfSlice *rrmPolicyNode;

   schSpcCell = (SchSliceBasedCellCb *)cellCb->schSpcCell;
   storedSliceCfg = &schCb[cellCb->instIdx].sliceCfg;
   sliceCfg = storedSliceCfg->first;
   sliceCbList = schSpcCell->sliceCbList.first;
   
   while(sliceCfg)
   {
      rrmPolicyNode = (SchRrmPolicyOfSlice *)sliceCfg->node;

      while(sliceCbList)
      {
         sliceCbNode = (SchSliceBasedSliceCb *)sliceCbList->node;

         if(memcmp(&rrmPolicyNode->snssai, &sliceCbNode->snssai, sizeof(Snssai)) == 0)
         {
            memcpy(&sliceCbNode->rrmPolicyRatioInfo, &rrmPolicyNode->rrmPolicyRatioInfo, sizeof(SchRrmPolicyRatio));
            DU_LOG("\nDennis --> SCH: Process Slice Config Request: SST:%d, SD:[%d, %d, %d] RRMMaxRatio:%d, RRMMinRatio:%d, RRMDedicatedRatio:%d",\
            rrmPolicyNode->snssai.sst, rrmPolicyNode->snssai.sd[0], rrmPolicyNode->snssai.sd[1], \
            rrmPolicyNode->snssai.sd[2], rrmPolicyNode->rrmPolicyRatioInfo.maxRatio, \
            rrmPolicyNode->rrmPolicyRatioInfo.minRatio, rrmPolicyNode->rrmPolicyRatioInfo.dedicatedRatio);
            
            // DU_LOG("\nDennis --> SCH: Calculate PRB quota: Total PRB of Bandwidth:%d, Shared PRB Quota:%d, Prioritized PRB Quota:%d, Dedicated PRB Quota:%d",\
            // MAX_NUM_RB, sliceCbNode->sharedPrb, sliceCbNode->prioritizedPrb, sliceCbNode->dedicatedPrb);
            
            break;
         }

         sliceCbList = sliceCbList->next;
      }

      sliceCfg = sliceCfg->next;
   }
   return;
}
```
- After
- 修改後： 仍然保留了外層循環，但不再需要額外的循環來存取 storedSliceCfg，直接使用 sliceCfg 進行處理。
- 減少了對 storedSliceCfg 的遍歷，降低了時間複雜度
```c=
void SchSliceBasedSliceRecfgReq(SchCellCb *cellCb, SchRrmPolicyOfSlice *rrmPolicy)
{
   CmLList *sliceCfg = NULLP;
   CmLList *sliceCbList = NULLP;
   CmLListCp *storedSliceCfg;
   SchSliceBasedCellCb  *schSpcCell;
   SchSliceBasedSliceCb *sliceCbNode;

   
   schSpcCell = (SchSliceBasedCellCb *)cellCb->schSpcCell;
   storedSliceCfg = &schCb[cellCb->instIdx].sliceCfg;
   sliceCfg = storedSliceCfg->first;
   sliceCbList = schSpcCell->sliceCbList.first;
   
   while(sliceCfg)
   {
      if(memcmp(&rrmPolicy->snssai, &sliceCbNode->snssai, sizeof(Snssai)) == 0)
      {
         memcpy(&sliceCbNode->rrmPolicyRatioInfo, &rrmPolicy->rrmPolicyRatioInfo, sizeof(SchRrmPolicyRatio));
         DU_LOG("\nDennis --> SCH: Process Slice Config Request: SST:%d, SD:[%d, %d, %d] RRMMaxRatio:%d, RRMMinRatio:%d, RRMDedicatedRatio:%d",\
         rrmPolicy->snssai.sst, rrmPolicy->snssai.sd[0], rrmPolicy->snssai.sd[1], \
         rrmPolicy->snssai.sd[2], rrmPolicy->rrmPolicyRatioInfo.maxRatio, \
         rrmPolicy->rrmPolicyRatioInfo.minRatio, rrmPolicy->rrmPolicyRatioInfo.dedicatedRatio);
                    
         // DU_LOG("\nDennis --> SCH: Calculate PRB quota: Total PRB of Bandwidth:%d, Shared PRB Quota:%d, Prioritized PRB Quota:%d, Dedicated PRB Quota:%d",\
         // MAX_NUM_RB, sliceCbNode->sharedPrb, sliceCbNode->prioritizedPrb, sliceCbNode->dedicatedPrb);
        
         break;
      }

      sliceCbList = sliceCbList->next;
      sliceCfg = sliceCfg->next;
   }
   return;
}
```
- Compare
![image](https://hackmd.io/_uploads/Byiu9UloR.png)

:::
### (x)1432 Why we didnt pass schSliceBasedDlScheduling for retx case as well ?
:::success

It was skipped at first because retransmission has the highest priority, and it is just a retransmission of the results calculated by the previous slice-enabled scheduling. There is no need to do any more scheduling, just use the original one.

And at that time, phystub did not have NACK at all. Even if it was written, it could not be measured, so there was no special optimization for it.

:::
---
### (?)1448 not answered appropriately. Either this function should be passing multiple UEs to make the sense of having List of UE stored inside this function or if this func is only processing one UE then that list purpose remain questionable
:::success
This is for the development of multi-UE TTIs and layered models

Our original per TTI functionality is not easy to present in Flat Mode, so we need a scheduling functionality that can handle multiple UEs in order to allocate resources more efficiently between different tiers, which will also help in the development of subsequent hierarchical mode architectures, but since the per TTI version is already available in the J-release, maybe we can remove it
:::


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

### (x)1875 My point is that this variable ueDlNewTransmission will be reset everytime this function is called and will always have one UE stored in this Linklist. Still confused why we need a List of UE to be maintained inside a funtion which is havng one UE processing at a time

:::success
Same as 1448, this was originally also intended to develop multi-UE per-TTI and hierarchical models

Since we haven't yet rendered our full multi-UE per-TTI functionality inside this patch, it seems confusing, why do we need to maintain a list of UEs within a function that only one UE handles at a time? But in fact, our original developers would have added all per-TTI UEs to this list for maintenance.

But it's the same problem, because of the per-TTI feature that J-release has released, maybe we need to delete this part as well?
:::

### (x)1900 Same condition ( if(cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId-1] == NULL)) is present in schSliceBasedDlFinalScheduling line 2487 Do we need this check here?
:::success
After moving the memory allocation for PDSCH and PDCCH (Step 3) to Find the maximum free PRB chunk (Step 4), make sure to confirm the available resources before making the memory allocation.

```c=
bool schSliceBasedDlScheduling(SchCellCb *cell, SlotTimingInfo currTime, uint8_t ueId, bool isRetx, SchDlHqProcCb **hqP)
{
   uint8_t pdschNumSymbols = 0, pdschStartSymbol = 0;
   uint16_t startPrb = 0;
   uint16_t maxFreePRB = 0;
   uint16_t totalRemainingPrb = 0;
   uint16_t currSliceCnt = 0;
   SchUeCb *ueCb = NULLP;
   SlotTimingInfo pdcchTime, pdschTime, pucchTime;
   DlMsgSchInfo *dciSlotAlloc = NULLP;
   SchSliceBasedCellCb *schSpcCell = NULLP;
   CmLList *sliceCbNode = NULLP; 
   SchSliceBasedSliceCb *sliceCb = NULLP;

   /* Hard-coded the UE DL LL for 1 UE per TTI (Ready for multi-UEs per TTI scheduling) */
   SchDlHqProcCb *ueNewHarqList[MAX_NUM_UE];
   ueNewHarqList[ueId-1] = *hqP;

   CmLListCp ueDlNewTransmission;
   cmLListInit(&ueDlNewTransmission);
   addNodeToLList(&ueDlNewTransmission, &ueId, NULLP);

   ueCb = &cell->ueCb[ueId-1];

   /* [Step1]: Get the available HARQ process for this UE */
   if (isRetx == FALSE)
   {
      if(schDlGetAvlHqProcess(cell, ueCb, &ueNewHarqList[ueId-1]) != ROK)
      {
         return false;
      }
   }

   /* [Step2]: Find the k0, k1 value for this UE (Time Resource Allocation) */
   if(findValidK0K1Value(cell, currTime, ueId, ueCb->k0K1TblPrsnt,\
            &pdschStartSymbol, &pdschNumSymbols, &pdcchTime, &pdschTime, &pucchTime, isRetx, ueNewHarqList[ueId-1]) != true )
   {
      return false;
   }

   /* [Step4]: Search the largest free PRB block to do the scheduling */
   maxFreePRB = searchLargestFreeBlock(ueNewHarqList[ueId-1]->hqEnt->cell, pdschTime, &startPrb, DIR_DL);
   totalRemainingPrb = maxFreePRB;

   if(maxFreePRB == 0)
   {
      return false;
   } 

   /* [Step3]: Allocate the memory to PDSCH and PDCCH allocation result of this UE */
   if(cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId-1] == NULL)
   {
      SCH_ALLOC(dciSlotAlloc, sizeof(DlMsgSchInfo));
      if(!dciSlotAlloc)
      {
         DU_LOG("\nERROR  -->  SCH : Memory Allocation failed for ded DL msg alloc");
         return false;
      }
      cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId -1] = dciSlotAlloc;
      memset(dciSlotAlloc, 0, sizeof(DlMsgSchInfo));
   }

   schSpcCell = (SchSliceBasedCellCb *)cell->schSpcCell;
   sliceCbNode = schSpcCell->sliceCbList.first;

   /* [Step5]: Traverse each slice to do the intra-slice scheduling individually */
   if (isRetx == FALSE)
   {
      while(sliceCbNode)
      {
         sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node;
         
#ifdef SCH_MULTI_THREAD

         /* Pack the argument for thread function */
         schSpcCell->threadArg[currSliceCnt]->cell = cell;
         schSpcCell->threadArg[currSliceCnt]->pdcchTime = &pdcchTime;
         schSpcCell->threadArg[currSliceCnt]->pdschNumSymbols = pdschNumSymbols;
         schSpcCell->threadArg[currSliceCnt]->totalRemainingPrb = &totalRemainingPrb;
         schSpcCell->threadArg[currSliceCnt]->maxFreePRB = &maxFreePRB;
         schSpcCell->threadArg[currSliceCnt]->sliceCb = sliceCb;
         schSpcCell->threadArg[currSliceCnt]->ueDlNewTransmission= &ueDlNewTransmission;

         /* Trigger Point: Run the intra-slice scheduling with multi-thread feature */
         *schSpcCell->threadArg[currSliceCnt]->triggerFlag = 1;

         currSliceCnt++;
         sliceCbNode = sliceCbNode->next;
      }

      /* Wait for each thread finishing the intra-slice scheduling */
      currSliceCnt = 1;
      DU_LOG("\nDennis  -->  waiting for intra-slice scheduling");
      while(currSliceCnt)
      {
         currSliceCnt = 0;
         for(int sliceCnt=0; sliceCnt < schSpcCell->sliceCbList.count; sliceCnt++)
         {
            if(*schSpcCell->threadArg[sliceCnt]->triggerFlag)
            {
               currSliceCnt++;
            }
         }
      }

#else
         /* [Step6]: Get the remaining resource after intra-slice scheduling
          * and leave it to final scheduling */
         if(schSliceBasedDlIntraSliceScheduling(cell, pdcchTime, pdschNumSymbols, &ueDlNewTransmission, maxFreePRB, \
            &totalRemainingPrb, sliceCb) != ROK)
         {
            DU_LOG("\nDennis --> DL Intra Slice Scheduling Failed");
            return false;
         }

         sliceCbNode = sliceCbNode->next;
      }
#endif

   }  

   /*  [Step7]: Run the final scheduling to allocate the remaining resource
    *  and fill the scheduling result */
   if(schSliceBasedDlFinalScheduling(cell, pdschTime, pdcchTime, pucchTime, pdschStartSymbol, pdschNumSymbols, &ueDlNewTransmission, isRetx, ueNewHarqList, totalRemainingPrb, startPrb) != ROK)
   {
      DU_LOG("\nDennis --> DL Final Scheduling Failed");
      return false;
   }

   /* Free the hard-coded UE DL New Transmission LL for 1 UE per TTI */
   SCH_FREE(ueDlNewTransmission.first, sizeof(CmLList));

   return true;
}


```
:::


### (x)2186 portion after this line, is exactly same as "schSliceBasedDlIntraSliceScheduling". Why cant we directly call this function here?
```c=
void *schSliceBasedDlIntraSliceThreadScheduling(void *threadArg)
{
    SchSliceBasedDlThreadArg *dlThreadArg;
    dlThreadArg = (SchSliceBasedDlThreadArg *)threadArg;

    /* [Step1]: Polling the trigger flag to trigger the intra-slice scheduling */
    while(1)
    {
        if(*dlThreadArg->triggerFlag)
        {
            SchCellCb *cellCb = dlThreadArg->cell;
            SlotTimingInfo pdcchTime = *dlThreadArg->pdcchTime;
            uint8_t pdschNumSymbols = dlThreadArg->pdschNumSymbols;
            CmLListCp *ueDlNewTransmission = dlThreadArg->ueDlNewTransmission;
            uint16_t maxFreePRB = *dlThreadArg->maxFreePRB;
            uint16_t *totalRemainingPrb = dlThreadArg->totalRemainingPrb;
            SchSliceBasedSliceCb *sliceCb = dlThreadArg->sliceCb;

            uint8_t result = schSliceBasedDlIntraSliceScheduling(cellCb, pdcchTime, pdschNumSymbols,
                                                                 ueDlNewTransmission, maxFreePRB,
                                                                 totalRemainingPrb, sliceCb);

            if (result != ROK)
            {
                DU_LOG("\nDennis  -->  In schSliceBasedDlIntraSliceThreadScheduling() : Scheduling failed");
            }

            /* Set the trigger flag to 0 and waiting for next trigger point */
            *dlThreadArg->triggerFlag = 0;

            /* Sleep to reduce the CPU usage */
            usleep(500);
        }
    }
}
```
### (!)2364 Here we are only considering LC with no SNSSAI. What abt those LC's which are assoc with a snssai but has no rrm policy(default snssai's)? Please note that these kind of LCs will have a valid value of snssai value in its context.
:::success
```c=
else if(ueCb->dlInfo.dlLcCtxt[lcIdx].snssai != NULLP && ueCb->dlInfo.dlLcCtxt[lcIdx].bo != 0)
               {
                  sliceCbNode = schSpcCell->sliceCbList.first;
                  while (sliceCbNode)
                  {
                     sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node;
                     if (&sliceCb->snssai == ueCb->dlInfo.dlLcCtxt[lcIdx].snssai )
                     {
                        /* Update the reqPRB and Payloadsize for this LC in the appropriate List */
                        if(updateLcListReqPRB(&defLcList, ueCb->dlInfo.dlLcCtxt[lcIdx].lcId,\
                           (ueCb->dlInfo.dlLcCtxt[lcIdx].bo + MAC_HDR_SIZE)) != ROK)
                           {
                              DU_LOG("\nERROR  --> SCH : Updation in default LC Info LL Failed");
                           }
                        else
                           {
   #ifdef SLICE_BASED_DEBUG_LOG
                        DU_LOG("\nDEBUG  -->  SCH : Append LC to default LC Info LL [LCID, reqBO] [%d, %d]", lcIdx, \
                        ueCb->dlInfo.dlLcCtxt[lcIdx].bo + MAC_HDR_SIZE);
   #endif
                           }
                     }
                  sliceCbNode = sliceCbNode->next;
                  }
                  
               }
```
:::
### (x)4429 any comments? Just to re-iterate, Function "schSliceBasedDlScheduling" includes all the functionality of "schSliceBasedScheduleDlLc". Why do we need this function in case of Slice based scheduing algo?
:::success
You are right, they are same. We'll remove this one
:::
### (x)4430 we can ignore this comment because "schSliceBasedUlScheduling" is not enabled yet.. when it will be enabled during UL data scheduling then we should consider this comment because we might not be using this function same as in the case of DL
:::success
okey,we ignore this comment
:::
## src/5gnrsch/sch_slice_based.h
### (~)53 Still not sure if we need separate list for Dedicate and default LC for this Slice based scheduling algo as for me it seems like this has been take care differently.
:::success
If dedicated LCs and preset LCs require different scheduling policies or priorities, keeping their separate lists makes resource allocation and management easier and more intuitive, especially in the case of multiple RRM policies.
:::
### (x)58 I dont think this Database is used for SliceBased Schedular. Can you recheck the ussage of this Database and remove if it is unused?
:::success
The data structure SchSliceBasedLcCb is the original OSC code, we only added two new comments.


It is true that this is not used in the SliceBased Scheduler, but this data structure was not created by us, and we are certainly happy to help remove it if necessary.
:::

### (~)79 These variable are being used in SchScheduleSlot() as local variable. Please elaborate in what scenarios we might be needing these variable value stored or used in multiple places? Also in this gerrit they are not getting used from this structure. Any reason for that?
:::success
This was originally reserved for per-TTI architecture, but we have not yet added it to this gerrit. Currently, we only use isDlMsgScheduled in schSliceBasedDlFinalScheduling() to mark UEs that have completed scheduling to avoid repeated scheduling.

Considering that there is already a formal per-TTI architecture in I-release, do we need to remove the unused parts?
:::
## (x)src/5gnrsch/sch_slot_ind.c
### (x)45 Not understanding the explanation. My point is that sch_slot_ind.c file is a common file, which must not refer to anything from Algo-Specific file. I meant architecture says that this file should be anonymous of the scheduling algorithm thus nothing should be refereed from sch_slice_based.h even in future also. Please explain in detail?
:::success
okey we will remove it
:::
## (x)src/5gnrsch/sch_utils.c
### (x)770 I think we can remove this table as we have merged it in the below table i.e. "fiveQiTable"
:::success
okey
:::
## (x)src/5gnrsch/sch.c
### (x)1982 The comment "Can we call the scheduler specific api here cellCb->api->SchSliceRecfgReq(rrmPolicyOfSlices)?" has not been implemented yet. Any reason why?
:::success
![image](https://hackmd.io/_uploads/S1Tqg1GsR.png)
:::
## (x)src/du_app/du_cfg.c
### (x)653 Here my point is NUM_OF_SUPPORTED_SLICE = 2 and we are adding 4 slices which will corrupt the array memory. Instead of using this variable we can use the MAX variable determining maximum slice which can be supported?
:::success
- du_cfg.h
```c=
#define MAX_OF_SUPPORTED_SLICE  4  
```
![image](https://hackmd.io/_uploads/SyW8xc-iC.png)

---
- du_cfg.c

```c=
#ifndef O1_ENABLE
   /* Note: Added these below variable for local testing*/
   Snssai snssai[MAX_OF_SUPPORTED_SLICE] = {{1,{2,3,4}},{2,{3,3,4}},{3,{4,3,4}},{4,{5,3,4}}};
#endif
```
:::
## (x)src/5gnrsch/sch_slice_based.c
### (x)1504 