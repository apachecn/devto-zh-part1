# 节点模块深入研究:查询字符串

> 原文：<https://dev.to/captainsafia/node-module-deep-dive-querystring-ea9>

所以，我想我会在这个好的博客上开始一个新的系列。有一段时间，我一直想在节点生态系统中完成标准库和流行包的代码遍历。我想是时候把这种想法变成行动，并写下来了。这是我第一次带注释的代码演练。

我想从查看节点标准库中最基本的模块之一开始:`querystring`。`querystring`是一个模块，允许用户提取 URL 的查询部分的值，并从键值关联的对象构建查询。这里有一个简短的代码片段，展示了四个不同的 API 函数，`string`、`parse`、`stringify`和`unescape`，这些函数是由`querystring`模块公开的。

```
> const querystring = require("querystring");
> querystring.escape("key=It's the final countdown");
'key%3DIt\'s%20the%20final%20countdown'
> querystring.parse("foo=bar&abc=xyz&abc=123");
{ foo: 'bar', abc: ['xyz', '123'] }
> querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: 'i' });
'foo=bar&baz=qux&baz=quux&corge=i'
> querystring.unescape("key%3DIt\'s%20the%20final%20countdown");
'key=It\'s the final countdown' 
```

Enter fullscreen mode Exit fullscreen mode

好吧！让我们开始有趣的部分。在我写这篇文章时，我将检查 querystring 的代码。你可以在这里找到这个版本的副本。

首先引起我注意的是第 47-64 行的这段代码。

```
const unhexTable = [
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 0 - 15
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 16 - 31
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 32 - 47
  +0, +1, +2, +3, +4, +5, +6, +7, +8, +9, -1, -1, -1, -1, -1, -1, // 48 - 63
  -1, 10, 11, 12, 13, 14, 15, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 64 - 79
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 80 - 95
  -1, 10, 11, 12, 13, 14, 15, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 96 - 111
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 112 - 127
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, // 128 ...
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1,
  -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1, -1 // ... 255
]; 
```

Enter fullscreen mode Exit fullscreen mode

这到底是什么鬼话？我在整个代码库中搜索了术语`unhexTable`,以找出它的使用位置。除了这个定义，搜索还返回了另外两个结果。它们出现在代码库的第 86 和 91 行，这是包含这些引用的代码块。

```
 if (currentChar === 37 /*'%'*/ && index < maxLength) {
      currentChar = s.charCodeAt(++index);
      hexHigh = unhexTable[currentChar];
      if (!(hexHigh >= 0)) {
        out[outIndex++] = 37; // '%'
      } else {
        nextChar = s.charCodeAt(++index);
        hexLow = unhexTable[nextChar];
        if (!(hexLow >= 0)) {
          out[outIndex++] = 37; // '%'
          out[outIndex++] = currentChar;
          currentChar = nextChar;
        } else {
          hasHex = true;
          currentChar = hexHigh * 16 + hexLow;
        }
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

所有这些都发生在`unescapeBuffer`函数中。快速搜索之后，我发现`unescapeBuffer`函数是由从我们的模块公开的`unescape`函数调用的(见第 113 行)。因此，这就是所有有趣的取消查询字符串转义的操作发生的地方！

好吧！那么这个`unhexTable`是怎么回事？我开始通读`unescapeBuffer`函数，以弄清楚它到底在做什么。我从第 67 行开始。

```
var out = Buffer.allocUnsafe(s.length); 
```

Enter fullscreen mode Exit fullscreen mode

因此，该函数首先初始化一个长度为传递给函数的字符串的缓冲区。

(在这一点上，我可以深入到`Buffer`类中的`allocUnsafe`正在做什么，但是我将把它保留到另一篇博客文章中。)

之后，有几个语句初始化不同的变量，这些变量将在函数的后面使用。

```
 var index = 0;
  var outIndex = 0;
  var currentChar;
  var nextChar;
  var hexHigh;
  var hexLow;
  var maxLength = s.length - 2;
  // Flag to know if some hex chars have been decoded
  var hasHex = false; 
