# QCloud AT Customized partition table
QCloud AT command set use its own partition table:
1. The Size of app in partitions_at.csv is 0x180000.
2. QCloud App needs several customized partitions in at_customize section, `qcloud_modinfo`, `qcloud_devinfo`, `qcloud_prdinfo` and `qcloud_errlog`, as can be found in at_customize.csv. Please make sure these partitions exist.
3. QCloud App also need a FLASH area to do firmware OTA for MCU side. The start address and size of the OTA area is module dependant and can be defined by `AT+TCMODINFOSET` command.