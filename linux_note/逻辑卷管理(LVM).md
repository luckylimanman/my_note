logical volume manager

物理卷----分区或磁盘或RAID
pvcreate /dev/vda1（分区）或/dev/vda（磁盘
添加新分区的时候把类型改成LINUX LVM

卷组---由一个或多个物理卷组成---组成存储池
vgcreate

逻辑卷---建立在卷组之上，标准块设备，在逻辑卷上创建文件系统
lvcreate

PE
physical extent 物理块
把物理卷分成大小相等的物理块作为存储的最小单位

如果卷组不够大先扩大卷组再扩大逻辑卷
扩大逻辑卷后告知文件系统,一般情况下只增大逻辑卷
扩大逻辑卷后告知系统命令
xfs---xfs_growfs LV 该系统逻辑卷只能增大
ext4---resizefs LV

逻辑卷的减小
umount /lvml
resize2fs /dev/myvg/mylvm1 200M
e2fsck -f /dev/myvg/mylvm1
resize2fs /dev/myvg/mylvm1 200M
lvreduce -L 200M /dev/myvg/mylvm1
mount /dev/myvg/mylvm/lvm1
