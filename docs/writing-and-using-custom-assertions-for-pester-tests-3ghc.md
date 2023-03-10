# 编写和使用自定义断言进行纠缠测试

> 原文：<https://dev.to/mathieubuisson/writing-and-using-custom-assertions-for-pester-tests-3ghc>

令人敬畏的 PowerShell 测试框架最近引入了一项功能，允许我们扩展我们的测试断言。这允许通过将定制的或复杂的断言逻辑从测试中抽象出来并放到单独的脚本或模块中来简化我们的测试。

尽可能保持测试脚本的简洁和可读性是利用 Pester 的 <abbr title="Domain Specific Language">DSL</abbr> 的简单和优雅的核心。此外，我非常喜欢这个想法，即测试脚本可以作为一个**可执行文件**(潜在的商业可读)**规范**。

具体来说，“*自定义断言*是指我们可以在**纠缠**的断言函数:`Should`中插入额外的运算符。假设我们用的是`Pester`4 . 0 . 8 版本，有不少内置的`Should`操作符:

```
C:\> $PesterPath = (Get-Module -Name 'Pester' -ListAvailable)[0].ModuleBase
C:\> $AllItems = (Get-ChildItem "$PesterPath\Functions\Assertions\").BaseName
C:\> $AllItems.Where({ $_ -notmatch 'Should|Set-' })
Be
BeGreaterThan
BeIn
BeLessThan
BeLike
BeLikeExactly
BeNullOrEmpty
BeOfType
Exist
FileContentMatch
FileContentMatchExactly
FileContentMatchMultiline
Match
MatchExactly
PesterThrow 
```

Enter fullscreen mode Exit fullscreen mode

但是在某些情况下，有可能这些操作符都不符合我们测试中需要的断言/比较类型。

例如，假设我们需要验证一个数字是否在**范围内。对于内置的操作符，我们只限于使用`BeGreaterThan`和`BeLessThan`，所以我们的测试应该是这样的:** 

```
Describe 'Built-in assertions with numbers' {
    It '55.5 should be in range [0-100]' {
        55.5 | Should -BeGreaterThan -0.000001
        55.5 | Should -BeLessThan 100.000001
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先，它迫使我们在一个测试中有两个断言，这是不理想的。

其次，因为我们想要一个**包含**的范围，而且没有像`BeGreaterThanOrEqualTo`或者`BeLessThanOrEqualTo`这样的包含运算符，所以我们要修改断言的“*预期*部分。

不是将该值与*预期的*范围的低端(0)进行比较，我们必须将它与“*预期的*”减去“*一点点*”进行比较，以确保如果值**等于***预期的*范围的低端，则断言通过。当断言范围的高端时，类似的体操是需要的。

这不仅混乱，而且不可靠。断言的精度取决于“*只有一点点*”有多小，或者换句话说，我们向断言的“*预期的*”部分添加/移除了多少个小数位。

这整个事情感觉是错误的，所以让我们使用一个定制的`Should`操作符来编写一个更好的断言。

## 编写自定义的应符

目前还没有太多的文档和例子，所以现在，最好的起点是纠缠 wiki 中的[这个例子。](https://github.com/pester/Pester/wiki/Add-AssertionOperator)

我们的自定义操作符将被命名为`BeInRange`，与我们可以找到的示例不同，它将需要 2 个值来表示断言的"*预期的*"部分:

*   `$Min`为范围的低端
*   `$Max`范围的高端

下面是断言操作符函数`BeInRange`的样子:

```
Function BeInRange {
<#
.SYNOPSIS
Tests whether a value is in a given inclusive range
#>
    [CmdletBinding()]
    Param(
        $ActualValue,
        $Min,
        $Max,
        [switch]$Negate
    )

    [bool]$Pass = $ActualValue -ge $Min -and $ActualValue -le $Max
    If ( $Negate ) { $Pass = -not($Pass) }

    If ( -not($Pass) ) {
        If ( $Negate ) {
            $FailureMessage = 'Expected: value {{{0}}} to be outside the range {{{1}-{2}}} but it was in the range.' -f $ActualValue, $Min, $Max
        }
        Else {
            $FailureMessage = 'Expected: value {{{0}}} to be in the range {{{1}-{2}}} but it was outside the range.' -f $ActualValue, $Min, $Max
        }
    }

    $ObjProperties = @{
        Succeeded = $Pass
        FailureMessage = $FailureMessage
    }
    return New-Object PSObject -Property $ObjProperties
} 
```

Enter fullscreen mode Exit fullscreen mode

首先要注意的是:表示断言的“*实际*部分的参数必须命名为`ActualValue`。如果没有，**persted**的内部函数`Invoke-Assertion`爆炸，因为它调用任何带有`ActualValue`参数的断言函数来传递断言值。

所有的`Should`操作符都可以通过在它们前面插入`-Not`来求反。为了让我们的自定义操作符尊重这种行为，我们需要实现`Negate`开关参数，如上所示。

> `$FailureMessage`字符串中的三重括号是怎么回事？
> 
> *   括号#1:字符串格式化操作符(`-f`)使用`{}`作为占位符
> *   括号#2:我们将当前断言的值放在`{}`中，以与内置断言的失败消息保持一致
> *   大括号#3:如果字符串包含大括号，字符串格式化操作符会抓狂，所以我们将第二组大括号加倍来转义它们

我们的断言操作符函数有一个契约义务:它必须返回一个类型为`[PSObject]`的对象，该对象有一个名为`Succeeded`的属性和一个名为`FailureMessage`的属性。于是我们相应地建造了`$ObjProperties`。

我们怎么知道的？

```
C:\> Get-Help 'Add-AssertionOperator' -Parameter 'Test'

