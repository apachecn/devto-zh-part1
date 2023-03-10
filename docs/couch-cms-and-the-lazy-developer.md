# 沙发 CMS 和懒惰的开发者

> 原文：<https://dev.to/antoinette0x53/couch-cms-and-the-lazy-developer>

我不自称是一个开发者，至少不是一个好的开发者。事实上，我认为称自己为开发者几乎是对其他开发者的侮辱。除了为工作编写一些小工具之外，我唯一深入的开发经验是在大学里为一家初创公司做网页开发。我用 Wordpress(用 PHP 编写)工作过，后来用 Magento 和 Liquid 编码。我对此没意见，但它让我知道和 CMS 一起工作有多乏味。现在，我经营着 Reboot Iowa，这是一家我创办的非营利性机构，旨在教授基本的计算机编程概念，鼓励技术素养。我们设计并建立了自己的网站，它看起来很棒(除了一些编辑需要发生在捐赠页面，嘿...我对此很诚实)。缺点是它完全是静态的，这对于动态加载关于即将到来的研讨会和活动的信息没有帮助。更新任何东西也是一件痛苦的事情，当你是一个任何类型的萌芽组织时，这种事情经常发生。

## 进入治疗床 CMS

我花了几天时间谷歌了一些轻量级 CMS 的解决方案。我真的不想写一个 Wordpress 主题，或者在整合 CMS 的时候重新开始一个新的网站。并不是因为我认为我做不到，而是我太懒了。正如我以前说过的，我不认为自己是一个开发人员，所以我花了不必要的时间来弄清楚一切，我只是不想花时间。进入沙发 CMS，一个适合我这种懒人的 CMS。易于设置和使用，甚至带有一个管理面板。祈祷应验了。别误会我的意思。我可以看到这个工具对于有需求的非技术人员来说是非常有用的，而且非技术人员也不会让他们变懒。我称自己懒惰，因为我有技术技能和知识来掌握一个更健壮的 CMS，甚至使用一个框架来构建自己的 CMS，只是选择不这样做。

我用 MAMP 在我的笔记本电脑上建立了一个本地开发环境来测试沙发。Couch CMS 是用 PHP 编写的，这是我最终使用的任何解决方案的一个要求，因为它是我最熟悉的 web 语言。PHP 是我学习的第一门网络语言，所以现在已经接近第二天性了。

