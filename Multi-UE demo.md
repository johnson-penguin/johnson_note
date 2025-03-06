---
title: Multi-UE demo
tags: [demo, BMW Lab.]

---

# Multi-UE demo
:::success
Reference:
- [[Rel-F] Demo of Slice xApp](/_AxjoDz6SaCrdTgga4ut4Q)
- [KPM xApp (For Slice) User Guide](/gxwZmhDIR1CYuCxQa_nG3Q)
- [RC xApp (For Slice) User Guide](/TLj3c6ATTFiz4oESlftk3w)
- [SMO VES-collector Test Note](/-xDcmxf9TiGxzyQnEZyYmw)
:::
[TOC]

## 1. Overview
We already developed the SMO, Non-RT RIC, xApps, slice-enabled Scheduler and E2 Handler. In this demo, we configure the assurance throughput for each slice. Finally show the throughput and PRB used of each slice in the Grafana of SMO. 


## 2. Topology
![](https://hackmd.io/_uploads/HJP94PLFh.png)




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
O1 sends cell configuration to O-DU

```bash=
cd  ~/jacky/l2_o1enable_multiUE/build/scripts
sudo ./add_netconf_user.sh
sudo ./load_yang.sh
sudo ./netopeer-server.sh start 
```
### 3.3 Compile CU and DU (Optional)
```bash=
cd  ~/jacky/l2_o1enable_multiUE/build/odu
make odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
```

Make Clean if needed:
```bash=
make clean_all
make clean_odu
make clean_cu
```
#### No O1
```bash=
cd build/odu
make odu MACHINE=BIT64 MODE=FDD
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD
```
### 3.4 Login the netconf client

```bash=
cd ~/jacky/l2_o1enable_multiUE/build/config
netopeer2-cli
connect --login netconf
# password: netconf!
```

### 3.5 Run CU stub and O-DU in 2 terminals
Running in other terminals
- CU_STUB
```bash=
cd ~/jacky/l2_o1enable_multiUE/bin/cu_stub
sudo ./cu_stub
```
- DU
```bash=
cd ~/jacky/l2_o1enable_multiUE/bin/odu
sudo ./odu
```


### 3.6 IP setting 
```c=
cd l2/build/config/config
vim startup_config.xml

```



```c=
<odu xmlns="urn:o-ran:odu:interface:1.0">
<interfaces>
<interface>
<interface-name>odu</interface-name>
<interface-address>192.168.130.81</interface-address>
<port>38472</port>
</interface>
<interface>
<interface-name>ocu</interface-name>
<interface-address>192.168.130.82</interface-address>
<port>38472</port>
</interface>
<interface>
<interface-name>ric</interface-name>
<interface-address>192.168.130.80</interface-address>
<port>36422</port>
</interface>
</interfaces>
</odu>

```


### 3.7 Commit the cell config

Smo acts as a netconf client on the o1 interface, the du acting as netconf server

The ID in line `3`, `8` and `35` shouldn't be deplicated with previous execution. The ID number should be plus 1.

```bash=
cd ~/jacky/l2_o1enable_multiUE/build/config
vim cellConfig.xml
```





When O-DU High is run with O1 enabled it waits for initial cell configuration to be pushed by SMO before starting the stack
At netconf client's terminal:
```bash=
edit-config --target candidate --config=cellConfig.xml
# This step need to run after o-du and cu_stub start running.
commit 
```
CU stub and O-DU starts to execute. CU's log
![](https://hackmd.io/_uploads/H1QS8XBY2.png)

### 3.7 Start to send dummy data
**Wait until six UE finish running then press `d` at CU stub to send the dummy data to O-DU.**

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
Control Pannel Home Page: http://192.168.8.46:8080/

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


---




![](https://hackmd.io/_uploads/BJxHPgwX3n.png)

Throughput text default
1. 1200000
2. 800000
3. 900000

### Throughput text ex1:
1. 1200000
2. 600000
3. 600000
![](https://hackmd.io/_uploads/rkM0h7w3h.png)

### Throughput text ex2:
1. 600000
2. 600000
3. 600000
![](https://hackmd.io/_uploads/HJcB0IPh2.png)

![](https://hackmd.io/_uploads/H1UNAIwnn.png)


### Throughput text ex3:
1. 600000
2. 600000
3. 1200000
![](https://hackmd.io/_uploads/ByXnywv2n.png)
![](https://hackmd.io/_uploads/r1QryDPn2.png)


## 6.Troubleshooting

學長提供near-rt-ric檔案,在與檔案相同路徑下下指令
Jacky provide Near-RT_RIC_F, under the same path as the file, you can do the following command

```=
chmod 700 Near-RT_RIC_F.pem
```

Connect via ssh

```=
ssh -i Near-RT_RIC_F.pem ubuntu@192.168.8.228
```

Get root privileges

```=
sudo -i
```

List all running containers
```=
kubectl get pods -A
```
Search slice xapp log
```=
kubectl logs -n ricxapp -f ricxapp-slicexapp-799dfcd5bd-864wk
```

Delete slice xApp's pods
```=
kubectl delete pods -n ricxapp <slice xApp container's ID>

kubectl delete pods -n ricxapp ricxapp-slicexapp-799dfcd5bd-864wk
```

Delete A1 mediator's pods
```=
kubectl delete pods -n ricplt <A1 mediator container's ID>


kubectl delete pods -n ricplt deployment-ricplt-a1mediator-74f45b6bc6-gqldb
```




smo 15秒傳送一次





## Something may be wrong

![](https://hackmd.io/_uploads/HyAo46N33.png)

![](https://hackmd.io/_uploads/H115xCVhn.png)

![](https://hackmd.io/_uploads/rJxlRXD3h.png)



