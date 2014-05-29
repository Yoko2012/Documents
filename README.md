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













海思SDK环境下实现：
1、下载gdb：
　　下载地址为：http://ftp.gnu.org/gnu/gdb/
2、解压缩：
tar xvf gdb-7.６.tar.bz2 
3、进入该目录
cd gdb-7.６
4、配置:(生成makefile)
./configure --target=arm-hisiv100nptl-linux --program-prefix=arm-hisiv100nptl-linux-  --prefix=/home/usr/gdb-build  

注：--target=arm-hisiv100nptl-linux意思是说目标平台是运行于ARM体系结构的linux内核；--program-prefix=arm-hisiv100nptl-linux是指生成的可执行文件的前缀，比如arm-hisiv100nptl-linux-gdb，--prefix是指生成的可执行文件安装在哪个目录，这个目录需要根据实际情况作选择。如果该目录不存在，会自动创建，当然，权限足够的话。 
5、编译、安装
make ;make install
会在--prefix指定的目录下生成三个子目录：bin、lib、share，我们需要的arm-linux-gdb就在其中的bin目录下。
编译gdb-server
1 cd /gdb-7.6/gdb/gdbserver
2  配置
 ./configure --target=arm-hisiv100nptl-linux  --host=arm-hisiv100nptl-linux 
这里的--host指定了生成可执行文件运行的平台和系统：运行于ARM平台的Linux系统
3 make
编译生成　gdb-server

gdbserver 远程调用的使用：
１将之前生成的gdbsrver 调试程序放在目标板上或者通过nfs方式运行也可以．这样就可以用gdb和gdbserve 来调试开发板上的程序了．
2 要调试程序首先在目标板上运行gdbserver 
./gdbserver --remot-debug 192.168.1.100:2345 test
192.168.1.100 为目标板ip地址，2345是开通目标板的这个端口号做为通信端口．
test　是要调试的程序(编e 译好的)
3　在开发板上运行gdb程序
．／arm-hisiv100nptl-linux-gdb test
进入gdb 调试状态后：
输入target remote 192.168.1.100:2345

4连接成功后可以调试程序：
如：输入　c运行程序．
