# 绝望的程序员的红色-第二部分

> 原文：<https://dev.to/lepinekong/red-for-hopeless-programmers---part-ii-258>

在第一部分中，我们学习了如何从谷歌金融下载历史报价。在第二部分中，我们将学习如何解析数据和 html 文件来生成 Google 图表。

## 简单解析:拆分引号行

要解析逗号分隔的行，只需使用 split 函数:

```
lines: read/lines %quotes.csv
line: lines/2; extract 2nd line (first line is header)
quote: split line "," 
```

作为完整的例子，你从 Google Finance 下载的文件是 Csv 格式的。如果你想处理它，例如计算 20 移动平均线，你必须:

1.  逐行读取文件，同时你需要跳过标题的第一行

2.  颠倒
    行的顺序，因为原来的行是按降序日期
    排序的，这样就得到按升序日期排序的行

3.  通过用逗号分隔符
    分割每行来解析引号
    的每一行，并将其存储到 quotes 变量中

4.  然后，您可以通过提取收盘列
    计算 p=20 移动平均线(MA)
    ，累计值
    ，然后除以累计的报价数

5.  将 MA 添加到最后一列

6.  选择性地写回另一个 csv 文件

这将转化为红色代码(如果您太懒，可以将整个代码复制并粘贴到 Red Console 中):

```
; init quotes variable so as to store quotes 
quotes: []
; init cum variable so as to store cumulated value of close
cum: 0
; init n variable so as to divide the cumulated value by n
n: 0

; 1\. Read the quotes file line by line
lines: read/lines %quotes.csv; -> ["Date,Open,High,Low,Close,Volume" "d1,o1,h1,l1,c1,v1" "d2,o2,h2,l2,c2,v2" ...]

; meanwhile you need to skip "Date,Open,High,Low,Close,Volume" on first line 
lines: skip lines 1

; 2\. Reverse the order of lines
; so as to get the lines sorted by ascending date
lines: reverse lines

; 3\. Parse each line of quote
foreach line lines [

    ; splitting each line by comma delimiter
    quote: split line ","; -> [date open high low close volume]

    ;For later use if needed convert string to float open high low close volume
    i: 2; starting at column 2 (skipping date column)
    foreach column skip quote 1 [; skip first date column 
      quote/:i: to-float quote/:i; quote:i refers to the ith column of quote
      i: i + 1
    ]

    ; store it into quotes variable
    append/only quotes quote; -> [[d1 o1 h1 l1 c1 v1] [d2 o2 h2 l2 c2 v2] ...]
    ;append adds quote while evaluating expressions inside the brackets, append/only will add quote literally with bracket

    ; 4\. Calculate the p=20 moving average for each line
    p: 20
    close: (to-float quote/5); quote/5: close is the 5th column of quote
    cum: cum + close
    n: n + 1
    either (n < p) [
        ma: ""; no moving average
    ][
        ma: cum / n; moving average by dividing by number of quotes cumulated
    ]

    ;5\. Add MA to last column
    append quotes/:n ma; :n refer to the nth line of quotes array
    ;-> [
        ;[d1 o1 h1 l1 c1 v1 ma1] 
        ;[d2 o2 h2 l2 c2 v2 ma2] ...
    ;]
] 
```

