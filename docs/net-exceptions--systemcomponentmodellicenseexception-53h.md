# 。净异常–系统。组件模型.许可证异常

> 原文：<https://dev.to/airbrake/net-exceptions--systemcomponentmodellicenseexception-53h>

蜿蜒深入我们的 [**。NET 异常处理**](https://airbrake.io/blog/dotnet-exception-handling/exception-class-hierarchy) 系列，今天我们来看看**系统。component model . license exception**。当创建专有应用程序和。NET 组件想要获得许可，您可能希望使用内置的 [`LicenseProvider`](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.licenseprovider?view=netframework-4.7.1) 类实现某种形式的许可，这使得向您的应用程序添加许可内容变得相对容易。如果执行应用程序时没有检测到有效的许可，就会抛出一个`LicenseException`,提示用户或开发人员必须满足许可。

在整篇文章中，我们将通过查看它在浩瀚宇宙中的位置来探索`LicenseException`。NET 异常层次结构。我们还将查看一些功能性的 C#代码示例，这些示例展示了在您自己的应用程序中实现许可的一些不同技术，以及不这样做如何会(并且应该)抛出`LicenseExceptions`，所以让我们开始吧！

## 技术破落

全部。NET 异常是 [`System.Exception`](https://airbrake.io/blog/dotnet-exception-handling/exception-class-hierarchy) 基类的派生类，或者从其中的另一个继承类派生。此错误的完整异常层次结构如下:

*   [T2`System.Object`](https://docs.microsoft.com/en-us/dotnet/api/system.object)
    *   [T2`System.Exception`](https://docs.microsoft.com/en-us/dotnet/api/system.exception)
    *   [T2`System.SystemException`](https://docs.microsoft.com/en-us/dotnet/api/system.systemexception)
        *   `LicenseException`

## 完整代码示例

下面是我们将在本文中使用的完整代码示例。如果您想亲自试验代码，看看一切是如何工作的，可以复制并粘贴它。

```
using System;
using System.Windows.Forms;
using Microsoft.Win32;
using Utility;

namespace Airbrake.ComponentModel.LicenseException
{
    internal static class Program
    {
        /// <summary>
        /// The main entry point for the application.
        /// </summary>
        [STAThread]
        private static void Main()
        {
            Application.EnableVisualStyles();
            Application.SetCompatibleTextRenderingDefault(false);
            var form = new Form1();

            Logging.LineSeparator("Creating LicensedComboBox", 60);
            new LicensedComboBox();

            // Delete registry license entry.
            DeleteLicenseRegistryEntry(typeof(RegistryLicensedComboBox));

            Logging.LineSeparator("Creating RegistryLicensedComboBox", 60);
            new RegistryLicensedComboBox();

            // Add registry license entry.
            AddLicenseRegistryEntry(typeof(RegistryLicensedComboBox));

            Logging.LineSeparator("Creating RegistryLicensedComboBox w/ Registry Entry", 60);
            new RegistryLicensedComboBox();

            Application.Run(form);
        }

        /// <summary>
        /// Adds license registry entry for passed control type.
        /// </summary>
        /// <param name="type">Control type to create license registry of.</param>
        private static void AddLicenseRegistryEntry(Type type)
        {
            var license = new CustomLicense(type);

            // Check if key exists.
            var registryKey = Registry.CurrentUser.OpenSubKey(license.RegistryKey);
            if (registryKey != null) return;

            // Set key value to license key.
            var newKey = Registry.CurrentUser.CreateSubKey(license.RegistryKey);
            newKey?.SetValue(type.Name, license.LicenseKey);
            Logging.Log($"Added registry key value: {Registry.CurrentUser}\\{license.RegistryKey}\\{type.Name} = {license.LicenseKey}.");
        }

        /// <summary>
        /// Deletes license registry entry for passed control type.
        /// </summary>
        /// <param name="type">Control type to delete license registry of.</param>
        private static void DeleteLicenseRegistryEntry(Type type)
        {
            var license = new CustomLicense(type);

            // Check if key exists.
            var registryKey = Registry.CurrentUser.OpenSubKey(license.RegistryKey);
            if (registryKey == null) return;

            // Delete key.
            Registry.CurrentUser.DeleteSubKey(license.RegistryKey);
            Logging.Log($"Deleted registry key: {Registry.CurrentUser}\\{license.RegistryKey}.");
        }
    }
}

using System;
using System.ComponentModel;

namespace Airbrake.ComponentModel.LicenseException
{
    /// <summary>
    /// CustomLicense used for RegostryLicenseProvider example.
    /// </summary>
    public class CustomLicense : License
    {
        private readonly Type _type;

        public CustomLicense(Type type)
        {
            // Type is required and must not be null.
            _type = type ?? throw new NullReferenceException("The licensed type reference may not be null.");
        }

        public override void Dispose() { }

        /// <summary>
        /// Returns an explicit license key string for demonstration.
        /// </summary>
        public override string LicenseKey => "12345";

        /// <summary>
        /// The registry key where the license can be found.
        /// </summary>
        public string RegistryKey => "Software\\Airbrake\\Licenses";
    }
}

using System;
using System.ComponentModel;
using System.Windows.Forms;
using Utility;

namespace Airbrake.ComponentModel.LicenseException
{
    [LicenseProvider(typeof(LicFileLicenseProvider))]
    public class LicensedComboBox : ComboBox
    {
        private License _license;

        public LicensedComboBox()
        {
            try
            {
                // Validate the license of this instance.
                _license = LicenseManager.Validate(typeof(LicensedComboBox), this);
                Logging.LineSeparator("VALID LicensedComboBox LICENSE", 60);
                Logging.Log(_license);
            }
            catch (System.ComponentModel.LicenseException exception)
            {
                // Output expected LicenseExceptions.
                Logging.Log(exception);
            }
            catch (Exception exception)
            {
                // Output unexpected Exceptions.
                Logging.Log(exception, false);
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (!disposing) return;
            if (_license == null) return;

            _license.Dispose();
            _license = null;
        }
    }
}

using System;
using System.ComponentModel;
using System.Windows.Forms;
using Utility;

namespace Airbrake.ComponentModel.LicenseException
{
    [LicenseProvider(typeof(RegistryLicenseProvider))]
    public class RegistryLicensedComboBox : ComboBox
    {
        private License _license;

        public RegistryLicensedComboBox()
        {
            try
            {
                // Validate the license of this instance.
                _license = LicenseManager.Validate(typeof(RegistryLicensedComboBox), this);
                Logging.LineSeparator("VALID RegistryLicensedComboBox LICENSE", 60);
                Logging.Log(_license);
            }
            catch (System.ComponentModel.LicenseException exception)
            {
                // Output expected LicenseExceptions.
                Logging.Log(exception);
            }
            catch (Exception exception)
            {
                // Output unexpected Exceptions.
                Logging.Log(exception, false);
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (!disposing) return;
            if (_license == null) return;

            _license.Dispose();
            _license = null;
        }
    }
}

using System;
using System.ComponentModel;
using Microsoft.Win32;

namespace Airbrake.ComponentModel.LicenseException
{
    public class RegistryLicenseProvider : LicenseProvider
    {
        /// <summary>
        /// Attempts to retrieve a valid license for the passed object instance.
        /// </summary>
        /// <param name="context">Context of this license indicating if it should be checked in runtime or compile time.</param>
        /// <param name="type">Object type to retrieve license for.</param>
        /// <param name="instance">Instance to check license of.</param>
        /// <param name="allowExceptions">Determines if exceptions should be allowed. If true, a LicenseException
        /// is thrown if no valid license can be obtained.</param>
        /// <returns>Valid CustomLicense instance.</returns>
        public override License GetLicense(LicenseContext context, Type type, object instance, bool allowExceptions)
        {
            var license = new CustomLicense(type);

            // If we're not in runtime mode license should be automatically approved.
            if (context.UsageMode != LicenseUsageMode.Runtime) return license;

            // Get the intended registry key.
            var registryKey = Registry.CurrentUser.OpenSubKey(license.RegistryKey);

            // Get the registry key value.
            var registryKeyValue = (string) registryKey?.GetValue(type.Name);

            if (registryKeyValue != null)
            {
                // Confirm that the keyValue from registry is equivalent to expected LicenseKey.
                if (string.CompareOrdinal(license.LicenseKey, registryKeyValue) == 0)
                {
                    // All verification successful, so return the valid license.
                    return license;
                }
            }

            // If exceptions are allowed, return a LicenseException indicating that the license is not valid.
            if (allowExceptions)
            {
                throw new System.ComponentModel.LicenseException(type, instance, $"The license of this {instance.GetType().Name} control is invalid.");
            }

            return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

该代码示例还使用了 [`Logging.cs`](https://github.com/GabeStah/Airbrake.io/blob/master/lib/csharp/Utility/Utility/Logging.cs) 助手类，其完整代码可以在 GitHub 的[中找到。](https://github.com/GabeStah/Airbrake.io/blob/master/lib/csharp/Utility/Utility/Logging.cs)

## 应该什么时候用？

由于当实例化的`Control`无法找到所需的有效许可证时会抛出`LicenseException`，我们将从了解应用程序许可在. NET 中的工作方式开始。不幸的是，关于许可的官方文档并不多，但基本知识并不太复杂。首先，。NET 将许可证绑定到你想绑定的任何对象的`Type`。这是通过将一个`object`实例传递给 [`LicenseManager.Validate(Type type, object instance)`](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.licensemanager.validate?view=netframework-4.7.1) 方法来实现的。然而，对于大多数应用程序来说，许可证将被绑定到一个从`System.Windows.Forms.Control`类继承的对象上。这确保了一个应用程序的`Control`的【main】`Form`可以被检查是否有有效的许可，并做出相应的反应。然而，正如我们将在示例代码中看到的，我们可以许可任何类型的对象。

许可的标准方法是将 [`LicFileLicenseProvider`](https://docs.microsoft.com/en-us/dotnet/api/system.componentmodel.licfilelicenseprovider?view=netframework-4.7.1) 类型的`LicenseProvider`属性应用于您希望许可的类。为了说明，下面我们创建了自定义的`LicensedComboBox`类，它继承自内置的`ComboBox`控件类:

```
using System;
using System.ComponentModel;
using System.Windows.Forms;
using Utility;

namespace Airbrake.ComponentModel.LicenseException
{
    [LicenseProvider(typeof(LicFileLicenseProvider))]
    public class LicensedComboBox : ComboBox
    {
        private License _license;

        public LicensedComboBox()
        {
            try
            {
                // Validate the license of this instance.
                _license = LicenseManager.Validate(typeof(LicensedComboBox), this);
                Logging.LineSeparator("VALID LicensedComboBox LICENSE", 60);
                Logging.Log(_license);
            }
            catch (System.ComponentModel.LicenseException exception)
            {
                // Output expected LicenseExceptions.
                Logging.Log(exception);
            }
            catch (Exception exception)
            {
                // Output unexpected Exceptions.
                Logging.Log(exception, false);
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (!disposing) return;
            if (_license == null) return;

            _license.Dispose();
            _license = null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的`LicensedComboBox`类中实现许可证检查的唯一要求是在某个地方执行`LicenseManager.Validate(Type type, object instance)`检查，然后当一个类实例是`Diposed`时，确保释放许可证。正如您在我们的`try`块中的第一行所看到的，我们试图使用与当前实例(`LicensedComboBox`)匹配的类型来`Validate(...)`当前实例(`this`)。

让我们试着创建一个新的`LicensedComboBox`实例，看看当我们的构造函数试图为这个对象
获取`Validate(...)`许可证时会发生什么

```
[STAThread]
private static void Main()
{
    // ...

    Logging.LineSeparator("Creating LicensedComboBox", 60);
    new LicensedComboBox();

    // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

运行上面的代码抛出一个`LicenseException`，如下图:

```
---------------- Creating LicensedComboBox -----------------
[EXPECTED] System.ComponentModel.LicenseException: An instance of type 'Airbrake.ComponentModel.LicenseException.LicensedComboBox' was being created, and a valid license could not be granted for the type 'Airbrake.ComponentModel.LicenseException.LicensedComboBox'. Please, contact the manufacturer of the component for more information. 
```

Enter fullscreen mode Exit fullscreen mode

碰巧的是默认情况下。NET 试图*定位*一个实际的许可是通过在可执行文件的基目录中寻找一个具有以下名称格式的许可文件:`{AssemblyNamespace}.{ClassName}.lic`。因此，在我们的例子中，我们的默认许可文件名是`Airbrake.ComponentModel.LicenseException.LicensedComboBox.lic`。由于我们没有任何这样的文件，我们得到了上面看到的`LicenseException`。

为了解决这个问题，我们在 Visual Studio 项目中手动创建了`Airbrake.ComponentModel.LicenseException.LicensedComboBox.lic`文件，并确保它在构建过程中复制到输出目录中。然而，仅仅有这样一个许可文件的*存在*是不够的。默认情况下，。NET 将在`.lic`文件的内容中寻找下面的*确切的*关键字串:

```
{AssemblyNamespace}.{ClassName} is a licensed component. 
```

Enter fullscreen mode Exit fullscreen mode

因此，在我们的例子中，我们将把下面的字符串添加到我们的`.lic`文件中:

```
Airbrake.ComponentModel.LicenseException.LicensedComboBox is a licensed component. 
```

Enter fullscreen mode Exit fullscreen mode

让我们再次尝试实例化一个`LicensedComboBox`，现在我们的`.lic`文件已经就位:

```
Logging.LineSeparator("Creating LicensedComboBox", 60);
new LicensedComboBox(); 
```

Enter fullscreen mode Exit fullscreen mode

现在再次运行这个程序可以正确地验证我们的许可证，并产生以下输出，其中包括生成的`License`实例:

```
---------------- Creating LicensedComboBox -----------------
-------------- VALID LicensedComboBox LICENSE --------------
{System.ComponentModel.LicFileLicenseProvider+LicFileLicense(HashCode:36849274)}
  LicenseKey: "Airbrake.ComponentModel.LicenseException.LicensedComboBox is a licensed component." 
```

Enter fullscreen mode Exit fullscreen mode

酷！然而，通过在一个正确命名的`.lic`文件中粘贴一个特殊字符串的默认许可方法有点基础。显然，我们可以用某种方式混淆生成的密钥，使其无法手动创建，但是让我们看看另一种常用于许可的技术:修改 Windows 注册表。

这变得有点复杂，因为我们现在需要实现我们自己的定制`License`和`LicenseProvider`类。前者允许我们修改什么是有效的`LicenseKey`值以及如何检查它，而后者允许我们修改如何检索本地许可证。让我们从继承了`License` :
的`CustomLicense`类开始

```
using System;
using System.ComponentModel;

namespace Airbrake.ComponentModel.LicenseException
{
    /// <summary>
    /// CustomLicense used for RegistryLicenseProvider example.
    /// </summary>
    public class CustomLicense : License
    {
        private readonly Type _type;

        public CustomLicense(Type type)
        {
            // Type is required and must not be null.
            _type = type ?? throw new NullReferenceException("The licensed type reference may not be null.");
        }

        public override void Dispose() { }

        /// <summary>
        /// Returns an explicit license key string for demonstration.
        /// </summary>
        public override string LicenseKey => "12345";

        /// <summary>
        /// The registry key where the license can be found.
        /// </summary>
        public string RegistryKey => "Software\\Airbrake\\Licenses";
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没什么特别的，但是使用定制的`License`实现允许我们覆盖某些方法，比如`LicenseKey`。在这种情况下，我们只是让它返回一个常量值`12345`，但是我们当然可以通过从像我们的许可 web 服务这样的第三方来源检索这个值来使事情变得复杂。我们还指定了将用于该许可证的基本`RegistryKey`路径。

习俗`LicenseProvider`对我们来说叫做`RegistryLicenseProvider`:

```
using System;
using System.ComponentModel;
using Microsoft.Win32;

namespace Airbrake.ComponentModel.LicenseException
{
    public class RegistryLicenseProvider : LicenseProvider
    {
        /// <summary>
        /// Attempts to retrieve a valid license for the passed object instance.
        /// </summary>
        /// <param name="context">Context of this license indicating if it should be checked in runtime or compile time.</param>
        /// <param name="type">Object type to retrieve license for.</param>
        /// <param name="instance">Instance to check license of.</param>
        /// <param name="allowExceptions">Determines if exceptions should be allowed. If true, a LicenseException
        /// is thrown if no valid license can be obtained.</param>
        /// <returns>Valid CustomLicense instance.</returns>
        public override License GetLicense(LicenseContext context, Type type, object instance, bool allowExceptions)
        {
            var license = new CustomLicense(type);

            // If we're not in runtime mode license should be automatically approved.
            if (context.UsageMode != LicenseUsageMode.Runtime) return license;

            // Get the intended registry key.
            var registryKey = Registry.CurrentUser.OpenSubKey(license.RegistryKey);

            // Get the registry key value.
            var registryKeyValue = (string) registryKey?.GetValue(type.Name);

            if (registryKeyValue != null)
            {
                // Confirm that the keyValue from registry is equivalent to expected LicenseKey.
                if (string.CompareOrdinal(license.LicenseKey, registryKeyValue) == 0)
                {
                    // All verification successful, so return the valid license.
                    return license;
                }
            }

            // If exceptions are allowed, return a LicenseException indicating that the license is not valid.
            if (allowExceptions)
            {
                throw new System.ComponentModel.LicenseException(type, instance, $"The license of this {instance.GetType().Name} control is invalid.");
            }

            return null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，我们在这里真正做的是覆盖`GetLicense(...)`方法，该方法试图基于传递的参数检索并返回一个有效的`License`实例。这些参数中最重要的是`Type type`，我们将它传递给`CustomLicense`类的构造函数调用。否则，我们的基本逻辑是检查目标注册表项是否存在，如果存在，`LicenseKey`值是否与注册表中该项的值匹配。如果是这样，我们返回一个有效的`CustomLicense`实例，否则我们抛出一个`LicenseException`来表明许可证不能被验证。

我们的`RegistryLicensedComboBox`控件类似于`LicensedComboBox`版本，除了我们在`LicenseProvider(...)`属性调用中使用了`typeof(RegistryLicenseProvider)`:

```
using System;
using System.ComponentModel;
using System.Windows.Forms;
using Utility;

namespace Airbrake.ComponentModel.LicenseException
{
    [LicenseProvider(typeof(RegistryLicenseProvider))]
    public class RegistryLicensedComboBox : ComboBox
    {
        private License _license;

        public RegistryLicensedComboBox()
        {
            try
            {
                // Validate the license of this instance.
                _license = LicenseManager.Validate(typeof(RegistryLicensedComboBox), this);
                Logging.LineSeparator("VALID RegistryLicensedComboBox LICENSE", 60);
                Logging.Log(_license);
            }
            catch (System.ComponentModel.LicenseException exception)
            {
                // Output expected LicenseExceptions.
                Logging.Log(exception);
            }
            catch (Exception exception)
            {
                // Output unexpected Exceptions.
                Logging.Log(exception, false);
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (!disposing) return;
            if (_license == null) return;

            _license.Dispose();
            _license = null;
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们对此进行测试之前，我们可能需要一种编程方式来添加/删除适当的注册表值进行测试，这样我们就不必每次都手动编辑注册表。为此，我们有两个助手方法，从`AddLicenseRegistryEntry(Type type)` :
开始

```
/// <summary>
/// Adds license registry entry for passed control type.
/// </summary>
/// <param name="type">Control type to create license registry of.</param>
private static void AddLicenseRegistryEntry(Type type)
{
    var license = new CustomLicense(type);

    // Check if key exists.
    var registryKey = Registry.CurrentUser.OpenSubKey(license.RegistryKey);
    if (registryKey != null) return;

    // Set key value to license key.
    var newKey = Registry.CurrentUser.CreateSubKey(license.RegistryKey);
    newKey?.SetValue(type.Name, license.LicenseKey);
    Logging.Log($"Added registry key value: {Registry.CurrentUser}\\{license.RegistryKey}\\{type.Name} = {license.LicenseKey}.");
} 
```

Enter fullscreen mode Exit fullscreen mode

这将为传递的`Type type`创建一个新的`CustomLicense`实例，然后检查预期的`RegistryKey`路径是否存在。如果它不存在，那么创建一个子键路径，然后添加一个带有传递的`Type type`名称和`license.LicenseKey`值的新值。

我们也希望能够删除这个条目，所以`DeleteLicenseRegistryEntry(Type type)`方法处理:

```
/// <summary>
/// Deletes license registry entry for passed control type.
/// </summary>
/// <param name="type">Control type to delete license registry of.</param>
private static void DeleteLicenseRegistryEntry(Type type)
{
    var license = new CustomLicense(type);

    // Check if key exists.
    var registryKey = Registry.CurrentUser.OpenSubKey(license.RegistryKey);
    if (registryKey == null) return;

    // Delete key.
    Registry.CurrentUser.DeleteSubKey(license.RegistryKey);
    Logging.Log($"Deleted registry key: {Registry.CurrentUser}\\{license.RegistryKey}.");
} 
```

Enter fullscreen mode Exit fullscreen mode

好吧。有了所有的设置，我们可以通过创建一个新的`RegistryLicensedComboBox`实例来测试，看看会发生什么。我们首先确保我们的注册表条目不存在，然后创建一个新的组合框:

```
// Delete registry license entry.
DeleteLicenseRegistryEntry(typeof(RegistryLicensedComboBox));

Logging.LineSeparator("Creating RegistryLicensedComboBox", 60);
new RegistryLicensedComboBox(); 
```

Enter fullscreen mode Exit fullscreen mode

正如您可能会怀疑的那样，如果没有适当的注册表值，我们会捕获一个`LicenseException`，其中包含我们的自定义消息:

```
------------ Creating RegistryLicensedComboBox -------------
[EXPECTED] System.ComponentModel.LicenseException: The license of this RegistryLicensedComboBox control is invalid. 
```

Enter fullscreen mode Exit fullscreen mode

让我们再试一次，但是我们首先将许可证值添加到注册表:

```
// Add registry license entry.
AddLicenseRegistryEntry(typeof(RegistryLicensedComboBox));

Logging.LineSeparator("Creating RegistryLicensedComboBox w/ Registry Entry", 60);
new RegistryLicensedComboBox(); 
```

Enter fullscreen mode Exit fullscreen mode

这一次，一切都按计划进行。正如输出所确认的，我们成功地为我们的`RegistryLicensedComboBox`条目添加了新的注册表项和值。然后我们能够根据匹配的注册表条目确认`12345`的`LicenseKey's`期望值，这导致`LicenseManager.Validate(...)`方法调用返回一个有效的`CustomLicense`实例，也可以在日志中看到:

```
Added registry key value: HKEY_CURRENT_USER\Software\Airbrake\Licenses\RegistryLicensedComboBox = 12345.
--- Creating RegistryLicensedComboBox w/ Registry Entry ----
---------- VALID RegistryLicensedComboBox LICENSE ----------
{Airbrake.ComponentModel.LicenseException.CustomLicense(HashCode:19575591)}
  LicenseKey: "12345"
  RegistryKey: "Software\Airbrake\Licenses" 
```

Enter fullscreen mode Exit fullscreen mode

我们做到了！这是组件许可如何工作的简要概述。但是很明显，还有更多的东西可以添加进来，让你自己的应用程序变得健壮和防篡改。

充分利用您自己的应用程序，并全面管理所有应用程序。净异常，检查一下[空气制动。NET Bug Handler](https://airbrake.io/languages/net_bug_tracker?utm_source=blog&utm_medium=end-post&utm_campaign=airbrake-net) ，提供实时警报和即时洞察。NET 代码，以及对各种流行的开发集成的内置支持，包括:JIRA、GitHub、Bitbucket 等等。