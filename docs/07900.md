# Rails 上的 JavaScript:跳出 DOM 思考

> 原文：<https://dev.to/underdogio/javascript-on-rails-thinking-outside-the-dom>

*这篇文章最初出现在 [Underdog.io 博客](http://blog.underdog.io/post/132489575627/eloquent-javascript-on-rails-thinking-outside-the-dom)上，由[马修·乔丁](https://twitter.com/mjording)撰写，并得到[史蒂夫·布鲁兹](https://twitter.com/SteveBrudz)的协助。*

当一个公司有一个用 Ruby on Rails 和 JavaScript 构建的成功产品，并且随着时间的推移变得非常复杂，难以维护和扩展时，Cyrus Innovation(现在是 Def 方法的一部分)经常会被调用来提供帮助。

复杂性的一个常见来源是前端代码。为大多数 Rails 应用程序编写的 JavaScript 仍然被认为是视图的一部分，并没有像后端代码一样按照严格的标准进行开发。然而，在大多数 web 应用程序项目的生命周期中,“前端”JavaScript 代码库在大小上(如果不是复杂性的话)很快与后端相匹配。

当评估一个新的代码库时，我们会检查以下几个方面，以便找出问题点的清单:

*   **内联 JavaScript** 。内联 JavaScript 增加了页面的下载时间，避免了代码模块化，并降低了单元测试的能力。
*   **建造者&经典传承**。JavaScript 是一种原型组合语言，因此原型继承应该优先于经典继承。
*   **特性测试检查 JavaScript 功能**。尽管他们给涉众一点信心，特性测试并不测试交付的代码，而仅仅是测试用户体验。这意味着，尽管作为特性示例的交互被测试，但产生功能的代码没有被测试。毫无疑问，混乱和未经测试的代码会导致意外的行为。功能测试是不够的。
*   **未经测试的第三方库**。开发人员很容易找到现成的库，为某个特性请求提供 UX 增强。不利的一面是负责任的开发人员减少了对底层实现的理解。当所讨论的库未经测试时，这种危险会加剧。

**测试**

重构有问题的前端的第一步是围绕测试建立一些最佳实践。可靠的单元测试对于设计合理、结构良好的代码来说是必不可少的。

单元测试不同于黄瓜/水豚用户特征测试或自动化质量保证测试。优先考虑功能测试会导致项目中更深层次的问题。更多信息，请研究[倒置自动化](http://martinfowler.com/bliki/TestPyramid.html) [测试金字塔](https://www.mountaingoatsoftware.com/blog/the-forgotten-layer-of-the-test-automation-pyramid) / [冰淇淋蛋筒](http://watirmelon.com/2012/01/31/introducing-the-software-testing-ice-cream-cone/)。此外，J.B. Rainsberger 的演讲“[集成测试是骗局](https://vimeo.com/80533536)”是一个很好的资源。

**测试工具**

虽然所有的 Rails 开发人员都应该熟悉 ruby/Rails 测试工具，比如 RSpec、minitest、capybara 等等，但是有些人可能不熟悉测试 JavaScript 代码的好工具。我们目前推荐[茉莉花](http://jasmine.github.io/)和[茶匙](https://github.com/modeset/teaspoon)。

Jasmine 是 JavaScript 单元测试的标准。它保持了与 RSpec、Test::Unit 或 minitest 类似的语法，增加了一些用于模仿和存根的附加实用程序。

茶匙是 Rails 中 JavaScript 测试的测试工具。它为 JavaScript 测试增添了传统 Rails 的魅力。通过一个简单的 rake 任务支持驾驶测试。它还支持标准的 Rails 资产管道。

**编写 JavaScript 单元测试**

单元测试应该限制他们的范围，只测试你写的 JavaScript 函数。而不是测试第三方代码、原生浏览器功能等。，您应该利用 Jasmine 的模拟/存根或测试双重实用程序 Spies。

例如，给定:

```
var UserInformation = function() {
  this.update = function(user_id, params) {
    jQuery.ajax({
      method: "PATCH",
      url: "/user_information/" + user_id,
      data: params
    });
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

一个合适的单元测试应该基于 jQuery AJAX 函数:

```
//= require jquery
//= require user_information
describe("UserInformation", function() {
      describe("#update", function() {
        it("calls AJAX with the correct parameters and endpoint", function() {
          spyOn(jQuery, "ajax");
          var params = {
            "company": "Cyrus Innovation"
          };
          var user_information = new UserInformation();
          user_information.update(1, params);
          expect(jQuery.ajax).toHaveBeenCalledWith({
            method: "PATCH",
            url: "/user_information/1",
            data: params
          });
        });
      }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的测试中的 JavaScript，UserInformation.js，只运行到它与 jQuery 接口的地方。

**林挺**

我们还推荐运行一个类似于 [JSHint](http://jshint.com/) 的 linter，它应用一个简单的代码风格检查器来执行组合中的最佳实践。

**接下来的步骤**

在大型 Rails 应用程序中还有许多其他常见的复杂领域，比如臃肿的模型或混乱的视图层。我们很乐意继续，但我们已经达到了我们的空间限制！