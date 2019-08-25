* ln -s sourcefileaame linkfilename
* ls -al或者ls -al linkfilename可以查看软链接文件及其真实映射的源文件
* 软链接是linux提供的功能，如果把链接文件拷出来，那么通过vitualbox、winscp等拷贝，虽然拷出来的是软链接名，但内容都会把源文件的内容带过来，可能是linux系统或者这几个工具帮忙做了事情。或者说Linux上的软链接文件本来就不是一个特殊的文件，并不是存了特殊的信息？可以尝试通过其他文件查看或复制？