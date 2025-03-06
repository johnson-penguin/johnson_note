---
title: OSC - Final scheduling
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
- UE ID
- Retranmission Flag
- Double Pointer to HARQ DL Process Controll Block
- Remaining PRBs after intra-slice scheduling
- Start PRB Index
      
### code
:::success
```c=
   ueNode = ueDlNewTransmission->first;
   while(ueNode)
   {  
      ueId = *(uint8_t *)(ueNode->node);
      ueCb = &cellCb->ueCb[ueId-1];
      ueSliceBasedCb = (SchSliceBasedUeCb *)ueCb->schSpcUeCb;

      mcsIdx = ueCb->ueCfg.dlModInfo.mcsIndex;
      if(remainingPrb != 0)
      {
         cmLListInit(&defLcList);

         /* Allocate the remaining PRB to default slice */
         for(lcIdx = 0; lcIdx < MAX_NUM_LC; lcIdx++)
         {
            if(ueCb->dlInfo.dlLcCtxt[lcIdx].snssai == NULLP && ueCb->dlInfo.dlLcCtxt[lcIdx].bo != 0)
            {
               /*[Step2]: Update the reqPRB and Payloadsize for this LC in the appropriate List*/
               if(updateLcListReqPRB(&defLcList, ueCb->dlInfo.dlLcCtxt[lcIdx].lcId,\
                        (ueCb->dlInfo.dlLcCtxt[lcIdx].bo + MAC_HDR_SIZE)) != ROK)
               {
                  DU_LOG("\nERROR  --> SCH : Updation in LC List Failed");
                  /* Free the dl ded msg info allocated in macSchDlRlcBoInfo */
                  // if(!dciSlotAlloc->dlMsgPdschCfg)
                  // {
                  //    SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
                  //    (*hqP)->hqEnt->cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueCb->ueId -1] = NULL;
                  // }
               }
               else
               {
#ifdef SLICE_BASED_DEBUG_LOG
                  DU_LOG("\nDennis  -->  SCH : Append LC to default LL [LCID, reqBO] [%d, %d]", lcIdx, \
                  ueCb->dlInfo.dlLcCtxt[lcIdx].bo + MAC_HDR_SIZE);
#endif
               }
            }
         }

         availablePrb = remainingPrb;
         schSliceBasedPrbAllocUsingRRMPolicy(
             &defLcList, mcsIdx, pdschNumSymbols, 
             &availablePrb, &ueSliceBasedCb->isTxPayloadLenAdded, NULLP
         );

#ifdef SLICE_BASED_DEBUG_LOG
         DU_LOG("\nDennis  -->  SCH Final Default Slice : [UE ID: %d, Allocated PRB: %d, Remaining PRB: %d]", ueId, remainingPrb - availablePrb, availablePrb);
#endif
         remainingPrb = availablePrb;
      }
      ueNode = ueNode->next;
   }
   availablePrb = remainingPrb;
         schSliceBasedPrbAllocUsingRRMPolicy(&defLcList, mcsIdx, pdschNumSymbols, &availablePrb, &ueSliceBasedCb->isTxPayloadLenAdded, NULLP);

#ifdef SLICE_BASED_DEBUG_LOG
         DU_LOG("\nDennis  -->  SCH Final Default Slice : [UE ID: %d, Allocated PRB: %d, Remaining PRB: %d]", ueId, remainingPrb - availablePrb, availablePrb);
#endif
         remainingPrb = availablePrb;
      }
      ueNode = ueNode->next;
   }
```    

This code is used to process each UE to determine how to allocate the remainingPrb to the logical channels (LC) of the UEs in the Default Slice.

Mainly process the UEs in the Default Slice to determine the requested resource blocks for the LCs of these UEs. These operations are associated with the subsequent scheduling algorithm and resource allocation.

- 透過傳送清單取得節點訊息
- 透過節點訊息取得使用者id
- 透過使用者id取得使用者控制塊以獲得各式各樣的傳送訊息（bsr dlcb ulcb map.......）
- 轉置成切片形式的結構
- 透過uecb取得MCS(?)

