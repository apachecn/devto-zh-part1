# 使用数据提供程序进行更好的单元测试

> 原文：<https://dev.to/ryanwelcher/better-unit-testing-with-dataproviders-4j2j>

当单元测试你的代码时，有时你需要用各种不同的参数测试相同的方法或函数。例如，我们有一个函数来检查一个被传递的电子邮件是否有效，并且不在已知电子邮件列表中:

```
/**
 * Confirm that a new email is both valid and not already in a list of known email addresses.
 *
 * @param string $new_email The new email we're going to add.
 * @param array $list_of_saved_emails The list of currently saved emails to check against.
 *
 * @return bool
 */
function is_new_and_valid_email( $new_email, $list_of_saved_emails ) {

    if ( ! filter_var( $new_email, FILTER_VALIDATE_EMAIL ) ) {
        return false;
    }

    if ( in_array( $new_email, $list_of_saved_emails, true ) ) {
        return false;
    }

    return true;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以通过创建一个新的测试类来运行一个简单的测试:

```
/**
 * Class Simple_Tests
 */
class Simple_Tests extends PHPUnit_Framework_TestCase {

    /**
     * Simple unit test with hardcoded values
     */
    function test_is_new_and_valid_email_new_email() {
        $expected_result = true;
        $actual_result = is_new_and_valid_email( 'new@email.com', array( 'alternate@email.com' ) );

        $this->assertSame( $expected_result, $actual_result );
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的测试将通过，但它没有涵盖这个函数在现实世界中可能遇到的所有可能性。如果邮件已经存在或者不是真实的邮件会怎么样？我们也需要那些测试。

我们有几个选项来创建这些测试:

*   为每个可能的组合写一个新的测试函数。
*   使用 DataProvider 并使用它提供的数据运行相同的测试。

## 选项一:多写测试。

让我们从第一个选项开始。我们需要为每种可能的情况编写一个新的断言。看起来是这样的:

```
/**
 * Simple unit test with hardcoded values
 */
function test_is_new_and_valid_email_new_email() {
   $expected_result = true;
   $actual_result = is_new_and_valid_email( 'new@email.com', array( 'alternate@email.com' ) );

   $this->assertSame( $expected_result, $actual_result );
}

function test_is_new_and_valied_email_already_known() {
   $expected_result = false;
   $actual_result = is_new_and_valid_email( 'new@email.com', array(
      'alternate@email.com',
      'alternate1@email.com',
      'new@email.com',
   ) );

   $this->assertSame( $expected_result, $actual_result );
}

function test_is_new_and_valied_email_invalid_email() {
   $expected_result = false;
   $actual_result = is_new_and_valid_email( 'not-an-email', array() );

   $this->assertSame( $expected_result, $actual_result );
} 
```

Enter fullscreen mode Exit fullscreen mode

这些测试是有效的，但是代码中有相当多的重复。这三个断言之间唯一真正的区别是预期结果、我们添加的电子邮件和已知列表。代码的其余部分是相同的。

如果我们有办法将这三个项目作为参数传递给同一个测试呢？我们有，它叫做数据提供器。

## 选项 2:使用 DataProvider

在它的基础上， [DataProvider](https://phpunit.de/manual/current/en/writing-tests-for-phpunit.html#writing-tests-for-phpunit.data-providers) 是一个函数，它返回一个数组，该数组由要运行的每个断言的参数组成:

```
/**
 * Example DataProvider
 */
function example_dataprovider() {
    return array(
    'param_for_test_1',
    'param_for_test_2',
    'param_for_test_3',
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

实际上，DataProviders 通常会为每个测试返回一个值数组，因为至少您会希望传递测试的预期结果和一个要传递给测试代码的值。

为了使用 DataProvider:
,我们需要稍微重构一下我们的测试代码

```
function test_is_new_and_valid_email( $expected_result, $new_email, $list_of_known_emails ) {
      $actual_result = is_new_and_valid_email( $new_email, $list_of_saved_emails );
      $this->assertSame( $expected_result, $actual_result );
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，测试函数现在接受 3 个参数，这些参数将从 DataProvider 传递给它，如下所示:

```
function data_is_new_and_valid_email() {
   return array(
      // Valid email address that doesn't exist.
      array(
         true,
         'new@email.com',
         array(
            'alternate@email.com',
            'alternate2@email.com',
         ),
      ),
      // Valid email that already exists.
      array(
         false,
         'new@email.com',
         array(
            'alternate@email.com',
            'alternate1@email.com',
            'new@email.com',
         ),
      ),
      // Invalid email.
      array(
         false,
         'not-an-email',
         array(),
      ),
   );
} 
```

Enter fullscreen mode Exit fullscreen mode

**顺便说一下，您为数据提供者使用的名称并不重要，我倾向于使用使用测试函数名称并将 test_ to data_ 的约定来使关联清晰。***

每个被返回的数组都依次包含测试函数所期望的参数；$expected_result、我们正在添加的$new_email 和$list_of_known_emails

此时，我们已经准备好了测试函数和 DataProvider，但是它们彼此并不了解。连接它们很容易，我们只需对测试函数添加一个新的注释，PHPUnit 就会为我们建立连接:

```
/**
 * Simple unit test with a dataprovider so we can run many assertions in one test.
 *
 * The dataProvider annotation tells PHPUnit which function it should use
 * for the data to test.
 *
 * @dataProvider data_is_new_and_valid_email
 */
function test_is_new_and_valid_email( $expected_result, $new_email, $list_of_saved_emails ) {
   $actual_result = is_new_and_valid_email( $new_email, $list_of_saved_emails );
   $this->assertSame( $expected_result, $actual_result );
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们运行我们的测试时，test_is_new_and_valid_email 测试将为 DataProvider 数组中的每个索引运行，并将向它们传递参数。如果您需要添加另一个测试用例，只需向 DataProvider 添加一个新数组即可！

正如您所看到的，尽管 DataProvider 涉及到更多的设置，但是您的测试变得更加简洁，更易于维护和扩展。

下面是最后的测试类，有一些注释:

```
/**
 * Class Simple_Tests
 *
 * Tests that just use PHPUnit
 *
 * @group simple
 */
class Simple_Tests extends PHPUnit_Framework_TestCase {

   /**
    * Simple unit test with a dataprovider so we can run many assertions in one test.
    *
    * The dataProvider annotation tells PHPUnit which function it should use
    * for the data to test.
    *
    *
    * @param bool $expected_result
    * @param string $new_email
    * @param array|mixed $list_of_saved_emails
    *
    * @dataProvider data_is_new_and_valid_email
    */
   function test_is_new_and_valid_email( $expected_result, $new_email, $list_of_saved_emails ) {
      $actual_result = is_new_and_valid_email( $new_email, $list_of_saved_emails );
      $this->assertSame( $expected_result, $actual_result );
   }

   /**
    * Data provider.
    *
    * @return array {
    * @type array {
    * @type bool $expected_result
    * @type string $new_email
    * @type array $list_of_saved_emails
    * }
    * }
    */
   function data_is_new_and_valid_email() {
      return array(
         // Valid email address that doesn't exist.
         array(
            true,
            'new@email.com',
            array(
               'alternate@email.com',
               'alternate2@email.com',
            ),
         ),
         // Valid email that already exists.
         array(
            false,
            'new@email.com',
            array(
               'alternate@email.com',
               'alternate1@email.com',
               'new@email.com',
            ),
         ),
         // Invalid email.
         array(
            false,
            'not-an-email',
            array(),
         ),
      );
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

你对 dataProviders 有什么看法？它们有用吗？请在评论里告诉我！

用数据提供者进行更好的单元测试的帖子[首先出现在](https://ryanwelcher.com/2016/12/better-unit-testing-dataproviders/)[的 Ryan Welcher](https://ryanwelcher.com) 上。