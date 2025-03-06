---
title: coding record

---

## issue [1985](https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/11653/comment/9e2416d8_b2881fa4/)
- remove `SchSliceBasedSliceRecfgReq(cellCb, rrmPolicyOfSlices);`
- add log in `updateDedLcInfo`
- Adjust `rrmpolicy ratio` for easier observation

### Q1
Before `fillSliceRecfgRsp`, `schSliceRecfgReq` will be triggered on `SchProcSliceRecfgReq`
```c=
uint8_t SchProcSliceRecfgReq(Pst *pst, SchSliceRecfgReq *schSliceRecfgReq)
{
   uint8_t ret = ROK;
   Inst   inst = pst->dstInst - SCH_INST_START;
   SchCellCb *cellCb;

   cellCb = schCb[inst].cells[0];
   DU_LOG("\nINFO  -->  SCH : Received Slice ReCfg request from MAC");
   if(schSliceRecfgReq)
   {
      if(schSliceRecfgReq->listOfSlices)
      {
         /* filling the slice configuration response of each slice */
         if(fillSliceRecfgRsp(inst, &schCb[inst].sliceCfg, schSliceRecfgReq) != ROK)
         {
            DU_LOG("\nERROR  -->  SCH : Failed to fill sch slice cfg response");
            ret = RFAILED;
         }
         freeSchSliceCfgReq(schSliceRecfgReq);
      }
   }
```

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
           break;
         }
