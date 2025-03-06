---
title: 'Thesis Handover of JOJO : by Johnson'

---

## Apply Scheduling Algorithm based on 5QI
![image](https://hackmd.io/_uploads/HyTZRiVdA.png)
:::success
- **Reason of Modification:** We should schedule each traffic based on 5QI on OSC O-DU High.
- **Result:** Using numerical result to do performance measurement.
- **Steps of Modification:**
    1. Illustrate the scheduling algorithm.
    2. Customize the data size for each traffic.
    3. Put QoS Info. in CU stub like 5QI
    4. Apply scheduling algorithm based on 5QI.
    5. Create Max Rate scheduling algorithm.
    6. Create Proportional Fair scheduling algorithm.
:::
:::warning
#### cm_inet.h
- Change the packet size `CM_INET_MAX_UDPRAW_MSGSIZE` RLC can get, we can modify it.
- The purpose of this macro is to set the maximum length of the UDP raw packet. If CM_INET_MAX_UDPRAW_MSGSIZE is not defined, it will be set to 2048. You mentioned that you could change it to 4096 to fix the buffer overflow.

```c=
...
/* cm_inet_h_001.main_25 : ifndef flag added as this can be defined by the customer now*/
#ifndef CM_INET_MAX_UDPRAW_MSGSIZE
#define CM_INET_MAX_UDPRAW_MSGSIZE 2048 // change to 4096 might be good option to solve BO problem
#endif
...

```

#### cu_stub_egtp.h
- Set MACRO `NUM_DL_PACKETS` as 1
- Question : Every loop send 1 packets?
    - It will make CU_STUB deliver one packet at a time, and one packet again after sleep. And For testing purposes, it is easier to adjust a very large packet than multiple smaller packets.

```c=
...
#define NUM_DL_PACKETS 1
...
```


---

#### cu_stub.c
##### startDlDataForExperiment12
- Create new function `startDlDataForExperiment12` to set different timing of transmission for each logical channel
- Depending on the time interval set in the `transmissionInterval array`, `NUM_DL_PACKETS` downstream packets are sent.
- By controlling the value in the `transmissionInterval array`, the packet transmission frequency of **each TEID can be adjusted to affect the data flow** pattern of the whole experiment.
- Question : Where is the TEID number determined? Is it defined according to the number of UEs?
    - TEID stands for tunnel ID, which depends on how many tunnels each UE can use. The number of tunnels that each UE can use can be defined in CU_STUB.
```c=
/*******************************************************************
 *
 * @brief Start individual Dl traffics
 *
 * @details
 *
 *    Function : startDlDataForExperiment12
 *
 *    Functionality: Start individual Dl traffics
 *
 * @params[in] 
 * @return ROK     - success
 *         RFAILED - failure
 *
 * ****************************************************************/
uint8_t startDlDataForExperiment12()
{
   uint32_t teId = 0;
   uint32_t duId;
   uint8_t numOfPacket;
   uint8_t ret = ROK;
   uint8_t cnt = 0;
   uint16_t timerCnt = 0;
   EgtpTeIdCb *teidCb = NULLP;
   /*JOJO: initialize the interval of transmission.*/
   uint8_t transmissionInterval[NUM_TUNNEL_TO_PUMP_DATA] = {1, 1, 1, 1};

   /*JOJO: The experiment runs for ten seconds.*/
   while(timerCnt < 10000)
   {
      for(duId = 1; duId <= cuCb.cuCfgParams.egtpParams.numDu; duId++)
      {
         for(teId = 1; teId <= NUM_TUNNEL_TO_PUMP_DATA; teId++)
         {
            numOfPacket = NUM_DL_PACKETS;
            teidCb = NULLP;
            cmHashListFind(&(egtpCb.dstCb[duId-1].teIdLst), (uint8_t *)&(teId), sizeof(uint32_t), 0, (PTR *)&teidCb);
            /*JOJO: Customize the timing of transmission for each traffic based on teId.*/
            if(teidCb && timerCnt % transmissionInterval[teId-1] == 0)
            {
               cnt =0;
               // DU_LOG("\nDEBUG  -->  EGTP: Sending DL User Data (duId: %d, teId: %d)\n", duId, teId);
               while(cnt < numOfPacket)
               {
                  ret =  cuEgtpDatReq(duId, teId);      
                  if(ret != ROK)
                  {
                     DU_LOG("\nERROR --> EGTP: Issue with teid=%d\n",teId);
                     break;
                  }
                  cnt++;
               }
            }           
         }
      } 
      usleep(950); /*JOJO: Minimum of transmission duration.*/
      timerCnt++;
   }
   DU_LOG("\nJOJO  -->  Stop traffic.\n");
   return ROK;
}
```
##### cuConsoleHandler
- Function `cuConsoleHandler`, replace function startDlDataForExperiment11 with startDlDataForExperiment12
##### BuildAppMsg
-  Set **different data size** for **each logical channel**
- Qusetion : Any different between BuildAppMsg and startDlDataForExperiment12
    - To avoid affecting the original code, Experiment 12 is only used for experiments with the specified packet size.
```c=
S16 BuildAppMsg(uint32_t duId, EgtpMsg  *egtpMsg)
{
   int datSize = 1215;
   uint8_t teId = egtpMsg->msgHdr.teId;
   /*JOJO: Customize the data size for each channel based on tunnel ID.*/
   char *data = NULL;
   
   if (teId == 1) 
   {
      data = (char *)malloc(10 * sizeof(char));
      strncpy(data,
         "aaaaaaaaaa", // 10 elements per line
         10);
      datSize = 10;
   } 
   else if (teId == 2) 
   {
      data = (char *)malloc(50 * sizeof(char));
      strncpy(data,
         "aaaaaaaaaa" // 10 elements per line
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa",
         50);
      datSize = 50;
   }
   else if (teId == 3) 
   {
      data = (char *)malloc(50 * sizeof(char));
      strncpy(data,
         "aaaaaaaaaa" // 10 elements per line
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa",
         50);
      datSize = 50;
   }
   else if (teId == 4) 
   {
      data = (char *)malloc(50 * sizeof(char));
      strncpy(data,
         "aaaaaaaaaa" // 10 elements per line
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa",
         50);
      datSize = 50;
   }
   else 
   {
      data = (char *)malloc(50 * sizeof(char));
      strncpy(data,
         "aaaaaaaaaa" // 10 elements per line
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa"
         "aaaaaaaaaa",
         50);
      datSize = 50;
   }
   ...
   else
   {
       DU_LOG("\nERROR  -->  EGTP : Failed to allocate memory");
       return RFAILED;
   }

   free(data); /*JOJO: free data after post the message.*/
    ...
}
```
##### SchSliceBasedAddUeConfigReq 
- In Dennis' branch, in function SchSliceBasedAddUeConfigReq, Dennis **hardcodes the weight of each UE** when SCH creates UE config.
- Generate a random number between 0.1 and 1.0 and assign this random value to the `ueSliceBasedCb->weight` variable as the **weight of the UE**
- Qusetion : why we need generate random weight of UE ?
> dennis use it for test but i don't know the detial
```c=
uint8_t SchSliceBasedAddUeConfigReq(SchUeCb *ueCb)
{
   ...
   /* Hard-coded the weight of each UE */
   random = ((rand() % 10) + 1) * 0.1;
   ueSliceBasedCb->weight = random;
   ...
}
```
##### schSliceBasedFillLcInfoToSliceCb
- According to the point `uecb`, the specified object is :
    - Initialized with an average throughput and delay
    - Calculated weight (5QI)
- Question:
    - What's `totalPriorLevel` purpose here
        - Dennis for test
    - Here no Initialized before ? if yes it didn't make any problem ? 
        - proportional fair(PF)
```c=
uint8_t schSliceBasedFillLcInfoToSliceCb(CmLListCp *sliceCbList, SchUeCb *ueCb)
{
    ...
    tempLcInfo->avgTpt = 0; /* JOJO: initialize the measurement of average throughput.*/
    tempLcInfo->avgDelay = 0; /* JOJO: initialize the measurement of average delay.*/
    tempLcInfo->fiveQI = ueCb->dlInfo.dlLcCtxt[lcIdx].fiveQi; /* JOJO: attach 5QI to logical channel*/
    tempLcInfo->priorLevel = schSliceBasedCalculatePriorLevel(ueCb->dlInfo.dlLcCtxt[lcIdx].fiveQi);
    totalPriorLevel += tempLcInfo->priorLevel;
    DU_LOG("\nJOJO --> LC ID:%d is with 5QI: %d, priority level: %d.", tempLcInfo->lcId,\
                  ueCb->dlInfo.dlLcCtxt[lcIdx].fiveQi, tempLcInfo->priorLevel);
    ...
}
```
:::




## Apply Scheduling Algorithm based on GFBR and MFBR
- Reason of Modification: We should gaurantee the bit rate of DRB which carries GBR traffic, and control the data rate below MFBR at the same time.
- Steps of Modification:
    1. Classify LC according to slice, UE and 5QI.
    2. Apply Scheduling Algorithm based on GFBR and MFBR.
    3. Encode and decode QoS Info.
---
### Defind (TS 23.501 section 5.7.2.5)
:::success
The GFBR denotes the bit rate that is **guaranteed** to be provided by the network to the QoS Flow over the Averaging Time Window.

The MFBR limits the bit rate to the highest bit rate that is expected by the QoS Flow (e.g. excess traffic may get discarded or delayed by a rate shaping or policing function at the UE, RAN, UPF).

Bit rates above the GFBR value and up to the MFBR value, may be provided with relative priority determined by the Priority Level of the QoS Flows (see clause 5.7.3.3).
:::
:::info
#### extractQosInfo
- Hardcode GFBR, MFBR and average window.
- Question:So only have one type GFBR,MFBR?
    - NO,這可以被忽略，因為已經在CU_STUB上做調整，支持多種GFBR,MFBR 在不同的UE上
```c=
void extractQosInfo(DrbQosInfo *qosToAdd, QoSFlowLevelQoSParameters_t *qosFlowCfg)
{
    ...
    // qosToAdd->u.nonDyn5Qi.avgWindow = 500;
    // qosToAdd->grbQosInfo.guarFlowBitRateDl = 10000;
    // qosToAdd->grbQosInfo.maxFlowBitRateDl = 50000;

    /*Extracting PDU_SESSION_ID*/
    ...
}
```

#### getQoSFlowResourceType 
- In file `sch_ue_mgr.c`, used to check the **resource type**.
```c=
...
uint8_t getQoSFlowResourceType(uint16_t fiveQI)
{
   uint8_t fiveQiIdx;
   for(fiveQiIdx = 0; fiveQiIdx < MAX_5QI_TABLE_IDX; fiveQiIdx++)
   {
      /* JOJO: Get the corresponding 5QI Index */
      if(fiveQiIdxTable[fiveQiIdx] == fiveQI)
         break;
   }
   return fiveQiTable[fiveQiIdx][0];
}
...
```
#### fillSchDlLcCtxt
- Fill GBR info in logical channel with **GBR traffic**.
- do nothing for logical channel with **non-GBR traffic**.
![image](https://hackmd.io/_uploads/B1GQAkBdC.png)
```c=
...
void fillSchDlLcCtxt(SchDlLcCtxt *ueCbLcCfg, SchLcCfg *lcCfg)
{
   uint8_t resourceType; /*JOJO: Check resource type for LC. */
   ...
   if(lcCfg->drbQos)
   {
     ueCbLcCfg->pduSessionId = lcCfg->drbQos->pduSessionId;

     if(lcCfg->drbQos->fiveQiType == SCH_QOS_NON_DYNAMIC) /* Non-Dynamic 5QI */
     {
         ueCbLcCfg->fiveQi = lcCfg->drbQos->u.nonDyn5Qi.fiveQi;
         ueCbLcCfg->avgWindow = lcCfg->drbQos->u.nonDyn5Qi.avgWindow; /*JOJO: Extend average window for QoS traffic. */
     }
     else /* Dynamic 5QI */
     {
         ueCbLcCfg->fiveQi = lcCfg->drbQos->u.dyn5Qi.fiveQi;
     }

      /*JOJO: Extend GFBR and MFBR for QoS traffic. */
      ueCbLcCfg->gfbr = 0;
      ueCbLcCfg->mfbr = lcCfg->drbQos->grbQosFlowInfo.maxFlowBitRateDl;
      
      resourceType = getQoSFlowResourceType(ueCbLcCfg->fiveQi);
      if(resourceType == 0 || resourceType == 2)
      {
         ueCbLcCfg->gfbr = lcCfg->drbQos->grbQosFlowInfo.guarFlowBitRateDl;
      }
   }
    ...
}
...
```

#### schSliceBasedFillLcInfoToSliceCb
- Function `schSliceBasedFillLcInfoToSliceCb` to fill the Logical Channel ID into specific slice control block.
- Beacuse each **LC is associated with slice**, this function **fills** and **classifies** the lcId of each UE into corresponding slice control block.
- Question : How to sure other ue didn't use `lcIdList[ueId-1]` data structure ?
```c=
uint8_t schSliceBasedFillLcInfoToSliceCb(CmLListCp *sliceCbList, SchUeCb *ueCb)
{
   CmLList *sliceCbNode;
   SchSliceBasedSliceCb *sliceCb;
   uint8_t ueId;
   uint8_t lcIdx;

   ueId = ueCb->ueId;
   sliceCbNode = sliceCbList->first;

   while(sliceCbNode)
   {
      sliceCb = (SchSliceBasedSliceCb *)sliceCbNode->node; 

      if(sliceCb->lcIdList[ueId-1].first != NULLP)
         cmLListDeleteLList(&sliceCb->lcIdList[ueId-1]);

      cmLListInit(&sliceCb->lcIdList[ueId-1]);

      for(lcIdx = 0; lcIdx < MAX_NUM_LC; lcIdx++)
      {  
         if(ueCb->dlInfo.dlLcCtxt[lcIdx].snssai)
         {
            if(memcmp(&sliceCb->snssai, ueCb->dlInfo.dlLcCtxt[lcIdx].snssai, sizeof(Snssai)) == 0)
            {
               DU_LOG("\nDennis --> LC ID:%d is added to SST:%d slice", lcIdx, sliceCb->snssai.sst);
               addNodeToLList(&sliceCb->lcIdList[ueId-1], lcIdx, NULL);
            }
         }
      }
      sliceCbNode = sliceCbNode->next;
   }
}
```

#### getQoSFlowResourceType
- 根據輸入的 5QI 值,獲取對應的 QoS 流資源類型
- Question : 迴圈到正確的5QI後，回傳指定的table格式
```c=
uint8_t getQoSFlowResourceType(uint16_t fiveQI)
{
   uint8_t fiveQiIdx;
   for(fiveQiIdx = 0; fiveQiIdx < MAX_5QI_TABLE_IDX; fiveQiIdx++)
   {
      /* JOJO: Get the corresponding 5QI Index */
      if(fiveQiIdxTable[fiveQiIdx] == fiveQI)
         break;
   }

   return fiveQiTable[fiveQiIdx][0];
}
```
#### fillSchDlLcCtxt
- 根據輸入的 `SchLcCfg` 結構體，填入 `SchDlLcCtxt`
- 判斷是否為非動態 5QI
- 作用是將 `SchLcCfg` 結構體中的 QoS 相關信息，如 5QI、平均視窗、最大流量比特率等，填充到 SchDlLcCtxt 結構體中

```c=
void fillSchDlLcCtxt(SchDlLcCtxt *ueCbLcCfg, SchLcCfg *lcCfg)
{
   uint8_t resourceType; /*JOJO: Check resource type for LC. */
   ...
   if(lcCfg->drbQos)
   {
     ueCbLcCfg->pduSessionId = lcCfg->drbQos->pduSessionId;

     if(lcCfg->drbQos->fiveQiType == SCH_QOS_NON_DYNAMIC) /* Non-Dynamic 5QI */
     {
         ueCbLcCfg->fiveQi = lcCfg->drbQos->u.nonDyn5Qi.fiveQi;
         ueCbLcCfg->avgWindow = lcCfg->drbQos->u.nonDyn5Qi.avgWindow; /*JOJO: Extend average window for QoS traffic. */
     }
     else /* Dynamic 5QI */
     {
         ueCbLcCfg->fiveQi = lcCfg->drbQos->u.dyn5Qi.fiveQi;
     }

      /*JOJO: Extend GFBR and MFBR for QoS traffic. */
      ueCbLcCfg->gfbr = 0;
      ueCbLcCfg->mfbr = lcCfg->drbQos->grbQosFlowInfo.maxFlowBitRateDl;
      
      resourceType = getQoSFlowResourceType(ueCbLcCfg->fiveQi);
      if(resourceType == 0 || resourceType == 2)
      {
         ueCbLcCfg->gfbr = lcCfg->drbQos->grbQosFlowInfo.guarFlowBitRateDl;
      }
   }
    ...
}
```