我真的很喜欢我可以轻松地将动态部分插入静态站点。我所要做的就是从沙发目录(Couch)中获取一个文件，并在文件末尾调用。我最终使用它轻松地为我自己和我的董事会成员更新了 bios，并更新了各种字段，如我们的“关于我们”、“愿景和使命”以及其他各种可能在未来发生变化的文本。我最喜欢的部分是能够让即将到来的事件出现在网站上，并在事件日期过后消失。现在，Couch 正在我们的产品 [Reboot Iowa 网站](//rebootiowa.org)上运行，到目前为止运行良好。我仍然需要添加必要的代码来使我们的公告和新闻动态化，但是..良好的...我很懒记得吗？

## 有点味道

以下是我如何在 Reboot Iowa 网站的主页上添加动态事件部分的示例。

我首先在一个名为 events.php 的文件中为一个事件定义了一个模板。使用这个模板，事件对象出现在管理面板中。

```
// events.php
<?php require_once( 'couch/cms.php' ); ?> 
<cms:template title='Event' clonable='1' > 
    <cms:editable name='event_description' type='richtext' />
    <cms:editable name='event_image'
        crop='1'
        width='610'
        height='150'
        type='image'
    />
    <cms:editable name='location' label='Location' type='text' />
    <cms:editable name="event_link" label='Event Sign Up Link' type='text'/>

    <cms:editable name="start_time" label="Time From (24 Hrs)"
      opt_values=' Unspecified |
                  00:00 | 00:30 | 01:00 | 01:30 | 02:00 | 02:30 | 03:00 | 03:30 |
                  04:00 | 04:30 | 05:00 | 05:30 | 06:00 | 06:30 | 07:00 | 07:30 |
                  08:00 | 08:30 | 09:00 | 09:30 | 10:00 | 10:30 | 11:00 | 11:30 |
                  12:00 | 12:30 | 13:00 | 13:30 | 14:00 | 14:30 | 15:00 | 15:30 |
                  16:00 | 16:30 | 17:00 | 17:30 | 18:00 | 18:30 | 19:00 | 19:30 |
                  20:00 | 20:30 | 21:00 | 21:30 | 22:00 | 22:30 | 23:00 | 23:30 |'
      type='dropdown'
    />

    <cms:editable name="end_time" label="Time Until (24 Hrs)"
      opt_values=' Unspecified |
                  00:00 | 00:30 | 01:00 | 01:30 | 02:00 | 02:30 | 03:00 | 03:30 |
                  04:00 | 04:30 | 05:00 | 05:30 | 06:00 | 06:30 | 07:00 | 07:30 |
                  08:00 | 08:30 | 09:00 | 09:30 | 10:00 | 10:30 | 11:00 | 11:30 |
                  12:00 | 12:30 | 13:00 | 13:30 | 14:00 | 14:30 | 15:00 | 15:30 |
                  16:00 | 16:30 | 17:00 | 17:30 | 18:00 | 18:30 | 19:00 | 19:30 |
                  20:00 | 20:30 | 21:00 | 21:30 | 22:00 | 22:30 | 23:00 | 23:30 |'
      type='dropdown'
    />

    <cms:editable name='end_date'
        label='Event End Date (if multi-days event)'
        desc='Enter date in yyyy-mm-dd format e.g. 2010-12-31'
        type='text'
        validator='regex=/(?:19|20)\d\d-(?:0[1-9]|1[012])-(?:0[1-9]|[12][0-9]|3[01])/'
        separator='#'
        validator_msg='regex=Incorrect date format'
    />
</cms:template>
<?php $title="Event"; include('header.php'); ?>
<div class="row" >
    <div class="col-sm-12" style="margin-top:35px;">
        <h2 style="margin-left:10px;">Upcoming Events</h2>
        <div class="row">

        </div><hr>
        <div class="row">

        </div>
    </div>
</div>
<?php include('footer.php'); ?>
<?php COUCH::invoke(); ?> 
```

Enter fullscreen mode Exit fullscreen mode

在创建了模板和一些事件之后，我向索引文件添加了必要的代码，以显示关于即将到来的事件的信息。

```
// index.php
...
...
...
<h2 style="margin-left:10px;">Announcements and Upcoming Events</h2>
        <div class="row">
            <div class="col-sm-3">
                <p class="lead section-lead"><i class="fa fa-calendar fa-2x" aria-hidden="true" style="padding:10px;"></i> <strong>Events </strong></p>
            </div>
            <div class="col-sm-9">
                <cms:set today="<cms:date format='Y-m-d' />" />
                <cms:pages masterpage='events.php' orderyby='end_date' order='asc'>
                <cms:if today lt end_date>
                  <p class="lead section-lead" style="/*margin-left:10px;*/"><cms:show k_page_title /> | <cms:date end_date format='F j' /> </p>

                  <p style="margin-left:10px;">
                    <cms:show event_description />

                      Time: <cms:date start_time format='g a' />-<cms:date end_time format='g a'/></br>
                      Location: <cms:show location />
                      <cms:if event_link !='Unspecified'>
                        <br><a target="_blank" href="<cms:show event_link />">Sign up Here</a>
                      </cms:if>
                  </p>
                </cms:if>
                </cms:pages>
            </div>
...
...
... 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到我设置了一个变量`today`，通过使用`cms:set`标签来比较事件日期和当前日期，以确定要显示哪些事件。

```
 <cms:set today="<cms:date format='Y-m-d' />" />
                <cms:pages masterpage='events.php' orderyby='end_date' order='asc'>
                <cms:if today lt end_date> 
```

Enter fullscreen mode Exit fullscreen mode

许多标签，如`cms:set`和`cms:if`，允许我在使用沙发显示我真正欣赏的信息时有小范围的灵活性。

设置文档可以在这里找到[。简而言之，你所做的就是解压一个文件夹，并把它放在一个目录中。用 SQL 数据库的凭证更新配置文件后，导航到管理面板，完成安装，一切就绪。我不会涉及所有的细节，但是如果你想了解更多关于 CouchCMS 的信息，他们有非常好的](http://docs.couchcms.com/tutorials/portfolio-site/building-a-real-world-site.html)[文档](http://docs.couchcms.com/)。我过去常常以文档为指南，找出我需要的一切。

我更想知道你们听说过的其他轻量级 CMS，或者你们在运行一个动态网站以使其更容易使用时学到的技巧。如果你最终尝试沙发，让我知道你的想法。