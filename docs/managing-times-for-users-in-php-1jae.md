# 在 PHP 中管理用户的时间

> 原文：<https://dev.to/sarfraznawaz2005/managing-times-for-users-in-php-1jae>

大多数时候，我们编写的应用程序是为了供世界各地来自不同时区的用户使用。想象一下，一个来自美国的用户发布了一个状态更新，而另一个来自亚洲的用户看到了完全不同的时间，即使原来的用户可能已经发布了状态更新。

处理这个问题最简单的方法是允许你的应用程序中的用户选择他们的时区；例如，您可以向他们展示所有时区的下拉列表。这是第一步。

现在，一旦你知道用户的时区，我们可以很容易地来回转换。我们可以使用 GMT 来实现这个目的，因为它可以很容易地转换用户的时区。例如，当保存时间到数据库时，我们首先将它转换成 GMT。当从数据库中读取并在实际网页上显示时，我们将其从 GMT 转换为用户的实际时区。这样，我们总是保存 GMT 时间，并根据用户的时区显示给他们。

我做了这个简单的类，你可以在你的应用程序中使用:

```
/**
 * A class to ease timezone managment for users in an applications
 *
 * @author Sarfraz Ahmed <sarfraznawaz2005@gmail.com>
 * @license http://www.opensource.org/licenses/mit-license.html MIT License
 */
class UserTimeZone
{

    /**
     * Time format to be used
     *
     * @var string
     */
    protected $format = null;

    /**
     * Timezone to be used
     *
     * @var string
     */
    protected $defaultTimeZone = null;

    /**
     * @param string $format
     * @param string $defaultTimeZone
     */
    public function __construct($format = 'Y-m-d h:i:s P', $defaultTimeZone = 'UTC')
    {
        $this->format = $format;
        $this->defaultTimeZone = $defaultTimeZone;
    }

    /**
     * Returns all timezones in an array.
     * Can be used to construct a dropdown of all timezones
     * for users to select
     *
     * @return array
     */
    public function getTimeZones()
    {
        return DateTimeZone::listIdentifiers();
    }

    /**
     * Sets dates in GMT format.
     * Should be used when saving dates in database for example.
     *
     * @param $date
     * @return string
     */
    public function setDate($date)
    {
        $date = new DateTime($date, new DateTimeZone($this->defaultTimeZone));
        $date->setTimezone(new DateTimeZone('GMT'));

        return $date->format($this->format);
    }

    /**
     * Gets data based on users' timezones.
     * Should be used when showing dates in pages for example.
     *
     * @param $date
     * @return string
     */
    public function getDate($date)
    {
        $date = new DateTime($date, new DateTimeZone('GMT'));
        $date->setTimezone(new DateTimeZone($this->defaultTimeZone));

        return $date->format($this->format);
    }
} 
```

这是不言自明的。请在课堂上读评论。

用法:

```
$format = 'd-m-Y h:i:s';
$userTZ = 'Asia/Karachi'; // user's time zone
$tz = new UserTimeZone($format, $userTZ);

$date = $tz->setDate('2015-01-01 6:32 PM');
// save $date in db for example

$date = $tz->getDate('2015-01-01 9:32 PM');
// show $date on webapge for example 
```

[![](img/14aabe049fec879db069d91864d115c8.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--ZwfP5H1D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://feeds.feedburner.com/%257Er/codeinphpfeed/%257E4/x-xDqYxHeFA)