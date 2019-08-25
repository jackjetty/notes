* **[crontab是不认识profile的 ](http://www.cnblogs.com/SuperXJ/archive/2012/03/16/2399856.html)**
    * 比如用root执行脚本，root是没有~/.bashrc的？**/etc/profile和~/.bashrc的作用类似，只是作用域不同，都是写死的export，也有动态的脚本去设置命令和环境变量**
    * 脚本中加入. /etc/profile和. ~/.bash_profile（可以不加？）
* [Linux 设置定时任务crontab命令](https://www.cnblogs.com/zoulongbin/p/6187238.html)
* 通过crontab -e修改，修改后立即生效
    * 其实只是修改了当前用户的设置，系统会自动就存放于/var/spool/cron/目录中,文件以用户名命名.linux的cron服务是每隔一分钟去读取一次/var/spool/cron,/etc/crontab,/etc/cron.d下面所有的内容.
* 语法
    * 星号（\*）：代表每的意思，例如month字段如果是星号，则表示每月都执行该命令操作。
    * 正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如\*/10，如果用在minute字段，表示每十分钟执行一次。
    * \*1 \* \* \* \* /usr/local/nagios/myplugin/check_state.sh
* **注意：crontab在运行sh脚本时，并不会加载用户的bash shell和环境变量**
    * [使用crontab应该注意的地方](https://blog.csdn.net/xianzixiang/article/details/52439960)
    * 由于crontab只加载/ect/environment，并不加载/etc/profile和~/.bash_profile，crontab只在  /sbin:/bin:/usr/sbin:/usr/bin 这几个目录下找，**可以在sh脚本中增加命令来查看错误：$? &> /mmm/nagiostemp/1.txt**，**添加source ~/.bashrc 解决**
    * 在运行crontab的时候，是non_login方式调用程序的，此时~/.bash_profile并不会被提前调用。所以，crontab的运行环境相对于login方式进入bash运行的环境来说小得多。如果程序涉及~/.bash_profile使用的环境变量，那么，部分在login方式可以正常运行的程序在crontab下就无法运行，需要设置环境变量