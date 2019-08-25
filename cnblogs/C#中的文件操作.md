* Winform获取程序路径
    * AppDomain.CurrentDomain.BaseDirectory
* Directory文件夹操作类
    * Directory.CreateDirectory
    * Directory.Delete
* File文件操作类
    * File.Exists(string path)
    * File.WriteAllText(string path, string contents)
    * File.ReadAllText(string path)
    * File.Create
    * File.Delete
* FileStream操作类
    * FileStream fsr = new FileStream(%%1, FileMode.Open, FileAccess.Read);
    * FileStream.Name
    * FileStream.Length
    * FileStream.ReadByte()
    * FileStream.Seek
    * fsr.Read(btArr, 0, btArr.Length);
    * fsw.Write(btArr, btArr.Length/2, btArr.Length-btArr.Length/2);
    * fsr.Close();