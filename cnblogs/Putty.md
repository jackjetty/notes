* 可以基于session对配置进行save和load
* 本地所有的putty的配置都存在注册表中，因此可以通过导出、导入注册表进行备份和共享
    * 注册表目录是HKEY_CURRENT_USER/Software/SimonTatham，将这个目录右击导出即可，文件类型为xxx.reg
    * 使用者双击该reg文件即可导入，不行的话可以regedit打开注册表界面后，在菜单栏点击File->Import进行导入，文件中已包含路径信息，因此不用特意先找好导入位置。
* 设置Proxy
    * ssh也可以使用http代理
        * ![proxy setting](https://img2018.cnblogs.com/blog/106125/201904/106125-20190412164519632-745903085.png)
* 设置SSH Tunnels
    * 这些Tunnel都是基于Session中的跳板机来中转的，因此这个跳板机必须是通的，可以ssh上去；然后这个跳板机又和想要建立Tunnel的这些主机端口通。
    * 在Connection->SSH->Tunnels下面
    * 可以添加多个Tunnel
    * Source port中填写想要映射到的本地端口（好像有人用了也没事，至少不报错，只是没用成功）
    * Destination中填写目标主机的Host和端口，比如xxx.xxx.xxx.xxx:xxxx
    * 填完后必须点击Add才是成功添加了
    * 可以从上面的列表中选择某个已经加上的Tunnel然后点Remove进行删除，这时putty会自动帮你把删除的这个Tunnel的信息填回下面的Source port和Destination文本框，方便你修改后再次点击Add进行添加。