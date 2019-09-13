* div上控制显示滚动条要结合overflow属性和布局、高/宽度
    * div的overflow、overflow-x、overflow-y样式
        * inherit：规定应该从父元素继承 overflow 属性的值。
        * hidden：永远不显示滚动条。内容会被修剪，并且其余内容是不可见的。
        * scroll：永远显示滚动条（至少有占位框）。内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。
        * auto：根据内容高度自动控制滚动条是否显示。如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容，并有可能导致布局发生改变。
        * visible（默认，不设置时）：内容不会被修剪，会呈现在元素框之外（子元素自身宽或高大于元素本身时）。
    * 想要显示滚动条
        * 通常要给div一个固定的height，然后结合overflow样式，才会显示出来滚动条。
            * 即使用了angular的ngx-infinite-scroll也是如此
        * 要不就是把div设置成position: absolute; top: 0px; bottom: 0px;
            * 可能上层div的position要设成relative等
            * 其实这种做法就是让这个div充满上层div，即height为真的生效的100%，而不是通常情况下的父div的高度由子div决定。
            * 这种也算是给了一个固定的height，再结合overflow就可以显示出来滚动条了
            * 然后如果父div中在本div前还有其他div如header，那么可以通过设置本div的margin-top样式来避免重合，这其实也算是有写死的height，只不过不是本div，而是前面的div。这通常适用于前面的header高度固定（即使随着浏览器宽度而不固定，也可以通过@Media来分阶段写死），而希望本div高度不固定，希望尽量大的情况。
            * 但是这种情况有可能由于而position: absolute;带来本div宽度不对的问题，或者滚动条只露出来一个头。这时候可以还是通过设置本div的height的方式来实现，大不了也用@Media在不同的情况下设置不同的height。
    * 正常情况下，如果用了flex布局（Flexible Box，弹性盒子），就变成了一个弹性盒子，不需要手动设置滚动条相关属性，div内部内容多也没关系
        * **flex**
            * 前端布局神器，用来为盒装模型提供最大的灵活性。
            * 任何一个容器都可以指定为Flex布局。
            * flex容器内所有子元素自动成为容器成员，成为Flex项目(Flex item)，简称“项目”。这些项目会延flex-direction属性设置的主轴方向排列（即项目的排列方向）
            * **bootstrap提供的row等class其实就是给容器设置了flex属性**
        * 但是这个时候如果两个相邻的div，前者有margin属性，那么就会影响下面的div，导致下面的div出现滚动条
* web页面中的height设置比较特殊
    * 不像width，可以简单的通过百分比或者像素数来控制
    * 父元素的height由子元素的height决定
* 获取、操作滚动条位置
    * xxx.scrollTop = 200;