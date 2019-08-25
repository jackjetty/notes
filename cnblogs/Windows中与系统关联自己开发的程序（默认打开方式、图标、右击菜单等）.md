#1. 默认打开方式
##1.1. 代码支持
* 在Windows下，某个特定后缀名类型的文件，如果要双击时默认用某个程序（比如自己开发的WinForm程序）打开，代码中首先肯定要支持直接根据这个文件进行下一步操作。比如说，如果自己开发的程序是个IDE，那么如果直接打开的这个文件是自定义的项目文件，那么代码就要支持根据这个文件直接启动程序并打开工程；如果这只是个普通文件，那么代码就要支持根据这个文件直接启动程序并打开编辑界面。
* 以WinForm为例，Program.cs启动的Form的构造函数就要支持传递参数并进行处理：
```
    public partial class TestProjectTree : Form
    {
        public TestProjectTree(string[] para)
        {
            InitializeComponent();

            if (para.Length > 0)
            {
                string pathC = para[0];//获取打开的文件的路径
                                       //下面就可以自己编写代码使用这个pathC参数了
                                       //FileStream fs = new FileStream(pathC, FileMode.Open, FileAccess.Read);
                label1.Text = pathC;
            }
        }
    }
```
##1.2. 修改注册表（代码或安装时，有可能不需要搞一个引用那么麻烦）
* 如果要设置默认打开方式的文件后缀名为.xxx，那么在注册表的HKEY_CLASSES_ROOT下面新建一个项就叫.xxx，然后把它下面的默认的项的值设为"Exec"（可能可以自己定，只要与下一步要建的第一级的项的名称相同即可）
* 然后再在HKEY_CLASSES_ROOT下面新建一个项叫“Exec”（可能可以自己定，只要与上一步建的项的名称相同即可），然后在Exec下面再建一个"shell"，然后在shell再建一个"open"，然后在open下面再建一个"command"，最后把command下面的默认的项的值设为"D:\vsworkspace\TestWinform\TestWinform\bin\Debug\TestWinform.exe" "%1" （其中前一个双引号中是程序路径，后一个双引号中代表双击的文件的全路径名）
* 如果手动设置过该后缀名的文件的默认打开方式，那么Windows会自动以类似的方式生成固定名称的注册表项（xxx_auto_file），因此安装程序需要以同样的方式覆盖掉之前用户可能的设置，或者在安装时先删掉系统默认的打开方式注册表项，然后再添加自己定义的。
* 貌似如果用户手动设置过的话，还会有[其他残留项](https://blog.csdn.net/lfw19891101/article/details/6103032)，比如多个FileExts、RecentDocs等。
* /HKEY_CLASSES_ROOT/Applications/xxx.exe会显示在打开方式软件列表中。

#2. 在右击菜单中增加自己的程序（空白处的右击菜单）
* [参考百度](https://jingyan.baidu.com/article/ce09321b9713202bff858fec.html)-->>增加注册表：HKEY_CLASSES_ROOT->Directory->Background->shell->XXX->command，最后一级command的默认项的值为D:\vsworkspace\TestWinform\TestWinform\bin\Debug\TestWinform.exe，即程序路径，上一级的XXX为在右击菜单上显示的名称。

#3. 在右击菜单中增加自己的程序（非空白）
* .xxx->shell->XXX->command下配置即可

#4. 在右击菜单中增加自己的程序（选中多个文件时）