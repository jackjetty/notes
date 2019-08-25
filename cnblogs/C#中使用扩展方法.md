* 扩展方法使你能够向现有类型“添加”方法，而无需创建新的派生类型、重新编译或以其他方式修改原始类型。
* 扩展方法是一种特殊的静态方法，但可以像扩展类型上的实例方法一样进行调用
* 扩展方法被定义为静态方法，但它们是通过实例方法语法进行调用的。它们的第一个参数指定该方法作用于哪个类型，并且该参数以 this 修饰符为前缀。仅当你使用 using 指令将命名空间显式导入到源代码中之后，扩展方法才位于范围中。
    * 必须使用using指令将命名空间显式导入到源代码中，否则visual studio中可以定位到该扩展方法的定义，但还是编译有错
* 优先级
    * 可以使用扩展方法来扩展类或接口，但不能重写扩展方法。与接口或类方法具有相同名称和签名的扩展方法永远不会被调用。
    * 编译时，扩展方法的优先级总是比类型本身中定义的实例方法低。换句话说，如果某个类型具有一个名为 Process(int i) 的方法，
    * 而你有一个具有相同签名的扩展方法，则编译器总是绑定到该实例方法。当编译器遇到方法调用时，它首先在该类型的实例方法中寻找匹配的方法。
    * 如果未找到任何匹配方法，编译器将搜索为该类型定义的任何扩展方法，并且绑定到它找到的第一个扩展方法。

```
namespace ExtensionMethods
{
    public static class MyExtensions
    {
        public static int WordCount(this String str)
        {
            return str.Split(new char[] { ' ', '.', '?' }, 
                             StringSplitOptions.RemoveEmptyEntries).Length;
        }
    }   
}


using ExtensionMethods;

string s = "Hello Extension Methods";
int i = s.WordCount();
```