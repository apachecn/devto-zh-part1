# 将 C#编译成程序集，然后加载并执行它

> 原文：<https://dev.to/adamkdean/compile-c-into-assembly-then-load-it-and-execute-it-2a87>

这是我发现的一种方法，我可以在运行时将代码编译成 DLL 文件，然后加载并调用它。

我要编译的代码是这样的:

```
class FruitVendor
{
    private string fruits = "";

    public FruitVendor()
    {
        fruits = "apples & bananas";
    }

    public string GetFruit()
    {
        return fruits;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我有两个文本框，一个用于输入，一个用于输出。为了编译和运行它，我这样做:

```
using System;
using System.Drawing;
using System.Windows.Forms;
using System.Reflection;
using System.CodeDom.Compiler;

namespace ProjectName
{
    public partial class frmMain : Form
    {
        public frmMain()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            CodeDomProvider codeProvider = CodeDomProvider.CreateProvider("CSharp");
            string outputFilename = "fruits.dll";
            Button ButtonObject = (Button)sender;

            System.CodeDom.Compiler.CompilerParameters parameters = new CompilerParameters();
            parameters.GenerateExecutable = false;
            parameters.OutputAssembly = outputFilename;
            CompilerResults results = codeProvider.CompileAssemblyFromSource(parameters, textBox1.Text);

            if (results.Errors.Count > 0)
            {
                textBox2.Text = "";
                textBox2.ForeColor = Color.Red;
                foreach (CompilerError CompErr in results.Errors)
                {
                    textBox2.Text = textBox2.Text +
                        "Line number " + CompErr.Line +
                        ", Error Number: " + CompErr.ErrorNumber +
                        ", '" + CompErr.ErrorText + ";" +
                        Environment.NewLine + Environment.NewLine;
                }
            }
            else
            {
                textBox2.ForeColor = Color.Black;

                string path = System.Environment.CurrentDirectory + "\\" + outputFilename;
                Assembly assembly = Assembly.LoadFrom(path);
                Type type = assembly.GetType("FruitVendor");
                MethodInfo mi = type.GetMethod("GetFruit");
                object result = mi.Invoke(Activator.CreateInstance(type), null);

                textBox2.Text = (string)result; ;
            }
        }

    }
} 
```

Enter fullscreen mode Exit fullscreen mode

它很有效，也很快。感谢在 [StackOverflow](http://www.stackoverflow.com/) 的所有人，这个[线程帮了大忙](http://stackoverflow.com/questions/8788338/is-there-a-way-i-can-parse-run-c-sharp-within-an-application-almost-like-a-scri)！