* 默认开关可能是关闭的，需要在Project的Settings中打开
* 支持Markdown、RDoc、AsciiDoc格式， 默认为Markdown
* 通过默认导航最上面的“Clone repository”按钮，可以使用git来管理wiki文件，一次提交多个修改或进行版本控制，避免太多太乱的commit
    * 图片或其他文件也可以传到repo中，然后md文档中用图片或者文件链接（可能不能在线查看，只能下载）来引用
* 导航
    * 自定义导航
        * 根目录放一个_sidebar，怎么动态读page？
    * 默认导航
        * 排序
            * 无法自定义顺序，大写优先。因此**建议Home页面、每个目录下的Introduction页面首字母大写，其他的md页面都用小写加下划线来命名。如果是模拟多级目录，只有Introduction首字母大写，如monitor_Introduction.md。**
        * 多级
            * 貌似只支持两级，再多的话会显示成xxx/xxx
            * workaround：只创建一级子目录，里面再有层次的话，用_在文件名中区分。从第二级目录开始，用下划线来区分每级目录，如devops是第一级目录，monitor是第二级目录，那么monitor下面的文件可以命名为monitor_Introduction.md，如果又有一级xxx，可以命名为monitor_xxx_Introduction.md。