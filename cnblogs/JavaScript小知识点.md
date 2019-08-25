* 使用DOM元素前要先判断是否存在
    * 如if (document.getElementById('xxx'))
* 原生js操作dom添加删除替换class
    * html5增加了classList
        * classList 属性返回元素的类名，作为 DOMTokenList 对象。该属性用于在元素中添加，移除及切换 CSS 类。
        * classList 属性是只读的，但你可以使用 add() 和 remove() 方法修改它。
        * 增加：document.getElementById("myDIV").classList.add("mystyle", "anotherClass", "thirdClass");
        * 去除：document.getElementById("myDIV").classList.remove("mystyle");
        * 替换：document.getElementById("myDIV").classList.replace("someClassName","otherClassName");
    * 比较传统的方法

```
var classVal = document.getElementById("id").getAttribute("class");

//删除的话
classVal = classVal.replace("someClassName","");
document.getElementById("id").setAttribute("class",classVal );

//添加的话
classVal = classVal.concat(" someClassName");
document.getElementById("id").setAttribute("class",classVal );

//替换的话
classVal = classVal.replace("someClassName","otherClassName");
document.getElementById("id").setAttribute("class",classVal );
```