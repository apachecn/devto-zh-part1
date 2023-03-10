# Nexus.js 简介:多线程 JavaScript 运行时

> 原文：<https://dev.to/voodooattack/introducing-nexusjs-a-multi-threaded-javascript-run-time-3g6>

首先，如果你不熟悉这个项目，我推荐你看一下[老系列](https://medium.com/@voodooattack/multi-threaded-javascript-introduction-faba95d3bd06)。如果你不想全部读完，不用担心！我掩护你！

既然已经解决了，让我们开始吧。

去年我开始实现 [Nexus.js](https://github.com/voodooattack/nexusjs) ，这是一个基于 WebKit/JavaScriptCore 的多线程服务器端 JavaScript 运行时。我放弃了一段时间，由于[的情况超出了我的控制](https://medium.com/@voodooattack/concurrent-javascript-a-years-absence-ea5ae93d3b91)，我不打算在这里讨论，但我们又在这里:我不能让自己长时间不工作。

* * *

因此，让我们从讨论 Nexus 的架构和工作原理开始:

## Nexus.js 运行时

### 事件循环

*   没有*没有*事件循环。
*   有一个带有(无锁)任务队列的线程池。
*   每次您调用`setTimeout`或`setImmediate`或创建一个承诺时，都会有一个任务排队到任务队列中。
*   每次调度任务时，第一个可用的线程将选择任务并执行它。
*   承诺解决所有 CPU 核心的问题。对`Promise.all()`的调用将并行解析承诺。

### ES6

*   `async` / `await`是支持，是鼓励。
*   `for await(...)`被支持。
*   支持析构。
*   支持异步`try/catch/finally`。

### 模块

*   不支持 CommonJS。(`require(...)`和`module.exports`)
*   所有模块都使用 ES6 `import/export`语法。
*   通过`import('file-or-package').then(...)`可以动态导入。
*   `import.meta`被支持。比如:`import.meta.filename`和`import.meta.dirname`等等。
*   额外功能:您可以直接从源代码导入 URL。

示例:

```
import { h } from 'https://unpkg.com/preact/dist/preact.esm.js'; 
```

Enter fullscreen mode Exit fullscreen mode

### 事件发行人

*   Nexus 实现了一个基于承诺的 EventEmitter 类。
*   事件处理程序将在所有线程上排队，并将并行执行。
*   `EventEmitter.emit(...)`的结果是一个承诺，它将解析为由事件处理程序返回的所有值的数组。

示例:

```
class EmitterTest extends Nexus.EventEmitter {
  constructor() {
    super();
    for(let i = 0; i < 4; i++)
      this.on('test', value => { console.log(`fired test ${i}!`); console.inspect(value); });
    for(let i = 0; i < 4; i++)
      this.on('returns-a-value', v => `${v + i}`);
  }
}

const test = new EmitterTest();

async function start() {
  await test.emit('test', { payload: 'test 1' });
  console.log('first test done!');
  await test.emit('test', { payload: 'test 2' });
  console.log('second test done!');
  const values = await test.emit('returns-a-value', 10);
  console.log('third test done, returned values are:'); console.inspect(values);
}

start().catch(console.error); 
```

Enter fullscreen mode Exit fullscreen mode

## 输入输出

*   所有的输入/输出都是通过三个原语完成的:设备、过滤器和流。
*   所有输入/输出原语都实现了`EventEmitter`类。
*   要使用一个设备，你需要在它上面构建一个`ReadableStream`或`WritableStream`。
*   要操作数据，您可以向您的`ReadableStream`或`WritableStream`添加过滤器。
*   最后，使用`source.pipe(...destinationStreams)`和`await source.resume()`来处理数据。
*   所有输入/输出操作都是使用`ArrayBuffer`对象完成的。
*   过滤器实现`process(buffer)`来处理数据。

示例:(用 4 个单独的输出文件将 UTF8 转换为 utf 16)

```
async function start() {
  const startTime = Date.now();
  try {
    const device = new Nexus.IO.FilePushDevice('enwik8');
    const stream = new Nexus.IO.ReadableStream(device);

    stream.pushFilter(new Nexus.IO.EncodingConversionFilter("UTF-8", "UTF-16LE"));

    const wstreams = [0,1,2,3]
      .map(i => new Nexus.IO.WritableStream(new Nexus.IO.FileSinkDevice('enwik16-' + i)));

    console.log('piping...');

    stream.pipe(...wstreams);

    console.log('streaming...');

    await stream.resume();

    await stream.close();

    await Promise.all(wstreams.map(stream => stream.close()));

    console.log(`finished in ${(Date.now() * startTime) / 1000} seconds!`);
  } catch (e) {
    console.error('An error occurred: ', e);
  }
}

start().catch(console.error); 
```

Enter fullscreen mode Exit fullscreen mode

### TCP/UDP

*   Nexus.js 提供了一个`Acceptor`类，负责绑定地址/端口，监听连接。
*   每次接收到一个连接，就会触发`connection`事件，并提供一个`Socket`设备。
*   每个`Socket`实例都是一个双向 I/O 设备。
*   您可以使用`ReadableStream`和`WritableStream`来操作`Socket`。

非常基本的例子:(写‘你好，世界！’给客户)

```
const acceptor = new Nexus.Net.TCP.Acceptor();
let count = 0;

acceptor.on('connection', (socket, endpoint) => {
  const connId = count++;
  console.log(`connection #${connId} from ${endpoint.address}:${endpoint.port}`);
  const rstream = new Nexus.IO.ReadableStream(socket);
  const wstream = new Nexus.IO.WritableStream(socket);
  const buffer = new Uint8Array(13);
  const message = 'Hello World!\n';
  for(let i = 0; i < 13; i++)
    buffer[i] = message.charCodeAt(i);
  rstream.pushFilter(new Nexus.IO.UTF8StringFilter());
  rstream.on('data', buffer => console.log(`got message: ${buffer}`));
  rstream.resume().catch(e => console.log(`client #${connId} at ${endpoint.address}:${endpoint.port} disconnected!`));
  console.log(`sending greeting to #${connId}!`);
  wstream.write(buffer);
});

