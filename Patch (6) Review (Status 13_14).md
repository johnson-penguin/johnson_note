---
title: Patch (6) Review (Status 13/14)

---

[TOC]

[Gerrit](https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/11653?tab=comments)

# src/5gnrsch/sch_slice_based.c 

### (?)211 Dont we need to dealloc and delete the LC information for this UE from sliceCbList?

1. Traverse sliceCbList: For each slice related to the UE, the corresponding LC information should be removed and released.
2. Make sure to free any dynamically allocated memory related to LC information.
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
## (x)239
```c=
         }
         else
         {
            sliceCbToStore->algorithm = RR;
            sliceCbToStore->algoMethod = FLAT;
         }
         addNodeToLList(&schSpcCell->sliceCbList, sliceCbToStore, NULL);
```

## (x)323
- Before

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

   if (!schSpcCell)
   {
      DU_LOG("\nError: schSpcCell is NULL");
      return;
   }

   if (!sliceCbList)
   {
      DU_LOG("\nError: sliceCbList is empty");
      return;
   }

   while (sliceCbList)
   {
      sliceCbNode = (SchSliceBasedSliceCb *)sliceCbList->node;

      if (!sliceCbNode)
      {
         DU_LOG("\nError: sliceCbNode is NULL");
         sliceCbList = sliceCbList->next;
         continue;
      }

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
}
/*******************************************************************

```


- After
```c=

void SchSliceBasedSliceRecfgReq(SchCellCb *cellCb, SchRrmPolicyOfSlice *rrmPolicy)
{
   CmLList *sliceCbList = NULLP;
   SchSliceBasedCellCb  *schSpcCell;
   SchSliceBasedSliceCb *sliceCbNode;
   schSpcCell = (SchSliceBasedCellCb *)cellCb->schSpcCell;
   sliceCbList = schSpcCell->sliceCbList.first;

   if (!schSpcCell)
   {
      DU_LOG("\nError: schSpcCell is NULL");
      return;
   }

   if (!sliceCbList)
   {
      DU_LOG("\nError: sliceCbList is empty");
      return;
   }

   while (sliceCbList)
   {
      sliceCbNode = (SchSliceBasedSliceCb *)sliceCbList->node;

      if (!sliceCbNode)
      {
         DU_LOG("\nError: sliceCbNode is NULL");
         sliceCbList = sliceCbList->next;
         continue;
      }

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
   }
   return
}
```
:::success
- Original version:
    - Wrong traversal in `while(sliceCfg)` loop, resulting in failure to properly check and update the RRM policy for each slice.
    - used `sliceCfg` to iterate over the `schCb->sliceCfg` list, which is unnecessary in the context since what you actually need to iterate over is schSpcCell->sliceCbList
    - SNSSAI comparison in the wrong loop, which may result in incorrect matching and updating of RRM policies
- After modification:
    - Directly traverse `schSpcCell->sliceCbList` to ensure that each slice control block (sliceCbNode) can be accessed and processed correctly. This ensures correct SNSSAI comparison and RRM policy updates for each slice.
    - Deleted the code related to `sliceCfg`, simplified the function structure and avoided unnecessary traversal, thus improving the readability and efficiency of the code.
    - Use memcmp to compare SNSSAI when iterating through each slice of `sliceCbList`. If it matches, rrmPolicy->rrmPolicyRatioInfo is copied to the current sliceCbNode. This ensures that updates are only made if the SNSSAI matches, thus avoiding unnecessary operations.
:::

## (x)325 Added some null pointer checks before entering function
```c=
   if (!schSpcCell)
   {
      DU_LOG("\nError: schSpcCell is NULL");
      return;
   }

   if (!sliceCbList)
   {
      DU_LOG("\nError: sliceCbList is empty");
      return;
   }

   while (sliceCbList)
   {
      sliceCbNode = (SchSliceBasedSliceCb *)sliceCbList->node;

      if (!sliceCbNode)
      {
         DU_LOG("\nError: sliceCbNode is NULL");
         sliceCbList = sliceCbList->next;
         continue;
      }

```
## (x)1432







## (x)1448
由於目前我們已經從鏈表輸入改為單一UE ID 輸入，因此此function 與原生僅差別在slice sch的呼叫



## (x)[1875](https://hackmd.io/@Johnson-72/By7vD9lfkg)



## (x)2364 Consider LCs associated with nssai but without rrm policy (default snssai) and schedule them to the default rrmpolicy
:::success
- Traverse slice configurations: 
    - Use the sliceCfg pointer to traverse all slice configurations in schCb[inst].sliceCfg.
- Compare SNSSAI: 
    - For each slice configuration, check whether its SNSSAI is the same as the provided SNSSAI.
- Update Dedicated PRB: 
    - If a matching SNSSAI is found, calculate and update rsvdDedicatedPRB and set isDedicated to TRUE.
:::

```c=
uint8_t updateDedLcInfo(Inst inst, Snssai *snssai, uint16_t *rsvdDedicatedPRB, bool *isDedicated)
{
   CmLList *sliceCfg = schCb[inst].sliceCfg.first;
   SchRrmPolicyOfSlice *rrmPolicyOfSlices;

   while(sliceCfg)
   {
      rrmPolicyOfSlices = (SchRrmPolicyOfSlice*)sliceCfg->node;
      if(rrmPolicyOfSlices && (memcmp(snssai, &(rrmPolicyOfSlices->snssai), sizeof(Snssai)) == 0))
      {
         /*Updating latest RrmPolicy*/
         *rsvdDedicatedPRB = \
                             (uint16_t)(((rrmPolicyOfSlices->rrmPolicyRatioInfo.dedicatedRatio)*(MAX_NUM_RB))/100);
         *isDedicated = TRUE;
         DU_LOG("\nINFO  -->  SCH : Updated RRM policy, reservedPOOL:%d",*rsvdDedicatedPRB);
         break;
      }
      sliceCfg = sliceCfg->next;
   }
   /*case: This LcCtxt  is either a Default LC or this LC is part of someother RRM_MemberList*/
   if(*isDedicated != TRUE) 
   {
      DU_LOG("\nINFO  -->  SCH : This SNSSAI is not a part of this RRMPolicy");
   }
   return ROK;	 
}

