* 微软出品，最终编译成JavaScript
* [TypeScript in 5 minutes](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)
* 安装
    * 单独安装：npm install -g typescript
    * 在Angular的package.json中定义，如在devDependencies中（以为运行时已经编译成了JavaScript，不再需要引用？）定义"typescript": "~2.5.3"
* 编译
    * 不一定用于Angular，可以单独编写ts文件，编译成js文件，然后直接使用这个js文件
    * 用tsc编译命令：tsc xxx.ts
    * 即使编译有错误，也能生成js文件，只不过有warning说可能代码行为可能会和预期不符
* 语法
    * 会做静态语法检查，可以检查出大部分错误，如参数数量、类型检查
    * 变量
        * 可以和JavaScript一样用let定义变量
        * 定义数组：let user = [0, 1, 2];
    * 函数
        * 参数中需要指定类型，如function greeter(person: string)
    * 接口
        * TypeScript中只要两个类型只要内部结构兼容，那么这两个类型就是兼容的。因此我们可以不用显式的implements语句就可以实现一个接口。如下面例子中的user1直接就可以当做一个Person类型。
    * 类
        * TypeScript支持JavaScript的新feature，如基于类的面向对象编程
        * 类中并不一定要一个个单独定义成员变量，只要在构造函数的参数列表中使用public关键字修饰参数，那么这些参数就会自动被当做同名的成员变量。如下面例子中的constructor(public firstName: string, public middleInitial: string, public lastName: string)。

```
class Student {
    fullName: string;
    constructor(public firstName: string, public middleInitial: string, public lastName: string) {
        this.fullName = firstName + " " + middleInitial + " " + lastName;
    }
}

interface Person {
    firstName: string;
    lastName: string;
}

function greeter(person: Person) {
    return "Hello, " + person.firstName + " " + person.lastName;
}

let user1 = { firstName: "Jane", lastName: "User" };
let user2 = new Student("Jane", "M.", "User");

document.body.textContent = greeter(user1);
document.body.textContent = greeter(user2);
```