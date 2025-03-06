---
title: ' No available tunnel on the CU side debug record'

---

[TOC]
## Status check
### check ueId in scheduling
- Add
```c=
   DU_LOG("\nJohnson_ueid: %d", ueId);   
```

Both `schSliceBasedUlIntraSliceScheduling` and `schSliceBasedDlFinalScheduling` <br>not output `ueId` log

### check `msg3Sent` and `msg5ShortBsrSent`
- Confirm whether `msg3Sent` and `msg5ShortBsrSent` are sent normally
```c=
if(!phyDb.ueDb.ueCb[ueId-1].msg3Sent)
      {
         phyDb.ueDb.ueCb[ueId-1].ueId = ueId;
         phyDb.ueDb.ueCb[ueId-1].crnti = puschPdu.rnti;
         phyDb.ueDb.ueCb[ueId-1].msg3Sent = true;
         DU_LOG("\n johnson debug msg3.");
         type = MSG_TYPE_MSG3;
         sleep(2);
      }
      else if(!phyDb.ueDb.ueCb[ueId-1].msg5ShortBsrSent)
      {
         phyDb.ueDb.ueCb[ueId-1].msg5ShortBsrSent = true;
         DU_LOG("\n johnson debug msg5.");
   
         type = MSG_TYPE_SHORT_BSR;
      }

```
> Both of them are not sent. Try to check if there is any abnormality in the<br> [schSliceBasedUlIntraSliceScheduling](#schSliceBasedDlIntraSliceScheduling)<br>[schSliceBasedDlFinalScheduling](#schSliceBasedDlFinalScheduling)
> It may be due to improper operation when using `ueId` to replace the `ueDlNewTransmission` linked list.

## schSliceBasedDlIntraSliceScheduling
### Function 並沒有正常加入 ueId
```c=
uint8_t schSliceBasedDlIntraSliceScheduling(SchCellCb *cellCb, SlotTimingInfo pdcchTime, uint8_t pdschNumSymbols, \
                                             uint16_t maxFreePRB, \
                                             uint16_t *totalRemainingPrb, SchSliceBasedSliceCb *sliceCb, uint8_t ueId);
```

### 修改呼叫schSliceBasedDlIntraSliceScheduling的參數輸入
```c=
schSliceBasedDlIntraSliceScheduling(cell, pdcchTime, pdschNumSymbols, maxFreePRB, &totalRemainingPrb, sliceCb, ueId) 
```
### 修改ueId的引用方式，並且移除While迴圈
由於使用`ueId`替換掉`ueDlNewTransmission`的緣故，因此在一個UE的情況下我們不需要遍歷link list

- before
```c=
   ueNode = ueDlNewTransmission->first;

   /* [Step2] */
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

- after
```c=
   // ueNode = ueId;
   DU_LOG("\nJohnson_ueid: %d", ueId);   
   /* [Step2] */
   // while(ueNode)
   // {
      // ueId = *(uint8_t *)(ueNode->node);
      ueCb = &cellCb->ueCb[ueId-1];
      ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;

      /* Sum the weight of each UE */
      totalUeWeight += ueSliceBasedCb->weight;
   
      /* Update the requested BO of each LC in current slice */
      schSliceBasedUpdateLcListReqBo(&sliceCb->lcInfoList[ueId-1], ueCb, DIR_DL);
      // ueNode = ueNode->next;
   // }
```
### 修改`schSliceBasedDlThreadArg`資料結構，新增`ueId`成員
- before
```c=
typedef struct schSliceBasedDlThreadArg   
{
   uint8_t *triggerFlag;
   SchCellCb *cell;
   SlotTimingInfo *pdcchTime;
   uint8_t *pdschNumSymbols;
   uint16_t *totalRemainingPrb;
   uint16_t *maxFreePRB;
   SchSliceBasedSliceCb *sliceCb;
   CmLListCp *ueDlNewTransmission;
}SchSliceBasedDlThreadArg;
```
- after
```c=
typedef struct schSliceBasedDlThreadArg   
{
   uint8_t *triggerFlag;
   SchCellCb *cell;
   SlotTimingInfo *pdcchTime;
   uint8_t *pdschNumSymbols;
   uint16_t *totalRemainingPrb;
   uint16_t *maxFreePRB;
   SchSliceBasedSliceCb *sliceCb;
   CmLListCp *ueDlNewTransmission;
   uint16_t ueId;
}SchSliceBasedDlThreadArg;
```
### 修改`schSliceBasedDlIntraSliceThreadScheduling`
從`schSliceBasedDlThreadArg`拉出UE資料，使`schSliceBasedDlIntraSliceScheduling`在其中能正常使用
- before
```c=
uint16_t maxFreePRB = *dlThreadArg->maxFreePRB;
uint16_t *totalRemainingPrb = dlThreadArg->totalRemainingPrb;
SchSliceBasedSliceCb *sliceCb = dlThreadArg->sliceCb;
```

- after
```c=
uint16_t maxFreePRB = *dlThreadArg->maxFreePRB;
uint16_t *totalRemainingPrb = dlThreadArg->totalRemainingPrb;
SchSliceBasedSliceCb *sliceCb = dlThreadArg->sliceCb;
uint16_t ueId = dlThreadArg->ueId;
```











## schSliceBasedDlFinalScheduling
### Function 並沒有正常加入 ueId
```c=
uint8_t schSliceBasedDlFinalScheduling(SchCellCb *cellCb, SlotTimingInfo pdschTime, SlotTimingInfo pdcchTime, \
                                       SlotTimingInfo pucchTime, uint8_t pdschStartSymbol, uint8_t pdschNumSymbols, 
                                       bool isRetx, SchDlHqProcCb **ueNewHarqList, \
                                       uint16_t remainingPrb, uint16_t startPrb, uint8_t ueId);

```
### 移除迴圈、移除原始ueId的呼叫方式 (step1 part)
- before
```c=
  /* [Step1]: Traverse each UE to allocate remaining PRB to default slice */
   ueNode = ueDlNewTransmission->first;
   while(ueNode)
   {  
      if(isRetx == FALSE)
      {
         ueId = *(uint8_t *)(ueNode->node);
         ueCb = &cellCb->ueCb[ueId-1];
         ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;
          .
          .
          .
          .
   #ifdef SLICE_BASED_DEBUG_LOG
            DU_LOG("\nDEBUG  -->  SCH Final Default Slice : [UE ID: %d, Allocated PRB: %d, Remaining PRB: %d]", \
                  ueId, remainingPrb - availablePrb, availablePrb);
   #endif
            remainingPrb = availablePrb;
         }
         ueNode = ueNode->next;
      }
      else
      {
         continue;         
      }
   }
