---
title: Multi - Thread - Version
tags: [code交接, Trace]

---

# schSliceBasedDlScheduling( Multi - Thread )
schSliceBasedDlScheduling will running this part

```c=
#ifdef SCH_MULTI_THREAD

         /* Pack the argument for thread function */
         schSpcCell->threadArg[currSliceCnt]->cell = cell;
         schSpcCell->threadArg[currSliceCnt]->pdcchTime = &pdcchTime;
         schSpcCell->threadArg[currSliceCnt]->pdschNumSymbols = pdschNumSymbols;
         schSpcCell->threadArg[currSliceCnt]->totalRemainingPrb = &totalRemainingPrb;
         schSpcCell->threadArg[currSliceCnt]->maxFreePRB = &maxFreePRB;
         schSpcCell->threadArg[currSliceCnt]->sliceCb = sliceCb;
         schSpcCell->threadArg[currSliceCnt]->ueDlNewTransmission = &ueDlNewTransmission;

         /* Trigger Point: Run the intra-slice scheduling with multi-thread feature */
         *schSpcCell->threadArg[currSliceCnt]->triggerFlag = 1;

         currSliceCnt++;
         sliceCbNode = sliceCbNode->next;
      }

      /* Wait for each thread finishing the intra-slice scheduling */
      currSliceCnt = 1;
      DU_LOG("\nDennis  -->  waiting for intra-slice scheduling");
      while (currSliceCnt)
      {
         currSliceCnt = 0;
         for (int sliceCnt = 0; sliceCnt < schSpcCell->sliceCbList.count; sliceCnt++)
         {
            if (*schSpcCell->threadArg[sliceCnt]->triggerFlag)
            {
               currSliceCnt++;
            }
         }
      }

```

Traverse each sliceCb, distribute the data to each Thread, and trigger the schSliceBasedDlIntraSliceThreadScheduling( ) by triggerFlag = 1.

And we can't guarantee that the scheduling time will be the same for each slice, so at the end of the loop, we do a condition check on each thread's trigger by using the for loop, when schSliceBasedDlIntraSliceThreadScheduling( ) scheduling is done,it will be confirmed. 


Loop for each thread's trigger to do conditional confirmation, when schSliceBasedDlIntraSliceThreadScheduling( ) scheduling is completed will be the Tread's triggerFlag adjusted to 0, so that means that you need to do confirmation of each Thread's triggerFlag to know whether there are still slice for the completion of the scheduling. whether there is still a slice for the completion of the scheduling.







# IntraSliceScheduling( Multi - Thread )