遍歷所有的lc

- 條件檢查1 ： 資源有剩的情況下
- - 初始化一個新的鏈結列表defLcList，用於存儲屬於預設片段且需要分配資源塊的UE的LC
- 條件檢查2 ： snssai以及blocking狀態是否有或者是否有未傳送的資料需要被分配資源
- 透過 updateLcListReqPRB()做更新

長此以往直到所有lc被遍歷過

>Q1 : what is cmLListInit(&defLcList) this mean ?
>
>Q2 : lc遍歷跟ue讀取資料的關係
>
>Q3 : availablePrb = remainingPrb; 中的remaingingPrb是由內網切片的過程中取得的嗎？
>
:::


#### updateLcListReqPRB()
:::danger


to be continued
:::

***

#### schSliceBasedPrbAllocUsingRRMPolicy()
:::danger

Allocate and estimated Prb for each LC within current slice

to be continued
:::

### Algorithm selection


The principles and examples of the algorithm can be found in the section on Intra-slice scheduling.

:::info

```c=
      if(sliceCb->algorithm == RR)
      {   
#ifdef SLICE_BASED_DEBUG_LOG
         DU_LOG("\n\n===============Dennis  -->  SCH Final : Start to run FinalScheduling [SST:%d, Shared PRB Quota:%d, Remaining PRB:%d, Algo: RR]===============", \
         sliceCb->snssai.sst, sliceCb->sharedPrb, remainingPrb);
#endif

         if(remainingPrb != 0)
         {
            if(sliceCb->sharedPrb >= remainingPrb)
            {
               availablePrb = remainingPrb; 
               schSliceBasedRoundRobinAlgo(cellCb, ueDlNewTransmission, sliceCb->lcInfoList, \
                                    pdschNumSymbols, &availablePrb, sliceCb->algoMethod, NULLP);
               sliceCb->allocatedPrb += remainingPrb - availablePrb;
               remainingPrb = availablePrb;
            }
            else
            {
               availablePrb = sliceCb->sharedPrb;
               schSliceBasedRoundRobinAlgo(cellCb, ueDlNewTransmission, sliceCb->lcInfoList, \
                                    pdschNumSymbols, &availablePrb, sliceCb->algoMethod, NULLP);
               sliceCb->allocatedPrb += sliceCb->sharedPrb - availablePrb;
               remainingPrb = remainingPrb - (sliceCb->sharedPrb - availablePrb);
            }
         }
      }


-------------------------------------------------------------------------

      else if(sliceCb->algorithm == WFQ)
      {
#ifdef SLICE_BASED_DEBUG_LOG
         DU_LOG("\n\n===============Dennis  -->  SCH Final : Start to run FinalScheduling [SST:%d, Shared PRB Quota:%d, Remaining PRB:%d, Algo: WFQ]===============", \
         sliceCb->snssai.sst, sliceCb->sharedPrb, remainingPrb);
#endif

         if(remainingPrb != 0)
         {
            if(sliceCb->sharedPrb >= remainingPrb)
            {
               availablePrb = remainingPrb; 
               schSliceBasedWeightedFairQueueAlgo(cellCb, ueDlNewTransmission, sliceCb->lcInfoList, \
                                    pdschNumSymbols, &availablePrb, sliceCb->algoMethod, NULLP);
               sliceCb->allocatedPrb += remainingPrb - availablePrb;
               remainingPrb = availablePrb;
            }
            else
            {
               availablePrb = sliceCb->sharedPrb;
               schSliceBasedWeightedFairQueueAlgo(cellCb, ueDlNewTransmission, sliceCb->lcInfoList, \
                                    pdschNumSymbols, &availablePrb, sliceCb->algoMethod, NULLP);
               sliceCb->allocatedPrb += sliceCb->sharedPrb - availablePrb;
               remainingPrb = remainingPrb - (sliceCb->sharedPrb - availablePrb);
            }
         }
      }
      else
      {
         DU_LOG("\nDennis  -->  In schSliceBasedDlFinalScheduling() : Invalid Scheduling Algorithm");
         return;
      }

```    

