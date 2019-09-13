* 切换DOM元素选中状态（即改变焦点）
    * 貌似JavaScript也有这些方法。。。
    * $(selector).focus()
    * $(selector).blur()
        * 有时候点击按钮之后，按钮会一致保持有border，就是因为其一直保持在了选中状态，可以使用blur()方式让它失去焦点。