```

- after

```c=
   /* [Step1]: Traverse each UE to allocate remaining PRB to default slice */
   // ueNode = ueId;
   DU_LOG("\nJohnson_ueid: %d",ueId);  
   // while(ueNode)
   // {  
      if(isRetx == FALSE)
      {
         // ueId = *(uint8_t *)(ueNode->node);
         ueCb = &cellCb->ueCb[ueId-1];
         ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;
          .
          .
          .
          .
   #ifdef SLICE_BASED_DEBUG_LOG
            DU_LOG("\nDEBUG  -->  SCH Final Default Slice : [UE ID: %d, Allocated PRB: %d, Remaining PRB: %d]", \
                  ueId, remainingPrb - availablePrb, availablePrb);
   #endif
            remainingPrb = availablePrb;
         }
         // ueNode = ueNode->next;
      }
      // else
      // {
      //    continue;         
      // }
   // }
```

### 移除迴圈、移除原始ueId的呼叫方式 (step4 part)
- before
```c=

   /* [Step4]: Traverse each UE to fill the scheduling result */
   ueNode = ueDlNewTransmission->first;
   while(ueNode)
   {
      ueId = *(uint8_t *)(ueNode->node);
      SchDlHqProcCb **hqP = &ueNewHarqList[ueId-1];
      ueCb = &cellCb->ueCb[ueId-1];
      ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;
      GET_CRNTI(crnti,ueId);
      accumalatedSize = 0;
           .
           .
           .
           .
         /* If failed, traverse next UE.*/
         ueNode = ueNode->next;
         continue;
      }

      /* [Step6]: Fill the scheduling result into PDCCH and PDSCH configuration */
      if((schDlRsrcAllocDlMsg(cellCb, pdschTime, crnti, accumalatedSize, dciSlotAlloc, startPrb, pdschStartSymbol, \
                              pdschNumSymbols, isRetx, *hqP)) != ROK)
      {
         DU_LOG("\nERROR  --> SCH : Scheduling of DL dedicated message failed");
         /* Free the dl ded msg info allocated in macSchDlRlcBoInfo */
         if(!dciSlotAlloc->dlMsgPdschCfg)
         {
            SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
            cellCb->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId -1] = NULL;
         }       
         /* If failed, traverse next UE */
         ueNode = ueNode->next;
         continue;
      }

    .
    .
    .
    .
    .
    
      /* After allocation is done, unset the bo bit for that ue */
      UNSET_ONE_BIT(ueId, cellCb->boIndBitMap);
      ueSliceBasedCb->isDlMsgScheduled = true;
      
      ueNode = ueNode->next;
   }

   return ROK;
}
    