```

Enter fullscreen mode Exit fullscreen mode

下一段代码是一个 while 循环，它遍历字符串中的每个字符。如果字符是一个`+`并且函数被设置为将`+`更改为空格，它将转义字符串中该字符的值设置为空格。

```
 while (index < s.length) {
    currentChar = s.charCodeAt(index);
    if (currentChar === 43 /*'+'*/ && decodeSpaces) {
      out[outIndex++] = 32; // ' '
      index++;
      continue;
    } 
```

Enter fullscreen mode Exit fullscreen mode

第二组 if 语句检查迭代器是否在以`%`开头的字符序列中，这意味着接下来的字符将代表一个十六进制代码。然后，程序获取下一个字符的字符代码。然后程序使用该字符代码作为索引在`hexTable`列表中查找。如果该查找返回的值是`-1`，该函数将输出字符串中字符的值设置为百分号。如果从`hexTable`中查找返回的值大于`-1`，该函数将把退出字符解析为十六进制字符代码。

```
 if (currentChar === 37 /*'%'*/ && index < maxLength) {
      currentChar = s.charCodeAt(++index);
      hexHigh = unhexTable[currentChar];
      if (!(hexHigh >= 0)) {
        out[outIndex++] = 37; // '%'
      } else {
        nextChar = s.charCodeAt(++index);
        hexLow = unhexTable[nextChar];
        if (!(hexLow >= 0)) {
          out[outIndex++] = 37; // '%'
          out[outIndex++] = currentChar;
          currentChar = nextChar;
        } else {
          hasHex = true;
          currentChar = hexHigh * 16 + hexLow;
        }
      }
    }
    out[outIndex++] = currentChar;
    index++;
  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们更深入地研究一下这部分代码。因此，如果第一个字符是有效的十六进制代码，它使用下一个字符的字符代码作为`unhexTable`的查找索引。该值在`hexLow`变量中开始。如果该变量等于`-1`，则该值不会被解析为十六进制字符序列。如果不等于`-1`，则字符被解析为十六进制字符代码。该函数取最高(第二)位(`hexHigh`)的十六进制代码值，将其乘以 16，并与第一位的十六进制代码值相加。

```
 } else {
        nextChar = s.charCodeAt(++index);
        hexLow = unhexTable[nextChar];
        if (!(hexLow >= 0)) {
          out[outIndex++] = 37; // '%'
          out[outIndex++] = currentChar;
          currentChar = nextChar;
        } else {
          hasHex = true;
          currentChar = hexHigh * 16 + hexLow;
        }
      } 
```

Enter fullscreen mode Exit fullscreen mode

函数的最后一行让我困惑了一段时间。

```
return hasHex ? out.slice(0, outIndex) : out; 
```

Enter fullscreen mode Exit fullscreen mode

如果我们在查询中检测到一个十六进制序列，那么将输出字符串从`0`分割到`outIndex`，否则保持原样。这让我很困惑，因为我假设`outIndex`的值等于程序结束时输出字符串的长度。我本可以自己花时间去弄清楚这个假设是否是真的，但是，说实话，已经快午夜了，我的生活中没有时间在深夜做这种无聊的事情。所以我在代码库上运行了`git blame`,试图找出与特定变更相关联的提交。结果发现这没多大帮助。我期望有一个独立的提交来描述为什么那一行是那样的，但是最近对它的修改是对`escape`函数的更大重构的一部分。我看得越多，就越确定这里不需要三元运算符，但是我还没有找到一些可重复的证据。

我研究的下一个函数是`parse`函数。我们函数的第一部分做一些基本的设置。默认情况下，该函数在 querystring 中解析出 1000 个键值对，但是用户可以在`options`对象中传递一个`maxKeys`值来改变这一点。除非用户在选项对象中提供了一些不同的东西，否则该函数还使用我们在上面研究过的`unescape`函数。

```
function parse(qs, sep, eq, options) {
  const obj = Object.create(null);

  if (typeof qs !== 'string' || qs.length === 0) {
    return obj;
  }

  var sepCodes = (!sep ? defSepCodes : charCodes(sep + ''));
  var eqCodes = (!eq ? defEqCodes : charCodes(eq + ''));
  const sepLen = sepCodes.length;
  const eqLen = eqCodes.length;

  var pairs = 1000;
  if (options && typeof options.maxKeys === 'number') {
    // -1 is used in place of a value like Infinity for meaning
    // "unlimited pairs" because of additional checks V8 (at least as of v5.4)
    // has to do when using variables that contain values like Infinity. Since
    // `pairs` is always decremented and checked explicitly for 0, -1 works
    // effectively the same as Infinity, while providing a significant
    // performance boost.
    pairs = (options.maxKeys > 0 ? options.maxKeys : -1);
  }

  var decode = QueryString.unescape;
  if (options && typeof options.decodeURIComponent === 'function') {
    decode = options.decodeURIComponent;
  }
  const customDecode = (decode !== qsUnescape); 
```

Enter fullscreen mode Exit fullscreen mode

然后，该函数遍历 querystring 中的每个字符，并获取该字符的字符代码。

```
 var lastPos = 0;
  var sepIdx = 0;
  var eqIdx = 0;
  var key = '';
  var value = '';
  var keyEncoded = customDecode;
  var valEncoded = customDecode;
  const plusChar = (customDecode ? '%20' : '  ');
  var encodeCheck = 0;
  for (var i = 0; i < qs.length; ++i) {
    const code = qs.charCodeAt(i); 
```

Enter fullscreen mode Exit fullscreen mode

然后，该函数检查被检查的字符是否对应于键值分隔符(如 querystring 中的' & '字符),并执行一些特殊的逻辑。它检查在' & '后面是否有' key=value '段，并试图从中提取适当的键和值对(第 304 - 347 行)。

如果字符代码不对应于分隔符，该函数将检查它是否对应于' = '符号或另一个用于从字符串序列中提取键的键-值分隔符。

接下来，该函数检查被检查的字符是否是一个“+”号。如果是这种情况，那么该函数构建一个空格分隔的字符串。如果字符是“%”，该函数会相应地解码其后的十六进制字符。

```
 if (code === 43/*+*/) {
        if (lastPos < i)
          value += qs.slice(lastPos, i);
        value += plusChar;
        lastPos = i + 1;
      } else if (!valEncoded) {
        // Try to match an (valid) encoded byte (once) to minimize unnecessary
        // calls to string decoding functions
        if (code === 37/*%*/) {
          encodeCheck = 1;
        } else if (encodeCheck > 0) {
          // eslint-disable-next-line no-extra-boolean-cast
          if (!!isHexTable[code]) {
            if (++encodeCheck === 3)
              valEncoded = true;
          } else {
            encodeCheck = 0;
          }
        }
      } 
```

Enter fullscreen mode Exit fullscreen mode

对于任何未处理的数据，还需要做一些检查。也就是说，该函数检查是否还有一个键-值对需要添加，或者该函数是否可以返回空数据。我假设这里包括这个是为了处理解析时可能出现的边缘情况。

```
 // Deal with any leftover key or value data
  if (lastPos < qs.length) {
    if (eqIdx < eqLen)
      key += qs.slice(lastPos);
    else if (sepIdx < sepLen)
      value += qs.slice(lastPos);
  } else if (eqIdx === 0 && key.length === 0) {
    // We ended on an empty substring
    return obj;
  } 
```

Enter fullscreen mode Exit fullscreen mode

最后一组检查是查看键或值是否需要解码(使用`unescape`函数),或者特定键的值是否需要构造为数组。

```
 if (key.length > 0 && keyEncoded)
    key = decodeStr(key, decode);
  if (value.length > 0 && valEncoded)
    value = decodeStr(value, decode);
  if (obj[key] === undefined) {
    obj[key] = value;
  } else {
    const curValue = obj[key];
    // A simple Array-specific property check is enough here to
    // distinguish from a string value and is faster and still safe since
    // we are generating all of the values being assigned.
    if (curValue.pop)
      curValue[curValue.length] = value;
    else
      obj[key] = [curValue, value];
  } 
```

Enter fullscreen mode Exit fullscreen mode

对于`parse`函数就是这样！

好吧！我接着看了看由`querystring`模块公开的另一个函数`stringify`。`stringify`函数从初始化一些必需的变量开始。它利用`escape`功能对值进行编码，除非用户在选项中提供编码功能。

```
function stringify(obj, sep, eq, options) {
  sep = sep || '&';
  eq = eq || '=';

  var encode = QueryString.escape;
  if (options && typeof options.encodeURIComponent === 'function') {
    encode = options.encodeURIComponent;
  } 
```

Enter fullscreen mode Exit fullscreen mode

之后，该函数遍历对象中的每个键值对。当它遍历每一对时，它对键进行编码和字符串化。

```
if (obj !== null && typeof obj === 'object') {
    var keys = Object.keys(obj);
    var len = keys.length;
    var flast = len - 1;
    var fields = '';
    for (var i = 0; i < len; ++i) {
      var k = keys[i];
      var v = obj[k];
      var ks = encode(stringifyPrimitive(k)) + eq; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，它检查键值对中的值是否是一个数组。如果是，它遍历数组中的每个元素，并向字符串添加一个`ks=element`关系。如果不是，该函数从键值对构建一个`ks=v`关联。

```
 if (Array.isArray(v)) {
        var vlen = v.length;
        var vlast = vlen - 1;
        for (var j = 0; j < vlen; ++j) {
          fields += ks + encode(stringifyPrimitive(v[j]));
          if (j < vlast)
            fields += sep;
        }
        if (vlen && i < flast)
          fields += sep;
      } else {
        fields += ks + encode(stringifyPrimitive(v));
        if (i < flast)
          fields += sep;
      } 
```

Enter fullscreen mode Exit fullscreen mode

这个函数对我来说非常简单易懂。到 API 公开的最后一个函数，`escape`。该函数遍历字符串中的每个字符，并获取与该字符对应的字符代码。

```
function qsEscape(str) {
  if (typeof str !== 'string') {
    if (typeof str === 'object')
      str = String(str);
    else
      str += '';
  }
  var out = '';
  var lastPos = 0;

  for (var i = 0; i < str.length; ++i) {
    var c = str.charCodeAt(i); 
```

Enter fullscreen mode Exit fullscreen mode

如果字符代码小于`0x80`，意味着表示的字符是有效的 ASCII 字符(ASCII 字符的十六进制代码范围从`0`到`0x7F`)。然后，该函数通过在`noEscape`表中进行查找来检查字符是否应该转义。该表允许不转义标点符号、数字或字符，并要求转义其他所有内容。然后，它检查被检查字符的位置是否大于`lastPos`找到的位置(意味着光标已经超过了字符串的长度),并适当地分割字符串。最后，如果字符确实需要转义，它会在`hexTable`中查找字符代码，并将其附加到输出字符串中。

```
 if (c < 0x80) {
      if (noEscape[c] === 1)
        continue;
      if (lastPos < i)
        out += str.slice(lastPos, i);
      lastPos = i + 1;
      out += hexTable[c];
      continue;
    } 
```

Enter fullscreen mode Exit fullscreen mode

下一个 if 语句检查字符是否是多字节字符代码。多字节字符通常代表重音字符和非英语字母。

```
 if (c < 0x800) {
      lastPos = i + 1;
      out += hexTable[0xC0 | (c >> 6)] + hexTable[0x80 | (c & 0x3F)];
      continue;
    } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，使用`hexTable`中的以下查找来计算输出字符串。

```
out += hexTable[0xC0 | (c >> 6)] + hexTable[0x80 | (c & 0x3F)]; 
```

Enter fullscreen mode Exit fullscreen mode

好吧！这里发生了很多事，所以我开始研究。`hexTable`是在`internal/querystring`支持模块中定义的，是这样生成的。

```
const hexTable = new Array(256);
for (var i = 0; i < 256; ++i)
  hexTable[i] = '%' + ((i < 16 ? '0' : '') + i.toString(16)).toUpperCase(); 
```

Enter fullscreen mode Exit fullscreen mode

因此，输出是一个 stirngs 数组，表示 256 个字符的十六进制字符代码。看起来有点像这个`['%00', '%01', '%02',..., '%FD', '%FE', '%FF']`。所以，上面的查找语句。

```
out += hexTable[0xC0 | (c >> 6)] + hexTable[0x80 | (c & 0x3F)]; 
```

Enter fullscreen mode Exit fullscreen mode

语句`c >> 6`将字符代码右移六位，并执行 192 的按位或二进制表示。它将查找结果与二进制数 128 的按位“或”运算以及字符码与二进制数 63 的按位“与”运算相加。所以我知道多字节序列从`0x80`开始，但是我不知道这里到底发生了什么。

下一个案例是这样的。

```
 if (c < 0xD800 || c >= 0xE000) {
      lastPos = i + 1;
      out += hexTable[0xE0 | (c >> 12)] +
             hexTable[0x80 | ((c >> 6) & 0x3F)] +
             hexTable[0x80 | (c & 0x3F)];
      continue;
    } 
```

Enter fullscreen mode Exit fullscreen mode

呀。

在所有其他情况下，该函数使用以下策略来生成输出字符串。

```
 var c2 = str.charCodeAt(i) & 0x3FF;
    lastPos = i + 1;
    c = 0x10000 + (((c & 0x3FF) << 10) | c2);
    out += hexTable[0xF0 | (c >> 18)] +
           hexTable[0x80 | ((c >> 12) & 0x3F)] +
           hexTable[0x80 | ((c >> 6) & 0x3F)] +
           hexTable[0x80 | (c & 0x3F)]; 
```

Enter fullscreen mode Exit fullscreen mode

我真的被这一切弄糊涂了。当我对此做一些调查时，我发现所有这些与十六进制相关的代码都来自于[这个单一提交](https://github.com/nodejs/node/pull/847)。这似乎是绩效相关因素的一部分。没有关于*为什么*使用这种特殊方法的大量信息，我怀疑这种逻辑是从其他地方的另一个编码函数复制来的。我将不得不在某个时候进一步挖掘这个问题。

最后，有一些逻辑处理如何返回输出字符串。如果`lastPos`的值为 0，意味着没有处理任何字符，则返回原始字符串。否则，返回生成的输出字符串。

```
 if (lastPos === 0)
    return str;
  if (lastPos < str.length)
    return out + str.slice(lastPos);
  return out; 
```

Enter fullscreen mode Exit fullscreen mode

就这样吧！我介绍了节点`querystring`模块公开的四个函数。

如果你发现我在这个带注释的演练中遗漏了什么，请在 Twitter 上告诉我。