---
title: download_3gpp guide

---

:::success
- [3gpp 0.7.0](https://pypi.org/project/download_3gpp/)
:::

## Install and use the download_3gpp package
### 1. Installation
Use pip to install the package to your IDLE environment:
```bash=
pip install download_3gpp
```
### 2. Get started
To view the available commands and options, you can run the following command:
```bash=
>download_3gpp --help

usage: download_3gpp [-h] [--base-url BASE_URL] [--destination DESTINATION]
                     [--rel REL] [--series SERIES] [--std STD]

Acquire 3GPP standards packages from archive

optional arguments:
  -h, --help            show this help message and exit
  --base-url BASE_URL   Base 3GPP download URL to target, default
                        "https://www.3gpp.org/ftp/Specs/latest/"
  --destination DESTINATION
                        Destination download directory, default "./"
  --rel REL             3GPP release number to target, default "all"
  --series SERIES       3GPP series number to target, default "all"
  --std STD             3GPP standard number to target, default "all"
```

### Operation Example
#### Case 1. Download all the latest files
By default, the tool will download all the latest files from the 3GPP "latest" archive and place them in the current directory:
:::info
```bash=
download_3gpp
```

:::
#### Case 2. Download all files for a specific 3GPP release
- Use the --rel parameter to specify the release version:
:::info
```bash=
download_3gpp --rel 16
```
:::
#### Case 3. Download all files of a specific 3GPP series
- Use the --series parameter to specify the series:
:::info
```bash=
download_3gpp --series 32
```
:::

- Combined with the ==--rel== and ==--series== parameters, this allows to filter more precisely for series within a specific release:

:::info
```bash=
download_3gpp --rel 16 --series 32
```
:::

#### Case 4. Download a specific 3GPP standard in a specific series
- Specify a standard number, use the ==--std== parameter to download a specific standard:

:::info
```bash=
download_3gpp --std 104
```
:::


- If you want to download a specific standard from a specific series, you can do this:
:::info
```bash=
download_3gpp --series 25 --std 104
```
:::

:::danger
- Download specific versions of 3GPP standards
Note, when you use the --series and --std parameters, the command will try to get that series or standard from all distributions. If the file does not exist on some distributions, a warning will be issued, but the download will continue and try to get the file from the remaining distributions.
:::
:::danger
- Manually download a specific distribution

Currently, there is no way to specify a subset of distributions to download or to exclude specific distributions, so you will need to manually download from each distribution for which the documentation is known to exist:

:::
```bash=
download_3gpp --rel 13 --series 25 --std 104
download_3gpp --rel 16 --series 25 --std 104
```

### Download standards from historical snapshots
3GPP specifications are snap-shotted over time, and many snap-shotted specifications have been archived and are no longer publicly available. If you want to get standards from a historical snapshot (checking first whether that snapshot has the files you expect), you can specify the base URL:
```bash=
download_3gpp --base-url https://www.3gpp.org/ftp/Specs/2019-09/
```

### 3. Sapmle
```bash=
download_3gpp --rel 16 --series 38
```

![image](https://hackmd.io/_uploads/Bynrezttyg.png)

![image](https://hackmd.io/_uploads/r1YLgfYY1e.png)
