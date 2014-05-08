Documents
=========

杂记



2）烧写u-boot

sf probe 0
mw.b 82000000 ff 80000
tftp 0x82000000 u-boot_hi3520d.bin
sf probe 0
sf erase 0 80000
sf write 82000000 0 80000
reset

    3）烧写内核

mw.b 82000000 ff 200000
tftp 0x82000000 uImage_hi3520d_mini
sf probe 0
sf erase 0xc0000 0x200000
sf write 0x82000000 0xc0000 0x200000

    4)烧写文件系统
sf probe 0
mw.b 82000000 ff d40000
tftp 0x82000000 rootfs.nand.jffs2
sf erase 2c0000 d40000
sf write 82000000 2c0000 d40000

    5）设置启动参数
        setenv bootargs 'mem=64M console=ttyAMA0,115200 root=/dev/mtdblock2 rootfstype=jffs2 mtdparts=hi_sfc:768k(boot),2048k(kernel),-(rootfs)'
        setenv bootcmd 'sf probe 0;sf read 0x82000000 0xc0000 0x200000;bootm 0x82000000'
        sa
