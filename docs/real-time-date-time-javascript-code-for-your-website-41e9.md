# 为您的网站提供实时的日期时间 JavaScript 代码

> 原文：<https://dev.to/pauljacobson/real-time-date-time-javascript-code-for-your-website-41e9>

在过去的 6 个月左右的时间里，我经历了一次小小的旅行。我会在以后的博客文章中解释，但简短的版本是我已经将我的技能扩展到了 web 开发。随着我了解的越来越多，随着我继续更新我的 [Modi'in Bus](http://modiinbus.com) 项目，我一直在为我所面临的挑战开发解决方案。除了其他有用的空间，我想我应该在我的博客上分享一些小的解决方案。

我想为公共汽车上的[莫迪创建一个实时日期/时间字符串。我的目标是使用 JavaScript 插入一行表示当前日期和时间的代码。我搜索了一些关于如何有效使用 JavaScript `getDate()`方法的解释(这里是](http://modiinbus.com)[MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getDate))。

添加日期相对简单。我找不到一个干净的解决方案来添加一个实时变化的时钟，直到我发现了由 [Eugenio Leon](http://www.k3no.com/) 做的[这个伟大的实现](https://codepen.io/k3no/pen/akEQJB)。时间的挑战在于，你希望实时显示是动态的，而不仅仅是在你刷新时更新。

我想到的解决方案(在我提到的参考资料的帮助下)使用了 ES6 语法，并且看起来像预期的那样工作。

这是[我的解决方案](https://gist.github.com/pauljacobson/f388945b61be9bd86acb06a99f475dbc)。该脚本确定当前的日期和时间，并将其插入到我的站点代码中的类为`container`的`div`元素中:

```
// The purpose of this block is to list the month names 
// which will correspond with the corresponding numerical 
// value of the particular month that is other derived from the getDate() method.
Date.prototype.monthName = function() {
  const monthsOfYear = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'];
  return monthsOfYear[this.getMonth()];
}

// As with the previous block, this block states the days 
// of the week so the script can match the named days of 
// the week to corresponding numerical values.
Date.prototype.dayName = function() {
  const daysOfWeek = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
  return daysOfWeek[this.getDay()];
}

function realtimeClock() {
  const now = new Date(),
  today = now.dayName(),
  year = now.getFullYear(),
  month = now.monthName(),
  day = now.getDate(),
  secs = ('0' + now.getSeconds()).slice(-2),
  mins = ('0' + now.getMinutes()).slice(-2),
  hours = now.getHours(),
  container = document.querySelector('.clock');

  container.innerHTML = Today is ${today}, ${day} ${month} ${year}. The current time is ${hours}:${mins}:${secs};
  requestAnimationFrame(realtimeClock);
};

requestAnimationFrame(realtimeClock); 
```

我想我应该分享一下，因为我在 StackOverflow 上找不到任何像这样的东西。