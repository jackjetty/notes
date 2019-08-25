* proxy等set、环境变量信息共享操作系统的CMD的配置
* 注意异常处理、进程的回收
* 下面实现的缺点是，返回的字符串中有冗余数据，如打开CMD时的运行环境信息，因此可能要自己再去过滤出来哪些才是命令返回的数据
* 不管命令是否成功均执行exit命令，否则当调用ReadToEnd()方法时，会处于假死状态

```
    /// <summary>
    /// 
    /// </summary>
    public class Cmd
    {
        /// <summary>
        /// 
        /// </summary>
        /// <param name="cmd"></param>
        public static string RunCmd(string cmd)
        {
            using (Process proc = new Process())
            {
                proc.StartInfo.CreateNoWindow = true;
                proc.StartInfo.FileName = "cmd.exe";
                proc.StartInfo.UseShellExecute = false;
                proc.StartInfo.RedirectStandardError = true;
                proc.StartInfo.RedirectStandardInput = true;
                proc.StartInfo.RedirectStandardOutput = true;
                proc.Start();
                proc.StandardInput.WriteLine(cmd);
                proc.StandardInput.WriteLine("exit");
                string outStr = proc.StandardOutput.ReadToEnd();
                proc.Close();
                return outStr;
            }
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="programName"></param>
        /// <param name="cmd"></param>
        public static void RunProgram(string programName, string cmd)
        {
            using (Process proc = new Process())
            {
                proc.StartInfo.CreateNoWindow = true;
                proc.StartInfo.FileName = programName;
                proc.StartInfo.UseShellExecute = false;
                proc.StartInfo.RedirectStandardError = true;
                proc.StartInfo.RedirectStandardInput = true;
                proc.StartInfo.RedirectStandardOutput = true;
                proc.Start();
                if (cmd.Length != 0)
                {
                    proc.StandardInput.WriteLine(cmd);
                }
                proc.Close();
            }
        }

        /// <summary>
        /// 
        /// </summary>
        /// <param name="programName"></param>
        public static void RunProgram(string programName)
        {
            RunProgram(programName, "");
        }
    }
```