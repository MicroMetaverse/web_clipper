# 虚拟机下ubuntu使用df命令查看磁盘空间小于实际空间（解决Linux /dev/mapper/ubuntu--vg-ubuntu--lv 磁盘空间不足） - 远洪 - 博客园
参考地址：[https://blog.csdn.net/Fly_1213/article/details/105142427](https://blog.csdn.net/Fly_1213/article/details/105142427)

使用 vmbox 安装了 ubuntu server 的虚拟机，分配了 50G 的磁盘空间，但是使用 df 命令查看，发现总的磁盘空间不足 50G

![](https://common.cnblogs.com/images/copycode.gif)

root@ubuntu1:/home/lyh# df -h
Filesystem                         Size  Used Avail Use% Mounted on
udev 1.9G     0  1.9G   0% /dev
tmpfs                              394M 1.1M  393M   1% /run /dev/mapper/ubuntu--vg-ubuntu--lv   24G   13G   11G  55% / tmpfs 2.0G     0  2.0G   0% /dev/shm
tmpfs 5.0M     0  5.0M   0% /run/lock
tmpfs 2.0G     0  2.0G   0% /sys/fs/cgroup /dev/loop1                          56M   56M     0 100% /snap/core18/2253
/dev/loop0                          56M   56M     0 100% /snap/core18/2246
/dev/loop4                          68M   68M     0 100% /snap/lxd/21545
/dev/loop3                          62M   62M     0 100% /snap/core20/1242
/dev/loop5                          68M   68M     0 100% /snap/lxd/21835
/dev/loop6                          33M   33M     0 100% /snap/snapd/13640
/dev/loop7                          43M   43M     0 100% /snap/snapd/14066
/dev/sda2                          976M  203M  707M  23% /boot
tmpfs                              394M 0  394M   0% /run/user/1000
/dev/loop8                          62M   62M     0 100% /snap/core20/1270

![](https://common.cnblogs.com/images/copycode.gif)

发现 /dev/mapper/ubuntu--vg-ubuntu--lv 只有 24 个 G 的空间

查看所有块设备，以及他们之间的依赖关系

![](https://common.cnblogs.com/images/copycode.gif)

root@ubuntu1:/home/lyh# lsblk
NAME                      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
loop0 7:0    0 55.5M  1 loop /snap/core18/2246 loop1 7:1    0 55.5M  1 loop /snap/core18/2253 loop3 7:3    0 61.9M  1 loop /snap/core20/1242 loop4 7:4    0 67.3M  1 loop /snap/lxd/21545 loop5 7:5    0 67.2M  1 loop /snap/lxd/21835 loop6 7:6    0 32.5M  1 loop /snap/snapd/13640 loop7 7:7    0 42.2M  1 loop /snap/snapd/14066 loop8 7:8    0 61.9M  1 loop /snap/core20/1270 sda 8:0    0   50G  0 disk
├─sda1 8:1    0    1M  0 part
├─sda2 8:2    0    1G  0 part /boot
└─sda3 8:3    0   49G  0 part
  └─ubuntu--vg-ubuntu--lv 253:0    0 24.5G  0 lvm  / sr0 11:0    1 1024M  0 rom

![](https://common.cnblogs.com/images/copycode.gif)

发现 sda3 实际有 50 个 G 的空间，但是 ubuntu--vg-ubuntu--lv 只有 24.5 个 G，剩余的空间并没有显示

查看 LVM 卷组的信息

![](https://common.cnblogs.com/images/copycode.gif)

root@ubuntu1:/home/lyh# vgdisplay --- Volume group --- VG Name               ubuntu-vg
  System ID
  Format                lvm2
  Metadata Areas 1 Metadata Sequence No 2 VG Access             read/write VG Status             resizable
  MAX LV 0 Cur LV 1 Open LV 1 Max PV 0 Cur PV 1 Act PV 1 VG Size &lt;49.00 GiB
  PE Size 4.00 MiB
  Total PE 12543 Alloc PE / Size       6272 / 24.50 GiB
  Free  PE / Size       6271 / &lt;24.50 GiB
  VG UUID               QV4pAN-R1BR-42Ln-Vv4J-58Nb-uniH-0qizNA

![](https://common.cnblogs.com/images/copycode.gif)

发现还有 24.5G 的 Free 空间未分配

lvextend -L 20G /dev/mapper/ubuntu--vg-ubuntu--lv      // 增大或减小至 20G
lvextend -L +10G /dev/mapper/ubuntu--vg-ubuntu--lv     // 增加 10G
lvreduce -L -10G /dev/mapper/ubuntu--vg-ubuntu--lv     // 减小 10G
lvresize -l  +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv   // 按百分比扩容
 resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv            // 执行调整

例如：在我原有的磁盘上增加 10 个 G

![](https://common.cnblogs.com/images/copycode.gif)

root@ubuntu1:/home/lyh# lvextend -L +10G /dev/mapper/ubuntu--vg-ubuntu--lv
  Size of logical volume ubuntu-vg/ubuntu-lv changed from 24.50 GiB (6272 extents) to 34.50 GiB (8832 extents).
  Logical volume ubuntu-vg/ubuntu-lv successfully resized.
root@ubuntu1:/home/lyh# resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv
resize2fs 1.45.5 (07-Jan-2020)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks \\= 4, new_desc_blocks = 5 The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 9043968 (4k) blocks long.
root@ubuntu1:/home/lyh# df -h
Filesystem                         Size  Used Avail Use% Mounted on
udev 1.9G     0  1.9G   0% /dev
tmpfs                              394M 1.1M  393M   1% /run /dev/mapper/ubuntu--vg-ubuntu--lv   34G   13G   20G  39% / tmpfs 2.0G     0  2.0G   0% /dev/shm
tmpfs 5.0M     0  5.0M   0% /run/lock
tmpfs 2.0G     0  2.0G   0% /sys/fs/cgroup /dev/loop1                          56M   56M     0 100% /snap/core18/2253
/dev/loop0                          56M   56M     0 100% /snap/core18/2246
/dev/loop4                          68M   68M     0 100% /snap/lxd/21545
/dev/loop3                          62M   62M     0 100% /snap/core20/1242
/dev/loop5                          68M   68M     0 100% /snap/lxd/21835
/dev/loop6                          33M   33M     0 100% /snap/snapd/13640
/dev/loop7                          43M   43M     0 100% /snap/snapd/14066
/dev/sda2                          976M  203M  707M  23% /boot
tmpfs                              394M 0  394M   0% /run/user/1000
/dev/loop8                          62M   62M     0 100% /snap/core20/1270

![](https://common.cnblogs.com/images/copycode.gif)

可以看到，/dev/mapper/ubuntu--vg-ubuntu--lv 已经增加了 10 个 G 
 [https://www.cnblogs.com/liyuanhong/articles/15660161.html](https://www.cnblogs.com/liyuanhong/articles/15660161.html)