acceptor.bind('127.0.0.1', 10000);
acceptor.listen();

console.log('server ready'); 
```

Enter fullscreen mode Exit fullscreen mode

### HTTP

*   Nexus 提供了一个本质上继承了`TCPAcceptor`的`Nexus.Net.HTTP.Server`类。
*   相同的基本界面。
*   当服务器完成对传入连接的基本 HTTP 头的解析/验证时，将使用连接和对等信息触发`connection`事件。
*   每个`Connection`实例都有一个`request`和一个`response`。那些是输入/输出设备。
*   您可以构造`ReadableStream`和`WritableStream`来操纵请求/响应。
*   如果您通过管道连接到一个`Response`对象，这些流将进入分块编码模式。否则，您可以使用`response.write()`来编写一个常规的字符串有效载荷。

复杂示例:(使用分块编码的基准 HTTP 服务器，细节省略)

```
 ....

/**
 * Creates an input stream from a path.
 * @param path
 * @returns {Promise<ReadableStream>}
 */
async function createInputStream(path) {
  if (path.startsWith('/')) // If it starts with '/', omit it.
    path = path.substr(1);
  if (path.startsWith('.')) // If it starts with '.', reject it.
    throw new NotFoundError(path);
  if (path === '/' || !path) // If it's empty, set to index.html.
    path = 'index.html';
  /**
   * `import.meta.dirname` and `import.meta.filename` replace the old CommonJS `__dirname` and `__filename`.
   */
  const filePath = Nexus.FileSystem.join(import.meta.dirname, 'server_root', path);
  try {
    // Stat the target path.
    const {type} = await Nexus.FileSystem.stat(filePath);
    if (type === Nexus.FileSystem.FileType.Directory) // If it's a directory, return its 'index.html'
      return createInputStream(Nexus.FileSystem.join(filePath, 'index.html'));
    else if (type === Nexus.FileSystem.FileType.Unknown || type === Nexus.FileSystem.FileType.NotFound)
      // If it's not found, throw NotFound.
      throw new NotFoundError(path);
  } catch(e) {
    if (e.code)
      throw e;
    throw new NotFoundError(path);
  }
  try {
    // First, we create a device.
    const fileDevice = new Nexus.IO.FilePushDevice(filePath);
    // Then we return a new ReadableStream created using our source device.
    return new Nexus.IO.ReadableStream(fileDevice);
  } catch(e) {
    throw new InternalServerError(e.message);
  }
}

/**
 * Connections counter.
 */
let connections = 0;

/**
 * Create a new HTTP server.
 * @type {Nexus.Net.HTTP.Server}
 */
const server = new Nexus.Net.HTTP.Server();

// A server error means an error occurred while the server was listening to connections.
// We can mostly ignore such errors, we display them anyway.
server.on('error', e => {
  console.error(FgRed + Bright + 'Server Error: ' + e.message + '\n' + e.stack, Reset);
});

/**
 * Listen to connections.
 */