[![parse-quotes](img/6bfdf106e9e9f458eae7ffbae1a2c58f.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--FGAKWEU7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5kod6scrwz45tfzoxrrc.png)

或者，要检查报价，您可以在红色控制台中键入以下内容(要清除屏幕，您可以键入 Ctrl+L):

```
print n: length? quotes
print quotes/:n
probe quotes/:n; will keep the brackets and formats
write-clipboard mold quotes; mold format block to string

; to output csv files
write %quotes-ma0.csv ""
write %quotes-ma1.csv ""
write %quotes-ma2.csv ""
foreach quote quotes [
  write/lines/append %quotes-ma0.csv mold quote;
  quote: form quote
  write/lines/append %quotes-ma1.csv quote; space separator
  quote: replace/all quote " " "," ; replace space by comma separator
  write/lines/append %quotes-ma2.csv quote; comma separator
] 
```

[![Check-quotes](img/c9fec74884754bb442e2942ac0a0cf99.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--4cKaliER--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ggoauyx1e731axicsoij.png)

## 复杂解析:解析 html 文件

现在，假设您想要使用 Google 烛台图表[[https://developers . Google . com/Chart/interactive/docs/gallery/candlestickchart](https://developers.google.com/chart/interactive/docs/gallery/candlestickchart)]展示您的报价

您需要用自己的数据替换下面 drawChart 函数中的样本数据:

```
<html>
    <head>
      <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
      <script type="text/javascript">
        google.charts.load('current', {'packages':['corechart']});
        google.charts.setOnLoadCallback(drawChart);

    function drawChart() {
      var data = google.visualization.arrayToDataTable([
        ['Mon', 20, 28, 38, 45, 0],
        ['Tue', 31, 38, 55, 66, 0],
        ['Wed', 50, 55, 77, 80, 10],
        ['Thu', 77, 77, 66, 50, 10],
        ['Fri', 68, 66, 22, 15, 10]
        // Treat first row as data as well.
      ], true);

      var options = {
        legend:'none',
        series: {
            1: {
                type: 'line'
            }
    }    
      };

      var chart = new google.visualization.CandlestickChart(document.getElementById('chart_div'));

      chart.draw(data, options);
    }
      </script>
    </head>
    <body>
      <div id="chart_div" style="width: 100%; height: 500px;"></div>
    </body>
  </html> 
```

步骤是:

1.  将 html 内容存储在变量中
2.  搜索字符串" var data = Google . visualization . arraytodatatable(["
3.  在上面的字符串之后，搜索字符串"//也将第一行视为数据。"
4.  移除两个字符串之间的样本数据
5.  在两个字符串之间插入数据

首先，您需要通过在红色控制台中复制并粘贴以下代码来准备 json 格式的数据:

```
;Prepare format quotes in json format required by Google Charts
json-quotes: {}
foreach quote quotes [
  remove back back tail quote; remove volume column 2nd column from tail of quote
  quote: mold quote; format block to string
  quote: replace/all quote " " ","; replace space separator with comma
  quote: replace/all quote {""} "null"; replace empty moving average with null value
  append json-quotes quote; append quote to json
  append json-quotes ","; append json comma separator between each quote
  append json-quotes newline; append json new line separator between each quote
] 
```

[![json-quotes](img/ab0c8b647b4316cb9b89d960b27d87ce.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--X3-ogx8M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/d4ocwh0x6mn292rg0zw5.png)

然后把上面的 html 模板复制到 template.html。然后，您可以使用该规则读取和解析 html 内容:

```
;1\. Store the html content in a variable
html-content: read %template.html

parse-rule: [

    ;2\. Search through string "var data = google.visualization.arrayToDataTable(["
    thru {var data = google.visualization.arrayToDataTable([} start:

    ;3\. Then search for string "// Treat first row as data as well."
    to {// Treat first row as data as well.} end:

    ; do not forget to enclose these actions between parenthesis
    ; or it won't work 
    (
      ;4\. Remove sample data between the two strings
      change/part start "" end

      ;5\. Insert your data between the two strings
      insert start json-quotes
    )

]

parse html-content parse-rule
write %chart.html html-content 
```

[![parse-html](img/c1b61f2e9930b6e98b101be824e96170.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--TBM3yAsk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n5987l4o87g92d7ctvm8.png)

如果你运行 chart.html，你应该得到这个:

[![chart](img/88692975aa454657b36b334e93b9ade7.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--sE--swmN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/87a4925rz1ooy79n8fhq.png)

您可以在红色控制台中复制和粘贴整个源代码:

```
 ; init quotes variable so as to store quotes 
quotes: []
; init cum variable so as to store cumulated value of close
cum: 0
; init n variable so as to divide the cumulated value by n
n: 0

; 1\. Read the quotes file line by line
lines: read/lines %quotes.csv; -> ["Date,Open,High,Low,Close,Volume" "d1,o1,h1,l1,c1,v1" "d2,o2,h2,l2,c2,v2" ...]

; meanwhile you need to skip "Date,Open,High,Low,Close,Volume" on first line 
lines: skip lines 1

; 2\. Reverse the order of lines
; so as to get the lines sorted by ascending date
lines: reverse lines

; 3\. Parse each line of quote
foreach line lines [

    ; splitting each line by comma delimiter
    quote: split line ","; -> [date open high low close volume]

    ;For later use if needed convert string to float open high low close volume
    i: 2; starting at column 2 (skipping date column)
    foreach column skip quote 1 [; skip first date column 
      quote/:i: to-float quote/:i; quote:i refers to the ith column of quote
      i: i + 1
    ]

    ; store it into quotes variable
    append/only quotes quote; -> [[d1 o1 h1 l1 c1 v1] [d2 o2 h2 l2 c2 v2] ...]
    ;append adds quote while evaluating expressions inside the brackets, append/only will add quote literally with bracket

    ; 4\. Calculate the p=20 moving average for each line
    p: 20
    close: (to-float quote/5); quote/5: close is the 5th column of quote
    cum: cum + close
    n: n + 1
    either (n < p) [
        ma: ""; no moving average
    ][
        ma: cum / n; moving average by dividing by number of quotes cumulated
    ]

    ;5\. Add MA to last column
    append quotes/:n ma; :n refer to the nth line of quotes array
    ;-> [
        ;[d1 o1 h1 l1 c1 v1 ma1] 
        ;[d2 o2 h2 l2 c2 v2 ma2] ...
    ;]
]

json-quotes: {}
foreach quote quotes [
  remove back back tail quote; remove volume column 2nd column from tail of quote
  quote: mold quote; format block to string
  quote: replace/all quote " " ","; replace space separator with comma
  quote: replace/all quote {""} "null"; replace empty moving average with null value
  append json-quotes quote; append quote to json
  append json-quotes ","; append json comma separator between each quote
  append json-quotes newline; append json new line separator between each quote
]

;1\. Store the html content in a variable
html-content: read %template.html

parse-rule: [

    ;2\. Search through string "var data = google.visualization.arrayToDataTable(["
    thru {var data = google.visualization.arrayToDataTable([} start:

    ;3\. Then Search for string "// Treat first row as data as well."
    to {// Treat first row as data as well.} end:

    ; do not forget to enclose these actions between parenthesis
    ; or it won't work 
    (
      ;4\. Remove sample data between the two strings
      change/part start "" end

      ;5\. Insert your data between the two strings
      insert start json-quotes
    )

]

parse html-content parse-rule

write %chart.html html-content 
```