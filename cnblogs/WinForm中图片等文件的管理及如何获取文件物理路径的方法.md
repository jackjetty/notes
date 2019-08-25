* AppDomain.CurrentDomain.BaseDirectory
    * 程序运行目录，如果是在VS中运行，那么就是对应的bin下面的Debug或者Release目录
* 注意在项目中增加图片等文件后，一定要Include到项目中，并且设置为Copy always或者Copy if newer
* 管理图片等资源文件也可以将他们加入Resource文件中，那么就可以在程序中直接通过名称来获取了