This function requires a trigger form schSliceBasedDlScheduling( ) for energization.
```c=

void *schSliceBasedDlIntraSliceThreadScheduling(void *threadArg)
{
   SchSliceBasedDlThreadArg *dlThreadArg;
   dlThreadArg = (SchSliceBasedDlThreadArg *)threadArg;

   /* [Step1]: Polling the trigger flag to trigger the intra-slice scheduling */
   while (1)
   {
      if (*dlThreadArg->triggerFlag)
      {
         uint16_t crnti = 0;
         uint16_t minimumPrb = 0, remainingPrb = 0;
         SchUeCb *ueCb = NULLP;
         uint8_t ueId;
         CmLList *ueNode;
         DlMsgSchInfo *dciSlotAlloc;
         float_t totalUeWeight = 0;
         SchSliceBasedUeCb *ueSliceBasedCb = NULLP;

         SchCellCb *cellCb;
         SlotTimingInfo pdcchTime;
         uint8_t pdschNumSymbols;
         uint16_t *totalRemainingPrb;
         uint16_t maxFreePRB;
         SchSliceBasedSliceCb *sliceCb;
         CmLListCp *ueDlNewTransmission;

         cellCb = dlThreadArg->cell;
         pdcchTime = *dlThreadArg->pdcchTime;
         pdschNumSymbols = dlThreadArg->pdschNumSymbols;
         totalRemainingPrb = dlThreadArg->totalRemainingPrb;
         maxFreePRB = *dlThreadArg->maxFreePRB;
         sliceCb = dlThreadArg->sliceCb;
         ueDlNewTransmission = dlThreadArg->ueDlNewTransmission;

         /* [Step2]: Calculate the slice PRB quota according to RRMPolicyRatio and MaxFreePRB */
         sliceCb->dedicatedPrb = (uint16_t)(((sliceCb->rrmPolicyRatioInfo.dedicatedRatio) * (maxFreePRB)) / 100);
         sliceCb->prioritizedPrb = (uint16_t)(((sliceCb->rrmPolicyRatioInfo.minRatio - sliceCb->rrmPolicyRatioInfo.dedicatedRatio) * (maxFreePRB)) / 100);
         sliceCb->sharedPrb = (uint16_t)(((sliceCb->rrmPolicyRatioInfo.maxRatio - sliceCb->rrmPolicyRatioInfo.minRatio) * (maxFreePRB)) / 100);
         minimumPrb = sliceCb->dedicatedPrb + sliceCb->prioritizedPrb;

#ifdef SLICE_BASED_DEBUG_LOG
         DU_LOG("\n\n===============Dennis  -->  SCH Intra-Slice : Start to run IntraSliceScheduling [SST:%d, MinimumPRB Quota:%d]===============",
                sliceCb->snssai.sst, minimumPrb);
#endif

         ueNode = ueDlNewTransmission->first;

         /* [Step3] */
         while (ueNode)
         {
            ueId = *(uint8_t *)(ueNode->node);
            ueCb = &cellCb->ueCb[ueId - 1];
            ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;

            /* Sum the weight of each UE */
            totalUeWeight += ueSliceBasedCb->weight;

            /* Update the requested BO of each LC in current slice */
            schSliceBasedUpdateLcListReqBo(&sliceCb->lcInfoList[ueId - 1], ueCb, DIR_DL);
            ueNode = ueNode->next;
         }

         /* [Step4]: Run the scheduling algorithm assigned to this slice */
         if (sliceCb->algorithm == RR)
         {
            if (minimumPrb != 0)
            {
               remainingPrb = minimumPrb;
               schSliceBasedRoundRobinAlgo(cellCb, ueDlNewTransmission, sliceCb->lcInfoList,
                                           pdschNumSymbols, &remainingPrb, sliceCb->algoMethod, NULLP);
            }

            /* Get the allocated PRB after scheduling algorithm */
            sliceCb->allocatedPrb = minimumPrb - remainingPrb;

#ifdef SLICE_BASED_DEBUG_LOG
            DU_LOG("\nDennis  -->  SCH Intra-Slice Result : [SST: %d, Allocated PRB: %d, Unallocated PRB: %d, Algo: RR]", sliceCb->snssai.sst,
                   sliceCb->allocatedPrb, remainingPrb);
#endif
         }
         else if (sliceCb->algorithm == WFQ)
         {
            /* Sort the UE list in terms of the weight */
            /* This function should be moved to schSliceBasedDlScheduling() when go through the UE list (for Jojo)*/
            schSliceBasedSortUeByWeight(cellCb, ueDlNewTransmission, totalUeWeight);

            if (minimumPrb != 0)
            {
               remainingPrb = minimumPrb;
               schSliceBasedWeightedFairQueueAlgo(cellCb, ueDlNewTransmission, sliceCb->lcInfoList,
                                                  pdschNumSymbols, &remainingPrb, sliceCb->algoMethod, NULLP);
            }

            /* Get the allocated PRB after scheduling algorithm */
            sliceCb->allocatedPrb = minimumPrb - remainingPrb;

#ifdef SLICE_BASED_DEBUG_LOG
            DU_LOG("\nDennis  -->  SCH Intra-Slice Result : [SST: %d, Allocated PRB: %d, Unallocated PRB: %d, Algo: WFQ]", sliceCb->snssai.sst,
                   sliceCb->allocatedPrb, remainingPrb);
#endif
         }
         else
         {
            DU_LOG("\nDennis  -->  In schSliceBasedDlIntraSliceScheduling() : Invalid Scheduling Algorithm");
            return;
         }

         /* [Step5]: Calculate the remaining PRB according to the rule */
         if (sliceCb->allocatedPrb > sliceCb->dedicatedPrb)
         {
            *totalRemainingPrb = *totalRemainingPrb - sliceCb->allocatedPrb;
         }
         else
         {
            *totalRemainingPrb = *totalRemainingPrb - sliceCb->dedicatedPrb;
         }

         /* Set the trigger flag to 0 and waiting for next trigger point */
         *dlThreadArg->triggerFlag = 0;

         /* Sleep to reduce the CPU usage */
         usleep(500);
      }
   }
}

```

The main difference with the single execution thread is that the ==multi-execution thread will trigger triggerFlag = 1, (STEP 1); to make it execute==, and then the 
- RRMPolicyRatio
- scheduling algorithm assigned
- Calculate the remaining PRB

Basically there is no difference with the single execution thread.There is a more detailed explanation on this in [OSC - Intra-slice scheduling](https://hackmd.io/eipL1-j2SByWqeVpaoNfCg).