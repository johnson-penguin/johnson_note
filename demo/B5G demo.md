---
title: B5G demo
tags: [demo, BMW Lab.]

---

:::success
Reference:

:::
[TOC]

## 1. Overview
We already developed the SMO, Non-RT RIC, xApps, slice-enabled Scheduler and E2 Handler. In this demo, we configure the assurance throughput for each slice. Finally show the throughput and PRB used of each slice in the Grafana of SMO. 


## 2. Topology
![](https://hackmd.io/_uploads/HJP94PLFh.png)


### SMO

### Near-RT RIC

### O-DU

## 3. User Guide - O-DU
:::success
Login info
```
ssh oran@192.168.8.5
# password: bmwlab
```
:::

### 3.1 Configure the IP for CU stub
```bash=
sudo ifconfig eno1:cu 192.168.8.245
```
### 3.2 Start the netconf server
```bash=
cd  ~/jacky/l2_o1enable/build/scripts
sudo ./add_netconf_user.sh
sudo ./load_yang.sh
sudo ./netopeer-server.sh start
cd ~/jacky/l2_o1enable
sudo ./netconf_server_configure.sh 
```
### 3.3 Compile CU and DU (Optional)
```bash=
make odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
```

Make Clean if needed:
```bash=
make clean_all
make clean_odu
make clean_cu
```
### 3.4 Login the netconf client

```bash=
cd ~/jacky/l2_o1enable/build/config
netopeer2-cli
connect --login netconf
# password: netconf!
```

### 3.5 Run CU stub and O-DU in 2 terminals
Running in other terminals
- CU_STUB
```bash=
cd ~/jacky/l2_o1enable/bin/cu_stub
sudo ./cu_stub
```
- DU
```bash=
cd ~/jacky/l2_o1enable/bin/odu
sudo ./odu
```

Wait until O-DU print this log:
![](https://hackmd.io/_uploads/B12JSXBKh.png)


### 3.6 Commit the cell config
When O-DU High is run with O1 enabled it waits for initial cell configuration to be pushed by SMO before starting the stack
At netconf client's terminal:
```bash=
edit-config --target candidate --config=cellConfig.xml
commit
```

The ID in line `3`, `8` and `35` shouldn't be deplicated with previous execution. The ID number should be plus 1.

```bash=
cd ~/jacky/l2_o1enable/build/config
vim cellConfig.xml
```


CU stub and O-DU starts to execute. CU's log
![](https://hackmd.io/_uploads/H1QS8XBY2.png)

### 3.7 Start to send dummy data
Press `d` at CU stub to send the dummy data to O-DU.

![](https://hackmd.io/_uploads/BJeuLXHFn.png)

## 4. User guide - SMO Grafana

### 4.1 Login the Grafana
Open the browser and Enter: http://192.168.8.229:30000/

- Account: admin
- Password: smo

### 4.2 Open the dashboard
Press upper left side button, select Dashboard

![](https://hackmd.io/_uploads/ryNlPXHF3.png)

Select General > `B5G_NTU_DEMO`

![](https://hackmd.io/_uploads/BJl2Gv7rt2.png)

### 4.3 Adjust the refresh time
Press upper right side button ![](https://hackmd.io/_uploads/HJBvDXSY3.png =100x)

Select `last 5 minutes` and you can see the clearier dashboard.

![](https://hackmd.io/_uploads/HyzreXHt2.png)

## 5. User Guide - Adjust SLA

### 5.1 Access Non-RT RIC Control Panel
Control Pannel Home Page: http://192.168.8.229:30091/

![](https://hackmd.io/_uploads/rJxGKQBF2.png)

### 5.2 Adjust the target throughput

Access `policy page` and select 22222. 

There are 3 policies. Fill in the slice information:
:::info
- Policy 1:
    - Target: ric1
    - SLAParameter:
        - TargetThroughput: 1400000
    - Member:
        - PlmnId: 311048
        - Sd: 020304
        - Sst: 01
- Policy 2:
    - Target: ric1
    - SLAParameter:
        - TargetThroughput: 700000
    - Member:
        - PlmnId: 311048
        - Sd: 030304
        - Sst: 02
- Policy 3:
    - Target: ric1
    - SLAParameter:
        - TargetThroughput: 700000
    - Member:
        - PlmnId: 311048
        - Sd: 040304
        - Sst: 03
:::

The target throughput can be modified. 

### 5.3 Check the target throughput in Slice xApp 
Enter Near-RT RIC Release F Platform:
:::info
Near-RT RIC Release F Platform's IP: 192.168.8.228
:::

Print the log of Slice xApp
```=
sudo -i
kubectl get pods -A
kuebctl logs -n ricxapp -f <Slice's contatiner IP>
```

If O-DU is running, you can see the target throughput. 
![](https://hackmd.io/_uploads/SkK__QrF3.png)



