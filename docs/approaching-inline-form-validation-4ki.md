# 接近内嵌表单验证

> 原文：<https://dev.to/niorad/approaching-inline-form-validation-4ki>

检查用户输入必须总是在服务器上进行，那么为什么要事先在浏览器中检查呢？因为 UX。用错误信息重新加载页面(从而删除密码字段)的日子已经一去不复返了，而且已经一去不复返了。您的用户应该不能提交缺少数据的表单。

我想证明内联验证不是火箭手术，用几行 JS 和当前的 web 标准就可以做到，不需要任何库和框架。

## 坚持浏览器标准

尽可能坚持网络标准，而不是重新实现浏览器特性，这是一个好方法。你可能会比浏览器供应商做得更差。现代浏览器都有某种即时验证，这将阻止表单提交，例如，当带有“必需”属性的输入没有值时。

在你尝试自己实现它之前，考虑坚持这个标准。对于现代浏览器中的简单表单，这就足够了。

阅读所有关于 MDN 上浏览器原生表单验证的信息。

## 推出您自己的内嵌验证

有时，内置的方式是不够的。你可能必须支持旧的浏览器，它们不做验证。通常客户或设计师对浏览器的风格不满意——反馈与你的网站风格不匹配。大多数情况下，验证会根据某些字段而变化，比如生日，默认情况下浏览器也不支持生日。

让我们看看如何处理这样的任务。

## HTML

我们将从一个最小的表单开始，用户必须输入他们的名字并勾选一个复选框。
(属性“novalidate”禁用浏览器的验证，这使得开发和测试您的代码更加容易。)

```
<form id="first-name-form" action="#" method="POST" novalidate>
    <label>
        Your first name:
        <input type="text" name="firstname">
    </label>
    <label>
        <input type="checkbox" name="acceptTerms">
        I accept the terms.
    </label>
    <button type="submit">Submit Form</button>
</form> 
```

首先，我们应该考虑将验证规则附加到相应字段的方法。一种可能的方法是定义一些通用规则，并将它们作为数据属性添加到我们的输入中。

```
<input type="text" name="firstname" data-validate="required noNumbers maxLength"> 
```

```
<input type="checkbox" name="acceptTerms" data-validate="mustBeChecked"> 
```

这样，一旦正确设置了验证规则，我们就可以轻松地添加和删除它们。

另一个缺失的东西是验证消息。用户需要知道，他们忘记了什么，或者输入错了什么。

对于每个验证规则，都应该有相应的消息。我们可以为反馈消息创建一个容器，并在发生验证错误时通过 JS 添加它们，或者我们可以将所有可能的消息添加到标记中，只显示适用的消息。我非常喜欢将所有消息都放在标记中，因为这样更容易处理语言。当服务器呈现页面时，我们可以得到正确的翻译，并且不需要在 JS 中了解它。

让我们为每个验证规则添加一条消息。我们将把它们添加到标签中的 input-tag 旁边。aria 和 role 属性有助于保持表单的可访问性。

```
<!-- Messages for the name-input -->
<small role="alert" aria-hidden="true" data-validation-message="noNumbers">
    Please don't enter any numbers.
</small>
<small role="alert" aria-hidden="true" data-validation-message="maxLength">
    Please enter 10 characters or fewer.
</small>
<small role="alert" aria-hidden="true" data-validation-message="required">
    Please enter a name.
</small> 
```

在我们的 CSS 中，我们将默认隐藏消息，只有当我们的脚本添加了类“message-visible”时才显示它们。

```
[data-validation-message] {
    display: none;
}
[data-validation-message].message-visible {
    display: block;
} 
```

这样，我们的表单在 JS 被停用或破坏的情况下看起来仍然没问题。

让我们找点乐子！现在，我们的 JS 实现完全没有建立在任何框架或库之上。我们使用 ES6 的语法和特性，这意味着如果你需要支持旧的浏览器，你需要使用类似 Babel 的东西。

## 方法

*   每个验证器(像“非数字的”)都是一个函数，它接收输入元素作为参数，并返回 true(有效)或 false(无效)。
*   我们将创建一个函数，从第一点开始调用函数，通过对照所有相应的验证器来检查字段的有效性。
*   每当用户的焦点离开它(模糊事件)时，它就会被调用。
*   如果一个验证器失败了，这个函数会向用户显示正确的错误消息。
*   当用户提交表单时，我们将检查每个表单字段一次，如果有任何无效字段，将阻止提交。

## 验证器

验证器是最简单的部分。我们将为我们需要的每个规则创建一个函数。让我们以“非数字”为例。

```
function noNumbers(element) {
    return !element.value.match(/[0-9]/g);
} 
```

我们还会有几个这样的东西，为什么不把它们收集在一个物体中呢？我们将添加示例中需要的另外两个元素——标记，同时添加一些 ES6 语法。

```
const validators = {
    required: element => element.value.length > 0,
    noNumbers: element => !element.value.match(/[0-9]/g),
    maxLength: element => element.value.length <= 10,
    mustBeChecked: element => element.checked
}; 
```

## 验证

