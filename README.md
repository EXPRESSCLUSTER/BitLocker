# BitLocker
## Note
- BitLocker can be used with **Mirror Disk Resource**. Disk Resource and Hybrid Disk Resource that use SAN can not be used.

## Evaluation Environment
- OS
  - Windows Server 2019
- Mirror Disk Resource
  |Parameter        |Value|
  |-----------------|--|
  |Resource Name    |md|
  |Cluster Partition|L:|
  |Data Partitoin   |M:|

## How to use BitLocker with EXPRESSCLUSTER
1. Install BitLocker.
1. Enable BitLocker for system drive (e.g. C: drive).
1. Install EXPRESSCLUSTER and create a cluster.
1. Start Mirror Disk Resource (e.g. md) on the primary server.
1. Enable BitLocker for M: drive (Data Partition) and enable auto unlock on the primari server.
   ```bat
   C:\> manage-bde.exe -on M: -rp -used
   C:\> manage-bde.exe -autounlock -enalbe M:
   C:\> manage-bde.exe -status M:
   BitLocker Drive Encryption: Configuration Tool version 10.0.17763
   Copyright (C) 2013 Microsoft Corporation. All rights reserved.
   
   Volume M: []
   [Data Volume]
   
       Size:                 Unknown GB
       BitLocker Version:    2.0
       Conversion Status:    Used Space Only Encrypted
       Percentage Encrypted: 100.0%
       Encryption Method:    XTS-AES 128
       Protection Status:    Protection On
       Lock Status:          Unlocked
       Identification Field: Unknown
       Automatic Unlock:     Enabled
       Key Protectors:
           Numerical Password
           External Key (Required for automatic unlock)
   ```
1. Move the failover group.
   ```bat
   C:\> clpgrp -m <failover group name>
   ```
1. Enable BitLocker and auto unlock on the secondary server.

## Remarks
- LowerFilters' value should be as below.
  ```bat
  C:\>reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\{71a27cdd-812a-11d0-bec7-08002be2092f} 
  
  HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Class\{71a27cdd-812a-11d0-bec7-08002be2092f}
      Class    REG_SZ    Volume
      ClassDesc    REG_SZ    @c_volume.inf,%ClassDesc%;Storage volumes
      IconPath    REG_MULTI_SZ    %SystemRoot%\System32\setupapi.dll,-53
      NoInstallClass    REG_SZ    1
      SilentInstall    REG_SZ    1
      UpperFilters    REG_MULTI_SZ    volsnap
      LowerFilters    REG_MULTI_SZ    fvevol\0clpdiskfltr
  (snip)  
  ```
  - fvevol: BitLocker's filter driver
  - clpdiskfltr: EXPRESSCLUSTER's filter driver
