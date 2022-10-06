# WebSocket簡介

## WebSocket介绍

> **WebSockets** 是一个可以创建和服务器间进行双向会话的高级技术。通过这个API你可以向服务器发送消息并接受基于事件驱动的响应，这样就不用向服务器轮询获取数据了。

上面是[MDN](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWebSockets_API)中关于WebSocket的说明。其中`双向会话`指的是客户端和服务端都能够通过WebSocket来进行数据的互相传递，即服务端可以给客户端推送数据，客户端也可以通过WebSocket来传递数据。

## 为什么要使用WebSocket

在不使用WebSocket时，如果我们需要建立一条长连接，有以下几种方法：

- 轮询
- 长轮询（常用）
- SSE(Server Send Event)

下面，我们对这几个都进行简单的介绍。

### 轮询

轮询是最早在客户端用来模拟长连接的一种方式。他通过客户端定时想服务端发送HTTP请求来模拟客户端向服务端发送数据，而服务端的数据则是在客户端发送HTTP请求后跟随返回。

这种方案能够让客户端的数据几乎实时的到达，但是缺点也显而易见：服务端的数据需要在客户端的请求回来后才能带回。如果HTTP请求的间隔太短，则会导致大量的网络开销；如果间隔太长，这将导致数据传递的不及时。

### 长轮询

长轮询是在轮询的基础上改进的一种方式。在客户端发送HTTP请求且服务端收到请求时，服务端会先维持这个请求不返回。在特定的时间内（一般为30秒，因为通常HTTP判断超时时间为30秒），如果服务端没有数据，则回应这个请求；服务端有数据需要发送时，则立即通过HTTP请求的响应将数据传递给客户端。客户端收到响应后，立即发起下一次的HTTP请求。

这种方案能够解决轮询中带来的服务端数据不能及时传递的问题，但是带来的网络花销大的问题仍然无法解决。

### SSE(Server Send Event)

SSE是一个新的协议，作用为服务端想客户端推送数据。他通过自定义的SSE协议来实现单项的数据推送。SSE的缺点是数据只能从服务端像客户端传递，而数据不能通过客户端向服务端传递。

### WebSocket能够解决上述问题

WebSocket能够有效的解决以下问题：

1. 带宽问题：WebSocket相对于HTTP来说协议头更加小，同时按需传递。
2. 数据实时性问题：WebSocket相对于轮询和长轮询来说，能够实时传递数据，延迟更小。
3. 状态问题：相较于HTTP的无状态请求，WebSocket在建立连接后能够维持特定的状态。

