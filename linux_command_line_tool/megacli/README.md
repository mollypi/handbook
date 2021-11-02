# Megacli

1. Show Rebuid procedure

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -PDRbld -ShowProg -physdrv[20:2] -aALL
   ```

2. Check ES

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -PDList -aAll -NoLog | grep -Ei "(enclosure|slot)"
   ```

3. Check all drives status

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -PDList -aAll -NoLog
   ```

4. Check all Virtual Disks status

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -LdPdInfo -aAll -NoLog
   ```

RAID Level：

| -- | -- |
| :--- | :--- |
| RAID Level : Primary-1, Secondary-0, RAID Level Qualifier-0 | RAID 1 |
| RAID Level : Primary-0, Secondary-0, RAID Level Qualifier-0 | RAID 0 |
| RAID Level : Primary-5, Secondary-0, RAID Level Qualifier-3 | RAID 5 |
| RAID Level : Primary-1, Secondary-3, RAID Level Qualifier-0 | RAID 10 |

1. Online Raid

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -CfgLdAdd -r0[0:11] WB NORA Direct CachedBadBBU -strpsz64 -a0 -NoLog
   /opt/MegaRAID/MegaCli/MegaCli64 -CfgLdAdd -r5 [12:2,12:3,12:4,12:5,12:6,12:7] WB Direct -a0
   ```

2. Allocate hard drive

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -PdLocate -start -physdrv[252:2] -a0
   ```

3. Clean Foreign status

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -CfgForeign -Clear -a0
   ```

4. Check offline drive in RAID array

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -pdgetmissing -a0
   ```

5. Replace bad drive

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -pdreplacemissing -physdrv[12:10] -Array5 -row0 -a0
   ```

6. Run rebuild manually

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -pdrbld -start -physdrv[12:10] -a0
   ```

7. Chek Megacli log

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -FwTermLog dsply -a0 > adp2.log
   ```

8. Set up HotSpare

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -pdhsp -set [-Dedicated [-Array2]] [-EnclAffinity] [-nonRevertible] -PhysDrv[4:11] -a0
   /opt/MegaRAID/MegaCli/MegaCli64 -pdhsp -set [-EnclAffinity] [-nonRevertible] -PhysDrv[32：1}] -a0
   ```

9. Disable Rebuild

   ```text
   /opt/MegaRAID/MegaCli/MegaCli64 -AdpAutoRbld -Dsbl -a0
   ```

10. Set up rebuild rate

    ```text
    /opt/MegaRAID/MegaCli/MegaCli64 -AdpSetProp RebuildRate -30 -a0
    ```

