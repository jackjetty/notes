* **sudo**：用户想要使用sudo提升权限运行命令的话，需要把他加到sudo的list中
    * 否则会报错：xxx is not in the sudoers file.
    * 步骤
        * 切换到root用户，运行visudo命令
        * 找到root ALL=(ALL) ALL，在下面添加一行 xxx ALL=(ALL) ALL 其中xxx是要加入的用户名称
    * 用的编辑器是nano，Ctrl+O是保存，Ctrl+X有时是退出，Terminal最下面有说明
* **su**：su只是切换了root身份，但Shell环境仍然是普通用户的Shell；而su -连用户和Shell环境一起切换成root身份了。**所以最好用su-**
* 修改密码
    * 你是普通用户的话，修改自己的密码，用：passwd，就可以了，会让你先输入自己的旧密码，再输入两遍新密码。
    * 你是root的话，用：passwd username，就可以修改username的密码了，直接输入两遍新密码就可以了，不用输入旧密码。
    * 如果又不想改了，就一直回车
* cat /etc/passwd可以查看用户的bash设置等用户信息
* cat /etc/group可以查看用户组、口令、GID、包含的用户等信息
* 用户目录
    * 其实和Windows类似，每个用户在/home目录下有独立工作目录（类似Windows的Users目录）
    * /home下的用户工作目录的owner和group要设置成自己，如果这个用户是某个service创建/使用的账号，要设置成相应的owner和group
    * 用useradd命令创建的用户通常会共用现有的bash，但有时如安装Nagios时用他的脚本生成的用户，可能会没有自己的bash，需要从root的~/.bashrc拷一份到/home/xxx下面
* 权限、Owner、Group
    * 其实和Windows概念类似，只不过Windows上个人用户不经常sign out来切换账号，Windows的文件和文件夹也是基于人、组来分配读写运行权限
        * 但是Windows的文件/文件夹可以给多个人、组设置不同的权限？？Linux不可以？？
    * 查看权限
        * 用ll命令（其实是bash中对ls命令的封装）时可以查看文件和文件夹的权限设置
        * 比如rwxrwxrwx，第一组rwx是root对它的权限，第二组是owner对它的权限，第三组是group对它的权限，r是读权限，w是写权限，x是运行权限
    * chmod 777 xxx修改权限设置
    * chown newownername xxx修改拥有者
    * chgrp newgroupname xxx修改组