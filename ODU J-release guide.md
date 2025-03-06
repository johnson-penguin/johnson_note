---
title: ODU J-release guide

---

## Environment & Access
:::success
- Core number : 8
- RAM : 8G
- Storage : 150G
- OS version : Ubuntu 20.04

---

- IP : 192.168.8.38
- Name : o-du
- Password : bmwlab
```c=
ssh o-du@192.168.8.38
```
:::
## Libraries
:::info
### Git
```c=
sudo apt install git
```


### GCC
```c=
sudo apt-get install -y build-essential
```



### LKSCTP
```c=
sudo apt-get install -y libsctp-dev
```
### PCAP:
```c=
sudo apt-get install -y libpcap-dev
```
### libxml2
```c=
sudo apt-get install -y libxml2-dev
```
:::










## Cloning code & J-release
```c=
git clone https://gerrit.o-ran-sc.org/r/o-du/l2
```
![image](https://hackmd.io/_uploads/SkS-PSLOC.png)

```c=
cd /l2
git checkout j-release
```
## Setting up Netconf server (Only if O1 interface enabled)
- Install Netconf libraries
```c=
cd ~/l2/build/scripts
sudo ./install_lib_O1.sh -c
```
![image](https://hackmd.io/_uploads/ry1W5Nu_A.png)


## Compilation with O1 interface enabled

```c=
cd l2/build/odu/
sudo ifconfig lo:ODU 192.168.130.81
sudo ifconfig lo:CU_STUB 192.168.130.82
sudo ifconfig lo:RIC_STUB 192.168.130.80
```
```c=
cd ~/Desktop/j-odu/l2/build/odu/bin
```
### Build O-DU High:
- Navigate to Build folder
    - `cd ~/l2/build/odu`
- Clean O-DU High binary
    - `make clean_odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES`
- Compile O-DU High binary
    - `make odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES`



### Build CU Stub :
- Navigate to Build folder
    - `cd ~/l2/build/odu`
- Clean CU Stub binary
    - `make clean_cu NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES`
- Compile CU Stub binary
    - `make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES`
### Build RIC Stub :
- Navigate to Build folder
    - `cd ~/l2/build/odu`
- Clean RIC Stub binary
    - `make clean_ric NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES`
- Compile RIC Stub binary
    - `make ric_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES`

### Quick test


#### Compile(O1)
```c=
make odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make cu_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make ric_stub NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
```


#### Clean
```c=
make clean_odu MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make clean_cu NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
make clean_ric NODE=TEST_STUB MACHINE=BIT64 MODE=FDD O1_ENABLE=YES
```


![image](https://hackmd.io/_uploads/BkAq82ut0.png)
- Open the `startup_config.xml` and edit the desired IP and Port for CU, DU and RIC.
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
- Open the nacm_config.xml and edit the desired user name to provide the access to that user.
```c=
<enable-nacm>true</enable-nacm>
<read-default>permit</read-default>
<write-default>deny</write-default>  
<exec-default>permit</exec-default>
  <enable-external-groups>true</enable-external-groups>
  <groups>
    <group>
      <name>sudo</name>
      <user-name>netconf</user-name>
    </group>
  </groups>
  <rule-list>
    <name>sudo-rules</name>
    <group>sudo</group>
    <rule>
      <name>allow-all-sudo</name>
      <module-name>*</module-name>
      <path>/</path>
      <access-operations>*</access-operations>
      <action>permit</action>
      <comment>Corresponds all the rules under the sudo group as defined in O-RAN.WG4.MP.0-v05.00</comment>
    </rule>
  </rule-list>
</nacm>
```
- Open the netconf_server_ipv6.xml and edit the desired netconf server configuration.


Open the oamVesConfig.json and edit the details of OAM VES collector.
Open the smoVesConfig.json and edit the details of SMO VES collector.
Open the netconfConfig.json and edit the details of Netopeer server.


### Issue list


:::danger
#### Issue 1 : Tool version didn't match
:::
:::success
#### Soluation 1 : Go to this [github](https://github.com/CESNET/netopeer2/tags) check your netopeer2 version and whether it match other tool(libyang、libnetconf2、sysrepo) or not
![image](https://hackmd.io/_uploads/r1fAjt8uR.png)
- PATH : `~/l2/build/scripts`

![image](https://hackmd.io/_uploads/rymO2Y8OR.png)

![image](https://hackmd.io/_uploads/ByVi3FIOA.png)
:::

:::danger
#### Issue 2 : 
![image](https://hackmd.io/_uploads/rylZ6Y8OR.png)
:::
:::success
#### Soluation 2
Use ubuntu 20.04 (18 & 22 will cause library or compile failed )
:::
:::danger
#### Issue 3 : GPG Error
![image](https://hackmd.io/_uploads/S1SmEn6K0.png)
:::
:::success
#### Soluation 3
The update may fail due to insufficient capacity. Please try deleting unnecessary files.

```c=
sudo apt update
```
![image](https://hackmd.io/_uploads/r1J6XhpYC.png)

:::
:::danger
#### Issue 4 :Connect to VM failed in vscode but it can be done manually
```c=
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: http://tw.archive.ubuntu.com/ubuntu focal InRelease: Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal_InRelease into data and signature failed
W: An error occurred during the signature verification. The repository is not updated and the previous index files will be used. GPG error: http://tw.archive.ubuntu.com/ubuntu focal-backports InRelease: Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal-backports_InRelease into data and signature failed      
W: Failed to fetch http://tw.archive.ubuntu.com/ubuntu/dists/focal/InRelease  Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal_InRelease into 
data and signature failed
W: Failed to fetch http://tw.archive.ubuntu.com/ubuntu/dists/focal-updates/InRelease  Error writing to output file - write (28: No space left on device) [IP: 140.110.240.80 80]
W: Failed to fetch http://tw.archive.ubuntu.com/ubuntu/dists/focal-backports/InRelease  Splitting up /var/lib/apt/lists/tw.archive.ubuntu.com_ubuntu_dists_focal-backports_InRelease into data and signature failed
W: Failed to fetch http://security.ubuntu.com/ubuntu/dists/focal-security/InRelease  Error writing to output file - write (28: No space left on device) [IP: 185.125.190.83 80]
W: Some index files failed to download. They have been ignored, or old ones used instead.
```
:::
:::success
#### Soluation 4
![image](https://hackmd.io/_uploads/SyhVH2aFR.png)
>When you running this command get GPG Error please check [Issue 3](#Issue-3--GPG-Error)
:::




