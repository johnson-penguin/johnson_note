---
title: OSC - schSliceBasedDlScheduling
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


## Flow Chart
![](https://hackmd.io/_uploads/r1bzk_8hn.jpg)

## Key Parameter
- Pointer to Cell Control
- BlockSlot timing info
- UE ID
- Retransmission boolean
- The address of pointer to DL HARQ Process Control Block
## Code
:::success
```c=
bool schSliceBasedDlScheduling
(SchCellCb *cell, SlotTimingInfo currTime, 
uint8_t ueId, bool isRetx, SchDlHqProcCb **hqP)
{
       ...
       ... 
}
```
:::

---


### Non-retransmission (New Message)

:::danger

```c=
    if (isRetx == FALSE)
       {
          if(schDlGetAvlHqProcess(cell, ueCb, &ueNewHarqList[ueId-1]) != ROK)
          {
             return false;
          }
       }
```
:::


:::danger
schDlGetAvlHqProcess()
![](https://hackmd.io/_uploads/SkZfeJscn.png)
if (ueCb->dlHqEnt.free.count == 0): 

Checks if the dlHqEnt.free.count member of the ueCb structure is 0. If it is 0, it means that there are ==no downstream HARQ handlers== available and ***returns RFAILED***.



This function is used to obtain an available downstream HARQ handler and set it to in-use for use during downstream data transfers. 

If an available handler cannot be obtained, a failure is returned.

---
**Notice**
```
tmp = (SchDlHqProcCb*)(cmLListFirst(&(ueCb->dlHqEnt.free))->node);
```

This code takes a pointer to an available downstream HARQ handler from the dlHqEnt.free member of the ueCb structure and assigns it to tmp.

if (NULLP == tmp): Checks if tmp is a null pointer. 

If it is a null pointer, no available downstream HARQ handler was found, and RFAILED is returned.

---

This subroutine will return status according to the availability of resources, and when I have an idle resource and can allocate it:

1. Delete the node from the list because the resource has already been allocated. 2. the program assigns the resource to the ue and writes the ue to the program.

2. The program assigns the resource to the UE and therefore writes the UE to the list of our scheduling objects.


這支副程式會依照資源的有無進行狀態回傳,當我有空閒資源並且能進行分配後將會：

1. 將這個空閒的位置（node）從清單上刪除,因為資源已經被分配出去了
2. 程式將資源分配給了這個Ue因此將這個UE寫入我們調度對象的清單上

---
Delete allocated resources from free space to avoid duplicate usage
將被分配的資源從可用空間刪除，避免重複使用

![](https://hackmd.io/_uploads/SJ2vJ3o9h.png)





>   cmLListDelFrm & use way


---
將新訊息的使用者加入使用者名單(服務清單)
Add users with new messages to the user list (service list)

:::danger
![](https://hackmd.io/_uploads/BkfEogi9n.png)

---
schDlHqProcCb
![](https://hackmd.io/_uploads/B1lLgJj93.png)

:::


### IndValidK0K1Value()

:::info
用於檢查是否找到了有效的 K0 和 K1 值以及一組用於調度 PDCCH、PDSCH 和 PUCCH 的時槽。

- k0是指下行調度DCI與其調度的PDSCH之間的Slot間隔
- k1指PDSCH與其HARQ-ACK反饋的Slot間隔
- k2指上行調度DCI與其調度的PUSCH之間的Slot間隔。

![](https://hackmd.io/_uploads/H1wDVf4oh.png)

Used to check that valid K0 and K1 values are found and a set of time slots for scheduling PDCCH, PDSCH and PUCCH.

- k0 is the Slot interval between the downstream scheduling DCI and its scheduled PDSCH.
- k1 is the Slot interval between the PDSCH and its HARQ-ACK feedback.
- k2 is the Slot interval between the upstream scheduling DCI and its scheduled PUSCH.

:::

:::warning
用於為特定使用者（UE）進行PDCCH（下行控制信道）和PDSCH（下行共享信道）資源的分配,透過指標的方式先確認是否為null（有資源未被分配）,若有資源能夠使用則透過 **SCH_ALLOC**做分配,並透過條件式確認資料是否寫入,若無（記憶體滿了,版本問題,bug......）則報錯,若有則將該指標的地址放入並且初始化記憶體空間。

It is used to allocate PDCCH (Downlink Control Channel) and PDSCH (Downlink Shared Channel) resources for a specific user (UE), through the pointer way to first confirm whether it is null (there are resources not allocated), if there are resources can be used, then through **SCH_ALLOC** to do the allocation, and through conditional confirmation of the data is written, if not (memory is full, version problems). If not (memory full, version problem, bug ......) If not (memory full, version problem, bug ), then report error, if so, put the address of the pointer and initialize the memory space.


>SCH_ALLOC()?


![](https://hackmd.io/_uploads/HkTgyhVoh.png)

特定類型的調度器結構的引用，並進行後續處理。另外會對剩餘的資源做最大塊的分析(如下)
連續情況剩下1 ~ 3、8 ~ 13，此時回報最大值為8 ~ 13的數量，這是由於OSC目前僅支持Type-1的緣故，還無法將一筆資料分成各種大小嵌入剛剛好的剩餘空間中。


A reference to a specific type of scheduler structure is made and post-processed. In addition, the remaining resources will be analyzed in terms of maximum blocks (as follows)

The consecutive cases left are 1 ~ 3, 8 ~ 13, and the maximum value of the return is the number of 8 ~ 13. This is because OSC only supports Type-1 at the moment, and it is not yet possible to split a piece of data into various sizes and embed it in the just-right amount of remaining space.
![](https://hackmd.io/_uploads/S1B_Jwrs2.png)





:::



:::danger
MULTI_THREAD


```c=

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

         /* Run the intra-slice scheduling with multi-thread feature */

         *schSpcCell->threadArg[currSliceCnt]->triggerFlag = 1;

         currSliceCnt++;
         sliceCbNode = sliceCbNode->next;
      }

      clock_gettime(1, &start2);

      /* Wait for every thread finish the intra-slice scheduling */
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
```
Traverse each slicecb, assign data to each thread, count by currslicecnt, and trigger multi-thread feature action by triggerFlag, when the node of this slicecb is traversed, then switch to the next slicecb until all slicecb is traversed.

All threads enter intra-slice scheduling, but we don't know when all the threads can complete the action, so we have to check the triggerFlag of each thread (1, execute; 0, complete), until the triggerFlag is equal to 0.

***
SINGLE_THREAD

Intra-slice scheduling((Important functions))
```c=

#else
         if(schSliceBasedDlIntraSliceScheduling(cell, pdcchTime, pdschNumSymbols, &totalRemainingPrb, maxFreePRB, sliceCb, &ueDlNewTransmission) != ROK)
         {
            DU_LOG("\nDennis --> DL Intra Slice Scheduling Failed");
            return false;
         }

         sliceCbNode = sliceCbNode->next;
      }
#endif

```

:::


### Message Retransm

:::info

```c=
   else
   {
         /* Allocate PDCCH and PDSCH resources for the ue */
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
   }

```




* 確保該UE尚未分配PDCCH和PDSCH的資源
* 分配 dciSlotAlloc 指向的記憶體
* * 錯誤:回報訊息，並且該次分配記憶體失敗
* * 正確:將ue的PDCCH PDSCH的資源指向給記憶體，並且將指標初始化


* Ensure that the UE has not yet allocated resources for PDCCH and PDSCH
* Allocate the memory pointed to by dciSlotAlloc.
* * Error: Message is returned and the memory allocation failed.
* * Correct: point ue's PDCCH and PDSCH resources to memory and initialize the pointer.
:::

### Final scheduling (Important functions)

:::danger
Final scheduling (Important functions)



最終下行排程,在此階段將對所有剩餘資源做分配
Final downlink scheduling, where all remaining resources are allocated.
![](https://hackmd.io/_uploads/BkiGO7Njh.png)


:::





https://hackmd.io/@Johnson-72/BypIVuoqh
https://hackmd.io/@dennis5581407/Sk5ETIFGj#Slides
https://hackmd.io/_7yovAqnRBG6RAHPDvzCeQ#Prerequisite