```

- after
```c=

   /* [Step4]: Traverse each UE to fill the scheduling result */
   // while(ueNode)
   // {
   //    ueId = *(uint8_t *)(ueNode->node);
      SchDlHqProcCb **hqP = &ueNewHarqList[ueId-1];
      ueCb = &cellCb->ueCb[ueId-1];
      ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;
      GET_CRNTI(crnti,ueId);
      accumalatedSize = 0;
    .
    .
    .
    .
    .
         /* If failed, traverse next UE.*/
         // ueNode = ueNode->next;
         // continue;
      }
      /* [Step6]: Fill the scheduling result into PDCCH and PDSCH configuration */
      if((schDlRsrcAllocDlMsg(cellCb, pdschTime, crnti, accumalatedSize, dciSlotAlloc, startPrb, pdschStartSymbol, \
                              pdschNumSymbols, isRetx, *hqP)) != ROK)
      {
         DU_LOG("\nERROR  --> SCH : Scheduling of DL dedicated message failed");
         /* Free the dl ded msg info allocated in macSchDlRlcBoInfo */
         if(!dciSlotAlloc->dlMsgPdschCfg)
         {
            SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
            cellCb->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId -1] = NULL;
         }       
         /* If failed, traverse next UE */
         // ueNode = ueNode->next;
         // continue;
      }
      /* After allocation is done, unset the bo bit for that ue */
      UNSET_ONE_BIT(ueId, cellCb->boIndBitMap);

   //    ueNode = ueNode->next;
   // }

   return ROK;
}


```



## Result
### EGTP can send DL user data 
![image](https://hackmd.io/_uploads/rkiuN6efkl.png)

### DU scheduling can get ueId
![image](https://hackmd.io/_uploads/B1U4ragz1g.png)

### Procedure check
![image](https://hackmd.io/_uploads/rkHC_pxMyl.png)

![image](https://hackmd.io/_uploads/HJoCHTeG1x.png)
![image](https://hackmd.io/_uploads/B1rk8alzJe.png)
![image](https://hackmd.io/_uploads/HJTqHagzkg.png)
![image](https://hackmd.io/_uploads/HJ43SpefJg.png)
![image](https://hackmd.io/_uploads/SJgaraefyg.png)
![image](https://hackmd.io/_uploads/SJjprpgz1l.png)


## Conclusion
:::success
- Problem : **Can not build up DRB**

- How to solve : That’s because UE doesn’t even ==finish attachment==, so we ==modified the scheduling function==, and find there is wrong assignment between parameters, we correct this to make UE finish attachment procedure to build up DRB
:::