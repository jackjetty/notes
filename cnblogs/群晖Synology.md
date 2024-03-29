# 简介
* 群晖是做的最好的一家NAS公司，声称是买软件卖硬件，软件有丰富的功能。
* 白群晖就是指从正规渠道买软件+硬件，价格昂贵，性价比低。
* 黑群晖是指自己搭建或购买单独的硬件（可以是电脑主机、可以是其他厂家的NAS、可以是淘宝上卖的用小机箱和低功耗CPU如J3455组装的NAS），性价比高，硬件自行选择，自由。

# 硬件选择
* 群晖一般把系统和数据放在不同的介质（如硬盘）上，比如：
    * 如果是小型机，如带J3455的主板放在小机箱中，通常会把引导系统装在U盘上，U盘一直插在机器上，就相当于系统盘了？？？
    * 如果是台式机，通常用一个SSD固态硬盘作为系统盘，然后装一个虚拟机软件，建一个虚拟机装上DSM作为系统盘，虚拟机开机启动即可，然后把单独的硬盘作为数据盘。好处是这个台式机还可以作为他用，比如做HDPC。

# 理论
* **引导系统只是引导安装、启动，群晖系统可以使单独的虚拟机文件，引导后选择安装，单独的虚拟硬盘文件或者物理硬盘只用来存储数据，但是上面也还有一些数据格式等信息，比如集群信息，因此即使引导系统重装，重新使用原硬盘，也是可以使用原数据的。**
* **后期即使把虚拟机重启了，也没关系，虚拟机上只是引导系统。真实的NAS系统数据和数据都在数据盘上。**
* **黑群晖需要关掉自动更新功能**
* **黑群晖如果忘了管理员账号密码，会很麻烦，只能ssh到硬盘上去修改文件**
    * 否则只能格式化数据硬盘了。。。
* 使用
    * **先建存储池（RAID），再建存储空间。存储池需要检查硬盘，时间比较久。再建共享文件夹（然后就可以通过手机app或者Windows访问了）。Video Station等软件会自动创建video等名称的共享文件夹。**

# 安装
* 安装引导系统
* 使用Synology Assistant连接成功创建的NAS服务器（DSM系统）进行配置
    * 工具软件，安装好DSM系统后，用于扫描安装成功的NAS，以网页的形式打开进行配置。
    * ![搜索NAS服务器](https://img2018.cnblogs.com/blog/106125/201903/106125-20190302161445098-718595257.png)
    * ![连接NAS服务器进行配置](https://img2018.cnblogs.com/blog/106125/201903/106125-20190302161428566-74646556.png)
* 配置过程
    * 账号
        * 需要创建一个管理员账号和密码。
    * 更新
        * **一定要关闭自动更新，否则以后可能变砖**。只能选择下载但不自动安装。
    * 磁盘群组（RAID）
        * 在存储空间管理员下面
        * 如果DSM系统运行在虚拟机上，那么只有成功挂载的物理硬盘或者虚拟硬盘文件才能看到并创建磁盘群组
        * 如果是用虚拟机挂载物理硬盘，需要先把硬盘挂起（磁盘管理中右击该硬盘左侧名字部分），然后在虚拟机软件中增加一个SCSI类型的硬盘驱动器并选择该硬盘。
            * ![挂起硬盘](https://img2018.cnblogs.com/blog/106125/201903/106125-20190302170420627-963124973.png)
            * ![虚拟机挂载物理硬盘](https://img2018.cnblogs.com/blog/106125/201903/106125-20190302170442280-1632139369.png)
    * 存储空间
        * 在存储空间管理员下面
        * 添加的磁盘群组检查完成后，才能基于它创建存储空间以供使用
* 真实独立的主机或Hyper-V或VirtualBox或VMWare
    * 硬盘可以使用独立的硬盘，也可以使用系统盘上创建的虚拟硬盘文件。
    * **安装在VMWare上**
        * [可能是最简单的VMware虚拟机安装黑群辉DSM方法](https://post.m.smzdm.com/p/ag89drvd/?send_by=1794014449)
        * **注意**
            * **默认的硬盘不用多大，默认的80MB即可**
            * **启动虚拟机之前，需要手动加一个物理硬盘，选择使用整个物理硬盘，这个硬盘好像要提前脱机。**
            * **设置开机启动**
                * **VMWare的快捷方式中的目标改成"{programpath}" -x ”D:\VM\DSM\DSM.vmx“**
                * **把这个快捷方式复制到C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp**