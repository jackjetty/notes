* 一般一个被测试类对应一个测试类，test包下面测试类结构和main包下面的被测试类的结构类似
* 使用@Mock来标识要返回假数据的类
    * 指定返回什么假数据：when(mockObject.getApi()).thenReturn(someData);
* 使用@InjectMocks来标识和注入要被测试的类