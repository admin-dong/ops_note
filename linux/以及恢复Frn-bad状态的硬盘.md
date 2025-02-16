![img](https://cdn.nlark.com/yuque/0/2024/png/35538885/1717490086010-16dad76e-1f55-4630-8c0f-074e1a4cc075.png)

开机的时候按ctrl+r 进去磁盘阵列模式  

**恢复Frn-bad状态的硬盘**

**Legacy模式下**

如图所示，如果硬盘状态变成了“Frn-Bad”，可通过以下步骤将硬盘恢复为“Online”状态。

![img](https://cdn.nlark.com/yuque/0/2023/png/35538885/1703043332266-970fada7-c50b-4912-a556-3ec5878f194f.png)

选中“Frn-Bad”状态的硬盘并按“F2”**。**

选择“Make unconfigured good”并按“Enter”**。**硬盘状态变成“Foreign”。

   3.外部配置

![img](https://cdn.nlark.com/yuque/0/2023/png/35538885/1703043332550-21b1567d-043e-4458-ac93-8bf96ca2b624.png)

4.导入/清除外部配置。

选中要导入或删除外部控制器的RAID卡，按“F2”。

选择“Import”导入外部配置，或选择“Clear”清除外部配置，并按“Enter”确认。

导入完成后，可在“VD Mgmt”页签中查看配置结果。

![img](https://cdn.nlark.com/yuque/0/2023/png/35538885/1703043332888-6221a4f2-4e50-4392-909c-1c68de280e0a.png)

**EFI/UEFI模式下**

EFI/UEFI模式下，Frn-bad状态的硬盘显示为“Unconfigured Bad”，如图7-173所示。可通过以下步骤将硬盘恢复为“Online”或“unconfigured good”状态。

图7-173 硬盘状态（1）

![img](https://cdn.nlark.com/yuque/0/2023/png/35538885/1703043333291-6e3f306c-5989-4c05-b7ec-3118e1ff7483.png)

选中“Unconfigured Bad”状态的硬盘并按“Enter”。进入硬盘属性界面。

选择“Operation”并按“Enter”。弹出硬盘操作菜单，如图7-174所示。图7-174 硬盘操作菜单

选择“Make Unconfigured Good”并按“Enter”。

选择“Go”并按“Enter”。提示操作成功，硬盘状态变成“(Foreign),Unconfigured Good”，如图7-175所示。图7-175 硬盘状态（2）

根据实际需要，导入或清除外部配置。具体操作请参见导入/清空外部配置。

执行导入外部配置操作，硬盘状态恢复为“Online”。

执行清除外部配置操作，硬盘状态恢复为“Unconfigured Good”。

# 机器内做raid0