:::

       /* TODO: Although it has the loop, but HARQ List part should be checked */



>Q1 : 關於TODO的harq提醒？
>暫時不確定誰寫的

### Allocate PDCCH and PDSCH resources for the ue

:::success
```c=
 /* Allocate PDCCH and PDSCH resources for the ue */
      if((*hqP)->hqEnt->cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueCb->ueId -1] == NULL)
      {
         SCH_ALLOC(dciSlotAlloc, sizeof(DlMsgSchInfo));
         if(!dciSlotAlloc)
         {
            DU_LOG("\nERROR  -->  SCH : Memory Allocation failed for ded DL msg alloc");
            return false;
         }
         (*hqP)->hqEnt->cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueCb->ueId -1] = dciSlotAlloc;
         memset(dciSlotAlloc, 0, sizeof(DlMsgSchInfo));
      }
      else
      {
         dciSlotAlloc = (*hqP)->hqEnt->cell->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueCb->ueId -1];
      }

      /* Dl ded Msg info is copied, this was earlier filled in macSchDlRlcBoInfo */
      fillDlMsgInfo(dciSlotAlloc, crnti, isRetx, *hqP);
      dciSlotAlloc->transportBlock[0].ndi = isRetx;

      
```    
指針指出的位置是否NUll

YES(沒資源) : 開始進行資料分配
- 資料分配,透SCH_ALLOC將DL資源分配於dciSlotAlloc這個記憶體位置
- 分配後檢查(成功)
- 將dciSlotAlloc分配的記憶體地址存儲在特定UE的DL資源分配列表中，以便之後可以訪問這些資源(沒用完的話之後指標能指出!=NULL,之後就不用作資源分配)。
- memset清空dciSlotAlloc

- 分配後檢查(失敗)
- 回報錯誤訊息


NO(有資源) : dciSlotAlloc指針設置為該已分配的資源的位置，不再執行記憶體分配。



>Q1 : 第一行指標路徑疑問？
>

>Q2 : dciSlotAlloc路徑以及其該算記憶體位置還是單純的指標
>區域變數
:::





#### schSliceBasedUpdateGrantSizeForBoRpt()
to be continued
:::info
1. Check the LC List and fill the LC and GrantSize to be sent to MAC as BO Report in dlMsgAlloc Pointer
2. Reset the lcInfoList after filling the scheduling result to DCI
:::

#### fillDlMsgInfo()
to be continued
:::success
1. fill DL message information for MSG4 and Dedicated DL Msg

:::



### Dl ded Msg info is copied, this was earlier filled in macSchDlRlcBoInfo
:::warning
```c=
/* Dl ded Msg info is copied, this was earlier filled in macSchDlRlcBoInfo */
      fillDlMsgInfo(dciSlotAlloc, crnti, isRetx, *hqP);
      dciSlotAlloc->transportBlock[0].ndi = isRetx;

      /*[Step5]:Traverse each LCID in LcList to calculate the exact Scheduled Bytes
      * using allocated BO per LC and Update dlMsgAlloc BO report for MAC */
      sliceCbNode = schSpcCell->sliceCbList.first;

      if (isRetx == FALSE)
      {
         /* Update default slice allocation result to DCI in terms of LC */
         if(defLcList.count != 0)
            schSliceBasedUpdateGrantSizeForBoRpt(&defLcList, dciSlotAlloc, NULLP, &accumalatedSize, FALSE);
         
         /* Update dedicated slice allocation result to DCI in terms of LC */
         while(sliceCbNode)
         {
            sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node;
            
            if(sliceCb->lcInfoList[ueId-1].count != 0)
               schSliceBasedUpdateGrantSizeForBoRpt(&sliceCb->lcInfoList[ueId-1], dciSlotAlloc, NULLP, &accumalatedSize, TRUE);
               
            sliceCbNode = sliceCbNode->next;
         }
      }
      else
      {
         accumalatedSize = (*hqP)->tbInfo[0].tbSzReq;
      }
```
- 訊息(*isRetx* ,crnti ,dciSlotAlloc, hqp)填入dciSlotAlloc
- 初始化各個節點
- 依照*isRetx*的bool值判斷重傳的情況

