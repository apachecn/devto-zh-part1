# 围棋中的模拟方法

> 原文：<https://dev.to/dmigwi/mocking-methods-in-go-5fg>

不言而喻，一个营的士兵有多强，就有多弱。在这种情况下，一段代码最薄弱的地方就是没有 bug 和其他问题。facebook 的 Kent Beck 曾经在 Andela 拜访过我们，做了一个关于 TDD 的演讲，在他的整个演讲中，很明显单元测试是软件开发的关键部分。

像 Python 这样的语言提供了标准化的库和框架，使得在不纯的函数或方法中模仿方法和函数变得容易。使用 python 进行单元测试非常容易，因此达到 100%的测试覆盖率是正常的。

Go 或 Golang 是一种轻量级语言，不是基于 C 的。它的语法非常简单，我喜欢称 golang 为 Java 的“*Python 化版本*”，因为从 Java 和 Python 引入的概念*非常相似。Golang 没有支持在单元测试期间模仿方法的官方包。*

golang 中的嘲讽是在[接口](http://www.golangbootcamp.com/book/interfaces)的帮助下完成的。单元测试中的模拟很重要，因为它确保在被测试函数范围之外修改的变量、方法和函数不会影响测试输出。

下面是 mocking.go
的实现

```
/** mocking.go **/

package main

import "fmt"

type (
    // Values defines interface to be used in mocking
    Values interface {
        GetVolume() int
        GetSurfaceArea() int
    }

    // Measurements defines the measurements used to calculate
    // the surface area and volume of a cube or a cuboid.
    Measurements struct {
        Length int
        Width  int
        Height int
    }
)

// GetVolume calculates and returns the volume of a cube or a cuboid
func (config *Measurements) GetVolume() int {
    return config.Height * config.Length * config.Width
}

// GetSurfaceArea calculates and returns the surface area of a cube or cuboid
func (config *Measurements) GetSurfaceArea() int {
    return (config.Length * config.Width * 2) +
        (config.Length * config.Width * 2) +
        (config.Height * config.Width * 2)
}

// GetVolumeAndArea fetches and returns the volume and the area
func GetVolumeAndArea(val Values) (int, int) {
    return val.GetVolume(), val.GetSurfaceArea()
}

// program execution starts here
func main() {
    var (
        area, volume int

        data = &Measurements{
            Length: 1,
            Height: 5,
            Width:  3,
        }
    )

    volume, area = GetVolumeAndArea(data)

    fmt.Printf("Volume : %d , Area : %d \n", volume, area)
} 
```

Enter fullscreen mode Exit fullscreen mode

函数接受一个接口作为它的输入参数。这意味着任何实现了`GetVolume`和`GetSurfaceArea`的接收者( *struct 用来创建方法*)也实现了`Values`接口。

对`GetVolume`和`GetSurfaceArea`的模仿是通过将它们的定制实现传递给充当它们的接收者的结构来完成的。

下面是如何使用接口模仿`GetVolume`和`GetSurfaceArea`的。不需要额外的库或框架。

```
/** mocking_test.go **/

package main

import (
    "strconv"
    "testing"
)

// MockTest helps implement our customized GetVolume and GetSurfaceArea
// needed to mock the original implementation in mocking.go
type MockTest struct {
    Elem int
}

// GetVolume returns the volume of a cube
func (config *MockTest) GetVolume() int {
    return config.Elem * config.Elem * config.Elem
}

// GetSurfaceArea returns the surface area of a cube
func (config *MockTest) GetSurfaceArea() int {
    return config.Elem * config.Elem * 6
}

// TestGetVolumeAndArea tests the functionality of GetVolumeAndArea
func TestGetVolumeAndArea(t *testing.T) {
    for key, val := range map[MockTest][]int{
        MockTest{Elem: 5}: []int{125, 150},
        MockTest{Elem: 2}: []int{8, 24},
        MockTest{Elem: 1}: []int{1, 6},
    } {
        t.Run("Cube side length "+strconv.Itoa(key.Elem), func(t *testing.T) {
            volume, area := GetVolumeAndArea(&key)

            if volume != val[0] {
                t.Errorf("Expected volume to be equal to %d but was equal to %d ",
                    volume, val[0])
                t.FailNow()
            }

            if area != val[1] {
                t.Errorf("Expected area to be equal to %d but was equal to %d ",
                    area, val[1])
                t.FailNow()
            }
        })
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

除了模仿之外，接口还有助于分离关注点，这样相互通信的代码/函数/方法可以相互独立地运行。

它们还有助于隐藏不应该向外界公开的底层实现，尤其是在构建 API 时。

*对于任何**澄清**、**更正**或**补充**通过邮件联系我:[migwindungu0@gmail.com](mailto:migwindungu0@gmail.com)T9】*