现在我们需要一个函数来调用我们在 input-element 的 data 属性中提供的所有检查。它将解析其“数据验证”属性的内容，并将其转换为数组。由于规则的名称等于它们对应的函数的名称，我们可以通过调用“validators[rule](value)”来调用它们。

```
function validateElement(element) {
    const rules = element.dataset.validate.split(" ");
    rules.forEach(rule => {
        if(validators[rule](element)) {
            return;
        } else {
            markElementInvalid(element, rule);
        }
    });
} 
```

如果验证器返回“false”，我们知道验证失败了，需要显示正确的反馈消息并添加一些类。为此，我们将创建一个名为“markElementInvalid”的新函数。

```
function markElementInvalid(element, validatorName) {
    element.classList.add("invalid");
    element.setAttribute("aria-invalid", true);
    const feedbackMessage = element
                            .parentNode
                            .querySelector(
                                `[data-validation-message=${validatorName}]`
                            );
    feedbackMessage.classList.add("message-visible");
    feedbackMessage.setAttribute('aria-hidden', false);
} 
```

“markElementInvalid”将在输入字段和反馈消息中设置一些类和属性。“aria”属性将有助于增强可访问性。我们将使用“invalid”类来设置输入的无效状态，所以我们也需要在 CSS 中设置它。

```
input.invalid {
  border-color: brown;
} 
```

主要功能已经存在，只需要在正确的时间触发。

有几个时间点可以触发验证。

*   页面加载时
*   在用户聚焦输入字段后
*   在用户改变输入字段后
*   在用户不聚焦输入域之后
*   用户提交表单后

我们不想惹恼用户，所以我们需要小心。过早地展示失败的验证可能会给人一种咄咄逼人的感觉。这是一个很好的做法，检查一个领域后，不集中，或模糊，这意味着输入失去焦点，按下“tab”或点击以外的。在用户提交表单后，我们将再次检查每个字段，以防止发送错误的数据。

让我们对模糊字段启用验证。这段代码将初始化我们以前的工作。

```
const form = document.getElementById("first-name-form");
const formElements = Array.from(form.elements);

formElements.forEach(formElement => {
  formElement.addEventListener("blur", () => {
    validateElement(formElement);
  });
}); 
```

有一点需要注意:“模糊”事件对于文本输入非常有效。对于其他类型，“聚焦”或“改变”可能更好，这取决于所需的行为。

用户现在在输入错误数据后会得到反馈。但是在他们纠正错误后，没有办法消除错误状态。我们将介绍“reset validation”——函数，它基本上与“markElementInvalid”完全相反。这稍微简单一点，因为不需要考虑验证器。输入字段将被重置为其初始状态。

```
function resetValidation(element) {
    element.classList.remove("invalid");
    element.setAttribute("aria-invalid", false);
    element
        .parentNode
        .querySelectorAll("[data-validation-message]")
        .forEach(e => {
            e.classList.remove("message-visible");
            e.setAttribute("aria-hidden", true);
        });
} 
```

为了应用这个重置函数，我们将在每次验证时，在检查之前调用它。这样我们可以确保在我们做任何事情之前，它总是被设置为初始状态。我们将首先在“validateElement”函数中插入下面一行。

```
function validateElement(element) {
  resetValidation(element);
  //...
} 
```

最后，如果有任何无效字段，我们需要防止表单提交。用户可能会直接点击“提交”而不关注某个字段，这将没有机会纠正输入。我们将在表单中添加一个事件监听器，它检查所有字段，如果有些字段无效，就阻止提交。我们还将在整个表单中添加“invalid”类，这样用户就可以确定发生了错误。

```
form.addEventListener("submit", event => {
    let formIsValid = true;
    form.classList.remove("invalid");

    formElements.forEach(formElement => {
        if (!formElement.dataset) return;
        if (!formElement.dataset.validate) return;
        validateElement(formElement);
    });

    formIsValid = form.querySelectorAll(".invalid").length === 0;

    if (formIsValid === false) {
        form.classList.add("invalid");
        event.preventDefault();
    }
}); 
```

## 一个工作实例

就是这样！我们现在有了一个基本的表单内联验证。下面是一个工作示例，您可以随意使用它，并对它进行一些调整:

[https://codepen.io/niorad/embed/wpQXMz%20?height=500&default-tab=result&embed-version=2](https://codepen.io/niorad/embed/wpQXMz%20?height=500&default-tab=result&embed-version=2)

## 扩展和改进

我们已经建立了一个最低限度的工作验证。以下是一些可能的扩展方式:

*   向错误消息添加动画以获得更好的可视性。反馈信息可能会闪烁两秒钟，所以很明显出了问题。
*   对于长表单，可以添加滚动到第一个错误，以防用户在提交表单时还有错误。
*   表单变得非常复杂。有时，一个字段的有效性取决于另一个字段的值。例如:如果用户小于某个年龄，则可能需要一个字段。在这种情况下，必须扩展验证器来接收来自数据属性的参数。
*   要激活/禁用整组输入，我们可以在容器中添加一个“数据禁用验证”属性，如果输入有这样的父元素，就不要检查它。

目前就这些。如果你想给出反馈或建议，请在 Twitter 上联系我。

感谢阅读！