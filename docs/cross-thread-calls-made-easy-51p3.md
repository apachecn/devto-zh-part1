# 跨线程调用变得简单

> 原文：<https://dev.to/adamkdean/cross-thread-calls-made-easy-51p3>

**更新(27/06/2013):** 关于参数化调用的新博文- [跨线程调用变得更容易](https://dev.to/adamkdean/cross-thread-calls-made-easier-59m1)。

我们都遇到过这个问题，我们试图做一些事情，但是表单被锁住了，所以我们决定使用另一个线程。太好了，意大利面代码在等着呢。我们喝了一杯咖啡，然后开始着手，最终想出了一个很棒的多线程解决方案，在我们处理我们正在处理的东西时，它能保持我们的表单重绘...除了当我们试图更新表单，并得到**可怕的线程间操作无效:控制从一个线程而不是创建它的线程访问。**

这似乎是最快的解决方法，但是请不要在任何情况下使用这种方法来制造灾难。

```
Form1.CheckForIllegalCrossThreadCalls = false; // this is bad! 
```

Enter fullscreen mode Exit fullscreen mode

情况并不像看起来那么糟糕，继续读下去，你会看到如何快速解决这个问题。这很快就会成为你的第二天性。第一个代码段适用于必须向控件传递参数的情况，例如，您希望向文本框中写入文本:

```
using System;
using System.Windows.Forms;
using System.Threading;

namespace CrossThreadCalls
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private delegate void ObjectDelegate(object obj);

        private void button1_Click(object sender, EventArgs e)
        {
            // first thing we do is create a delegate pointing to update method
            ObjectDelegate del = new ObjectDelegate(UpdateTextBox);

            // then simply enough, we invoke it
            del.Invoke("Hello from button1_Click!");

            // if we wanted to create a thread to use it, we'd use a
            // params threadstart and pass the delegate as an object
            Thread th = new Thread(new ParameterizedThreadStart(WorkThread));
            th.Start(del);
        }

        private void WorkThread(object obj)
        {
            // we would then unbox the obj into the delegate
            ObjectDelegate del = (ObjectDelegate)obj;

            // and invoke it like before
            del.Invoke("Hello from WorkThread!");
        }

        private void UpdateTextBox(object obj)
        {
            // do we need to switch threads?
            if (InvokeRequired)
            {
                // we then create the delegate again
                // if you've made it global then you won't need to do this
                ObjectDelegate method = new ObjectDelegate(UpdateTextBox);

                // we then simply invoke it and return
                Invoke(method, obj);
                return;
            }

            // ok so now we're here, this means we're able to update the control
            // so we unbox the object into a string
            string text = (string)obj;

            // and update
            textBox1.Text += text + "\r\n";
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在假设你只是想运行一个不带任何参数的方法，事情就变得简单多了。和前面一样，我们调用方法，但是不需要委托:

```
using System;
using System.Windows.Forms;
using System.Threading;

namespace TestApp2
{
    public partial class Form2 : Form
    {
        public Form2()
        {
            InitializeComponent();
        }  

        private void button1_Click(object sender, EventArgs e)
        {
            // no delegates are needed here
            UpdateTextBox();

            // just to show what it'd be like from another thread
            // we create a standard thread without params
            Thread th = new Thread(new ThreadStart(WorkThread));
            th.Start();
        }

        private void WorkThread()
        {
            // we have no params, so just call the method
            UpdateTextBox();
        }

        private void UpdateTextBox()
        {
            // do we need to switch threads?
            if (InvokeRequired)
            {
                // slightly different now, as we dont need params
                // we can just use MethodInvoker
                MethodInvoker method = new MethodInvoker(UpdateTextBox);
                Invoke(method);
                return;
            }

            // as we didn't take a param, we just update
            textBox1.Text += "Hello world!\r\n";
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这其实很简单。如果你对我在这里使用的一些东西有点困惑，发表评论，或者阅读更多关于[装箱和拆箱](http://www.google.co.uk/#hl=en&q=csharp+boxing+unboxing)和[线程](http://www.google.co.uk/#hl=en&q=csharp+threading)的内容。

如果你有困难，请到 [Dream 来问我们。输入代码](http://www.dreamincode.net/forums/forum/84-c%23/)。