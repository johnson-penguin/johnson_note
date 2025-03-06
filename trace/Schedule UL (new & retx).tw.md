---
title: Schedule UL (new & retx).tw
tags: [MAC Scheduler]

---

## Function Call Flow Chart

![uplink_sch.drawio](https://hackmd.io/_uploads/r1eDoJjo0.png)


## schProcessSrOrBsrReq
:::success
### 參數:
- SchCellCb *cell: 指向小區控制塊的指針。
- SlotTimingInfo currTime: 當前時間的槽定時信息。
- uint8_t ueId: 用戶設備的 ID。
- bool isRetx: 是否為重傳標記。
- SchUlHqProcCb **hqP: 上行高級調度處理控制塊的指針

### 流程



#### 空指針檢查:
檢查 cell 和 ueCb 是否為空，若是則記錄錯誤並返回 false。

1. cell 為空
    - 初始化失敗: 如果小區控制塊（SchCellCb）未正確初始化或分配，則可能會傳遞 NULLP（空指針）。
    - 小區不存在: 在系統中，當沒有可用的小區時，可能會導致 cell 為空。

2. ueCb 為空
    - 用戶設備 ID 無效: 如果傳遞的 ueId 超出範圍（例如，為 0 或大於可用用戶數量），則 ueCb 將無法正確索引，可能會導致獲取的指針為空。
    - 用戶設備未註冊: 在小區中，如果該用戶設備尚未註冊或已經被移除，則對應的 ueCb 也會是空的。
    - 內存分配問題: 如果在創建或管理用戶設備控制塊時出現內存分配失敗，則相應的 ueCb 可能會為空。
#### 高級調度處理獲取:

如果不是重傳，則調用 [schUlGetAvlHqProcess](#schUlGetAvlHqProcess) 獲取可用的高級調度處理。
#### 計算 DCI 發送時間:

使用 [ADD_DELTA_TO_TIME](#ADD_DELTA_TO_TIME) 這個marco計算發送 DCI（Downlink Control Information）的時間。
#### 獲取 K2 時間信息:

1. K2 資料表的選擇：
根據 `ueCb->k2TblPrsnt` 的值，決定使用 ==UE 的 K2 資料表==還是==小區的 K2 資料表==。若 k2TblPrsnt 為真，則使用 ueCb->k2InfoTbl，否則使用 cell->k2InfoTbl。
2. 遍歷 K2 時間信息：
使用 for 循環遍歷 k2InfoTbl->k2TimingInfo[dciTime.slot].numK2，這裡的 dciTime.slot 表示當前的時間槽。
```c=
if(ueCb->k2TblPrsnt)
    k2InfoTbl = &ueCb->k2InfoTbl;
else
    k2InfoTbl =  &cell->k2InfoTbl;

      for(k2TblIdx = 0; k2TblIdx < k2InfoTbl->k2TimingInfo[dciTime.slot].numK2; k2TblIdx++)
      {
         k2Index = k2InfoTbl->k2TimingInfo[dciTime.slot].k2Indexes[k2TblIdx];

         if(!ueCb->k2TblPrsnt)
         {
            k2Val = cell->cellCfg.ulCfgCommon.schInitialUlBwp.puschCommon.timeDomRsrcAllocList[k2Index].k2;
            startSymb = cell->cellCfg.ulCfgCommon.schInitialUlBwp.puschCommon.timeDomRsrcAllocList[k2Index].startSymbol;
            symbLen = cell->cellCfg.ulCfgCommon.schInitialUlBwp.puschCommon.timeDomRsrcAllocList[k2Index].symbolLength;
         }
         else
         {
            k2Val = ueCb->ueCfg.spCellCfg.servCellRecfg.initUlBwp.puschCfg.timeDomRsrcAllocList[k2Index].k2;
            startSymb =  ueCb->ueCfg.spCellCfg.servCellRecfg.initUlBwp.puschCfg.timeDomRsrcAllocList[k2Index].startSymbol;
            symbLen =  ueCb->ueCfg.spCellCfg.servCellRecfg.initUlBwp.puschCfg.timeDomRsrcAllocList[k2Index].symbolLength;
         }

```
#### 計算 PUSCH 發送時間:

使用 ADD_DELTA_TO_TIME 計算 PUSCH（Physical Uplink Shared Channel）的發送時間。
#### 檢查 PUSCH 信息:

檢查在計算出的 PUSCH 時間槽中是否已經存在該用戶的 PUSCH 信息。
#### 調度上行邏輯:

如果找到可用的 K2 值，則調用 SchScheduleUlLc 進行上行調度，否則返回 false。

:::
## schUlGetAvlHqProcess

:::success
### 流程:
#### 檢查空閒列表：
首先檢查 `ueCb->ulHqEnt.free.count` 是否為零(檢查是否能從 Harq 實體取得可用的 Harq 流程)，若為零則無資源，報錯處理。
#### 獲取第一個空閒處理程序：
使用 cmLListFirst 獲取(`ueCb->ulHqEnt`)空閒列表中的第一個處理程序。
#### 檢查獲取的處理程序：
如果獲取的處理程序為 NULLP，則返回 RFAILED。
#### 更新列表：
- 從空閒列表中刪除該處理程序 --> [schUlHqDeleteFromFreeList]()
- 將該處理程序添加到使用列表中 --> [schUlHqAddToInUseList]()
#### 設置最大重傳次數(透過uecb)：
將獲取的處理程序指針賦值給 hqP，並設置其最大重傳次數
- ueCb->ulHqEnt.maxHqTx：
    - ueCb 是指向用戶設備控制塊的指針，包含與該 UE 相關的各種參數和設置
    - ulHqEnt 是上行 HARQ 實體的結構，包含與 HARQ 相關的所有信息
    - maxHqTx 是 ulHqEnt 中的一個成員，表示最大重傳次數
#### 返回成功：
返回 ROK 表示成功。

:::

## schUlHqDeleteFromFreeList
```c=
void schUlHqDeleteFromFreeList(SchUlHqProcCb *hqP)
{
   if(hqP->hqEnt->free.count == 0)
   {
      DU_LOG("\n ERROR schUlHqDeleteFromInUseList no proc in in free\n");
   }
   cmLListDelFrm(&(hqP->hqEnt->free), &hqP->ulHqEntLnk);
}
```
- 主要目的是從上行 HARQ 實體的空閒列表中刪除指定的 HARQ 處理程序。
- 並且在刪除之前，函數會==檢查空閒列表是否為空==，以防止從空列表中刪除，並在此情況下記錄錯誤信息。

## schUlHqAddToInUseList
```c=
void schUlHqAddToInUseList(SchUlHqProcCb *hqP)
{
   cmLListAdd2Tail(&(hqP->hqEnt->inUse), &hqP->ulHqEntLnk);
}
```

- 使用 cmLListAdd2Tail 函數將 HARQ 處理程序的鏈接 ulHqEntLnk 添加到 hqP->hqEnt->inUse 列表的尾部。
- 這表示該 HARQ 處理程序現在==處於使用(inUse)==中。


## ADD_DELTA_TO_TIME
:::success
- 參數說明：
    - crntTime：當前時間的結構，應包含 sfn（系統幀號）和 slot（時隙）。
    - toFill：將被更新的結構，用於存儲計算後的時間。
    - incr：要增加的時隙數。
    - numOfSlot：總時隙數。
- 邏輯流程：
    - 首先檢查 crntTime.slot + incr 是否超過 numOfSlot - 1。如果超過，則意味著需要進入下一個系統幀（SFN），因此 toFill.sfn 增加 1。
    - 如果不超過，則 toFill.sfn 保持與 crntTime.sfn 相同。
    - 然後計算新的時隙 toFill.slot，使用模運算來確保它在有效範圍內。
    - 最後，檢查 toFill.sfn 是否達到或超過 MAX_SFN，如果是，則對其取模以保持在有效範圍內。
```c=
#define ADD_DELTA_TO_TIME(crntTime, toFill, incr, numOfSlot)          \
{                                                          \
   if ((crntTime.slot + incr) > (numOfSlot - 1))           \
   {                                                       \
      toFill.sfn = (crntTime.sfn + 1);                     \
   }                                                       \
   else                                                    \
   {                                                       \
      toFill.sfn = crntTime.sfn;                           \
   }                                                       \
   toFill.slot = (crntTime.slot + incr) % numOfSlot;       \
   if (toFill.sfn >= MAX_SFN)                              \
   {                                                       \
      toFill.sfn%=MAX_SFN;                                 \
   }                                                       \
}
```
:::

## schGetSlotSymbFrmt
:::success
根據給定的時隙（slot）和位圖（bitMap）來獲取相應的時隙配置格式（SlotConfig）

1. 計算偏移量，每個時隙佔用兩個位，因此計算當前時隙的偏移量。
2. 使用位運算從 bitMap 中提取對應於當前時隙的兩位數據。0x3 代表二進制的 11，用於選取兩位。
```c=
SlotConfig schGetSlotSymbFrmt(uint16_t slot, uint32_t bitMap)
{
   uint32_t offset = (slot)*2;
   return (bitMap & 0x3<<offset)>>offset;
#if 0
   SlotConfig slotFrmt;
   int mask1 = 0, mask2 = 0;

   slot = (slot%n)*2;//n num of slots in defined periodicity or num of symbols
   mask1 = 1<<(slot);
   mask2 = 1<<(slot+1);
   slotFrmt = ((mask1 & bitMap)>>slot) + (2*((mask2 & bitMap)>>(slot+1)));

   //printf("\n\n\n\n*****FormatType:%d Slot:%d****\n\n\n\n", slotFrmt, slot/2);

   return slotFrmt;
#endif
}
```
:::
## (API)SchScheduleUlLc
:::success
1. 變量初始化：定義了一些變量，包括小區控制塊 cell、用戶設備控制塊 ueCb 和 PUSCH 信息 puschInfo 等。
2. 獲取小區和用戶設備信息：從 `hqP` 中獲取小區和用戶設備的控制塊。
3. 調用 [schSliceBasedCalculateUlTbs](#schSliceBasedCalculateUlTbs) 函數計算上行 TBS，並獲取所需的 PRB（物理資源塊）起始位置和總數據請求大小。
```c=
uint8_t schSliceBasedScheduleUlLc(SlotTimingInfo dciTime, SlotTimingInfo puschTime, uint8_t startSymb , uint8_t symbLen, bool isRetx, SchUlHqProcCb **hqP)
{
   SchCellCb *cell;
   SchSliceBasedHqProcCb *schSpcHqProcCb;
   uint8_t ret = RFAILED;
   uint16_t startPrb = 0;
   uint32_t totDataReq = 0; /* in bytes */
   SchUeCb *ueCb;
   SchPuschInfo *puschInfo = NULLP;
   DciInfo  *dciInfo = NULLP;

   cell = (*hqP)->hqEnt->cell;
   ueCb = (*hqP)->hqEnt->ue;
   schSpcHqProcCb = (SchSliceBasedHqProcCb *)(*hqP)->schSpcUlHqProcCb;
   ret = schSliceBasedCalculateUlTbs(ueCb, puschTime, symbLen, &startPrb, &totDataReq, isRetx, *hqP, schSpcHqProcCb);
```
1. 檢查數據請求：如果 totDataReq 大於 0 且返回值為 ROK，則進行後續操作。
2. 使用 SCH_ALLOC 分配 dciInfo 的內存。如果分配失敗，則記錄錯誤並更新 BSR（帶寬請求）和邏輯通道列表。
```c=
   if(totDataReq > 0 && ret == ROK)
   {
      SCH_ALLOC(dciInfo, sizeof(DciInfo));
      if(!dciInfo)
      {
         DU_LOG("\nERROR  -->  SCH : Memory Allocation failed for dciInfo alloc");
         if(isRetx != TRUE)
         {
            if(schSpcHqProcCb->lcCb.dedLcList.count != 0)
               updateBsrAndLcList(&(schSpcHqProcCb->lcCb.dedLcList), ueCb->bsrInfo, RFAILED);

            updateBsrAndLcList(&(schSpcHqProcCb->lcCb.defLcList), ueCb->bsrInfo, RFAILED);
         }
         return RFAILED;
      }
      cell->schDlSlotInfo[dciTime.slot]->ulGrant = dciInfo;
      memset(dciInfo,0,sizeof(DciInfo));
```
1. 調用 schFillPuschAlloc 函數：該函數負責根據用戶設備（UE）的配置和請求信息填充 PUSCH 資源分配。如果返回值為 ROK，表示填充成功
2. 檢查 PUSCH 信息，確認在當前時間槽 (puschTime.slot) 中是否有對應用戶的 PUSCH 信息。如果存在，則將其指向 puschInfo
3. 填充 DCI 信息，如果 puschInfo 不為空，則調用 schFillUlDci 函數來填充 DCI（上行控制信息）以進行上行授權。
4. 重置接收標誌，將用戶設備的 SR（調度請求）和 BSR（帶寬請求）接收標誌設置為 false，表示這些請求已被處理。
```c=
      /* Update PUSCH allocation */
      if(schFillPuschAlloc(ueCb, puschTime, totDataReq, startSymb, symbLen, startPrb, isRetx, *hqP) == ROK)
      {
         if(cell->schUlSlotInfo[puschTime.slot]->schPuschInfo[ueCb->ueId - 1])
         {
            puschInfo = cell->schUlSlotInfo[puschTime.slot]->schPuschInfo[ueCb->ueId - 1];
            if(puschInfo != NULLP)
            {
               /* Fill DCI for UL grant */
               schFillUlDci(ueCb, puschInfo, dciInfo, isRetx, *hqP);
               ueCb->srRcvd = false;
               ueCb->bsrRcvd = false;
```
1. 檢查是否有專用邏輯通道（dedicated logical channels），若有則更新其 BSR 和邏輯通道列表，並將結果設置為 ROK。接著，無論如何都更新默認邏輯通道的 BSR
```c=
               if(schSpcHqProcCb->lcCb.dedLcList.count != 0)
                  updateBsrAndLcList(&(schSpcHqProcCb->lcCb.dedLcList), ueCb->bsrInfo, ROK);
               updateBsrAndLcList(&(schSpcHqProcCb->lcCb.defLcList), ueCb->bsrInfo, ROK);
               cmLListAdd2Tail(&(ueCb->hqUlmap[puschTime.slot]->hqList), &(*hqP)->ulSlotLnk);                  
               return ROK;
            }
         }
      }
```
1. 錯誤處理
```c=
      if(schSpcHqProcCb->lcCb.dedLcList.count != 0)
         updateBsrAndLcList(&(schSpcHqProcCb->lcCb.dedLcList), ueCb->bsrInfo, RFAILED);
      updateBsrAndLcList(&(schSpcHqProcCb->lcCb.defLcList), ueCb->bsrInfo, RFAILED);
   }
   return ROK;
}


```
:::

### schSliceBasedCalculateUlTbs
:::success
用於計算上行傳輸塊大小（TBS）的函數 schSliceBasedCalculateUlTbs，主要功能是根據用戶設備的狀態和請求信息來計算可分配的資源和傳輸大小。
```c=
uint8_t schSliceBasedCalculateUlTbs(SchUeCb *ueCb, SlotTimingInfo puschTime, uint8_t symbLen,\
                          uint16_t *startPrb, uint32_t *totTBS, bool isRetx, SchUlHqProcCb *hqP, SchSliceBasedHqProcCb *schSpcHqP)
{
   uint16_t mcsIdx = 0;
   CmLListCp *lcLL = NULLP;
   uint16_t lcgIdx = 0, lcId =0, maxFreePRB = 0;
   uint16_t rsvdDedicatedPRB;
   *startPrb = 0;
   *totTBS = 0;
```
1. 遍歷所有邏輯通道組，檢查帶寬請求（BSR）是否存在。
```c=
   /* check for BSR */
   for(lcgIdx=0; lcgIdx<MAX_NUM_LOGICAL_CHANNEL_GROUPS; lcgIdx++)
   {
      if(ueCb->bsrInfo[lcgIdx].dataVol == 0)
      {
         continue;
      }

      /*TODO: lcgIdx and LCID has been implemented as one to one mapping.
       * Need to check the mapping to figure out the LCID and lcgIdx once L2
       * spec specifies any logic*/
      lcId = lcgIdx;
      if(ueCb->ulInfo.ulLcCtxt[lcId].isDedicated)
      {
         lcLL = &(schSpcHqP->lcCb.dedLcList);
         rsvdDedicatedPRB = ueCb->ulInfo.ulLcCtxt[lcId].rsvdDedicatedPRB;
      }
      else
      {
         lcLL = &(schSpcHqP->lcCb.defLcList);
      }
```
1. 根據 BSR 透過[updateLcListReqPRB](#updateLcListReqPRB)更新每個邏輯通道的請求 PRB 和有效負載大小。根據邏輯通道的類型（專用或默認），選擇相應的邏輯通道列表
2. 檢查可用邏輯通道，如果沒有可用的邏輯通道，則檢查是否接收到調度請求或為重傳，並設置 `startPrb` 和 透過[schCalcTbSize](#schCalcTbSize)`totTBS`
3. 使用 [searchLargestFreeBlock]() 函數查找可用的最大 PRB 數量
```c=
      /*[Step2]: Update the reqPRB and Payloadsize for this LC in the appropriate List*/
      if(updateLcListReqPRB(lcLL, lcId, ueCb->bsrInfo[lcgIdx].dataVol) != ROK)
      {
         DU_LOG("\nERROR  --> SCH: LcgId:%d updation failed",lcId);         
         return RFAILED;
      }
   }

   if ((schSpcHqP->lcCb.defLcList.count == 0) && (schSpcHqP->lcCb.dedLcList.count == 0))
   {
      if( (ueCb->srRcvd) || (isRetx) )
      {
         *startPrb = MAX_NUM_RB;
         *totTBS = schCalcTbSize(UL_GRANT_SIZE);
      }
      /*Returning true when NO Grant is there for UE as this is not scheduling
       * error*/      
      return ROK;
   }

   maxFreePRB = searchLargestFreeBlock(ueCb->cellCb, puschTime, startPrb, DIR_UL);
```
1. 根據可用的 PRB 和專用邏輯通道的保留 PRB 情況，透過RRM分配 PRB 給專用和默認邏輯通道
2. 在確認仍有資源後(maxFreePRB != 0)，優先分配資源給專用邏輯通道，在專用邏輯通道以及小於保留資源時才開始進行預設邏輯通道的資源分配
```c=
   /*[Step4]: Estimation of PRB and BO which can be allocated to each LC in
    * the list based on RRM policy*/

   /*Either this UE contains no reservedPRB pool fir dedicated S-NSSAI or 
    * Num of Free PRB available is not enough to reserve Dedicated PRBs*/
   if(maxFreePRB != 0)
   {
      mcsIdx = ueCb->ueCfg.ulModInfo.mcsIndex;
      if((schSpcHqP->lcCb.dedLcList.count == 0) || ((maxFreePRB < rsvdDedicatedPRB)))
      {
         schSpcHqP->lcCb.sharedNumPrb = maxFreePRB;
         DU_LOG("\nDEBUG  -->  SCH : UL Only Default Slice is scheduled, sharedPRB Count:%d",\
               schSpcHqP->lcCb.sharedNumPrb);

         /*PRB Alloc for Default LCs*/
         prbAllocUsingRRMPolicy(&(schSpcHqP->lcCb.defLcList), FALSE, mcsIdx, symbLen,\
               &(schSpcHqP->lcCb.sharedNumPrb), NULLP, NULLP,&(ueCb->srRcvd));
      }
      else
      {
         schSpcHqP->lcCb.sharedNumPrb = maxFreePRB - rsvdDedicatedPRB;

         /*PRB Alloc for Dedicated LCs*/
         prbAllocUsingRRMPolicy(&(schSpcHqP->lcCb.dedLcList), TRUE, mcsIdx, symbLen,\
               &(schSpcHqP->lcCb.sharedNumPrb), &(rsvdDedicatedPRB),\
               NULLP, &(ueCb->srRcvd));

         /*PRB Alloc for Default LCs*/
         prbAllocUsingRRMPolicy(&(schSpcHqP->lcCb.defLcList), FALSE, mcsIdx, symbLen, \
               &(schSpcHqP->lcCb.sharedNumPrb), &(rsvdDedicatedPRB),\
               NULLP,&(ueCb->srRcvd));
      }
   }
```
遍歷每個邏輯信道（LCID），計算已分配的比特率（BO）所對應的實際調度字節，並透過[updateGrantSizeForBoRpt](#updateGrantSizeForBoRpt)更新報告給媒介訪問控制（MAC）層
```c=
   /*[Step5]:Traverse each LCID in LcList to calculate the exact Scheduled Bytes
    * using allocated BO per LC and Update dlMsgAlloc(BO report for MAC*/ 
   if(schSpcHqP->lcCb.dedLcList.count != 0)
      updateGrantSizeForBoRpt(&(schSpcHqP->lcCb.dedLcList), NULLP, ueCb->bsrInfo, totTBS);

   updateGrantSizeForBoRpt(&(schSpcHqP->lcCb.defLcList), NULLP, ueCb->bsrInfo, totTBS);

   /*Below case will hit if NO LC(s) are allocated due to resource crunch*/
   if (*totTBS == 0)
   {
      if(maxFreePRB == 0)
      {
         DU_LOG("\nERROR  --> SCH : NO FREE PRB!!");
      }
      else
      {
         /*Schedule the LC for next slot*/
         DU_LOG("\nDEBUG  -->  SCH : No LC has been scheduled");
      }      
      return RFAILED;
   }   
   return ROK;
}


```
:::
## updateLcListReqPRB
:::success
此fun，透過[handleLcLList]()獲取或創建對應的 LC 節點。
如果該節點不存在，則返回失敗。
在確認LC存在後，更新特定邏輯信道（LCID）在邏輯信道鏈表中的請求物理資源區塊（PRB）
```c=
uint8_t updateLcListReqPRB(CmLListCp *lcLL, uint8_t lcId, uint32_t payloadSize)
{
   LcInfo    *lcNode = NULLP;
   lcNode = handleLcLList(lcLL, lcId, CREATE);

   if(lcNode == NULLP)
   {
      DU_LOG("\nERROR  -->  SCH : LC is neither present nor able to create in List lcId:%d",lcId);
      return RFAILED;
   }

   lcNode->reqBO = payloadSize;
   lcNode->allocBO = 0;
   lcNode->allocPRB = 0; /*Re-Initializing the AllocPRB*/
   return ROK;
}
```
:::

### handleLcLList
:::success
函數接收三個參數，並且在確認LC存在後才開始進行操作
- 邏輯信道鏈表指針 lcLL
- 邏輯信道 ID lcId 
- 操作類型 action


```c=
LcInfo* handleLcLList(CmLListCp *lcLL, uint8_t lcId, ActionTypeLL action)
{
   CmLList  *node = NULLP;
   LcInfo *lcNode = NULLP;
   bool found = FALSE;

   if(lcLL == NULLP)
   {
      DU_LOG("\nERROR  -->  SCH: LcList doesnt exist");
      return NULLP;
   }
   node = lcLL->first;

   /*Traversing the LC LinkList*/
   while(node)
   {
      lcNode = (LcInfo *)node->node;
      if(lcNode->lcId == lcId)
      { 
         found = TRUE;
         break;
      }
      node = node->next;
   }//end of while
```
- 搜索
```c=
   switch(action)
   {
      case SEARCH:
         {
            if(!found)
            {
               lcNode = NULLP;
            }
            return lcNode;
         }
```
- 創建
```c=
      case CREATE:
         {
            if(node != NULLP)
               return lcNode;

            /*Need to add a new node for this LC*/

            /*List is empty; Initialize the LL ControlPointer*/
            if(lcLL->count == 0)
            {
               cmLListInit(lcLL);
            }

            lcNode = NULLP;
            /*Allocate the List*/
            SCH_ALLOC(lcNode, sizeof(LcInfo));
            if(lcNode)
            {
               lcNode->lcId = lcId;
               lcNode->reqBO = 0;
               lcNode->allocBO = 0;
               lcNode->allocPRB = 0;
            }
            else
            {
               DU_LOG("\nERROR  -->  SCH : Allocation of List failed,lcId:%d",lcId);
               return NULLP;
            }

            if(addNodeToLList(lcLL, lcNode, NULLP) == RFAILED)
            {
               DU_LOG("\nERROR  -->  SCH : failed to Add Node,lcId:%d",lcId);
               SCH_FREE(lcNode, sizeof(LcInfo));
               return NULLP;
            }
            return lcNode;
         }
```
- 刪除
```c=
      case DELETE:
         {
            if(!found ||  lcNode == NULLP)
            {
               DU_LOG("\nERROR  -->  SCH: LCID%d not found; thus Deletion unsuccessful",lcId);
            }
            else
            {
               if(deleteNodeFromLList(lcLL, node) == ROK)
                  SCH_FREE(lcNode, sizeof(LcInfo));

               DU_LOG("\nDEBUG  -->  SCH: LCID%d Deleted successfully",lcId);
            }
            return NULLP; 
         }
```
- PRINT 和 TRAVERSE_ALL 尚未實現具體功能
```c=
      case PRINT:
      case TRAVERSE_ALL:
        {
           break;
        }
      default:
         {
            DU_LOG("\nERROR  -->  SCH: Action type wrong: %d",action);
            break;
         }
   }
   return lcNode;
}

```

:::

## schCalcTbSize
:::success
用於計算傳輸塊大小（TBSize）的函數 schCalcTbSize，根據 3GPP 規範中的表格來確定傳輸塊的大小
通過查找 tbSizeTable 中的最後一個值來獲取最大傳輸塊大小，並將其轉換為字節單位（因為 tbSizeTable 中的值通常是以比特計算的）

- 38.214
![image](https://hackmd.io/_uploads/ryQ8c7_2R.png)

```c=
uint16_t schCalcTbSize(uint32_t payLoadSize)
{
   uint8_t tbsIndex = 0;
   uint16_t maxTbSize;

   maxTbSize = tbSizeTable[TOTAL_TBSIZE_VALUES -1]/8;
```
payLoadSize 指的是數據載荷的大小，通常以字節（bytes）為單位。這個大小代表了要傳輸的實際數據量，不包括任何協議開銷或標頭信息。
```c=
   if(payLoadSize >= maxTbSize)
      return maxTbSize;

   payLoadSize = payLoadSize*8;
   while(payLoadSize > tbSizeTable[tbsIndex])
   {
      tbsIndex++;
   }

   /* return the TBsize in bytes */
   return (tbSizeTable[tbsIndex]/8);
}
```
:::
## searchLargestFreeBlock


```c=
uint16_t searchLargestFreeBlock(SchCellCb *cell, SlotTimingInfo slotTime,uint16_t *startPrb, Direction dir)
{
   uint16_t       reservedPrbStart=0, reservedPrbEnd=0, maxFreePRB = 0;
   FreePrbBlock   *freePrbBlock = NULLP;
   CmLList        *freePrbNode = NULLP;
   SchPrbAlloc    *prbAlloc = NULLP;
   bool           checkOccasion = FALSE;

   *startPrb = 0; /*Initialize the StartPRB to zero*/

   /*Based on Direction, Reserved Messsages will differi.e.
    * DL >> SSB and SIB1 ocassions wheres for UL, PRACH ocassions to be checked
    * and reserved before allocation for dedicated DL/UL msg*/
```
- Downlink，透過 [schCheckSsbOcc]()、[schCheckSib1Occ]() 檢查 SSB 和 SIB1 的時機，並根據這些時機設置保留 PRB 的範圍
```c=
    if(dir == DIR_DL)
   {
      SchDlSlotInfo  *schDlSlotInfo = cell->schDlSlotInfo[slotTime.slot];
      PduTxOccsaion  ssbOccasion=0, sib1Occasion=0;

      prbAlloc = &schDlSlotInfo->prbAlloc;

      ssbOccasion = schCheckSsbOcc(cell, slotTime);
      sib1Occasion = schCheckSib1Occ(cell, slotTime);

      checkOccasion = TRUE;
```
```c=
      if(ssbOccasion && sib1Occasion)
      {
         reservedPrbStart = cell->cellCfg.dlCfgCommon.schFreqInfoDlSib.offsetToPointA; 
         reservedPrbEnd = reservedPrbStart + SCH_SSB_NUM_PRB + \
                          cell->sib1SchCfg.sib1PdcchCfg.dci[0].pdschCfg.pdschFreqAlloc.numPrb -1;
      }
      else if(ssbOccasion)
      {
         reservedPrbStart = cell->cellCfg.dlCfgCommon.schFreqInfoDlSib.offsetToPointA;
         reservedPrbEnd = reservedPrbStart + SCH_SSB_NUM_PRB -1;
      }
      else if(sib1Occasion)
      {
         reservedPrbStart = cell->sib1SchCfg.sib1PdcchCfg.dci[0].pdschCfg.pdschFreqAlloc.startPrb;
         reservedPrbEnd = reservedPrbStart + cell->sib1SchCfg.sib1PdcchCfg.dci[0].pdschCfg.pdschFreqAlloc.numPrb -1;
      }
      else
      {
         checkOccasion = FALSE;  
      }
   }
```
- Uplink

使用 [schCheckPrachOcc]() 函數來檢查在當前時隙中是否有 PRACH 被調度。

reservedPrbStart 被設置為從配置結構中獲取的 PRACH（物理隨機接入信道）==起始頻率==。

這個值通常來自於小區配置中的上行配置（ulCfgCommon）和隨機接入配置（schRachCfg），具體是 msg1FreqStart，它指示了 PRACH 的起始頻率。
```c=
   else if(dir == DIR_UL)
   {
      prbAlloc = &cell->schUlSlotInfo[slotTime.slot]->prbAlloc;

      /* Check if PRACH is also scheduled in this slot */
      checkOccasion = schCheckPrachOcc(cell, slotTime);
      if(checkOccasion)
      {
         reservedPrbStart =  cell->cellCfg.ulCfgCommon.schInitialUlBwp.schRachCfg.prachCfgGeneric.msg1FreqStart;
         reservedPrbEnd = reservedPrbStart + (schCalcPrachNumRb(cell)) -1;
      }
   }
   else
   {
      DU_LOG("\nERROR --> SCH: Invalid Direction!");
      return (maxFreePRB);
   }
```
循環遍歷自由 PRB 塊列表，找到擁有最多自由 PRB 的塊
```c=
   freePrbNode = prbAlloc->freePrbBlockList.first; 
   while(freePrbNode)
   {
      freePrbBlock = (FreePrbBlock *)freePrbNode->node;
```
由於我們需要的是最大的PRB塊，因此這個環節比當前最大值小的 PRB 塊會被跳過
```c=
      /*For block with same numFreeBlocks, choose the one with HighestPRB range
       *Since FreeBLockList are arranged in Descending order of PRB range thus Skipping this block*/
      if(maxFreePRB >= freePrbBlock->numFreePrb) 
      {
         //skip this block
         freePrbNode = freePrbNode->next;
         continue;
      }
```
在第一個判斷式上檢查保留消息的 PRB 是否在當前自由塊中
1. (保留訊息的PRB==起始==位置要比自由塊==起始==位置大 並且保留訊息的PRB==起始位置要比自由塊==終點==位置小)==>>保留訊息的PRB的==起點==必須在當前自由塊的範圍內
2. (保留訊息的PRB==終點==位置要比自由塊==起始==位置大 並且保留訊息的PRB==終點位置要比自由塊==終點==位置小)==>>保留訊息的PRB的==終點==必須在當前自由塊的範圍內
3. 確認保留訊息的prb全程都待在自由塊裡面
```c=
      /* If Broadcast/Prach message is scheduled in this slot, then check if its PRBs belong to the current free block.
       * Since SSB/SIB1 PRB location is fixed, these PRBs cannot be allocated to other message in same slot */
      if(checkOccasion && 
            ((reservedPrbStart >= freePrbBlock->startPrb) && (reservedPrbStart <= freePrbBlock->endPrb)) && \
            ((reservedPrbEnd >= freePrbBlock->startPrb) && (reservedPrbEnd <= freePrbBlock->endPrb)))
      {

         /* Implmentation is done such that highest-numbered free-RB is Checked first
            and freePRB in this block is greater than Max till now */
         if((freePrbBlock->endPrb > reservedPrbEnd) && ((freePrbBlock->endPrb - reservedPrbEnd) > maxFreePRB))
         {
            /* If sufficient free PRBs are available above reserved message*/
            *startPrb = reservedPrbEnd + 1;
            maxFreePRB = (freePrbBlock->endPrb - reservedPrbEnd);		 
         }
         /*Also check the other freeBlock (i.e. Above the reserved message) for MAX FREE PRB*/
         if((reservedPrbStart > freePrbBlock->startPrb) && ((reservedPrbStart - freePrbBlock->startPrb) > maxFreePRB))
         {
            /* If free PRBs are available below reserved message*/
            *startPrb = freePrbBlock->startPrb;
            maxFreePRB = (reservedPrbStart - freePrbBlock->startPrb);
         }
      }
```
1. 無任何保留訊息的最佳自由塊
```c=
      else  //Best Block
      {
         if(maxFreePRB < freePrbBlock->numFreePrb)
         {
            *startPrb = freePrbBlock->startPrb;
            maxFreePRB = freePrbBlock->numFreePrb;
         }

      }
      freePrbNode = freePrbNode->next;
   }  
   return(maxFreePRB);
}

```

## prbAllocUsingRRMPolicy
用於根據 RRM 策略分配 PRB

參數包括 :
LC（邏輯通道）鏈表指針、是否使用專用 PRB 的標誌、MCS 指數、符號數量
、共享和保留 PRB 計數、TX 負載長度添加標誌及上行接收標誌
```c=
void prbAllocUsingRRMPolicy(CmLListCp *lcLL, bool isDedicatedPRB, uint16_t mcsIdx,uint8_t numSymbols,\
                  uint16_t *sharedPRB, uint16_t *reservedPRB, bool *isTxPayloadLenAdded, bool *srRcvd)
{
   CmLList *node = NULLP;
   LcInfo *lcNode = NULLP;
   uint16_t remReservedPRB = 0, estPrb = 0, maxPRB = 0;

   if(lcLL == NULLP)
   {
      DU_LOG("\nERROR --> SCH: LcList not present");
      return;
   }
   node = lcLL->first;
```

初始化保留 Dedicated PRB：
```c=
   /*Only for Dedicated LcList, Valid value will be assigned to remReservedPRB
    * For Other LcList, remReservedPRB = 0*/
   if(reservedPRB != NULLP && isDedicatedPRB == TRUE)
   {
      remReservedPRB = *reservedPRB;
   }
```

檢查LC需求是否為空，此處用於避免極少數的bug，因為理論上無需求者應該已經在前面被剃除
```c=
   /*[Step1]*/
   while(node)
   {
#if 0
      /*For Debugging purpose*/
      printLcLL(lcLL);
#endif
      lcNode = (LcInfo *)node->node;

      /* [Step2]: Below condition will hit in rare case as it has been taken care during the cleaning 
       * process of LCID which was fully allocated. Check is just for safety purpose*/
      if(lcNode->reqBO == 0 && lcNode->allocBO == 0)
      {
         DU_LOG("\nERROR --> SCH: LCID:%d has no requirement, clearing this node",\
               lcNode->lcId);
         deleteNodeFromLList(lcLL, node);
         SCH_FREE(lcNode, sizeof(LcInfo));
         node = lcLL->first; 
         continue;
      }
```

特殊情況 : 所有LC已經被分配資源
```c=
      /*[Exit1]: All LCs are allocated(allocBO = 0 for fully unallocated LC)*/
      if(lcNode->allocBO != 0)
      {
         DU_LOG("\nDEBUG  -->  SCH: All LC are allocated [SharedPRB:%d]",*sharedPRB);
         return;
      }
```
特殊情況 : PRB耗盡
```c=
      /*[Exit2]: If PRBs are exhausted*/
      if(isDedicatedPRB)
      {
         /*Loop Exit: All resources exhausted*/
         if(remReservedPRB == 0 && *sharedPRB == 0)
         {
            DU_LOG("\nDEBUG  -->  SCH: Dedicated resources exhausted for LC:%d",lcNode->lcId);
            return;
         }
      }
      else
      {
         /*Loop Exit: All resources exhausted*/
         if(*sharedPRB == 0)
         {
            DU_LOG("\nDEBUG  -->  SCH: Default resources exhausted for LC:%d",lcNode->lcId);
            return;
         }
      }
```
isTxPayloadLenAdded != NULLP，代表有header因此為第一節點，由於第一個節點比較特殊需要做額外處理，刪除標投所需的資源
```c=
      /*[Step3]*/
      maxPRB = remReservedPRB + *sharedPRB;

      /*[Step4]*/
      if((isTxPayloadLenAdded != NULLP) && (*isTxPayloadLenAdded == FALSE))
      {
         DU_LOG("\nDEBUG  -->  SCH: LC:%d is the First node to be allocated which includes TX_PAYLOAD_HDR_LEN",\
               lcNode->lcId);
         *isTxPayloadLenAdded = TRUE;
         lcNode->allocBO = calculateEstimateTBSize((lcNode->reqBO + TX_PAYLOAD_HDR_LEN),\
               mcsIdx, numSymbols, maxPRB, &estPrb);
         lcNode->allocBO -=TX_PAYLOAD_HDR_LEN;
      }
```
`srRcvd` 從uecb取得用於判斷目前是否用盡UL Grant，若無則傳送時仍需考慮UL Grant的資料量
```c=
      else if((srRcvd != NULLP) && (*srRcvd == TRUE))
      {
         DU_LOG("\nDEBUG  --> SCH: LC:%d is the First node to be allocated which includes UL_GRANT_SIZE",\
               lcNode->lcId);
         *srRcvd = FALSE;
         lcNode->reqBO += UL_GRANT_SIZE;
         lcNode->allocBO = calculateEstimateTBSize(lcNode->reqBO, mcsIdx, numSymbols, maxPRB, &estPrb);
      }
```
反之則正常計算TBS即可
```c=
      else
      {
         /*[Step4]*/
         lcNode->allocBO = calculateEstimateTBSize(lcNode->reqBO,\
               mcsIdx, numSymbols, maxPRB, &estPrb);
      }
```
分配資源，依照專用資源用量以及當前剩餘量是否超越預估用量進行分配，若不夠，則會在下個迴圈使用sharePRB進行補充，並且更新剩餘量以及初始化。真正觸發remReservedPRB=0時，代表資源已經用盡 
```c=
      /*[Step6]:Re-adjust the reservedPRB pool count and *SharedPRB Count based on
       * estimated PRB allocated*/
      if((isDedicatedPRB == TRUE) && (estPrb <= remReservedPRB))
      {
         remReservedPRB = remReservedPRB - estPrb;
      }
      else   /*LC requirement need PRB share from SharedPRB*/
      {
         if(*sharedPRB <=  (estPrb - remReservedPRB))
         {
            DU_LOG("\nDEBUG  -->  SCH: SharedPRB is less");
            *sharedPRB = 0;
         }
         else
         {
            *sharedPRB = *sharedPRB - (estPrb - remReservedPRB);
         }
         remReservedPRB = 0;
      }
```

```c=
      /*[Step7]*/
      lcNode->reqBO -= lcNode->allocBO;  /*Update the reqBO with remaining bytes unallocated*/
      lcNode->allocPRB = estPrb;
      cmLListAdd2Tail(lcLL, cmLListDelFrm(lcLL, node));

      /*[Step8]:Next loop: First LC to be picked from the list
       * because Allocated Nodes are moved to the last*/
      node = lcLL->first; 

   }
   return;
}
```
## updateGrantSizeForBoRpt
用於檢查 LC（邏輯通道）列表並填充要發送到 MAC 的 BO（比特報告）報告中的 LC 和授權大小
```c=
void updateGrantSizeForBoRpt(CmLListCp *lcLL, DlMsgSchInfo *dlMsgAlloc,\
                                BsrInfo *bsrInfo, uint32_t *accumalatedBOSize)
{
   CmLList *node = NULLP, *next = NULLP;
   LcInfo *lcNode = NULLP;

   if(lcLL == NULLP)
   {
      DU_LOG("\nERROR --> SCH: LcList not present");
      return;
   }

   if(lcLL->count)
   {
      node = lcLL->first;
   }
   else
   {
      /*lcLL is empty*/
      return;
   }
```
```c=
   /*Traverse List*/
   while(node)
   {
      next = node->next;
      lcNode = (LcInfo *)node->node;
      if(lcNode != NULLP)
      {
         DU_LOG("\nINFO   -->  SCH : LcID:%d, [reqBO, allocBO, allocPRB]:[%d,%d,%d]",\
               lcNode->lcId, lcNode->reqBO, lcNode->allocBO, lcNode->allocPRB);
```
dlMsgAlloc 不為空，則將當前 LC 的信息添加到 DL 消息中，以便在分配時能夠發送 BO 報告給 MAC
透過`accumalatedBOSize`累積目前BO用量，並且在處理完該LC後進行刪除
:::warning
不能為1以外的值嗎?(可以，但得看是否支援不支援空分複用)
>dlMsgAlloc->numOfTbs = 1;
:::
```c=
         if(dlMsgAlloc != NULLP)
         {

            /*Add this LC to dlMsgAlloc so that if this LC gets allocated, BO
             * report for allocation can be sent to MAC*/
            dlMsgAlloc->numOfTbs = 1;
            dlMsgAlloc->transportBlock[0].lcSchInfo[dlMsgAlloc->transportBlock[0].numLc].lcId = lcNode->lcId;
            dlMsgAlloc->transportBlock[0].lcSchInfo[dlMsgAlloc->transportBlock[0].numLc].schBytes = lcNode->allocBO;

            /*Calculate the Total Payload/BO size allocated*/
            *accumalatedBOSize += dlMsgAlloc->transportBlock[0].lcSchInfo[dlMsgAlloc->transportBlock[0].numLc].schBytes; 

            DU_LOG("\nINFO   -->  SCH: Added in MAC BO report: LCID:%d,reqBO:%d,Idx:%d, TotalBO Size:%d",\
                  lcNode->lcId,lcNode->reqBO, dlMsgAlloc->transportBlock[0].numLc, *accumalatedBOSize);

            dlMsgAlloc->transportBlock[0].numLc++;
            handleLcLList(lcLL, lcNode->lcId, DELETE);
         }
```

當沒有任何邏輯通道需要上行數據時，系統可能不會生成比特報告，因此 bsrInfo 將為空。

在系統啟動或重置後，尚未進行任何上行數據傳輸，bsrInfo 可能會保持為空。

如果調度算法決定在某個時刻不需要上行資源分配，則可能不會生成比特報告。
```c=
         else if(bsrInfo != NULLP)
         {
            *accumalatedBOSize += lcNode->allocBO;   
            DU_LOG("\nINFO   --> SCH: UL : LCID:%d,reqBO:%d, TotalBO Size:%d",\
                  lcNode->lcId,lcNode->reqBO, *accumalatedBOSize);
         }
      }
      node = next;
   }/*End of while*/
   return;
}
```