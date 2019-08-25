* 变量
    * 定义变量不需要指定变量类型
* 数组
    * 定义：xxx = ['x','y']
* 字符串
    * 定义：name = 'cnblogs'
    * 获取字符串长度：len(str)
    * 字符串特定位置的字符：str[i]
    * 字符串截取：str[i:j]
    * 格式化：'{1},{0},{1}'.format('kzc',18) ，'{},{}'.format('kzc',18)
* 语句
    * 不用大括号来控制函数等范围，通过缩进来判断
    * 语句结尾不需要分号
* 判断
    * if post_content[0:2] == '\r\n':
* 循环
    * for x in xs
* 函数
    * 用def定义函数。如def parse_car_config_page(self, response):
    * 第一个参数self通常就是代表自己？
    * 定义要在调用前
* 类
    * 用class关键字来定义类，同时定义了构造函数。如class ConfigPageItem(scrapy.Item):
    * 用构造函数创建类。如configPageItem = ConfigPageItem()
* 注释
    * 函数注释：函数第一行通常为```xxx```形式的注释，描述函数功能，貌似可以被工具提取出来？
    * 单行注释：#
* 异常处理
    * try:......except(IndexError, TypeError):......
* 包与导入
    * 文件即包名，不需要额外指明报名？？
    * import scrapy
    * from autohome.items import ConfigPageItem
* 文件操作（使用codecs）
    * 写入文件
        * import codecs
        * post_file = codecs.open('posts' + os.path.sep + item['title'] + '.md', 'wb', encoding='utf-8')
        * post_file.write(item['content'])
        * post_file.close()
    * 删除文件
        * 只能删一个文件：os.remove(c_path)
    * 列出子目录
        * os.listdir(path)
    * 判断是不是目录
        * os.path.isdir(c_path)
    * 替换文件名中的非法字符
        * import re
        * rstr = r"[\/\\\:\*\?\"\<\>\|]"
        * re.sub(rstr, "_", item['title'])
    * 递归删除目录下所有文件

```
def del_folder(path):
    ls = os.listdir(path)
    for i in ls:
        c_path = os.path.join(path, i)
        if os.path.isdir(c_path):
            path.del_folder(c_path)
        else:
            os.remove(c_path)


del_folder('posts')
```

* xpath
    * response**.xpath('//tr[contains(@id,"post-row-")]')**
    * response.xpath('//input[@id="Editor_Edit_txbTitle"]/@value')**.extract_first()**
    * response.xpath('//textarea[@id="Editor_Edit_EditorBody"]')**.xpath('text()')**.extract_first()
* 其他
    * pass是空语句，仅做占位用，没有实际意义
    * continue
    * yield