其他的优点可以参考[维基百科](https://link.juejin.cn?target=https%3A%2F%2Fzh.wikipedia.org%2Fwiki%2FWebSocket)。

## WebSocket协议

了解了为什么需要使用WebSocket，下面让我们来了解下WebSocket协议相关的内容。

WebSocket协议是通过HTTP协议升级而来。只需要在HTTP协议基础上增加两次握手，即可建立WebSocket连接（如果是需要通过SSL加密，则还需要进行SSL握手过程），握手的部分详情可以见[WebSocket文档](https://link.juejin.cn?target=https%3A%2F%2Ftools.ietf.org%2Fhtml%2Frfc6455%23section-1.3)，下面我们简单介绍以下Header相关字段。

### 请求Header

请求Header如下：

```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Origin: http://example.com
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```

其中：

- `Host: server.example.com`：表示将要连接的WebSocket地址。
- `Connection: Upgrade`：需要升级HTTP连接。
- `Upgrade: websocket`：将HTTP连接升级至WebSocket连接。
- `Sec-WebSocket-Key:dGhlIHNhbXBsZSBub25jZQ==`：客户端生成的WebSocket连接密钥。
- `Sec-WebSocket-Protocol: chat, superchat`：指定哪些协议是客户端可以接受的。
- `Sec-WebSocket-Version: 13`：WebSocket版本号。

响应Header

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
复制代码
```

其中：

- `Upgrade: websocket`：确认将HTTP连接升级至WebSocket连接。
- `Connection: Upgrade`：确认升级HTTP连接。
- `Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo`：服务端根据客户端的连接密钥生成的服务端密钥。
- `Sec-WebSocket-Protocol: chat`：选择的WebSocket协议。

## WebSocket API介绍

对WebSocket的协议有了一个初步的了解，下面让我们看下，在具体的使用场景中，如何使用WebSocket。

WebSocket的API不多，下面我们就根据使用的顺序：

- 建立连接
- 收到消息
- 发送消息
- 关闭连接

来逐一进行介绍，具体的MDN资料可以见[此处](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWebSocket)。

### 建立连接

WebSocket通过初始化实例来建立连接，通过`open`事件回调函数来确认连接建立成功，具体示例如下：

```
const webSocket = new WebSocket('ws://server.example.com');

webSocket.addEventListener('open', (event) => {
    // 建立连接成功
});
复制代码
```

在WebSocket建立ws连接时，url可以是域名或者IP地址；但是当建立的连接是wss（加密WebSocket）时，url必须是域名，因为需要配置相应的证书，而证书是针对域名的。

收到消息

WebSocket通过`message`事件来接收消息。

```
socket.addEventListener('message', function (event) {
    console.log('Message from server', event.data);
});
复制代码
```

WebSocket可以传递`String`、`ArrayBuffer`和`Blob`三种数据类型，因此在收到消息时可能是其中的任意一种。其中，`String`和`ArrayBuffer`使用的最多。

- 如果是`String`类型，直接通过字符串处理函数即可进行相关转换，如JSON等格式。
- 如果是二进制`ArrayBuffer`类型，则需要使用`DataView`来进行处理，相关的内容将在本系列第二篇中进行介绍。

### 发送消息

WebSocket通过`send`方法来发送消息。

```
webSocket.send(data);
复制代码
```

示例中的`data`字段，也有可能是收到消息所说的`String`、`ArrayBuffer`和`Blob`三种数据类型之一。其中，`Blob`作为一种类文件数据类型，再此不进行过多介绍。我们使用最多的就是`String`和`ArrayBuffer`。

- `String`类型只需要传递一个字符串给`send`方法作为参数即可。
- `ArrayBuffer`类型则需要传递一个ArrayBuffer对象作为参数，相关的内容也将在本系列第二篇中进行介绍。

### 关闭连接

### 被动关闭

当服务端主动关闭WebSocket连接时，会通过WebSocket向客户端发送一个close数据包，WebSocket的`close`事件会触发。

```
webSocket.addEventListener('close', (closeEvent) => {
    
});
复制代码
```

**注：当网络断开时，WebSocket连接并不会被动关闭，因为没有收到关闭的数据包。**

### 主动关闭

客户端可以通过WebSocket提供的`close`方法来主动关闭长连接。

```
webSocket.close();
复制代码
```

目前该方法有两个参数（在某些版本中不支持，详情见[MDN文档](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FWebSocket%23close())）：

- 第一个参数表示关闭连接的状态号，默认为1000，表示正常关闭。
- 第二个参数为关闭原因，是一个不长于123字节的UTF-8文本。

## 总结

本文主要是介绍了一下WebSocket相关的基础知识。

通过WebSocket的长连接，客户端和服务端可以进行大量的数据传输而不会带来相关的性能问题，这给Web端带来了极大的功能增强。目前Web端可以使用WebSocket来进行IM相关功能开发，或者实时协作等需要与服务端进行大量数据交互的功能，并且不需要像之前一样使用长轮询的Hack方式来实现。

具体的使用方式和线上的使用问题，将会在本系列后几篇博客中进行介绍。

## WebSocket系列之JavaScript数字数据如何转换为二进制数据

[![img](https://p9-passport.byteacctimg.com/img/user-avatar/bf65438038c7a0cf3be50554dff87dce~300x300.image)](https://juejin.cn/user/1257497030566446)



### 概述

本文主要通过对JavaScript中数字数据与二进制数据之间的转换，让读者能够了解在JavaScript中如何对数字类型（包括但不限于Number类型）进行处理。

二进制数据在日常的JavaScript中很少遇到，但是当你使用WebSocket与后端进行数据交互时，就有可能会用到二进制的数据格式。因此，为了更好的理解本系列中之后发布的关于WebSocket传输二进制相关的内容，我们有必要了解二进制数据在JavaScript中是如何进行操作和存储的。

本文内容主要为：

- JavaScript中如何操作与存储二进制数据——ArrayBuffer存储结构相关基础知识以及对应的DataView相关数据类型基础知识和和API接口，同时对字节序问题进行介绍。
- 以Int和Short为例，说明JavaScript中的数字数据如何转换为二进制数据。
- 以Long类型为例，说明JavaScript中如何表示Long类型并且如何将其转换为二进制数据。
- 如何将二进制数据中转换为JavaScript中的数字数据。

本文与WebSocket并无太强关联，不过作为在WebSocket中传递二进制数据的基础知识储备，因此放入了此系列当中。

如果读者对WebSocket并不了解，或者说不明白它的使用场景和细节，可以阅读我的前一篇博客——[WebSocket系列之基础知识入门篇](https://juejin.cn/post/6844903583222071304)。

如果读者想了解String类型与二进制之间的处理和转换，可以于都WebSocket系列稍后发布的文章（文章发布后会替换此段）。

如果读者想了解在WebSocket中如何进行二进制的传递和解析，可以阅读WebSocket系列稍后发布的文章（文章发布后会替换此段）。

### JavaScript中如何存储和操作二进制数据

了解了为什么需要使用二进制数据，我们来看下，在JavaScript中如何存储和操作二进制数据。

## ArrayBuffer

首先，我们要介绍下在JavaScript中用来存储二进制数据的`ArrayBuffer`。

> **ArrayBuffer** 对象用来表示通用的、固定长度的原始二进制数据缓冲区。

在[MDN](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FArrayBuffer)的文档中，我们能够看到`ArrayBuffer`的介绍。它是在JavaScript中用来进行二进制数据存储的一种数据对象。

下面我们通过一个示例来简单介绍下ArrayBuffer相关操作。

```
const buffer = new ArrayBuffer(8);

buffer.byteLength; // 结果为8
复制代码
```

上面的示例通过创建一个长度为8Byte的二进制数据缓冲区。缓冲区只是一个数据存储的空间，如何对这个存储空间进行读取，完全取决于使用者。例如：8个字节可以当成是2个Int类型的数据，也可以是一个Long类型的数据，或者4个Short型的数据。

### DataView

看完了存储数据的`ArrayBuffer`，我们来看下数据读写的`DataView`。

> **DataView** 视图是一个可以从 [`ArrayBuffer`](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FArrayBuffer) 对象中读写多种数值类型的底层接口，在读写时不用考虑平台字节序问题。

这个是在[MDN](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FDataView)中关于DataView的介绍。DataView提供了大量的API接口来进行数据的读和写操作，我们在后三章将会举例进行说明。但是，首先我们得先看下说明中提到的字节序问题。

### 字节序

在现有的计算机体系中，有两种字节序：

- 大端字节序：高位在前，低位在后。符合人类阅读习惯。
- 小端字节序：低位在前，高位在后。符合计算机读取习惯。

上面所说的顺序均是针对多字节对象而言，如Int类型，Long类型。以Int类型数据`0x1234`为例，如果是大端字节序，那么数据从人类对数值的通常写法上来看就是`0x1234`；如果是小端字节序，那么从人类对数值的通常写法上来看，应该写成`0x3412`。

对于单字节对象如Byte类型数据而言，没有字节序一说。

在不同的平台中，可能使用不同的字节序，这就是所谓的字节序问题。DataView所谓的在读写时不需要考虑平台字节序问题是指：同时使用DataView进行写入和读取的数据保持一致。

JavaScript中的数字数据如何转换为二进制数据

对ArrayBuffer和DataView有了一个大概的了解，下面让我们来看下它是如何进行二进制数据操作的。

本章，我以Short类型和Int类型为例，介绍下相关操作步骤。

```
let buffer = new ArrayBuffer(6); // 初始化6个Byte的二进制数据缓冲区
let dataView = new DataView(buffer);

dataView.setInt16(0, 3); // 从第0个Byte位置开始，放置一个数字为3的Short类型数据(占2 Byte)
dataView.setInt32(2, 15); // 从第2个Byte位置开始，放置一个数字为15的Short类型数据(占4 Byte)
复制代码
```

通过上面的示例，我们一共初始化了6个Byte的存储空间，使用1个Short类型（占2 Byte）和一个Int类型（占4 Byte）的数据进行填充。

DataView还提供了许多的API接口来进行其他数据类型的处理，如无符号型，浮点数等。他们的使用方法和上面介绍的API相同，我们在这里就不一一进行介绍了，希望了解更多API接口的读者可以查看[MDN文档](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FDataView)。

### JavaScript中如何表示Long类型并且如何将其转换为二进制数据

通过DataView提供的API接口，我们知道了如何处理Short类型、Int类型、Float类型和Double类型。那么，如果是对于Long类型这种原生API中没有提供处理函数的数据类型，我们应该如何处理呢？

首先，我们需要理解Long数据类型的结构，它是由一个高位的4个Byte和低位的4个Byte组成的数据类型。因为Long类型表示的范围比Number类型大，所以我们在JavaScript中是使用了两个Number类型（即Int类型）的对象来表示Long类型数据，相关的具体细节可以见我之前的博客[Long.js源码分析与学习](https://juejin.cn/post/6844903565291421703)。

理解了JavaScript中如何存储Long类型，我们就知道如果对其进行存储。

```
import Long from 'long';

let long = Long.fromString('123');
let buffer = new ArrayBuffer(8);
let dataView = new DataView(buffer);

dataView.setInt32(0, long.high); // 采用大端字节序放置
dataView.setInt32(4, long.low);
复制代码
```

通过上面的示例，我们将一个Long类型的数据拆分成了两个Int类型的数据，按照大端字节序放入到了ArrayBuffer中。同理，如果是想按照小端字节序放置，只需要将数据进行部分处理后再放入即可，在此我就不过多介绍了。

# 如何将二进制数据中转换为JavaScript中的数据类型

当你知道了如何将数据转换为ArrayBuffer中存储的二进制数据后，就能够简单推测出如何进行反向操作——将数据从ArrayBuffer中读取出来，再转换成JavaScript中常用数据类型。

```
import Long from 'long';

let buffer = new ArrayBuffer(14); // 初始化14个Byte的二进制数据缓冲区
let dataView = new DataView(buffer);
let long = Long.fromString('123');


// 数据写入过程

dataView.setInt16(0, 3); // 从第0个Byte位置开始，放置一个数字为3的Short类型数据(占2 Byte)
dataView.setInt32(2, 15); // 从第2个Byte位置开始，放置一个数字为15的Short类型数据(占4 Byte)

dataView.setInt32(6, long.high); // 采用大端字节序放置
dataView.setInt32(10, long.low);

// 数据读取过程

let shortNumber = dataView.getInt16(0);
let intNumber = dataView.getInt32(2);

let longNumber = Long.fromBits(dataView.getInt32(10), dataView.getInt32(6)); // 根据大端字节序读取，该构造函数入参依次为：低16位，高16位
复制代码
```

通过上面的示例，我们将一串二进制数据转换成为了JavaScript中通用的数据类型。

# 总结

通过使用ArrayBuffer和DataView，我们能够快速的将数字数据从二进制转换为JavaScript常用数据类型如Int、Short等；同时，我们也可以将这些数据类型转换为二进制数据。有了这些基础知识，我们就能够理解在之后的博客中讲到的关于使用WebSocket进行二进制数据传递的过程和处理逻辑。

# WebSocket系列之JavaScript字符串如何与二进制数据间进行互相转换

[![img](https://p9-passport.byteacctimg.com/img/user-avatar/bf65438038c7a0cf3be50554dff87dce~300x300.image)](https://juejin.cn/user/1257497030566446)

[黄Java ![lv-3](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/e108c685147dfe1fb03d4a37257fb417.svg)](https://juejin.cn/user/1257497030566446)

2018年03月30日 12:59 · 阅读 4244

关注

# 概述

上一篇博客我们说到了如何进行数字类型（如Short、Int、Long类型）如何在JavaScript中进行二进制转换，如果感兴趣的可以可以阅读本系列第二篇博客——[WebSocket系列之JavaScript中数字数据如何转换为二进制数据](https://juejin.cn/post/6844903584018989063)。这次，我们来说下string类型的数据如何进行处理。 本文是WebSocket系列的第三篇，主要介绍string数据与二进制数据之间的转换方法，具体的内容如下：

- JavaScript中string类型基础知识
- JavaScript如何将string类型转换为二进制数据
- JavaScript如何将二进制数据转换为string类型 本文与WebSocket并无太强关联，不过作为在WebSocket中传递二进制数据的基础知识储备，因此放入了此系列当中。 如果读者对WebSocket并不了解，或者说不明白它的使用场景和细节，可以阅读我的本系列的第一篇博客——[WebSocket系列之基础知识入门篇](https://juejin.cn/post/6844903583222071304)。

# string类型基础知识

string这个类型，对于熟悉JavaScript的同学应该都不陌生，它是属于JavaScript中基础数据类型的一种。不过，我们今天要先介绍下`DOMString`。

> DOMString 是一个UTF-16字符串。由于JavaScript已经使用了这样的字符串，所以DOMString 直接映射到 一个String。将null传递给接受DOMString的方法或参数时通常会把其转换成为“null”。

在WebSocket中进行string类型数据传输时，使用的其实也是`DOMString`。不过，根据[MDN](https://link.juejin.cn/?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FAPI%2FDOMString)中对`DOMString`的介绍我们可以了解到，大部分日常使用场景中，我们可以认为`DOMString`就是一个string类型。所以，我们只需要了解此类型，使用时仍然当成string类型处理即可。

## 编码

既然上面提到了UTF-16，那么我们就来简单介绍下UTF-16，以及后端常用的UTF-8这两种编码方式。 为什么需要介绍编码类型呢？因为我们在与后端进行字符串数据传递时，可能使用的编码方式不同，这样就会导致双方得到不同的数据。因此，我们在进行string的二进制数据通信时，不仅仅需要将字符串转换成二进制，还需要协商一致的string编码。

### UTF-16

> UTF-16 (16-bit Unicode Transformation Format) 是Unicode字符编码五层次模型的第三层：字符编码表（Character Encoding Form，也称为"storage format"）的一种实现方式。即把Unicode字符集的抽象码位映射为16位长的整数（即码元）的序列，用于数据存储或传递。 Unicode字符的码位，需要1个或者2个16位长的码元来表示，因此这是一个变长表示。

UTF-16是JavaScript中的string编码类型。

### UTF-8

> UTF-8（8-bit Unicode Transformation Format）是一种针对Unicode的可变长度字符编码，也是一种前缀码。它可以用来表示Unicode标准中的任何字元，且其编码中的第一个字节仍与ASCII兼容，这使得原来处理ASCII字元的软件无须或只须做少部分修改，即可继续使用。UTF-8使用一至四个字节为每个字符编码（2003年11月重新规范）。

UTF-8是很多语言使用的通用编码类型，在后端应用中非常常见。

### JavaScript中UTF16和UTF-8如何进行编码转换

在Github上有转换库[GitHub - dcodeIO/utfx: A compact library to encode, decode and convert UTF8 / UTF16 in JavaScript.](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FdcodeIO%2Futfx)，通过这个库，可以将字符串在UTF-8编码和UTF-16编码中进行转换。该库的具体原理和内容以及两种编码方式的详细内容说明将会在之后的博客中进行讲解。我们下面简单的介绍下它是使用方式：

```
import utfx from './util/utfx';

let str = 'abcdefg';
let result = [];

function stringSource(s) {
    let i = 0;
    return function () {
        return i < s.length ? s.charCodeAt(i++) : null;
    };
}

utfx.encodeUTF16toUTF8(stringSource(str), function (b) {
    result.push(b);
}.bind(this));
复制代码
```

同理，该类库提供了其他的方法：

- `decodeUTF8toUTF16`，将UTF-8的string数据转换为UTF-16的string数据。
- `calculateUTF16asUTF8`，计算UTF-16编码的string类型类型转换为UTF-8后所占Byte长度。 这两个方法我们在之后的章节中也会用到。

# JavaScript如何将string类型转换为二进制数据

了解了JavaScript中string类型的编码和在UTF-8和UTF-16之间转换编码的方式，下面我们来看下如何将string类型转换为二进制数据。 首先，我们假定与后端交互时使用的编码方式为UTF-8，这样能够满足更多的使用场景。如果仍然使用UTF-16的话，则直接忽略转换编码的逻辑即可。 简单介绍下实现思路：我们得到一个需要转换的字符串后，先知道其长度后，初始化ArrayBuffer中相关参数，将数据放入ArrayBuffer中即可。我们将上面的示例稍作改动：

```
import utfx from './util/utfx';

function stringSource(s) {
    var i = 0;
    return function () {
        return i < s.length ? s.charCodeAt(i++) : null;
    };
}

let str = 'abcdefg';

let strCodes = stringSource(str);
let length = utfx.calculateUTF16asUTF8(strCodes)[1];
let buffer = new ArrayBuffer(length + 4); // 初始化长度为UTF8编码后字符串长度+4个Byte的二进制缓冲区
let view = new DataView(buffer);
let offset = 4;

view.setUint32(0, length); // 将长度放置在字符串的头部

utfx.encodeUTF16toUTF8(stringSource(str), function (b) {
    view.setUint8(offset++, b);
}.bind(this));
复制代码
```

通过上面的示例，我们就已经将一个二进制数据根据UTF-8编码后放入了ArrayBuffer中，同时，将其长度作为一个Unsigned Int类型存储在了二进制头部4个Byte的位置。

# JavaScript如何将二进制数据转换为string类型

知道了如何将string类型转换为二进制数据，下面我们看下如何将整个数据从二进制中读取，转换回string类型。 根据上面转换为二进制的过程，我们不难想到相关的二进制转string类型方法。具体示例如下：

```
import utfx from './util/utfx';
let str = 'abcdefg';

function stringSource(s) {
    var i = 0;
    return function () {
        return i < s.length ? s.charCodeAt(i++) : null;
    };
}

let strCodes = stringSource(str);
let length = utfx.calculateUTF16asUTF8(strCodes)[1];
let buffer = new ArrayBuffer(length + 4); // 初始化长度为UTF8编码后字符串长度+4个Byte的二进制缓冲区
let view = new DataView(buffer);
let offset = 4;

// 字符串转换二进制过程
view.setUint32(0, length); // 将长度放置在字符串的

utfx.encodeUTF16toUTF8(stringSource(str), function (b) {
    view.setUint8(offset++, b);
}.bind(this));

// 二进制转换字符串过程
let Strlength = view.getUint32(0);
offset = 4;
let result = []; // Unicode编码字符
let end = offset + Strlength;


utfx.decodeUTF8toUTF16(function () {
    return offset < end ? view.getUint8(offset++) : null; // 返回null时会退出此转换函数
}.bind(this), (char) => {
    console.log(char)
    result.push(char);
});

let strResult = result.reduce((prev, next)=>{
    return prev + String.fromCharCode(next);
}, '');
复制代码
```

通过上面的示例我们可以知道，我们只需要在前面4个Byte中将字符串长度读取出来，然后再从第4个Byte（从0开始算）的位置开始读取指定长度的字符串字符编码即可。最后，我们得到了一个Unicode码数组，只需要`fromCharCode`方法即可将其转换为字符串。

# 总结

通过使用ArrayBuffer和DataView，我们能够在string数据和二进制数据中进行互相转换。 有了string类型转换的相关基础，读者就能够在之后的WebSocket进行二进制数据传递时理解相关的内容和处理逻辑。 下一篇WebSocket系列相关的博客，将会介绍如何通过WebSocket来向后端传递二进制数据，以及如何处理通过WebSocket收到的二进制数据。有兴趣的同学可以继续关注。

# WebSocket系列之二进制数据设计与传输

[![img](https://p9-passport.byteacctimg.com/img/user-avatar/bf65438038c7a0cf3be50554dff87dce~300x300.image)](https://juejin.cn/user/1257497030566446)

[黄Java ![lv-3](https://lf3-cdn-tos.bytescm.com/obj/static/xitu_juejin_web/e108c685147dfe1fb03d4a37257fb417.svg)](https://juejin.cn/user/1257497030566446)

2018年03月31日 17:25 · 阅读 8588

关注

# 概述

通过前三篇博客，我们能够了解在通过WebSocket发送数据之前，我们需要传递的数据是如何变成ArrayBuffer二进制数据的；在我们收到二进制数据之后，我们又如何将其变成了JavaScript中的常见数据类型。 本文作为WebSocket系列的第四篇内容，将会用一个简单的IM聊天应用把整个WebSocket传输二进制数据类型的内容连接起来，让用户对整个WebSocket传输二进制数据的方法有个了解。 本文的主要内容如下：

- 如何设计一个二进制协议
- WebSocket如何发送二进制数据
- WebSocket如何处理接收的二进制数据

之前的博客我们介绍过了WebSocket基础知识，数字类型和字符串类型与二进制数据间的转换，如果没有相关的基础，建议先依次阅读以下文章：

- [WebSocket系列之基础知识入门篇](https://juejin.cn/post/6844903583222071304)
- [WebSocket系列之JavaScript中数字数据如何转换为二进制数据](https://juejin.cn/post/6844903584018989063)
- [WebSocket系列之字符串如何与二进制数据进行转换](https://juejin.cn/post/6844903585528954894)

# 如何设计一个二进制协议

## 什么是协议

> 协议，网络协议的简称，网络协议是通信计算机双方必须共同遵从的一组约定。如怎么样建立连接、怎么样互相识别等。只有遵守这个约定，计算机之间才能相互通信交流。它的三要素是：语法、语义、时序。

通过[百度百科](https://link.juejin.cn/?target=https%3A%2F%2Fbaike.baidu.com%2Fitem%2F%E5%8D%8F%E8%AE%AE%2F13020269)中的介绍，我们对协议的概念有了一个基础的了解。通俗来说，协议就是通信双方约定好的一套规则。

## 为什么要设计协议

没有规矩不成方圆。通信双方只有通过协议，才能够识别对方发送的数据内容。

## 我们应该如何设计这套协议

首先，协议的设计应该能够区分不同的各个数据包；其次，它还需要具备一定的兼容性。 根据上述两点要求，我们设计了一套简单的IM聊天协议，支持文本、图片、文件三种消息。这套协议是按照最简单的思路来设计的，因此也只是给大家一个参考的观点，在真正的线上使用场景中，协议会比本文中的复杂和更加有层次。具体格式如下：

```
{
    "id": "short", // 消息类型，1是文本协议格式；2是图片协议格式；3是文件协议格式
    "sender": "long", // 发送用户唯一id
    "reciever": "long", // 接受用户唯一id
    "data": "string" // 消息内容，如果是文本协议则为文本内容；如果是图片协议则为图片地址；如果是文件协议则为文件地址
}
复制代码
```

## 这套协议如何使用

### 发送消息

从协议格式可知，将上述数据按照上述固定顺序放入ArrayBuffer中，即可得到一个有特定含义的二进制数据。例如：

```
{
    "id": 1,
    "sender": "123",
    "reciever": "456",
    "data": "Hellow World"
}
复制代码
```

当我们需要发送此消息时，只需要：

1. 在前2个Byte放入`id`。
2. 接下来8个Byte中放入`sender`。
3. 再接下来8个Byte放入`reciever`。
4. 最后紧接着放入一个string类型（以[WebSocket系列之字符串如何与二进制数据进行转换](https://juejin.cn/post/6844903585528954894)博客中的格式为例，先将字符串长度构造成一个int类型，放在前4个Byte中，接下来将string类型编码后放入）。 此数据就完全按照协议构造完成了。我们只需将次协议通过WebSocket发送即可。具体方法将会在后面章节中说明。

### 接收消息

从协议格式可知，当我们收到一条消息时，只需要按照协议规范来进行反向解析即可。例如：

```
{
    "id": 1,
    "sender": "123",
    "reciever": "456",
    "data": "Hellow World"
}
复制代码
```

如果发送端发送的数据仍然为此消息，我们的解析顺序为：

1. 先根据前2个Byte读取一个Short类型的`id`数值。
2. 将接下来的8个Byte读取为Long类型的`sender`字段。
3. 再接下来的8个Byte读取为Long类型的`reciever`字段。
4. 接下来读取一个string类型（以`发送消息`这一节的数据为例，先读取4个Byte长度的int类型字符串长度，然后再根据长度读取字符串即可）。

### 扩展此协议

当此协议字段无法满足并且已经在线上使用时，我们应该如何扩展呢？ 根据我们的写入和读取步骤，我们可以知道：**每次我们读取的二进制数据可以认为是一个格式固定的数据（string类型在构造时会有长度信息，因此认为也是长度相对固定），所以我们在读取二进制数据时读取的长度也是固定的。**因此，我们在扩展时只需要往协议后面增加字段即可。

- 扩展前的应用仍然会读取之前已经确定的数据协议，只需要保证内容不变，那么功能也不会变。
- 扩展后的应用能够解析扩展后的协议，因此得到更多的数据，从而可以实现更多的功能。

# WebSocket如何发送二进制数据

通过`如何设计一个二进制协议`一章，我们知道了如何定义WebSocket传输的二进制数据格式。下面，我们来看下如何在WebSocket中发送二进制数据：

```
let arrayBuffer = getArrayBufferMessagesFromUser(); // 获取用户需要发送的消息数据,为一个ArrayBuffer对象
let webSocket = getWebSocket(); // 获取已经连接成功的WebSocket实例

websocket.binaryType = 'arraybuffer'; // 指定WebSocket接受ArrayBuffer实例作为参数

webSocket.send(arrayBuffer);
复制代码
```

通过上面的示例我们可以知道，WebSocket在发送string类型的数据或者ArrayBuffer类型的数据时，使用的API接口都是`send`方法，我们只需要在WebSocket初始化后指定传输类型`binaryType`即可。

# WebSocket如何处理接收的二进制数据

通过`WebSocket如何发送二进制数据`一章，我们知道了如何发送二进制数据。接下来，让我们开看下如何处理WebSocket接收到的二进制数据：

```
let webSocket = getWebSocket(); // 获取已经连接成功的WebSocket实例

websocket.binaryType = 'arraybuffer'; // 指定WebSocket接受ArrayBuffer实例作为参数

webSocket.addEventListener('message', (message) => {
    let arrayBuffer = message.data; // 获取用户接收到的消息数据,为一个ArrayBuffer对象
    let data = parseMessage(arrayBuffer); // 解析二进制数据
});
复制代码
```

通过上面的示例我们可以知道，当我们在建立连接时指定了传输类型`binaryType`为ArrayBuffer之后，我们通过WebSocket收到的数据也是一个ArrayBuffer实例。我们只需要根据第一章讲解的方式进行解析即可。

# 总结

本文作为WebSocket系列的第四篇，通过一个IM聊天应用的示例将前三篇博客分享的内容串联起来，给读者完整介绍了在WebSocket中使用二进制数据进行传输的方法以及相关的数据类型转换。 通过前面4篇博客的内容，读者可以根据自己的需求快速的构造和封装WebSocket进行二进制数据传输，基本能够串联整个应用流程。 WebSocket系列下一篇文章将会介绍在线上环境中，如何保证WebSocket的连接，以及线上可能遇到的问题。通过应对WebSocket可能出现的问题，我们能够让整个长连接更加健壮。

# SOCKET.IO

>
>
>尽管 Socket.IO 确实在可能的情况下使用 WebSocket 进行传输，但它为每个数据包添加了额外的元数据。这就是为什么 WebSocket 客户端将无法成功连接到 Socket.IO 服务器，而 Socket.IO 客户端也将无法连接到普通 WebSocket 服务器。