server.on('connection', async (connection, peer) => {
  // Start with a connection ID of 0, increment with every new connection.
  const connId = connections++;
  // Record the start time for this connection.
  const startTime = Date.now();
  // Destructuring is supported, why not use it?
  const { request, response } = connection;
  // Parse the URL parts.
  const { path } = parseURL(request.url);
  // Here we'll store any errors that occur during the connection.
  const errors = [];
  // inStream is our ReadableStream file source, outStream is our response (device) wrapped in a WritableStream.
  let inStream, outStream;
  try {
    // Log the request.
    console.log(`> #${FgCyan + connId + Reset}  ${Bright + peer.address}:${peer.port + Reset}  ${
      FgGreen + request.method + Reset} "${FgYellow}${path}${Reset}"`, Reset);
    // Set the 'Server' header.
    response.set('Server', `nexus.js/0.1.1`);
    // Create our input stream.
    inStream = await createInputStream(path);
    // Create our output stream.
    outStream = new Nexus.IO.WritableStream(response);
    // Hook all `error` events, add any errors to our `errors` array.
    inStream.on('error', e => { errors.push(e); });
    request.on('error', e => { errors.push(e); });
    response.on('error', e => { errors.push(e); });
    outStream.on('error', e => { errors.push(e); });
    // Set content type and request status.
    response
      .set('Content-Type', mimeType(path))
      .status(200);
    // Hook input to output(s).
    const disconnect = inStream.pipe(outStream);
    try {
      // Resume our file stream, this causes the stream to switch to HTTP chunked encoding.
      // This will return a promise that will only resolve after the last byte (HTTP chunk) is written.
      await inStream.resume();
    } catch (e) {
      // Capture any errors that happen during the streaming.
      errors.push(e);
    }
    // Disconnect all the callbacks created by `.pipe()`.
    return disconnect();
  } catch(e) {
    // If an error occurred, push it to the array.
    errors.push(e);
    // Set the content type, status, and write a basic message.
    response
      .set('Content-Type', 'text/plain')
      .status(e.code || 500)
      .send(e.message || 'An error has occurred.');
  } finally {
    // Close the streams manually. This is important because we may run out of file handles otherwise.
    if (inStream)
      await inStream.close();
    if (outStream)
      await outStream.close();
    // Close the connection, has no real effect with keep-alive connections.
    await connection.close();
    // Grab the response's status.
    let status = response.status();
    // Determine what colour to output to the terminal.
    const statusColors = {
      '200': Bright + FgGreen, // Green for 200 (OK),
      '404': Bright + FgYellow, // Yellow for 404 (Not Found)
      '500': Bright + FgRed // Red for 500 (Internal Server Error)
    };
    let statusColor = statusColors[status];
    if (statusColor)
      status = statusColor + status + Reset;
    // Log the connection (and time to complete) to the console.
    console.log(`< #${FgCyan + connId + Reset}  ${Bright + peer.address}:${peer.port + Reset}  ${
      FgGreen + request.method + Reset} "${FgYellow}${path}${Reset}" ${status}  ${(Date.now() * startTime)}ms` +
      (errors.length ? "  " + FgRed + Bright + errors.map(error => error.message).join(', ') + Reset : Reset));
  }
});

/**
 * IP and port to listen on.
 */
const ip = '0.0.0.0', port = 3000;
/**
 * Whether or not to set the `reuse` flag. (optional, default=false)
 */
const portReuse = true;
/**
 * Maximum allowed concurrent connections. Default is 128 on my system. (optional, system specific)
 * @type {number}
 */
const maxConcurrentConnections = 1000;
/**
 * Bind the selected address and port.
 */
server.bind(ip, port, portReuse);
/**
 * Start listening to requests.
 */
server.listen(maxConcurrentConnections);
/**
 * Happy streaming!
 */
console.log(FgGreen + `Nexus.js HTTP server listening at ${ip}:${port}` + Reset); 
```

Enter fullscreen mode Exit fullscreen mode

## 基准

我想我已经涵盖了迄今为止我实现的几乎所有内容。那么现在，我们来谈谈性能。

下面是上述 HTTP 服务器的当前基准，有 100 个并发连接和总共 10，000 个请求:

```
This is ApacheBench, Version 2.3 <$Revision: 1796539 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking localhost (be patient).....done

Server Software:        nexus.js/0.1.1
Server Hostname:        localhost
Server Port:            3000

Document Path:          /
Document Length:        8673 bytes

