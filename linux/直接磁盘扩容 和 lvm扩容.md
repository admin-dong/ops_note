<https://cloud.tencent.com/developer/article/1671893> 

<https://blog.csdn.net/qq_63890458/article/details/144561762>









适用于没lvm 直接扩容 

 yum install [cloud](https://search.bilibili.com/all?from_source=webcommentline_search&keyword=cloud&seid=14667995902591160848)-utils-[growpart](https://search.bilibili.com/all?from_source=webcommentline_search&keyword=growpart&seid=14667995902591160848)

LC_ALL=en_US.UTF-8 growpart /dev/sda 3 

想扩容那个分区 就填那个分区的号  会把磁盘剩下的所有空间分配到上 





虚拟机磁盘  加完直接执行  

```
for host in $(ls /sys/class/scsi_host/); do
    echo "- - -" > /sys/class/scsi_host/$host/scan
done
```