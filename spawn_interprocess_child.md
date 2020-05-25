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

* In this example we will spawn a python child process and get bulk data through its output stream till it exits. This scenario is applicable for one time child process spawning and getting bulk data from it.
```cs
using System;
using System.Collections.Generic;
using System.Diagnostics;

namespace inter_process_bulk_data_fetch
{
    class Program
    {
        static void Main(string[] args)
        {
            /*
            http://www.blackwasp.co.uk/CaptureProcessOutput.aspx
            In this example we will spawn a python child process 
            and get data through its output stream
            */
            string res = "";
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
                // start the child process
                process.Start();
                // read the whole child process output till end
                res = process.StandardOutput.ReadToEnd();
                // wait till child process is exited
                process.WaitForExit();
            }
            catch (Exception ex)
            {
                res = "";
                ShowOutput(ex.Message, ConsoleColor.Red);
            }
            ShowOutput(res, ConsoleColor.Green);
        }

        static void ShowOutput(string data, ConsoleColor color)
        {
            if (data != null)
            {
                ConsoleColor oldColor = Console.ForegroundColor;
                Console.ForegroundColor = color;
                Console.WriteLine("{0}: {1}", DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss"), data);
                Console.ForegroundColor = oldColor;
            }
        }
    }
}
```

* The python child process used in the above examples is shown below
```python
import random
import time


def main():
    for pIter in range(1, 11):
        time.sleep(0.5)
        print('The output for iteration {0} is {1}'.format(
            pIter, random.randrange(10, 100)), flush=True)


# start process
if __name__ == '__main__':
    main()
```
