---
title: 'DU[J]+SMO[J]'

---

# O-DU High With O1 Enabled
:::success
**Goal:**
- Check the status of SMO and O-DU
- Connect O-DU to SMO
- Presenting DU messages through o1 on Granfana

**Main Reference:**
- [Integration & Testing [G] O-DU and [I] SMO](https://hackmd.io/RDE4f6lxQeyuo284amXpIg?view#312-O-DU-Issue-libnetconf2-version)
- [[F] O-DU High With O1 Enabled](https://hackmd.io/@H131413/B1-cjIK_h/https%3A%2F%2Fhackmd.io%2FtmTNEE7ET4S8mt3G6wCJzA)
- [O-DU High Installation Guide](https://docs.o-ran-sc.org/projects/o-ran-sc-o-du-l2/en/latest/installation-guide.html#o-du-high-installation-guide)
- [O-DU High User Guide](https://docs.o-ran-sc.org/projects/o-ran-sc-o-du-l2/en/latest/user-guide.html#user-guide)
:::


[TOC]

## SMO VES Preparatory work
SMO : Service Management and Orchestration
VES : Virtual Event Streaming 
```c=
kubectl get ingress -A
```
![image](https://hackmd.io/_uploads/SklcQ8k50.png)

==kong== helps these network components (SMO/RIC) to do internal routing.

## View SMO internal routing settings
```c=
kubectl edit ingress -n onap ingress-onap-dcaeves
```

![image](https://hackmd.io/_uploads/HJ9AmIJ9A.png)

---

Use the following Commend to test whether VES responds
```c=
curl https://192.168.8.6:30780/dcae-ves-collector  --insecure
```
Used on SMO.
- Success : 
    - No problem with routing
- Failure : 
    - There is an internal routing problem (service port to service routing problem).

Use on DU:
- Success: DU can correctly connect to SMO through IP and correctly direct the service through the service port
![image](https://hackmd.io/_uploads/SJoUD8k50.png)


- Failure: Depending on the situation, it can be roughly divided into:
        - IP error
        - Service Port
        - There is a problem with the routing of ==dcae-ves-collector== itself
---
## Install the YANG modules and load initial configuration

### smoVesConfig
```shell=
cd cd <O-DU High Directory>/l2/build/config
vim smoVesConfig.json
```
:::info
細節需參閱[Heidei筆記](#Reference)

SMO透過web進行設置修改，因此你需要透過以下的指令尋找服務端口(此為SMO預設功能，服務名稱==sdnc-web==)

其中Port:8080為對內端口，Port:30205對外端口

```c=
 kubectl get svc -A | grep sdnc
```
![image](https://hackmd.io/_uploads/SyjuA81cR.png)

- vesV4IpAddress : SMO IP
- vesPort : 服務端口，依據服務做設定
- username : 創建服務時便已經預設完成，請詢問創建者
- password : 創建服務時便已經預設完成，請詢問創建者
```c=
{
    "vesConfig": {
        "vesV4IpAddress" : "192.168.8.6", 
        "vesPort" : "30205",
        "username" : "user",
        "password" : "password"
    }
}
```
對應 odlux: http://192.168.8.6:30205
:::


### oamVesConfig
```shell=
vim oamVesConfig.json
```
:::info
使用以下指令尋找kong所使用的服務端口
```shell=
kubectl get svc -A | grep kong
```
![image](https://hackmd.io/_uploads/B11REvy5A.png)

- vesV4IpAddress : SMO IP
- vesPort : 服務端口，依據服務做設定
- username : 創建服務時便已經預設完成，請詢問創建者
- password : 創建服務時便已經預設完成，請詢問創建者
```c=
{
    "vesConfig": {
        "vesV4IpAddress" : "192.168.8.6",
        "vesPort" : "30780",
        "username" : "sample1",
        "password" : "sample1"
    }
}
```
VES: http://192.168.8.6:30780
:::

### netconfConfig

```shell=
vim netconfConfig.json
```
:::info
Netconf configuration

* Edit the details of Netopeer server:
    * MacAddress
    * NetconfServerIpv4
    * NetconfServerIpv6
```shell=
{
    "NetconfServer": {
        "MacAddress": "<DU Mac>",
        "NetconfServerIpv4": "<DU Ipv4>",
        "NetconfServerIpv6": "<DU Ipv6>",
        "NetconfPort": "830",
        "NetconfUsername": "netconf",
        "NetconfPassword": "netconf!"
    }
}

```
:::

### Load Yang modules initial configuration
```bash=
cd <O-DU High Directory>/l2/build/scripts
sudo ./add_netconf_user.sh
sudo ./load_yang.sh
```

### Enable Standard Defined VES format
**floder:** ```<O-DU High Directory>/l2/src/o1/ves```
**File:** ```VesUtils.h```
Find the command ```#define StdDef``` and **uncomment** it.
![image](https://hackmd.io/_uploads/Hy3_dwno0.png)



## Modify the O-DU source codes

### src/o1/ves/VesEvent.cpp
* In ```VesEevent::createUrl()```add ```/dcae-ves-collector``` infront of ```/eventListener/v7```
```c=
void VesEvent::createUrl()
{
   mVesUrl = "https://" + mVesServerIp + ":" + mVesServerPort + "/dcae-ves-collector/eventListener/v7";
}
```
![image](https://hackmd.io/_uploads/BJqT4heqA.png)


### src/o1/ves/PerfMeasurementEvent.cpp
* In ```PerfMeasurementEvent::createUrl()``` add ```/dcae-ves-collector``` in both ```mVesUrl```

```c=
void PerfMeasurementEvent::createUrl()
{
   #ifdef StdDef
   mVesUrl = "https://" + mVesServerIp + ":" + mVesServerPort + "/dcae-ves-collector/eventListener/v7";
   #else
   mVesUrl = "https://" + mVesServerIp + ":" + mVesServerPort + "/dcae-ves-collector/eventListener/v7/events";
   #endif
   O1_LOG("\nURL=%s", mVesUrl.c_str());
}
```
![image](https://hackmd.io/_uploads/B1scShlq0.png)


## Install Netconf in SMO

### Install the Dependent Tools
```shell=
sudo apt-get update
sudo apt-get install git cmake build-essential bison flex libpcre3-dev libev-dev libavl-dev libprotobuf-c-dev protobuf-c-compiler swig python2-dev python3-dev lua5.2 pkg-config libpcre++-dev openssl libssl-dev libcrypto++-dev zlib1g-dev libssh-dev libpcre2-dev libcurl4-gnutls-dev graphviz doxygen
```

### Install the libssh
```shell=
wget https://git.libssh.org/projects/libssh.git/snapshot/libssh-0.9.5.tar.gz
tar -xf libssh-0.9.5.tar.gz
rm libssh-0.9.5.tar.gz
cd libssh-0.9.5
mkdir build && cd build
cmake ..
make
sudo make install
```

### Install the libyang
```shell=
sudo -i
git clone https://github.com/CESNET/libyang.git
cd libyang
mkdir build && cd build && cmake .. && make && make install
```

### Install the sysrepo
```shell=
cd ~
git clone https://github.com/sysrepo/sysrepo.git
cd sysrepo
mkdir build && cd build && cmake .. && make && make install
```

### Install the libnetconf2
```shell=
cd ~
git clone https://github.com/CESNET/libnetconf2.git
cd libnetconf2
mkdir build && cd build && cmake .. && make && make install
```

### Install the netopeer2
```shell=
cd ~
git clone https://github.com/CESNET/netopeer2.git
cd netopeer2
export LD_LIBRARY_PATH=/usr/local/lib
mkdir build && cd build && cmake .. && make && make install
```



## Test O1 VES and Netconf

### Build odu、cu_stub、ric_stub.
```shell=
cd l2/build/odu
make clean_all // if you compile first time, you can skip this command.
make odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make ric_stub MACHINE=BIT64 NODE=TEST_STUB MODE=FDD O1_ENABLE=YES
```

### Assign virtual IP addresses
```shell=
cd l2/build/odu/
sudo ifconfig lo:ODU 192.168.130.81
sudo ifconfig lo:CU_STUB 192.168.130.82
sudo ifconfig lo:RIC_STUB 192.168.130.80
```
![image](https://hackmd.io/_uploads/Skw1F2E20.png)


### Start Netopeer2-server
```shell=
cd <O-DU High Directory>/l2/build/scripts
sudo ./netopeer-server.sh start
```
:::info
* You can use this command ```cat /var/log/netopeer2-server.log``` to check the status
:::
:::warning
If you modify the configuration file of YANG modules, you need to load YANG module again and restart the server.
```shell=
cd <O-DU High Directory>/l2/build/scripts
sudo ./load_yang.sh
sudo ./netopeer-server.sh start
```
:::


### Execution with O1 enable: DU
Open 3 terminations for **cu_stub、ric_stub and odu**

#### Execute cu_stub
termination 1: 
```shell=
cd l2/bin/cu_stub
sudo ./cu_stub
```


#### Execute ric_stub
termination 2: 
```shell=
cd l2/bin/ric_stub
sudo ./ric_stub
```

#### Execute odu
termination 3: 
```shell=
cd l2/bin/odu
sudo ./odu
```

### Execution with O1 enable: SMO


#### Login the netconf client
```shell=
cd l2/build/config
netopeer2-cli
connect --host <server ip> --port 830 --login netconf
//password: netconf!
```
![image](https://hackmd.io/_uploads/r18K93E2C.png)



#### Push Cell Configuration
```shell=
edit-config --target candidate --config=cellConfig.xml
commit
```







## Issues

:::danger
### Issue 1:
When start the netconf server and login the netconf client
```connect --login netconf```: 
![image](https://hackmd.io/_uploads/rycEUhe90.png)
:::

:::success
### Solution:
Re-install O1 library.

```shell=
sudo ./install_lib_O1.sh -c
```
![image](https://hackmd.io/_uploads/B1D58moj0.png)
:::

:::danger
### Issue 2:
In Step [Start Netopeer2-server](#Start-Netopeer2-server).
After ```sudo ./netopeer-server.sh start``` and check the log by ```cat /var/log/netopeer2-server.log```.
Then found the following error.
![image](https://hackmd.io/_uploads/rykap_2jC.png)

:::

:::success
Solution:

```shell=
cd <O-DU High Directory>/l2/build/netconf/sysrepo/build
sudo make sr_clean
cd <O-DU High Directory>/l2/build/netconf/Netopeer2/build
sudo make install
```
Then load YANG module and restart the server.
```shell=
cd <O-DU High Directory>/l2/build/scripts
sudo ./load_yang.sh
sudo ./netopeer-server.sh start
```
==Use ```cat /var/log/netopeer2-server.log``` to check.==
:::

:::danger
### Issue 3:

If the result is ```OK``` after using ```edit-config --target candidate --config=cellConfig.xml``` and ```commit``` in netconf client.
![image](https://hackmd.io/_uploads/r1ej-pai0.png)
But ODU, cu_stub and ric_stub are not running.
:::

:::success
Solution:

Edit ```cellConfig.xml```(Location: l2/build/config) and increment number in the <id> tag to a new value.
```shell=
<id>me-3</id
```
    
![image](https://hackmd.io/_uploads/rk66MT6sR.png)

:::