Concurrency Level:      100
Time taken for tests:   9.991 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      87880000 bytes
HTML transferred:       86730000 bytes
Requests per second:    1000.94 [#/sec] (mean)
Time per request:       99.906 [ms] (mean)
Time per request:       0.999 [ms] (mean, across all concurrent requests)
Transfer rate:          8590.14 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       1
Processing:     6   99  36.6     84     464
Waiting:        5   99  36.4     84     463
Total:          6  100  36.6     84     464

Percentage of the requests served within a certain time (ms)
  50%     84
  66%     97
  75%    105
  80%    112
  90%    134
  95%    188
  98%    233
  99%    238
 100%    464 (longest request) 
```

Enter fullscreen mode Exit fullscreen mode

也就是每秒**1000 个**请求！在运行基准软件、消耗 5GB 内存的 IDE、*和*服务器本身的旧 i7 上！

```
voodooattack@voodooattack:~$ cat /proc/cpuinfo 
processor   : 0
vendor_id   : GenuineIntel
cpu family  : 6
model       : 60
model name  : Intel(R) Core(TM) i7-4770 CPU @ 3.40GHz
stepping    : 3
microcode   : 0x22
cpu MHz     : 3392.093
cache size  : 8192 KB
physical id : 0
siblings    : 8
core id     : 0
cpu cores   : 4
apicid      : 0
initial apicid  : 0
fpu     : yes
fpu_exception   : yes
cpuid level : 13
wp      : yes
flags       : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi mmx fxsr sse sse2 ss ht tm pbe syscall nx pdpe1gb rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl xtopology nonstop_tsc cpuid aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 sdbg fma cx16 xtpr pdcm pcid sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand lahf_lm abm cpuid_fault tpr_shadow vnmi flexpriority ept vpid fsgsbase tsc_adjust bmi1 avx2 smep bmi2 erms invpcid xsaveopt dtherm ida arat pln pts
bugs        :
bogomips    : 6784.18
clflush size    : 64
cache_alignment : 64
address sizes   : 39 bits physical, 48 bits virtual
power management: 
```

Enter fullscreen mode Exit fullscreen mode

以下是图表形式的结果:

[![Benchmark results](img/bbeef248c5c5c06933537df0142c8127.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--t5aCEAvW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5vndppzd7a5xk8qcw2id.png)

我尝试用 1，000 个并发请求进行测试，但是 ApacheBench 超时了，因为打开了太多的套接字。我尝试了 httperf，结果如下:

```
voodooattack@voodooattack:~$ httperf --port=3000 --num-conns=10000 --rate=1000
httperf --client=0/1 --server=localhost --port=3000 --uri=/ --rate=1000 --send-buffer=4096 --recv-buffer=16384 --num-conns=10000 --num-calls=1
httperf: warning: open file limit > FD_SETSIZE; limiting max. # of open files to FD_SETSIZE
Maximum connect burst length: 262

Total: connections 9779 requests 9779 replies 9779 test-duration 10.029 s

Connection rate: 975.1 conn/s (1.0 ms/conn, <=1022 concurrent connections)
Connection time [ms]: min 0.5 avg 337.9 max 7191.8 median 79.5 stddev 848.1
Connection time [ms]: connect 207.3
Connection length [replies/conn]: 1.000

Request rate: 975.1 req/s (1.0 ms/req)
Request size [B]: 62.0

Reply rate [replies/s]: min 903.5 avg 974.6 max 1045.7 stddev 100.5 (2 samples)
Reply time [ms]: response 129.5 transfer 1.1
Reply size [B]: header 89.0 content 8660.0 footer 2.0 (total 8751.0)
Reply status: 1xx=0 2xx=9779 3xx=0 4xx=0 5xx=0

CPU time [s]: user 0.35 system 9.67 (user 3.5% system 96.4% total 99.9%)
Net I/O: 8389.9 KB/s (68.7*10^6 bps)

Errors: total 221 client-timo 0 socket-timo 0 connrefused 0 connreset 0
Errors: fd-unavail 221 addrunavail 0 ftab-full 0 other 0 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，它仍然有效；尽管有些连接由于压力而超时。我仍在努力找出导致此问题的原因。

该项目的源代码可以在 [GitHub](https://github.com/voodooattack/nexusjs) 获得，欢迎随时查看。

祝黑客愉快，下次再见！

**编辑:**

如果你想做你自己的基准测试，这里有一个预 alpha 版本。请注意，这只是在两台运行 Ubuntu 17.10 的机器上进行的测试:

[https://github . com/voodoo attack/nexus js/releases/tag/4dd 3419](https://github.com/voodooattack/nexusjs/releases/tag/4dd3419)