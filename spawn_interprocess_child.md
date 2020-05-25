* In this example we will spawn a python child process and get real-time updates through its output stream

```cs
using System;
using System.Collections.Generic;
using System.Diagnostics;

namespace inter_process_comm
{
    class Program
    {
        static void Main(string[] args)
        {
            /*
            http://www.blackwasp.co.uk/CaptureProcessOutput.aspx
            In this example we will spawn a python child process 
            and get real-time updates through its output stream
            */
            try
            {
                // spawn process to get python data
                var process = new Process()
                {
                    StartInfo = new ProcessStartInfo
                    {
                        FileName = "python",
                        RedirectStandardOutput = true,
                        UseShellExecute = false,
                        CreateNoWindow = true,
                        Arguments = String.Join(" ", new List<string>() { "child.py" })
                    }
                };
                // listen to the output stream of child process
                process.OutputDataReceived += CaptureOutput;
                // start the child process
                process.Start();
                // begin to read the output stream of child
                process.BeginOutputReadLine();
                // wait till child process is exited
                process.WaitForExit();

            }
            catch (Exception ex)
            {
                ShowOutput(ex.Message, ConsoleColor.Red);
            }

        }

        static void CaptureOutput(object sender, DataReceivedEventArgs e)
        {
            // this the listener for the output stream of child process
            ShowOutput(e.Data, ConsoleColor.Green);
        }

        static void ShowOutput(string data, ConsoleColor color)
        {
            if (data != null)
            {
                ConsoleColor oldColor = Console.ForegroundColor;
                Console.ForegroundColor = color;
                Console.WriteLine("{0}: {1}", DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss"),data);
                Console.ForegroundColor = oldColor;
            }
        }
    }
}
```
