* Json包含Object和Array两种表示结构
* 现有库
    * 下列各库均可封装成工具类，统一进行类型检查、空检查、异常处理、类型转换、泛型处理等逻辑。
    * 不论哪种方式，要序列化成json的对象定义中不能存在循环引用，比如子类中又包含父类
    * NewtonSoft
        * 最好用，最常用
        * 最好通过NuGet来管理引用
        * 序列化的模式:OptOut和OptIn
            * OptOut：默认值,类中所有公有成员会被序列化,如果不想被序列化,可以用特性JsonIgnore
                * 序列化/反序列化使用特性时忽略某个属性：**[JsonIgnore]**
            * OptIn：默认情况下,所有的成员不会被序列化,类中的成员只有标有特性JsonProperty的才会被序列化,当类的成员很多,但客户端仅仅需要一部分数据时,很有用
        * JsonConvert.SerializeObject
        * JsonConvert.DeserializeObject<T>
    * DataContractJsonSerializer
        * 来自System.Runtime.Serialization.dll

```
class JsonDataContractJsonSerializer
    {
        /// <summary>
        /// 内存对象转换为json字符串
        /// </summary>
        /// <param name="obj"></param>
        /// <returns></returns>
        public static string ObjectToJson(object obj)
        {
           DataContractJsonSerializer serializer = new DataContractJsonSerializer(obj.GetType());
           MemoryStream stream = new MemoryStream();
           serializer.WriteObject(stream, obj);
           byte[] dataBytes = new byte[stream.Length];
           stream.Position = 0;
           stream.Read(dataBytes, 0, (int)stream.Length);
           return Encoding.UTF8.GetString(dataBytes);
        }
       /// <summary>
       /// Json字符串转内存对象
       /// </summary>
       /// <param name="jsonString"></param>
       /// <param name="obj"></param>
       /// <returns></returns>
        public static object JsonToObject(string jsonString, object obj)
        {
           DataContractJsonSerializer serializer = new DataContractJsonSerializer(obj.GetType());
           MemoryStream mStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonString));
           return serializer.ReadObject(mStream);
        }
    }
```

    * JavaScriptSerializer
        * 来自System.Web.Extensions.dll，因此最好用于Web项目，省得桌面应用也引用Web包（当然如果已经引用了，那就无所谓了）

```
public static class JsonJavaScriptSerializer
    {
        /// <summary>
        /// 内存对象转换为json字符串
        /// </summary>
        /// <param name="obj"></param>
        /// <returns></returns>
        public static string ToJSON(object obj)
        {
            StringBuilder sb = new StringBuilder();
            JavaScriptSerializer json = new JavaScriptSerializer();
            json.Serialize(obj, sb);
            return sb.ToString();
        }
        /// <summary>
        /// Json字符串转内存对象
        /// </summary>
        /// <param name="jsonString"></param>
        /// <param name="obj"></param>
        /// <returns></returns>
        public static T FromJSON<T>(string jsonString)
        {
            JavaScriptSerializer json = new JavaScriptSerializer();
            return json.Deserialize<T>(jsonString);
        }

    }
```