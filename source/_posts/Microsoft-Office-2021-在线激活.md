---
title: Microsoft Office 2021 在线激活
author: leazhi
tags:
  - windows
  - office
categories:
  - windows
date: 2023-07-15 20:29:39
cover: https://hexo.linuser.com/gallery/wallpaper/win-1037699.jpg
discription:
keywords:
password: chekir
message: 抱歉，需要输入密码，查看文章
---

---

**该方法可以在线激活 office 2021** 

这里以安装的 [cn_office_professional_plus_2019_x86_x64_dvd_5e5be643](https://blog.51cto.com/u_14702690/2472468) 为激活实例：

1、以管理员身份打开 cmd，进入到 office 2019 的安装目录：

```powershell
C:\Windows\system32>cd C:\Program Files\Microsoft Office\Office16
```

2、查看安装时默认的激活码：8MBCX

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /dstatus
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
PRODUCT ID: 00413-50000-00000-AA937
SKU ID: 52c4d79f-6e1a-45b7-b479-36b666e0a2f8
LICENSE NAME: Office 19, Office19ProPlus2019R_Grace edition
LICENSE DESCRIPTION: Office 19, RETAIL(Grace) channel
BETA EXPIRATION: 1601/1/1
LICENSE STATUS:  ---OOB_GRACE---
ERROR CODE: 0x4004F00C
ERROR DESCRIPTION: The Software Licensing Service reported that the application is running within the valid grace period.
REMAINING GRACE: 4 days  (7195 minute(s) before expiring)
Last 5 characters of installed product key: 8MBCX
---------------------------------------
---------------------------------------
---Exiting-----------------------------
```

3、安装 VL 版本相关许可证文件：

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_KMS_Client_AE-ppd.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_kms_client_ae-ppd.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------


C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_KMS_Client_AE-ul.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_kms_client_ae-ul.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------


C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_KMS_Client_AE-ul-oob.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_kms_client_ae-ul-oob.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------


C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_MAK_AE-pl.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_mak_ae-pl.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------


C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_MAK_AE-ppd.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_mak_ae-ppd.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------


C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_MAK_AE-ul-oob.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_mak_ae-ul-oob.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------


C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inslic:..\root\Licenses16\ProPlus2019VL_MAK_AE-ul-phn.xrm-ms
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installing Office license: ..\root\licenses16\proplus2019vl_mak_ae-ul-phn.xrm-ms
Office license installed successfully.
---------------------------------------
---Exiting-----------------------------
```

4、安装激活密钥：

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /inpkey:NMMKJ-6RK4F-KMJVX-8D9MJ-6MWKP
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
<Product key installation successful>
---------------------------------------
---Exiting-----------------------------
```

5、设置 KMS 服务器地址：

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /sethst:kms.ghpym.com
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Successfully applied setting.
---------------------------------------
---Exiting-----------------------------
```

6、激活 office 2019:

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /act
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Installed product key detected - attempting to activate the following product:
SKU ID: 52c4d79f-6e1a-45b7-b479-36b666e0a2f8
LICENSE NAME: Office 19, Office19ProPlus2019R_Grace edition
LICENSE DESCRIPTION: Office 19, RETAIL(Grace) channel
Last 5 characters of installed product key: 8MBCX
ERROR CODE: 0xC004F017
ERROR DESCRIPTION: The Software Licensing Service reported that the license is not installed.
---------------------------------------
Installed product key detected - attempting to activate the following product:
SKU ID: 85dd8b5f-eaa4-4af3-a628-cce9e77c9a03
LICENSE NAME: Office 19, Office19ProPlus2019VL_KMS_Client_AE edition
LICENSE DESCRIPTION: Office 19, VOLUME_KMSCLIENT channel
Last 5 characters of installed product key: 6MWKP
<Product activation successful>
---------------------------------------
---------------------------------------
---Exiting-----------------------------
```

<span style="color:red">说明：</span>已安装有最后 5 位数是 8MBCX 的激活密钥（零售版的试用 5 天激活密钥），已安装有最后 5 位数是 6MWKP 的激活密钥（刚安装的激活密钥）

7、删除旧的激活码：

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /unpkey:8MBCX
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
Uninstalling product key for: Office 19, Office19ProPlus2019R_Grace edition
<Product key uninstall successful>
---------------------------------------
---Exiting-----------------------------
```

8、最后，查看下已安装的激活密钥：

```powershell
C:\Program Files\Microsoft Office\Office16>cscript ospp.vbs /dstatus
Microsoft (R) Windows Script Host Version 5.812
版权所有(C) Microsoft Corporation。保留所有权利。

---Processing--------------------------
---------------------------------------
PRODUCT ID: 00414-50000-00000-AA968
SKU ID: 85dd8b5f-eaa4-4af3-a628-cce9e77c9a03
LICENSE NAME: Office 19, Office19ProPlus2019VL_KMS_Client_AE edition
LICENSE DESCRIPTION: Office 19, VOLUME_KMSCLIENT channel
BETA EXPIRATION: 1601/1/1
LICENSE STATUS:  ---LICENSED---
REMAINING GRACE: 180 days  (259200 minute(s) before expiring)
Last 5 characters of installed product key: 6MWKP
Activation Type Configuration: ALL
        DNS auto-discovery: KMS name not available
        KMS machine registry override defined: kms.ghpym.com:1688
        Activation Interval: 120 minutes
        Renewal Interval: 10080 minutes
        KMS host caching: Enabled
---------------------------------------
---------------------------------------
---Exiting-----------------------------
```