非重傳
- 更新預設切片的DCI（FALSE）
- traverse 每個切片並且在( sliceCb->lcInfoList[ueId-1].count != 0 )的條件下,更新專用切片的資源(TRUE)


重傳
- 將重傳對象的大小做變數儲存



>Q1 transportBlock array number mean ?
>Q2 (defLcList.conunt == 0) situation and what is conunt in this way mean ?
>Q3 accumalatedSize mean lc remain resource ?
:::




#### schSliceBasedUpdateGrantSizeForBoRpt()
to be continued


:::warning
Functionality:
1. Check the LC List and fill the LC and GrantSize to be sent to MAC as BO Report in dlMsgAlloc Pointer
2. Reset the lcInfoList after filling the scheduling result to DCI

---

1. 查看 LC 列表，並在 dlMsgAlloc 卸載器中填寫要作為 BO 報告發送給 MAC 的 LC 和GrantSize。

2. 將調度結果填入DCI後，重置lcInfoList
:::

### Below case will hit if NO LC(s) are allocated due to resource crunch

:::success

```c=

if(!accumalatedSize)
      {
         if(remainingPrb == 0)
         {
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
         
         /*JOJO: If failed, traverse next UE.*/
         ueNode = ueNode->next;
         continue;
      }

```

- 當accumalatedSize不為0時進入判斷   if(!0)=if(1)
- 區分是沒有PRB還是沒有lc資源
- traverse所有UE做判斷


Q1 : 沒被排程到的accumalatedSize會透過continue等下個迴圈重新分配嗎?
A1 : accumalatedSize代表所有被分配出去的資源有多大,這個環節可能不大會出現


Q2 : ueNode看起來會一直輪流被檢查,但迴圈跳出的條件好像與ueNode無關。

A2 :continue 會自動忽略底下其餘code重新回到迴圈上端進行下個使用者的操作

:::


### pdcch and pdsch data is filled

:::warning
```c=


      /*[Step6]: pdcch and pdsch data is filled */
      if((schDlRsrcAllocDlMsg(cellCb, pdschTime, crnti, accumalatedSize, dciSlotAlloc, startPrb, pdschStartSymbol, pdschNumSymbols, isRetx, *hqP)) != ROK)
      {
         DU_LOG("\nERROR  --> SCH : Scheduling of DL dedicated message failed");
         /* Free the dl ded msg info allocated in macSchDlRlcBoInfo */
         if(!dciSlotAlloc->dlMsgPdschCfg)
         {
            SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
            cellCb->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId -1] = NULL;
         }       
         /*JOJO: If failed, traverse next UE.*/
         ueNode = ueNode->next;
         continue;
      }
  
      if (isRetx != TRUE)
      {
         accumalatedSize += TX_PAYLOAD_HDR_LEN;
      }
      numPRB = schCalcNumPrb(accumalatedSize, ueCb->ueCfg.dlModInfo.mcsIndex, pdschNumSymbols);
      //DU_LOG("\nJOJO  -->  UE id: %d, is allocated %d PRBs (add header).", ueId, numPRB);
      startPrb += numPRB; /*JOJO: accumulate start PRB.*/
```

- 使用schDlRsrcAllocDlMsg()做資料填入,倘若失敗則會
- 依照(!dciSlotAlloc->dlMsgPdschCfg)是否為NULL來判斷可能是資源分配失敗或是資源分配尚未成功,此時將原先分配的記憶體釋放,並且清除這個ue,這表示該 UE 將不再有分配的 DL 資源,將會在切換uenode後進入continue,反之則繼續往下執行

- 非重傳訊息則將把傳送資料得標頭加入accumalatedSize並透過schCalcNumPrb（）計算PRB總數並加入至起始PRB位置,確保下次運算能正確位置進行資源分配






:::

#### schDlRsrcAllocDlMsg()

to be continued


:::info
- Fills pdcch and pdsch info for dl msg
:::


#### schCalcNumPrb()

to be continued