```
### Q2

```c=
uint8_t updateDedLcInfo(Inst inst, Snssai *snssai, uint16_t *rsvdDedicatedPRB, bool *isDedicated)
{
   CmLList *sliceCfg = schCb[inst].sliceCfg.first;
   SchRrmPolicyOfSlice *rrmPolicyOfSlices;

   while(sliceCfg)
   {
      rrmPolicyOfSlices = (SchRrmPolicyOfSlice*)sliceCfg->node;
      DU_LOG("\njohnson_before  -->  SNSSAI: {SST: %d, SD: %d}", rrmPolicyOfSlices->snssai.sst, rrmPolicyOfSlices->snssai.sd);
      DU_LOG("\njohnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: %d, minRatio: %d, dedicatedRatio: %d} ",rrmPolicyOfSlices->rrmPolicyRatioInfo.maxRatio,rrmPolicyOfSlices->rrmPolicyRatioInfo.minRatio,rrmPolicyOfSlices->rrmPolicyRatioInfo.dedicatedRatio);

      /*Testing*/
      if(rrmPolicyOfSlices && (memcmp(snssai, &(rrmPolicyOfSlices->snssai), sizeof(Snssai)) == 0))
      {
         /*Updating latest RrmPolicy*/
         *rsvdDedicatedPRB = \
                             (uint16_t)(((rrmPolicyOfSlices->rrmPolicyRatioInfo.dedicatedRatio)*(MAX_NUM_RB))/100);
         *isDedicated = TRUE;
         DU_LOG("\nINFO  -->  SCH : Updated RRM policy, reservedPOOL:%d",*rsvdDedicatedPRB);
         
         DU_LOG("\njohnson_after  -->  SNSSAI: {SST: %d, SD: %d}", rrmPolicyOfSlices->snssai.sst, rrmPolicyOfSlices->snssai.sd);
         DU_LOG("\njohnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: %d, minRatio: %d, dedicatedRatio: %d} ",rrmPolicyOfSlices->rrmPolicyRatioInfo.maxRatio,rrmPolicyOfSlices->rrmPolicyRatioInfo.minRatio,rrmPolicyOfSlices->rrmPolicyRatioInfo.dedicatedRatio);    
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
- before remove `SchSliceBasedSliceRecfgReq(cellCb, rrmPolicyOfSlices);`

```c=
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:31
johnson_after  -->  SNSSAI: {SST: 1, SD: -328429039}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 1, SD: -328429039}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:31
johnson_after  -->  SNSSAI: {SST: 1, SD: -328429039}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 1, SD: -328429039}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 2, SD: -328428527}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:10
johnson_after  -->  SNSSAI: {SST: 2, SD: -328428527}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
johnson_before  -->  SNSSAI: {SST: 1, SD: -328429039}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 2, SD: -328428527}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:10
johnson_after  -->  SNSSAI: {SST: 2, SD: -328428527}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
```

- after remove `SchSliceBasedSliceRecfgReq(cellCb, rrmPolicyOfSlices);`

```c=
johnson_before  -->  SNSSAI: {SST: 1, SD: 91484689}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:31
johnson_after  -->  SNSSAI: {SST: 1, SD: 91484689}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 1, SD: 91484689}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:31
johnson_after  -->  SNSSAI: {SST: 1, SD: 91484689}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 1, SD: 91484689}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 2, SD: 91485201}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:10
johnson_after  -->  SNSSAI: {SST: 2, SD: 91485201}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
johnson_before  -->  SNSSAI: {SST: 1, SD: 91484689}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 30} 
johnson_before  -->  SNSSAI: {SST: 2, SD: 91485201}
johnson_before  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
INFO  -->  SCH : Updated RRM policy, reservedPOOL:10
johnson_after  -->  SNSSAI: {SST: 2, SD: 91485201}
johnson_after  -->  RRM Policy Ratio Info: Ratio: {maxRatio: 100, minRatio: 50, dedicatedRatio: 10} 
```

`dedicatedRatio` can be updated normally (30--->10)

## issue [#1490](https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/11653/comment/f23620e2_0baa480f/)

:::success
This case is for sometimes UE has only UL data, and this moment, we still have to initialize the slice control block.
To make the resetting valid, we moved the else condition to the end of the function, this is how it works:

if there is no UE to be scheduled, then reset the slice control block.
:::

- before

```c=
            /* DL Data */
            node = NULLP;
            if(schSpcUeCb)
               node = schSpcUeCb->hqRetxCb.dlRetxHqList.first;
            if(node != NULLP)
            {
               /* DL Data ReTransmisson */
               isDlMsgPending = true;
               //isDlMsgScheduled = schFillBoGrantDlSchedInfo(cell, *slotInd, ueId, TRUE, ((SchDlHqProcCb**) &(node->node)));
               isDlMsgScheduled = schSliceBasedDlScheduling(cell, *slotInd, ueId, TRUE, ((SchDlHqProcCb**) &(node->node)));
               if(isDlMsgScheduled)
               {
#ifdef NR_DRX 
                  schDrxStopDlHqRetxTmr(cell, &cell->ueCb[ueId-1], ((SchDlHqProcCb**) &(node->node)));
#endif
                  schSliceBasedRemoveFrmDlHqRetxList(&cell->ueCb[ueId-1], node);
               }
            }
            else
            {
               /* DL Data new transmission */
               if((cell->boIndBitMap) & (1<<ueId))
               {
                  isDlMsgPending = true;
                  //isDlMsgScheduled = schFillBoGrantDlSchedInfo(cell, *slotInd, ueId, FALSE, &hqP);
                  isDlMsgScheduled = schSliceBasedDlScheduling(cell, *slotInd, ueId, FALSE, &hqP);

                  /* If DL scheduling failed, free the newly assigned HARQ process */
                  if(!isDlMsgScheduled)
                     schDlReleaseHqProcess(hqP);
                  else
                  {
#ifdef NR_DRX
                     schHdlDrxInActvStrtTmr(cell, &cell->ueCb[ueId-1], PHY_DELTA_DL + SCHED_DELTA);
#endif
                  }
               }
               else
               {
                  /* Reset the allocated PRB in each sliceCB */
                  CmLList *sliceCbNode = NULLP; 
                  SchSliceBasedSliceCb *sliceCb = NULLP;
                  SchSliceBasedCellCb *schSpcCell = NULLP;

                  schSpcCell = (SchSliceBasedCellCb *)cell->schSpcCell;
                  sliceCbNode = schSpcCell->sliceCbList.first;

                  while(sliceCbNode)
                  {
                     sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node;
                     sliceCb->allocatedPrb = 0;

                     sliceCbNode = sliceCbNode->next;
                  }
               }
            }

            /* Scheduling of UL grant */
            node = NULLP;
            if(schSpcUeCb)
               node = schSpcUeCb->hqRetxCb.ulRetxHqList.first;
            if(node != NULLP)
            {
               /* UL Data ReTransmisson */
               isUlGrantPending = true;
               isUlGrantScheduled = schProcessSrOrBsrReq(cell, *slotInd, ueId, TRUE, (SchUlHqProcCb**) &(node->node));
               if(isUlGrantScheduled)
               {
#ifdef NR_DRX 
                  schDrxStopUlHqRetxTmr(cell, &cell->ueCb[ueId-1], ((SchUlHqProcCb**) &(node->node)));
#endif
                  schSliceBasedRemoveFrmUlHqRetxList(&cell->ueCb[ueId-1], node);
               }
            }
            else
            {
               /* UL Data new transmission */
               if(cell->ueCb[ueId-1].srRcvd || cell->ueCb[ueId-1].bsrRcvd)
               {
                  isUlGrantPending = true;
                  isUlGrantScheduled = schProcessSrOrBsrReq(cell, *slotInd, ueId, FALSE, &ulHqP);
                  //isUlGrantScheduled = schSliceBasedUlScheduling(cell, *slotInd, ueId, FALSE, &ulHqP);
                  if(!isUlGrantScheduled)
                     schUlReleaseHqProcess(ulHqP, FALSE);
                  else
                  {
#ifdef NR_DRX
                     schHdlDrxInActvStrtTmr(cell, &cell->ueCb[ueId-1], PHY_DELTA_UL + SCHED_DELTA);
#endif
                  }
               }
            }

            if(!isUlGrantPending && !isDlMsgPending)
            {
               /* No action required */  
            }
            else if((isUlGrantPending && !isUlGrantScheduled) || (isDlMsgPending && !isDlMsgScheduled))
            {
               cmLListAdd2Tail(&schSpcCell->ueToBeScheduled, cmLListDelFrm(&schSpcCell->ueToBeScheduled, pendingUeNode));
            }
            else
            {
               schSliceBasedRemoveUeFrmScheduleLst(cell, pendingUeNode);
            }
         }
      }
   }
```


- after

```c=
/* DL Data */
            node = NULLP;
            if(schSpcUeCb)
               node = schSpcUeCb->hqRetxCb.dlRetxHqList.first;
            if(node != NULLP)
            {
               /* DL Data ReTransmisson */
               isDlMsgPending = true;
               isDlMsgScheduled = schSliceBasedDlScheduling(cell, *slotInd, ueId, TRUE, ((SchDlHqProcCb**) &(node->node)));
               if(isDlMsgScheduled)
               {
#ifdef NR_DRX 
                  schDrxStopDlHqRetxTmr(cell, &cell->ueCb[ueId-1], ((SchDlHqProcCb**) &(node->node)));
#endif
                  schSliceBasedRemoveFrmDlHqRetxList(&cell->ueCb[ueId-1], node);
               }
            }
            else
            {
               /* DL Data new transmission */
               if((cell->boIndBitMap) & (1<<ueId))
               {
                  isDlMsgPending = true;
                  isDlMsgScheduled = schSliceBasedDlScheduling(cell, *slotInd, ueId, FALSE, &hqP);

                  /* If DL scheduling failed, free the newly assigned HARQ process */
                  if(!isDlMsgScheduled)
                     schDlReleaseHqProcess(hqP);
                  else
                  {
#ifdef NR_DRX
                     schHdlDrxInActvStrtTmr(cell, &cell->ueCb[ueId-1], PHY_DELTA_DL + SCHED_DELTA);
#endif
                  }
               }
               
            }

            /* Scheduling of UL grant */
            node = NULLP;
            if(schSpcUeCb)
               node = schSpcUeCb->hqRetxCb.ulRetxHqList.first;
            if(node != NULLP)
            {
               /* UL Data ReTransmisson */
               isUlGrantPending = true;
               isUlGrantScheduled = schProcessSrOrBsrReq(cell, *slotInd, ueId, TRUE, (SchUlHqProcCb**) &(node->node));
               if(isUlGrantScheduled)
               {
#ifdef NR_DRX 
                  schDrxStopUlHqRetxTmr(cell, &cell->ueCb[ueId-1], ((SchUlHqProcCb**) &(node->node)));
#endif
                  schSliceBasedRemoveFrmUlHqRetxList(&cell->ueCb[ueId-1], node);
               }
            }
            else
            {
               /* UL Data new transmission */
               if(cell->ueCb[ueId-1].srRcvd || cell->ueCb[ueId-1].bsrRcvd)
               {
                  isUlGrantPending = true;
                  isUlGrantScheduled = schSliceBasedUlScheduling(cell, *slotInd, ueId, FALSE, &ulHqP);
                  if(!isUlGrantScheduled)
                     schUlReleaseHqProcess(ulHqP, FALSE);
                  else
                  {
#ifdef NR_DRX
                     schHdlDrxInActvStrtTmr(cell, &cell->ueCb[ueId-1], PHY_DELTA_UL + SCHED_DELTA);
#endif
                  }
               }
            }

            if(!isUlGrantPending && !isDlMsgPending)
            {
               /* No action required */  
            }
            else if((isUlGrantPending && !isUlGrantScheduled) || (isDlMsgPending && !isDlMsgScheduled))
            {
               cmLListAdd2Tail(&schSpcCell->ueToBeScheduled, cmLListDelFrm(&schSpcCell->ueToBeScheduled, pendingUeNode));
            }
            else
            {
               schSliceBasedRemoveUeFrmScheduleLst(cell, pendingUeNode);
            }
         }
      }
   }
   else
   {
      /* Reset the allocated PRB in each sliceCB */
      CmLList *sliceCbNode = NULLP; 
      SchSliceBasedSliceCb *sliceCb = NULLP;
      SchSliceBasedCellCb *schSpcCell = NULLP;

      schSpcCell = (SchSliceBasedCellCb *)cell->schSpcCell;
      sliceCbNode = schSpcCell->sliceCbList.first;

      while(sliceCbNode)
      {
         sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node;
         sliceCb->allocatedPrb = 0;

         sliceCbNode = sliceCbNode->next;
      }
   }

```

## Supplement push content on slack
[Fix segmentation fault after enabling the retransmission by modifying randomseed ACK](https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/12755)
[Fix Memory allocation failed for UCI Indication during PRB resource crunch.](https://gerrit.o-ran-sc.org/r/c/o-du/l2/+/12797)
- path : `src/5gnrmac/mac_slot_ind.c`
- function : `MacProcDlAlloc`
```c=
                           &macCb.macCell[cellIdx]->ueCb[ueIdx], \
                           dlSchedInfo->dlMsgAlloc[ueIdx]->harqProcNum, txPduLen);

                     /* Store PDU in corresponding DL slot */
                     MAC_ALLOC(txPdu, txPduLen);
                     if(!txPdu)
                     {
                        DU_LOG("\nERROR  -->  MAC : Memory allocation failed in MacProcDlAlloc");
                        return RFAILED;
                     }   

                     /*Allocate memory when HARQ TB is not NULL.*/
                     // memcpy(txPdu, retxTb,  txPduLen);
                     if (retxTb != NULL) {
                        memcpy(txPdu, retxTb, txPduLen);
                        
                     } else {
                        DU_LOG("\nDEBUG  -->  MAC : HARQ TB is NULL.");
                     }

                     currDlSlot->dlInfo.dlMsgAlloc[ueIdx]->dlMsgPduLen = txPduLen;
                     currDlSlot->dlInfo.dlMsgAlloc[ueIdx]->dlMsgPdu = txPdu;
                  }
               }
            }
```
- path :`src/phy_stub/phy_stub_msg_hdl.c`
- function : `fillPucchF0F1PduInfo`
```c=
/*Start triggering retransmission after UE finishes UE attachment.*/
         if(ind < 10)
         {
            pduInfo->harqInfo.harqValue[idx] = result[ind%50];
         }
         else
         {
            /*NTUST Bimo: Get the current time as the seed.*/
            struct timespec ts;
            clock_gettime(CLOCK_REALTIME, &ts);
            srand((unsigned int)(ts.tv_nsec / 1000000));

            uint8_t random_seed = rand()%(100);
            pduInfo->harqInfo.harqValue[idx] = (70 >= random_seed)?HARQ_PASS:HARQ_FAIL;
         }
```


- path : `src/5gnrsch/sch_fcfs.c`
- function : `schFcfsScheduleDlLc`
```c=
         DU_LOG("\nERROR  --> SCH : NO FREE PRB!!");
      }
      else
      {
         /*Schedule the LC for next slot*/
         DU_LOG("\nDEBUG  -->  SCH : No LC has been scheduled");
      }
      /* Not Freeing dlMsgAlloc as ZERO BO REPORT to be sent to RLC so that
       * Allocation can be done in next slot*/
      accumalatedSize = 0;

      /* Release schDlSlotInfo if resource crunch. */
      SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
      (*hqP)->hqEnt->cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueCb->ueId -1] = NULL;
   }

   return accumalatedSize;
```

- path : `src/5gnrsch/sch_slice_based.c`
- function : `schSliceBasedScheduleDlLc`

```c=
         DU_LOG("\nERROR  --> SCH : NO FREE PRB!!");
      }
      else
      {
         /*Schedule the LC for next slot*/
         DU_LOG("\nDEBUG  -->  SCH : No LC has been scheduled");
      }
      /* Not Freeing dlMsgAlloc as ZERO BO REPORT to be sent to RLC so that
       * Allocation can be done in next slot*/
      accumalatedSize = 0;

      /*Release schDlSlotInfo if resource crunch. */
      SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
      (*hqP)->hqEnt->cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueCb->ueId -1] = NULL;
   }

   return accumalatedSize;
}

```