-Test <ScriptBlock>
    The test function. The function must return a PSObject with a [Bool]succeeded
    and a [string]failureMessage property.

    Required? true
    Position? 2
    Default value
    Accept pipeline input? false
    Accept wildcard characters? false 
```

Enter fullscreen mode Exit fullscreen mode

## 在我们的测试中使用定制的 Should 操作符

我们将`BeInRange`函数放在一个名为`CustomAssertions.psm1`的模块中，以便于重用。我们甚至可以将模块存储在我们的`$Env:PSModulePath`中的一个位置，以允许通过名称而不是路径来导入它。

还有另一种方法:将自定义操作符函数放在**纠缠**的断言文件夹:`"$((Get-Module Pester).ModuleBase)\Functions\Assertions\"`内的一个. ps1 文件中。

**纠缠**从该位置自动挑选操作员，但这可能不太灵活，因此请根据您的偏好/场景选择一种方法。

现在是时候编写测试来验证我们的自定义操作符的断言有多干净，并确保它的行为符合预期:

```
Import-Module "$PSScriptRoot\CustomAssertions.psm1" -Force
Add-AssertionOperator -Name 'BeInRange' -Test $Function:BeInRange

Describe 'BeInRange assertions with numbers' {
    It '55.5 should be in range [0-100]' {
        55.5 | Should -BeInRange -Min 0 -Max 100
    }
    It '0 should be in range [0-100] (inclusive range)' {
        0 | Should -BeInRange -Min 0 -Max 100
    }
    It '80 should not be in range [0-55.5]' {
        80 | Should -Not -BeInRange -Min 0 -Max 55.5
    }
    It 'Should fail (to verify the failure message)' {
        1 | Should -BeInRange -Min 10 -Max 20
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

第一行导入模块，使我们的`BeInRange`函数在全局范围内可见，以确保`Add-AssertionOperator`能看到它。

`Add-AssertionOperator`是来自**纠缠**的启用魔法的功能。它向**纠缠**注册了一个自定义断言操作符函数，后者将它与`Should`函数集成为一个动态参数。聪明的东西。

不幸的是，一旦我们多次运行这个测试脚本(没有卸载`Pester`模块)，我们就会得到这个错误:

```
Executing script .\CustomAssertions.Tests.ps1
  [-] Error occurred in test script '.\CustomAssertions.Tests.ps1' 35ms
    RuntimeException: Assertion operator name 'BeInRange' has been added multiple times.
    at Assert-AssertionOperatorNameIsUnique, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Pester.psm1: line 243
    at Add-AssertionOperator, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Pester.psm1: line 211
    at <ScriptBlock>, C:\CustomAssertions\CustomAssertions.Tests.ps1: line 2
    at <ScriptBlock>, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Pester.psm1: line 802
    at Invoke-Pester<End>, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Pester.psm1: line 817 