:::success
- This function calculates the number of PRbs 

:::



### Check if both DCI and DL_MSG are sent in the same slot. If not, allocate memory for DL_MSG PDSCH slot to store PDSCH info */
:::warning


```c=
if(cellCb->schDlSlotInfo[pdschTime.slot]->dlMsgAlloc[ueId-1] == NULLP)
         {
            SCH_ALLOC(dlMsgAlloc, sizeof(DlMsgSchInfo));
            if(dlMsgAlloc == NULLP)
            {
               DU_LOG("\nERROR  -->  SCH : Memory Allocation failed for dlMsgAlloc");
               SCH_FREE(dciSlotAlloc->dlMsgPdcchCfg, sizeof(PdcchCfg));
               if(dciSlotAlloc->dlMsgPdschCfg == NULLP)
               {
                  SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
                  cellCb->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId-1] = NULLP;
               }
               return false;
            }
            cellCb->schDlSlotInfo[pdschTime.slot]->dlMsgAlloc[ueId-1] = dlMsgAlloc;
            memset(dlMsgAlloc, 0, sizeof(DlMsgSchInfo));
         }
         else
            dlMsgAlloc = cellCb->schDlSlotInfo[pdschTime.slot]->dlMsgAlloc[ueId-1];

         /* Copy all DL_MSG info */
         dlMsgAlloc->crnti =crnti;
         dlMsgAlloc->bwp = dciSlotAlloc->bwp;
         SCH_ALLOC(dlMsgAlloc->dlMsgPdschCfg, sizeof(PdschCfg));
         if(dlMsgAlloc->dlMsgPdschCfg)
         {
            memcpy(dlMsgAlloc->dlMsgPdschCfg, &dciSlotAlloc->dlMsgPdcchCfg->dci.pdschCfg, sizeof(PdschCfg));
         }
         else
         {
            SCH_FREE(dciSlotAlloc->dlMsgPdcchCfg, sizeof(PdcchCfg));    
            if(dciSlotAlloc->dlMsgPdschCfg == NULLP)
            {
               SCH_FREE(dciSlotAlloc, sizeof(DlMsgSchInfo));
               cellCb->schDlSlotInfo[pdcchTime.slot]->dlMsgAlloc[ueId-1] = NULLP;

            }
            SCH_FREE(dlMsgAlloc, sizeof(DlMsgSchInfo));
            cellCb->schDlSlotInfo[pdschTime.slot]->dlMsgAlloc[ueId-1] = NULLP;
            DU_LOG("\nERROR  -->  SCH : Memory Allocation failed for dlMsgAlloc->dlMsgPdschCfg");
            return false;
         }
      }

```

- 確認pdcch跟pdsch的處於同個slot
- 將資源分配給pdsch,並進行確認


- 確認==失敗==則報錯,釋放已經使用的記憶體,並且在該ue的slot上標記nullp代表未被使用
> if(!dciSlotAlloc->dlMsgPdschCfg)
> 分配失敗則會發生NULLP
> if(!NULLP)則代表必定執行
- 最後將pdcch的資料複製到pdsch以保證資料一致


- 確認==成功==
- 進行對slot的資源檢查
- slot沒有資源則進行分配,並且根據分配的情況進行固定流程
- slot有資源則從cb>slotinfo>dlmsgalloc根據ue id 做檢查

***
- 將crnti, bwo ,並且將pdsch的配置先分配給一塊記憶體做備用

***
- 倘若記憶體分配成功,則將pdcch複製給pdsch
- 倘若記憶體分配失敗,釋放已經使用的pdcch記憶體
- 並評估pdsch是否為nullp
- - 是,釋放pdsch已經使用的空間,刪除使用者
- - 不是,釋放pdsch已經使用的空間,刪除使用者回報錯誤



Q1 why pdcchtime == pdschtime ?
pdcch是告訴ue
pdsch是告訴基地臺該在哪裡放


Q2 why 將pdsch的資料複製到pdcch確保兩者一致


Q3 pdcch分配失敗,對於pdsch的操作差別在哪
>研究k0





:::