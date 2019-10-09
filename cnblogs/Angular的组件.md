* 可以使用ng generate component xxx来创建组件相关文件html文件、样式表、ts文件、spec.ts单元测试文件
* 组件的命名默认要符合XxxComponent，除非关掉tslint中的component-class-suffix设置
* selector的命名默认要符合app-xxx，除非在tslint中修改component-selector的规则内容

```
    "component-selector": [
      true,
      "element",
      "makealife",
      "kebab-case"
    ]
```