```
:::info
- Conditional judgment: 
    - The new part checks whether the current LC is not dedicated and has a valid SNSSAI and number of bits (bo).
- Traverse the slice configuration: 
    - Traverse the sliceCbList to find the slice that matches the SNSSAI of the current LC.
- Update request PRB: 
    - If a matching slice is found, update the request PRB and payload size, and handle errors accordingly.
:::
```c=
               else if(ueCb->dlInfo.dlLcCtxt[lcIdx].isDedicated != true && ueCb->dlInfo.dlLcCtxt[lcIdx].snssai != NULLP && ueCb->dlInfo.dlLcCtxt[lcIdx].bo != 0)
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

```
![image](https://hackmd.io/_uploads/rymrugQxkx.png)




# src/5gnrsch/sch_slice_based.h
## 53
## (x)58
## (x)79

# (x)src/5gnrsch/sch.c
## (x)1984 Fix API call errors and compilation issues
```c=
uint8_t fillSliceRecfgRsp(Inst inst, CmLListCp *storedSliceCfg, SchSliceRecfgReq *schSliceRecfgReq)
{
   SchCellCb *cellCb;
   SchMacRsp sliceFound;
   uint8_t cfgIdx = 0;
   SchRrmPolicyOfSlice *rrmPolicyOfSlices;
   SchSliceRecfgRsp schSliceRecfgRsp;
   
   cellCb = schCb[inst].cells[inst];

   for(cfgIdx = 0; cfgIdx<schSliceRecfgReq->numOfConfiguredSlice; cfgIdx++)
   {
      sliceFound = RSP_NOK;
      /* Here comparing the slice recfg request with the StoredSliceCfg */
      CmLList *sliceCfg = storedSliceCfg->first;

      while(sliceCfg)
      {
         rrmPolicyOfSlices = (SchRrmPolicyOfSlice*)sliceCfg->node;
         
         if(rrmPolicyOfSlices && (memcmp(&schSliceRecfgReq->listOfSlices[cfgIdx]->snssai, &(rrmPolicyOfSlices->snssai), sizeof(Snssai)) == 0))
         {
            memcpy(&rrmPolicyOfSlices->rrmPolicyRatioInfo, &schSliceRecfgReq->listOfSlices[cfgIdx]->rrmPolicyRatioInfo, sizeof(SchRrmPolicyRatio));
            sliceFound = RSP_OK;
            SchSliceBasedSliceRecfgReq(cellCb, rrmPolicyOfSlices);

            break;
         }
         sliceCfg = sliceCfg->next;
      }
```


## (x)2038 Remove unrelated items

