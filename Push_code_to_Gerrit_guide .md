---
title: 'Push_code_to_Gerrit_guide '

---

## Refrence
:::success
- [Use with Gerrit](https://hackmd.io/@Yueh-Huan/HydYqyYH2#Step-1-Setup-)
- [Contribute Your Code to OSC](https://hackmd.io/@dennis5581407/BkjeWBU6n)
- [[教學] 產生SSH Key並且透過KEY進行免密碼登入](https://xenby.com/b/220-%E6%95%99%E5%AD%B8-%E7%94%A2%E7%94%9Fssh-key%E4%B8%A6%E4%B8%94%E9%80%8F%E9%81%8Ekey%E9%80%B2%E8%A1%8C%E5%85%8D%E5%AF%86%E7%A2%BC%E7%99%BB%E5%85%A5)
- [Git – GitHub 出現 「Please make sure you have the correct access rights and the repository exists.」](https://kiiuo.com/archives/3147/git-github-%E5%87%BA%E7%8F%BE-%E3%80%8Cplease-make-sure-you-have-the-correct-access-rights-and-the-repository-exists-%E3%80%8D/)
- [Working with Gerrit: An example](https://gerrit-review.googlesource.com/Documentation/intro-gerrit-walkthrough.html)
:::

## Step 1: Setup SSH in your device

### Step 1-1: Create key
==~/.ssh/id_rsa== this path can find in GUI

For myself
```c=
ssh-keygen -t rsa -C "wasd096123@gmail.com" -f ~/.ssh/id_rsa
```
If you need to use **Bemo Account**
```c=
ssh-keygen -t rsa -C "d11002806@gapps.ntust.edu.tw" -f ~/.ssh/id_rsa
```

You will get two document
- id_rsa
- id_rsa.pub

==They are very Important and easy to delete by mistake==

### *id_rsa*
:::spoiler
```
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```
:::
### *id_rsa.pub*
:::spoiler
```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCxCn5KrHwLkfkbX52FkZIEq4bKHADEkCInBUE4Sd+3G201H3uQV+na2K1TgBlnk7iPtrqvlBpdnQPNK/g0HNi7Xi1KJLzxFGDXBE05lCMJNndtc/oCKuATll03uzm2o+qkQXFN3Hfi8Uk+N7Tfbebg7pNySNvuW/twycl/0+jIUA/cGXWhpqnfp3AEZZtuXKMxwJ83hIg1C/h7sZLJ6X3TFMqmNxxVk+TXrOVNCKPswDyXohG1d+kVoAR+Thfx/HSqX6Mcu2Uh5xbdnfVWJ/U/JELi+Bn7/SThcnCF7YCKKv07bJJVCFPIcqr5vep+1X+3sIsq3PfXo/XyUe/lX+by7j+qL6Y++RoIaEsNPSKkyR1BsX5AmMLBoXJW6Bt0Mk9w3h8QaasmPbXCnXpQJuOZhJ1jvgACUFcGAFTsrD/HLvX9cI3kf57H+jxj+cWRu3YnYzhn9jq5bSczSGtfzM4W/+1imwdFmNBmUOctymOivsHPYAz231cocvskjlIVgec= d11002806@gapps.ntust.edu.tw

```
:::


You will get this screen

![](https://hackmd.io/_uploads/Bk4E9xHbp.png)
:::warning
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
:::

If passphrase is set, you need to enter this password every time you log in with this key (higher security, can be set according to personal needs)

For this example ,I just set nothing(Blank)

### Step 1-2: Check key(if you create in ssh or something Folder that have'n been hidden by Linux)

GUI:
![](https://hackmd.io/_uploads/rJt1jerW6.png)

Terminal:
![](https://hackmd.io/_uploads/SJx_igSWp.png)


Open id rsa.pub

GUI
![](https://hackmd.io/_uploads/rJGRSWH-T.png)

Terminal ( cat ~/ssh/id_rsa.pub ):


![](https://hackmd.io/_uploads/BJB_rbBbT.png)

### =====If you push by yourslef keep part below ========

### Step 1-3: Setting key in your Gerrit 

![](https://hackmd.io/_uploads/Bk_aI-SZ6.png)


### Add ssh key to the SSH agent
```c=
# Execute SSH agent
eval $(ssh-agent -s)
```
![](https://hackmd.io/_uploads/HJOst-Bbp.png)

```c=
# Add private key to the agent
ssh-add ~/yuehhuan/ssh/id_rsa
```
![](https://hackmd.io/_uploads/S1Qnt-BZ6.png)


## Step 2(Optional): Sign ICLA(Individual Contributor License Agreement)
### Step 2-1

![](https://hackmd.io/_uploads/ByT-fzrZ6.png)

---
![](https://hackmd.io/_uploads/Sy27zMBZ6.png)

and choose ==ICLA - O-RAN Specification Code Project (ORAN-SCP)==




## Step 3 Connect gerrit by Bimo account
```c=
ssh -p 29418 frbimo2@gerrit.o-ran-sc.org
```
![image](https://hackmd.io/_uploads/SkI4W1Zt0.png)

---
## Step 4 Clone and Checkout commit

- Clone commits that need to be modified
```c=
git review -d 11653
```
- Check out specific commits
```c=
git checkout bc06ea21c14b105b0a79db41d1e4c4733d084a5f
git checkout <commit name>
```
![image](https://hackmd.io/_uploads/Hyz8WJWKC.png)

## Step 5 Do some change and modify in same commit

- Do some change
```c=
git add <modified-files>
```
- To modify the same commit
```c=
git commit --amend
```

- Getting the latest commits and branches from the remote repository
```c=
git fetch
```
![image](https://hackmd.io/_uploads/HyoD-kWF0.png)

---

## Step 6 Push your change

- Push
```c=
git push origin HEAD:refs/for/sch_slice_based
```
- Result
![image](https://hackmd.io/_uploads/H1tTWJ-KA.png)

---



## Quick start
:::success
- Connect gerrit 
```c=
ssh -p 29418 frbimo2@gerrit.o-ran-sc.org
```
![image](https://hackmd.io/_uploads/SkI4W1Zt0.png)
- Clone commits that need to be modified
```c=
git review -d 11653
```
- Check out specific commits
```c=
git checkout bc06ea21c14b105b0a79db41d1e4c4733d084a5f
git checkout <commit name>
```
![image](https://hackmd.io/_uploads/Hyz8WJWKC.png)
- Do some change
```c=
git add <modified-files>
```
- To modify the same commit
```c=
git commit --amend
```

- Getting the latest commits and branches from the remote repository
```c=
git fetch
```
![image](https://hackmd.io/_uploads/HyoD-kWF0.png)
- Push
```c=
git push origin HEAD:refs/for/sch_slice_based
```
- Result
![image](https://hackmd.io/_uploads/H1tTWJ-KA.png)
:::

:::info
## Issue 1 commit already closed

![image](https://hackmd.io/_uploads/rJJ1EyWKR.png)

---
## Solution 1 (Before U do any change the status of commit u should discuss with Bimo)
```c=
ssh -p 29418 review.example.com gerrit review --abandon 8242,2
ssh -p <bimo port> <bimo gerrit mail> gerrit review --abandon <commit number>
```

- Restore (if your commit be abandon and you want to open it again)
```c=
ssh -p 29418 frbimo2@gerrit.o-ran-sc.org gerrit review --restore db307526838df945e9142d77ff64fa20f61d7733
```
![image](https://hackmd.io/_uploads/Hk28dxWtC.png)

- Abandon (Turn status to Abandon)
```c=
ssh -p 29418 frbimo2@gerrit.o-ran-sc.org gerrit review --abandon e84036c8d6d76034745a9b6baa44d3555316a2af
```
![image](https://hackmd.io/_uploads/Sk-jaybKR.png)
![image](https://hackmd.io/_uploads/r1SCT1WYR.png)

:::

