# 安装
* win10上如果要使用VirtualBox安装64位系统（如Ubuntu），那么就要：
    * CPU、主板支持虚拟化技术
    * 打开主板BIOS上的虚拟化开关（前提是前面说的CPU、主板支持虚拟化技术）
        * 不同主板上的BIOS中的开关位置略有不同，有的在Advanced下面，有的在Security下面，一般叫做VT-x或者AMD-V什么的，或者CPU Virtualization什么的。
        * 可以在任务管理器->Performance->CPU中通过查看Virtualization是否为Enabled来查看是否成功打开了。
        * **注意打开开关之后，**
    * （否则会蓝屏？也可能是打开开关之前的虚拟机不能再用了，懒得试了。）
        * 在控制面板的"程序和功能"的"打开/关闭Windows功能"中，关闭win10的Hyper-V功能。
            * 可能是win10的Hyper-V功能和VirtualBox、VMware的虚拟化功能冲突，但Docker又不一样了，Docker必须打开Hyper-V开关，可能Docker的机制是会使用操作系统的虚拟化功能。
            * 重启一下
        * 关闭Windows Defender Device Guard和Windows Defender Credential Guard功能
            * [微软文档](https://docs.microsoft.com/en-us/windows/security/identity-protection/credential-guard/credential-guard-manage)
            * 最好重启一下
            * 可以通过界面修改
                * 在主机操作系统上，右键单击“开始” > “运行”，键入gpedit.msc，然后单击“ 确定”。本地组策略编辑器打开
                * 转至本地计算机策略 > 计算机配置 > 管理模板>系统 >Device Guard（或者是： 设备防护） > 启用基于虚拟化的安全性。
                * 选择已禁用。
            * 也可以通过注册表修改
                * HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\LSA\LsaCfgFlags
                * HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\DeviceGuard\EnableVirtualizationBasedSecurity
                * HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\DeviceGuard\RequirePlatformSecurityFeatures
* 先创建虚拟机，然后在设置->存储->Controller：IDE->Empty->IDE Secondary Master->点击后面的按钮选择iso系统镜像，然后启动虚拟机开始安装。

# 网络设置
* 使用NAT Network共享宿主机网络（NAT不是这样的）
    * 适用于虚拟机不需要真实ip的情况，即不需要被当成一个真实的机器，而是寄宿与宿主机。
    * 安装完VirtualBox后，宿主机会多一个VirtualBox的网卡
    * Tools->Preferences->Network下新建一个Nat Network，比如名称为NatNetwork，双击打开Details，查看NatNetwork的子网网段供后面步骤使用，比如是10.0.2.0/24。
    * 虚拟机的Settings->Network->Adapter 1配置成Attached to NAT Network，使用的NAT Network的Name是前面新建的Nat Network的名称，如NatNetwork。
    * 宿主机上（控制面板->Network and Internet->Network Connections下可以查看所有网卡），修改VirtualBox虚拟网卡的ip（与NatNetwork同子网）、子网掩码（通常是255.255.255.0）、网关（通常是NatNetwork的.1地址，如10.0.2.1）。
    * 虚拟机中，修改ip（与NatNetwork同子网）、子网掩码（通常是255.255.255.0）、网关（通常是NatNetwork的.1地址，如10.0.2.1）、DNS服务（可以在宿主机上ipconfig/all查看主网卡用的DNS服务器地址），然后要重启网络才生效。
* 虚拟机创建的Ubuntu可能是无线网络，需要在外面增加一个有线网卡。
* 修改了虚拟机的网络配置不用重启，实时生效

# 共享剪切板/支持拖拽文件
* 一般只需要选中虚拟机，然后设置剪贴板和拖拽为双向就ok了（General->Advanced下面）。
* 但是有时候需要VirtualBox和虚拟机都安装组件。需要去虚拟机工具栏->Devices->Insert Guest Additions CD images...，然后虚拟机系统中如Linux会弹出Terminal让你输入密码然后开始从安装镜像中安装插件，完成后可能要重启虚拟机。中文界面可能是在虚拟机的左上方，有一些工具，选中"设置"，然后点击下拉框里面的“安装增强功能”。

# 备份
* 原始文件夹
    * Log文件夹
    * Snapshots文件夹
    * xxx.vdi虚拟机文件
    * xxx.vbox文件
    * xxx.vbox-prev文件
* 直接拷贝文件夹
    * 直接拷贝后，可能不能直接通过"Add"来把这个文件夹下的文件加入成为虚拟机，可能需要在Add的步骤中，指定硬盘文件为原文件夹中的vdi文件，但是这样的话其实就不是真正的从这个文件夹打开了，而是需要另外建一个文件夹，只是从这个文件导入/启动。这样可能就有点乱了，如果两个文件夹都要用，只是从这个文件启动且各有一部分文件的话。
* 快照
    * 在上一次的快照基础上做差异化快照，相当于checkpoint检查点，可以restore到每一个snapshot
    * 会在Snapshots文件夹下面生成快照文件：xxx.vdi
    * 对某一个虚拟机的快照操作可以进行多次，每次都是在之前快照的基础上，相当于是差异化的，这个在界面上可以看出来是树状的，可以回退到每个快照。
    * 可以不关机
* 备份（Appliance）
    * 真正意义上的备份，导出一个单独的合并的文件，用于以后再导入
    * 也就是导入、导出Appliance
    * 会生成一个xxx.ova文件（只有这一个文件），下次使用需要导入
    * 可以不关机
    * 可以选择是否包含ISO镜像文件
    * 可以选择包含哪些网卡的物理地址
* 克隆（Clone）
    * 克隆完成后，VirtualBox的机器列表中也会显示出来这个克隆出来的虚拟机，直接就可以使用。
    * 会生成一个单独的克隆目录，包含xxx.vbox和xxx.vdi文件
    * 必须先关机
    * 可以选择包含哪些网卡的物理地址
    * 可以选择是否保留硬盘名称
    * 可以选择是否保留硬件UUIDs
    * 可以选择Full clone还是Linked clone