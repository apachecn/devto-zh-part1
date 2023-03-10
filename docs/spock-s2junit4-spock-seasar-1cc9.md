# 因为不能混合使用 Spock 和 S2JUnit4，所以制作了 spock-seasar

> 原文：<https://dev.to/shiena/spock-s2junit4-spock-seasar-1cc9>

# 前言

在制作 SAStruts 等基于 Seasar 的测试用例时使用了 S2JUnit4，但是最近知道的 Spock 因为可以写得很简洁，所以反复尝试能否混合使用。 但是，有几个问题，没能简单地混合起来。
于是利用 Spock 的功能扩展，制作了加入了 S2JUnit4 的 spock-seasar。

[Spock 框架- Seasar2 模块](https://bitbucket.org/shiena/spock-seasar)

# 样品

[尝试用 spock-seasar 改写了用 SAStruts 和 JSON](http://qiita.com/shiena/items/146a0504c026ba00fbd0) 制作的 JSON 样本 APP 的测试用例[AddActionTest.java](https://bitbucket.org/shiena/sample-sastruts-json-webapps/src/805111ae8392aa718a2eed081c86a623a376f6c6/src/test/java/sample/sastruts/json/webapps/action/AddActionTest.java) 。
为了使用 spock-seasar 的设定，只是对测试类进行`@Seasar2Support`注释。
其中`response`和`addAction`与 S2JUnit4 一样进行了自动字段绑定。
另外，包含`s2junit4.dicon`等的全套文件请参考[sample-sa struts-JSON-web apps](https://bitbucket.org/shiena/sample-sastruts-json-webapps)。

```
package sample.sastruts.json.webapps.action

import org.seasar.framework.mock.servlet.MockHttpServletResponse
import org.shiena.seasar.Seasar2Support
import spock.lang.Specification
import spock.lang.Unroll

import javax.servlet.http.HttpServletResponse

@Seasar2Support
class AddActionSpec extends Specification {
    HttpServletResponse response
    AddAction addAction

    @Unroll
    def '#a1 + #a2 = #json'() {
        setup:
        addAction.addForm.with {
            arg1 = a1
            arg2 = a2
        }

        when:
        def result = addAction.submit()
        def mockHttpServletResponse = response as MockHttpServletResponse

        then:
        result == null
        mockHttpServletResponse.contentType == 'application/json; charset=UTF-8'
        new String(mockHttpServletResponse.responseBytes, 'UTF-8') == json

        where:
        a1            | a2
        '1'           | '2'
        '-3'          | '2'
        '99999999999' | '1'
        '1'           | '99999999999'
        '-9999999999' | '1'
        '1'           | '-9999999999'
        'foobar'      | '1'
        '1'           | 'foobar'
        null          | '1'
        '1'           | null

        json << [
            '{"result":3,"messages":null}',
            '{"result":-1,"messages":null}',
            '{"result":null,"messages":["numeric value out of bounds (<9 digits>.<0 digits> expected)"]}',
            '{"result":null,"messages":["numeric value out of bounds (<9 digits>.<0 digits> expected)"]}',
            '{"result":null,"messages":["numeric value out of bounds (<9 digits>.<0 digits> expected)"]}',
            '{"result":null,"messages":["numeric value out of bounds (<9 digits>.<0 digits> expected)"]}',
            '{"result":null,"messages":["numeric value out of bounds (<9 digits>.<0 digits> expected)"]}',
            '{"result":null,"messages":["numeric value out of bounds (<9 digits>.<0 digits> expected)"]}',
            '{"result":null,"messages":["may not be empty"]}',
            '{"result":null,"messages":["may not be empty"]}',
        ]
    }
} 
```

# spock-seasarの機能

基本上与[S2JUnit4](http://s2container.seasar.org/2.4/ja/S2JUnit4.html) 相同，但有追加和删除的功能。

## 追加的功能

因为 Spock 由 groovy 创建，所以`TestContext.getMethodName()`返回不希望的字符串，如$spock_feature_0_0，而不是测试方法名称。 因此，我们提供了一个 TestContext 实现，用于返回测试用例中描述的方法名称。

### org . shiena . seasar . s 2s pockinteraltestcontextimpl

InternalTestContextImplを継承したTestContextです。

*   getFeatureName ( ) -返回- Unroll 之前的测试方法名称。
*   getIterationName ( ) -返回空值后的测试方法名称。

### org . shiena . seasar . S2 spocksimpleinternaltestcontext

SimpleInternalTestContextを継承したTestContextです。

*   getFeatureName ( ) -返回- Unroll 之前的测试方法名称。
*   getIterationName ( ) -返回空值后的测试方法名称。

## 删除的功能

删除了 Spock 和 S2JUnit4 中重复的功能和 Spock 中难以使用的功能。

*   方法的命名规则
*   `@Prerequisite`注释
*   `@Mock`注释
*   `@Mocks`注释
*   `@EasyMock`注释
*   `@PostBindFields`注释
*   `@PreUnbindFields`注释
*   使用`s2junit4config.dicon`定制 S2JUnit4

## 处理的顺序

以下用粗体字表示 spock-seasar 的处理顺序和导入的功能。
例如，自动字段绑定在`setup()`之后，所以可以在测试方法的`setup`块之后引用字段。

1.  setupSpec()
2.  开始重复每个测试方法
3.  **测试用容器的初始化和 TestContext 的绑定**
4.  设置()
5.  **自动字段绑定**
6.  测试方法执行
7.  **丢弃自动字段绑定的字段**
8.  清理()
9.  **测试用集装箱的废弃**
10.  各测试方法的重复结束
11.  cleanupSpec()

# 使用方法

## 栅极 le 的情况

```
repositories {
    maven {
        url 'https://bitbucket.org/shiena/mvn-repo/raw/tip/'
    }
}

dependencies {
    testCompile 'org.shiena:spock-seasar:0.0.1'
} 
```

## maven 的情况

```
<repositories>
    <repository>
        <url>https://bitbucket.org/shiena/mvn-repo/raw/tip/</url>
    </repository>
</repositories>

<dependencies>
    <dependency>
        <groupId>org.shiena</groupId>
        <artifactId>spock-seasar</artifactId>
        <version>0.0.1</version>
        <scope>test</scope>
    </dependency>
</dependencies> 
```

# Pay attention

很遗憾，Spock 和 S2JUnit4 所需的 JUnit 版本不同，不能混合使用。
这是最开始没能写的理由。 无论匹配哪一个版本，另一个版本都会出现错误。

*   Spock 需要[JUnit4.7 以上。](http://code.google.com/p/spock/wiki/SpockVersionsAndDependencies)
*   S2JUnit4 需要[JUnit4.4。](http://s2container.seasar.org/2.4/ja/S2JUnit4.html#requisite)

# 总结

Spock 的功能扩展的制作方法与 Seasar2 和 Spring Framework 的拦截器给人的印象相似。
在 JUnit 中准备共同处理的时候，虽然制作了父级，但是在 Spock 中好像也可以通过功能扩展来制作。

# Reference

*   [使用 Spock 的注释驱动扩展](http://ldaley.com/post/971946675/annotation-driven-extensions-with-spock)
*   [斯波克@RunJetty](http://tawus.wordpress.com/2012/08/03/spock-runjetty/)