```

Enter fullscreen mode Exit fullscreen mode

谢天谢地[这个 bug](https://github.com/pester/Pester/issues/891) 已经在 master 分支中修复，并计划在 4.1 里程碑中发布。

正如我们在上面看到的，使用`BeInRange`操作符的断言比最初使用内置操作符的例子更简单，可读性更好。

现在让我们看看它是否有效:

```
C:\> $TestsPath = 'C:\CustomAssertions\CustomAssertions.Tests.ps1'
C:\> Invoke-Pester -Script $TestsPath
Executing all tests in 'C:\CustomAssertions\CustomAssertions.Tests.ps1'

Executing script C:\CustomAssertions\CustomAssertions.Tests.ps1

  Describing BeInRange assertions with numbers
    [+] 55.5 should be in range [0-100] 94ms
    [+] 0 should be in range [0-100] (inclusive range) 53ms
    [+] 80 should not be in range [0-55.5] 51ms
    [-] Should fail (to verify the failure message) 38ms
      Expected: value {1} to be in the range {10-20} but it was outside the range.
      15: 1 | Should -BeInRange -Min 10 -Max 20
      at Invoke-Assertion, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Functions\Assertions\Should.ps1: line 209
      at <ScriptBlock>, C:\CustomAssertions\CustomAssertions.Tests.ps1: line 15
Tests completed in 237ms
Tests Passed: 3, Failed: 1, Skipped: 0, Pending: 0, Inconclusive: 0 
```

Enter fullscreen mode Exit fullscreen mode

这和预期的一样有效。

但是它对类型为`[string]`的对象有效吗？这将允许我们做与字母排序相关的断言。

那么`[datetime]`对象呢？这将允许验证一个`[datetime]`值在一个预期的日期(或时间)范围内。

我们将以下测试添加到我们的测试脚本中:

```
Context 'Assertions on [string] objects' {

    It '"abcd" should be in range ["ab"-"yz"]' {
        'abcd' | Should -BeInRange -Min 'ab' -Max 'yz'
    }
    It '"a" should be in range ["a"-"yz"]' {
        'a' | Should -BeInRange -Min 'a' -Max 'yz'
    }
    It '"az" should not be in range ["ab"-"aefg"]' {
        'az' | Should -Not -BeInRange -Min 'ab' -Max 'aefg'
    }
}
Context 'Assertions on [datetime] objects' {
    $Now = [datetime]::Now

    It 'The 1st of October 2017 should be in the range representing the current year' {
        $YearStart = Get-Date -Month 1 -Day 1
        $YearEnd = Get-Date -Month 12 -Day 31
        $FirstOct = Get-Date -Year 2017 -Month 10 -Day 1
        $FirstOct | Should -BeInRange -Min $YearStart -Max $YearEnd
    }
    It 'Today at 10 AM should be between today at 01 AM and now' {
        $DayStart = Get-Date -Hour 1 -Minute 0
        Get-Date -Hour 10 -Minute 0 | Should -BeInRange -Min $DayStart -Max $Now
    }
    It 'Now should not be in the range representing last month' {
        $LastMonthStart = (Get-Date -Day 1 -Hour 0 -Minute 0).AddMonths(-1)
        $LastMonthEnd = (Get-Date -Day 1 -Hour 0 -Minute 0).AddMinutes(-1)
        $Now | Should -Not -BeInRange -Min $LastMonthStart -Max $LastMonthEnd
    }
    It 'Now should not be in a range located in the future' {
        $FutureStart = Get-Date -Year 2117
        $FutureEnd = Get-Date -Year 2217
        $Now | Should -Not -BeInRange -Min $FutureStart -Max $FutureEnd
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们再次运行测试脚本:

```
C:\> Invoke-Pester -Script $TestsPath                                                         
Executing all tests in 'C:\CustomAssertions\CustomAssertions.Tests.ps1'                       

Executing script C:\CustomAssertions\CustomAssertions.Tests.ps1                               

  Describing BeInRange assertions with numbers                                                

    Context Assertions on numbers                                                             
      [+] 55.5 should be in range [0-100] 76ms                                                
      [+] 0 should be in range [0-100] (inclusive range) 27ms                                 
      [+] 80 should not be in range [0-55.5] 28ms                                             
      [-] Should fail (to verify the failure message) 28ms                                    
        Expected: value {1} to be in the range {10-20} but it was outside the range.          
        17: 1 | Should -BeInRange -Min 10 -Max 20                                 
        at Invoke-Assertion, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Functions
\Assertions\Should.ps1: line 209                                                              
        at <ScriptBlock>, C:\CustomAssertions\CustomAssertions.Tests.ps1: line 17             

    Context Assertions on [string] objects                                                    
      [+] "abcd" should be in range ["ab"-"yz"] 65ms                                          
      [+] "a" should be in range ["a"-"yz"] 26ms                                              
      [+] "az" should not be in range ["ab"-"aefg"] 16ms                                      

    Context Assertions on [datetime] objects                                                  
      [+] The 1st of October 2017 should be in the range representing the current year 68ms   
      [+] Today at 10 AM should be between today at 01 AM and now 31ms                        
      [+] Now should not be in the range representing last month 32ms                         
      [+] Now should not be in a range located in the future 33ms                             
Tests completed in 436ms                                                                      
Tests Passed: 10, Failed: 1, Skipped: 0, Pending: 0, Inconclusive: 0 
```

Enter fullscreen mode Exit fullscreen mode

事实上，它确实适用于这三种类型的对象。

既然我们知道断言的"*预期的*"部分可以由多个值组成，这就使得复杂的或专门的断言成为可能。让我们尝试一个更复杂的断言操作符，它可能在基础设施验证场景中有用。

## 断言一个 IP 地址在给定的子网中

我们将编写一个名为`BeInSubnet`的新函数，它包含检查 IPv4 地址是否与指定地址(这可以是网络 ID，但不是必须的)和指定子网掩码在同一个子网中的逻辑。我们将把它添加到现有的 **CustomAssertions** 模块中。

下面是函数:

```
Function BeInSubnet {
<#
.SYNOPSIS
Tests whether an IPv4 address in the same subnet as a given address with a given subnet mask.
#>
    [CmdletBinding()]
    Param(
        $ActualValue,
        $Network,
        $Mask,
        [switch]$Negate
    )
    If ( $ActualValue -isnot [ipaddress] ) {
        $ActualValue = $ActualValue -as [ipaddress]
    }
    If ( $Network -isnot [ipaddress] ) {
        $Network = $Network -as [ipaddress]
    }
    If ( $Mask -isnot [ipaddress] ) {
        $Mask = $Mask -as [ipaddress]
    }

    $ActualNetworkBinary = $ActualValue.Address -band $Mask.Address
    $ExpectedNetworkBinary = $Network.Address -band $Mask.Address

    [bool]$Pass = $ActualNetworkBinary -eq $ExpectedNetworkBinary
    If ( $Negate ) { $Pass = -not($Pass) }

    If ( -not($Pass) ) {
        $ActualSubnetString = ($ActualNetworkBinary -as [ipaddress]).IPAddressToString
        If ( $Negate ) {
            $FailureMessage = 'Expected: address {{{0}}} to be outside subnet {{{1}}} with mask {{{2}}} but was within it.' -f $ActualValue.IPAddressToString, $Network.IPAddressToString, $Mask.IPAddressToString
        }
        Else {
            $FailureMessage = 'Expected: address {{{0}}} to be in subnet {{{1}}} with mask {{{2}}} but was in subnet {{{3}}}.' -f $ActualValue.IPAddressToString, $Network.IPAddressToString, $Mask.IPAddressToString, $ActualSubnetString
        }
    }

    $ObjProperties = @{
        Succeeded = $Pass
        FailureMessage = $FailureMessage
    }
    return New-Object PSObject -Property $ObjProperties
} 
```

Enter fullscreen mode Exit fullscreen mode

该函数的参数不强制使用特定的`[type]`，这是为了使该函数更加灵活。这样，使用这个操作符的断言将能够向它传递`[string]`或`[ipaddress]`对象。

这就是为什么我们检查通过函数的参数`$ActualValue`、`$Network`、`$Mask`传递的每个值的类型，并将它们转换为类型`[ipaddress]`以便进一步操作。

该函数的其余部分与我们之前的自定义操作符函数非常相似。

然后，我们创建一个测试脚本(`BeInSubnet.Tests.ps1`)来验证我们的`BeInSubnet`自定义断言操作符的行为符合预期:

```
Import-Module "$PSScriptRoot\CustomAssertions.psm1" -Force
Add-AssertionOperator -Name 'BeInSubnet' -Test $Function:BeInSubnet

Describe 'BeInSubnet assertions' {
    Context 'Assertions on [string] objects' {

        It '"10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0"' {
            '10.1.5.193' | Should -BeInSubnet -Network '10.1.5.0' -Mask '255.255.255.0'
        }
        It '"10.1.5.0" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0"' {
            '10.1.5.0' | Should -BeInSubnet -Network '10.1.5.0' -Mask '255.255.255.0'
        }
        It '"10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.0.0.0"' {
            '10.1.5.193' | Should -BeInSubnet -Network '10.1.5.0' -Mask '255.0.0.0'
        }
        It '"10.1.5.193" should not be in the same subnet as "10.1.5.0" with mask "255.255.255.128"' {
            '10.1.5.193' | Should -Not -BeInSubnet -Network '10.1.5.0' -Mask '255.255.255.128'
        }
        It 'Should fail (to verify the failure message)' {
            '10.1.5.193' | Should -BeInSubnet -Network '10.1.5.0' -Mask '255.255.255.128'
        }
    }
    Context 'Assertions on [ipaddress] objects' {
        $Value = '10.1.5.193' -as [ipaddress]
        $Network = '10.1.5.0' -as [ipaddress]
        $SubnetMask = '255.255.255.0' -as [ipaddress]
        $LargeSubnetMask = '255.0.0.0' -as [ipaddress]
        $SmallSubnetMask = '255.255.255.128' -as [ipaddress]

        It '"10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0"' {
            $Value | Should -BeInSubnet -Network $Network -Mask $SubnetMask
        }
        It '"10.1.5.0" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0"' {
            $Network | Should -BeInSubnet -Network $Network -Mask $SubnetMask
        }
        It '"10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.0.0.0"' {
            $Value | Should -BeInSubnet -Network $Network -Mask $LargeSubnetMask
        }
        It '"10.1.5.193" should not be in the same subnet as "10.1.5.0" with mask "255.255.255.128"' {
            $Value | Should -Not -BeInSubnet -Network $Network -Mask $SmallSubnetMask
        }
        It 'Should fail (to verify the failure message)' {
            $Value | Should -BeInSubnet -Network $Network -Mask $SmallSubnetMask
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这些测试是清晰易读的。现在，想象一下，如果我们必须用内置的`Should`操作符做同样的断言……测试脚本将会被大量的`[ipaddress]`操作代码弄得乱七八糟，除非这些代码被提取到一个助手函数中。

让我们运行这些测试并检查结果:

```
C:\> $SubnetTestsPath = 'C:\CustomAssertions\BeInSubnet.Tests.ps1'
C:\> Invoke-Pester -Script $SubnetTestsPath
Executing all tests in 'C:\CustomAssertions\BeInSubnet.Tests.ps1'

Executing script C:\CustomAssertions\BeInSubnet.Tests.ps1

  Describing BeInSubnet assertions

    Context Assertions on [string] objects
      [+] "10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0" 138ms
      [+] "10.1.5.0" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0" 20ms
      [+] "10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.0.0.0" 23ms
      [+] "10.1.5.193" should not be in the same subnet as "10.1.5.0" with mask "255.255.255.128" 23ms
      [-] Should fail (to verify the failure message) 27ms
        Expected: address {10.1.5.193} to be in subnet {10.1.5.0} with mask {255.255.255.128} but was in subnet {10.1.5.128}.
        20: '10.1.5.193' | Should -BeInSubnet -Network '10.1.5.0' -Mask '255.255.255.128'
        at Invoke-Assertion, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Functions\Assertions\Should.ps1: line 209
        at <ScriptBlock>, C:\CustomAssertions\BeInSubnet.Tests.ps1: line 20

    Context Assertions on [ipaddress] objects
      [+] "10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0" 80ms
      [+] "10.1.5.0" should be in the same subnet as "10.1.5.0" with mask "255.255.255.0" 26ms
      [+] "10.1.5.193" should be in the same subnet as "10.1.5.0" with mask "255.0.0.0" 28ms
      [+] "10.1.5.193" should not be in the same subnet as "10.1.5.0" with mask "255.255.255.128" 18ms
      [-] Should fail (to verify the failure message) 29ms
        Expected: address {10.1.5.193} to be in subnet {10.1.5.0} with mask {255.255.255.128} but was in subnet {10.1.5.128}.
        43: $Value | Should -BeInSubnet -Network $Network -Mask $SmallSubnetMask
        at Invoke-Assertion, C:\Program Files\WindowsPowerShell\Modules\Pester\4.0.8\Functions\Assertions\Should.ps1: line 209
        at <ScriptBlock>, C:\CustomAssertions\BeInSubnet.Tests.ps1: line 43
Tests completed in 418ms
Tests Passed: 8, Failed: 2, Skipped: 0, Pending: 0, Inconclusive: 0 
```

Enter fullscreen mode Exit fullscreen mode

很好，一切正常。

另外，我们可以看到失败消息非常有用。它用“*实际*值和“*预期*值告诉我们到底发生了什么。这是自定义断言的一个主要优点，它使我们能够创建更有意义的失败消息，因为这些消息可以针对特定的断言进行定制。

## 结论

虽然有一些小问题需要解决，以使这个特性完全可用和成熟，但是用简单的 PowerShell 函数扩展**纠缠**的断言操作符的能力是非常强大的。

这允许在我们的测试中执行非常复杂或专门的断言，同时保持它们相对人类可读，并对**纠缠**的 <abbr title="Domain Specific Language">DSL</abbr> 保持真实。这扩展了**纠缠**的断言能力，以及它的用例。

不过，不要太疯狂。我建议将断言逻辑的复杂性保持在最低限度，因为越复杂，某些事情不完全按照预期工作的可能性就越大。我们希望断言**非常可靠**，我们希望测试失败，因为测试中的代码没有按照预期的方式运行，**而不是断言逻辑**。**