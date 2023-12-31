# nodejs基础

## 安装

### 下载

- [官方下载](https://nodejs.org/en/download/current)
- [npm](https://www.npmjs.com/)

### 二进制包安装

+ [文档](https://github.com/nodejs/help/wiki/Installation)

```bash
# 选择 LTS 版本的二进制包
wget https://nodejs.org/dist/v20.9.0/node-v20.9.0-linux-x64.tar.xz  
# 解压
mkdir -p /usr/local/nodejs
tar -xJvf node-v20.9.0-linux-x64.tar.xz  -C /usr/local/nodejs    
cd node-v20.9.0-linux-x64
# check
./bin/node -v 
```

+ 设置环境变量 `~/.profile` or `/etc/profile`

```bash
export PATH=/usr/local/lib/nodejs/nodejs/bin:$PATH
```

```bash
node -v
npm version
npm -v
```

## 介绍

- Node.js 是一个基于 `Chrome V8` 引擎的 `JavaScript` 运行时环境，用于构建快速、可扩展的网络应用程序。它允许开发人员使用 `JavaScript` 编程语言来编写服务器端应用程序，与传统的浏览器端 JavaScript 不同。Node.js 提供了丰富的内置库，使开发者可以轻松处理文件操作、网络通信、HTTP 请求、数据库访问等任务。
- Node.js 采用事件驱动的非阻塞 I/O 模型，这意味着它可以处理大量并发请求而不阻塞应用程序的执行。这使得 Node.js 特别适用于构建实时应用程序、聊天应用、在线游戏、数据流处理和其他需要高性能的应用。

## 内置模块

### path

> [官方文档](https://nodejs.cn/api/path)

* `__filename`。全局值，当前文件绝对路径
* `__dirname`。全局值，当前文件夹绝对路径。等效于path.resolve(__filename, '..')
* `path.join([...paths])`。相当于把所传入的任意多的参数 按照顺序 进行命令行般的推进
* `path.resolve([...paths])`。方法将路径或路径片段的序列解析为绝对路径。
* `path.dirname(path)`。返回指定路径**所在文件夹的路径**
* `path.basename(path)`。返回指定Path路径**所在文件的名字**
* `path.extname(path)`。获取指定字符串或者文件路径**名字的后缀名**，带.比如.txt
* `path.isAbsolute(path)` 是否是绝对路径,返回boolean值

``` js
path.join('a','b','../c/lolo') // a/c/lolo

path.resolve('/foo/bar', './baz'); // Returns: '/foo/bar/baz'
path.resolve('/foo/bar', '/tmp/file/'); // Returns: '/tmp/file'

const filePath = './bar/baz/asdf/quux.html'
path.basename(filePath) // quux.html
path.dirname(filePath) // ./bar/baz/asdf
path.extname(filePath) // .html
path.isAbsolute(filePath) // false
```

### url

> [官方文档](https://nodejs.cn/api/url)

url 模块提供了两套 API 来处理 URL 字符串：一个是Node.js特有的API，是旧版本的遗留；另一个则是实现了WHATWG URL Standard的 API (const {URL} = require('url')方式)，该标准也在各种浏览器中被使用。

+ 使用 WHATWG API 解析网址字符串：

```js
const myURL =
  new URL('https://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash'); 
```

+ 使用旧版 API 解析 URL 字符串：

```js
const url = require('node:url');
const myURL =
  url.parse('https://user:pass@sub.example.com:8080/p/a/t/h?query=string#hash');
```

### querystring

> [官方文档](https://nodejs.cn/api/querystring.html)

* `querystring.parse`。一个URL查询字符串 str 解析成一个键值对的集合。

``` js
const querystring = require('querystring')
let query = querystring.parse('type=123')
console.log(query) // { type: '123' }
```

* `querystring.stringify`。遍历给定的 obj 对象的自身属性，生成 URL 查询字符串。

```js
querystring.stringify({ foo: 'bar', baz: ['qux', 'quux'], corge: '' });
// Returns 'foo=bar&baz=qux&baz=quux&corge='
querystring.stringify({ foo: 'bar', baz: 'qux' }, ';', ':');
// Returns 'foo:bar;baz:qux' 
```

### Stream

> [官方文档](https://nodejs.cn/api/stream.html)

- 流是用于在 Node.js 中处理流数据的抽象接口。 `node:stream` 模块提供了用于实现流接口的 API。
- Node.js 提供了许多流对象。 例如，a `向 HTTP 服务器请求` 和 `process.stdout` 都是流实例。
- 流可以是可读的、可写的、或两者兼而有之。 所有的流都是 `EventEmitter` 的实例。

```js
const stream = require('node:stream'); 
```

#### 流的类型

Node.js 中有四种基本的流类型：

- `Writable`: 可以写入数据的流（例如，fs.createWriteStream()）。
- `Readable`: 可以从中读取数据的流（例如，fs.createReadStream()）。
- `Duplex`: Readable 和 Writable 的流（例如，net.Socket）。
- `Transform`: 可以在写入和读取数据时修改或转换数据的 Duplex 流（例如，zlib.createDeflate()）。

> 此外，此模块还包括实用函数 `stream.pipeline()`、`stream.finished()`、`stream.Readable.from()` 和 `stream.addAbortSignal()`。

### http

> [官方文档](https://nodejs.cn/api/http.html)

- 要使用 HTTP 服务器和客户端，则必须 `require('node:http')`。
- Node.js 中的 HTTP 接口旨在支持该协议的许多传统上难以使用的功能。 特别是大的，可能是块编码的消息。 接口从不缓冲整个请求或响应，因此用户能够流式传输数据。

演示如何监听 `connect` 事件的客户端和服务器对：

```js
const http = require('node:http');
const net = require('node:net');
const { URL } = require('node:url');

// Create an HTTP tunneling proxy
const proxy = http.createServer((req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('okay');
});
proxy.on('connect', (req, clientSocket, head) => {
  // Connect to an origin server
  const { port, hostname } = new URL(`http://${req.url}`);
  const serverSocket = net.connect(port || 80, hostname, () => {
    clientSocket.write('HTTP/1.1 200 Connection Established\r\n' +
                    'Proxy-agent: Node.js-Proxy\r\n' +
                    '\r\n');
    serverSocket.write(head);
    serverSocket.pipe(clientSocket);
    clientSocket.pipe(serverSocket);
  });
});

// Now that proxy is running
proxy.listen(1337, '127.0.0.1', () => {

  // Make a request to a tunneling proxy
  const options = {
    port: 1337,
    host: '127.0.0.1',
    method: 'CONNECT',
    path: 'www.google.com:80',
  };

  const req = http.request(options);
  req.end();

  req.on('connect', (res, socket, head) => {
    console.log('got connected!');

    // Make a request over an HTTP tunnel
    socket.write('GET / HTTP/1.1\r\n' +
                 'Host: www.google.com:80\r\n' +
                 'Connection: close\r\n' +
                 '\r\n');
    socket.on('data', (chunk) => {
      console.log(chunk.toString());
    });
    socket.on('end', () => {
      proxy.close();
    });
  });
});
```

### buffer

> [官方文档](https://nodejs.cn/api/buffer.html)

- `Buffer` 对象用于表示固定长度的字节序列。 许多 Node.js API 都支持 Buffer。
- `Buffer` 类是 JavaScript `Uint8Array` 类的子类，并使用涵盖额外用例的方法对其进行扩展。 Node.js API 在支持 Buffer 的地方也接受纯 `Uint8Array`
- 虽然 Buffer 类在全局作用域内可用，但仍然建议通过 `import` 或 `require` 语句显式地引用它。
  
```js
const { Buffer } = require('node:buffer');
```

``` js
const { Buffer } = require('node:buffer');

// Creates a zero-filled Buffer of length 10.
const buf1 = Buffer.alloc(10);

// Creates a Buffer of length 10,
// filled with bytes which all have the value `1`.
const buf2 = Buffer.alloc(10, 1);

// Creates an uninitialized buffer of length 10.
// This is faster than calling Buffer.alloc() but the returned
// Buffer instance might contain old data that needs to be
// overwritten using fill(), write(), or other functions that fill the Buffer's
// contents.
const buf3 = Buffer.allocUnsafe(10);

// Creates a Buffer containing the bytes [1, 2, 3].
const buf4 = Buffer.from([1, 2, 3]);

// Creates a Buffer containing the bytes [1, 1, 1, 1] – the entries
// are all truncated using `(value & 255)` to fit into the range 0–255.
const buf5 = Buffer.from([257, 257.5, -255, '1']);

// Creates a Buffer containing the UTF-8-encoded bytes for the string 'tést':
// [0x74, 0xc3, 0xa9, 0x73, 0x74] (in hexadecimal notation)
// [116, 195, 169, 115, 116] (in decimal notation)
const buf6 = Buffer.from('tést');

// Creates a Buffer containing the Latin-1 bytes [0x74, 0xe9, 0x73, 0x74].
const buf7 = Buffer.from('tést', 'latin1');
```

在 Buffer 和字符串之间转换时，可以指定字符编码。 如果未指定字符编码，则默认使用 UTF-8。

```js
const { Buffer } = require('node:buffer');

const buf = Buffer.from('hello world', 'utf8');

console.log(buf.toString('hex'));
// Prints: 68656c6c6f20776f726c64
console.log(buf.toString('base64'));
// Prints: aGVsbG8gd29ybGQ=

console.log(Buffer.from('fhqwhgads', 'utf8'));
// Prints: <Buffer 66 68 71 77 68 67 61 64 73>
console.log(Buffer.from('fhqwhgads', 'utf16le'));
// Prints: <Buffer 66 00 68 00 71 00 77 00 68 00 67 00 61 00 64 00 73 00>
```

### fs

> [官方文档](https://nodejs.cn/api/fs.html)

+ node:fs 模块能够以标准 POSIX 函数为模型的方式与文件系统进行交互。

- 要使用基于 promise 的 API：

```js
const fs = require('node:fs/promises');
```

- 要使用回调和同步的 API：

```js
const fs = require('node:fs');
```

#### Promise

基于 promise 的操作会返回一个当异步操作完成时被履行的 promise。

```js
const { unlink } = require('node:fs/promises');

(async function(path) {
  try {
    await unlink(path);
    console.log(`successfully deleted ${path}`);
  } catch (error) {
    console.error('there was an error:', error.message);
  }
})('/tmp/hello');
```

#### 回调

回调的形式将完成回调函数作为其最后一个参数并且异步地调用该操作。 传给完成回调的参数取决于方法，但是第一个参数始终预留用于异常。 如果操作成功地完成，则第一个参数为 null 或 undefined。

```js
const { unlink } = require('node:fs');

unlink('/tmp/hello', (err) => {
  if (err) throw err;
  console.log('successfully deleted /tmp/hello');
});
```

> 当需要最大性能（在执行时间和内存分配方面）时，node:fs 模块 API 的基于回调的版本比使用 promise API 更可取。

#### 同步

同步的 API 会阻塞 Node.js 事件循环和下一步的 JavaScript 执行，直到操作完成。 异常会被立即地抛出，可以使用 try…catch 来处理，也可以允许冒泡。

```js
const { unlinkSync } = require('node:fs');

try {
  unlinkSync('/tmp/hello');
  console.log('successfully deleted /tmp/hello');
} catch (err) {
  // handle the error
}
```

### Process

> [官方文档](https://nodejs.cn/api/process.html)

process 对象提供有关当前 Node.js 进程的信息并对其进行控制。

```js
const process = require('node:process');
```

## 加解密 Crypto

### 常见编码

- hex编码 将一个 8 位的字节数据用两个 16 进制数表示出来

```html
ASCII码：A(65)

二进制码:0100 0001

重新分组: 00000100  00000001

十六进制: 4         1

Hex编码：41
```

- Base64 编码是通过 64 个字符来表示二进制数据，64 个字符表示二进制数据只能表示 6 位，所以它可以通过 4 个 Base64 字符来表示 3 个字节，
- urlencode 编码 非英文编码为%xx(16 进制)的形式，其中xx就是这个字节对应的 hex 编码

### md5

```js
var crypto = require('crypto');
var md5 = crypto.createHash('md5');

var result = md5.update('123456').digest('hex');
```

#### MD5碰撞

> 简单的说，就是两段不同的字符串，经过MD5运算后，得出相同的结果。

```js
function getHashResult(hexString){

    // 转成16进制，比如 0x4d 0xc9 ...
    hexString = hexString.replace(/(\w{2,2})/g, '0x$1 ').trim();

    // 转成16进制数组，如 [0x4d, 0xc9, ...]
    var arr = hexString.split(' ');

    // 转成对应的buffer，如：<Buffer 4d c9 ...>
    var buff = Buffer.from(arr);

    var crypto = require('crypto');
    var hash = crypto.createHash('md5');

    // 计算md5值
    var result = hash.update(buff).digest('hex');

    return result;  
}

var str1 = 'd131dd02c5e6eec4693d9a0698aff95c2fcab58712467eab4004583eb8fb7f8955ad340609f4b30283e488832571415a085125e8f7cdc99fd91dbdf280373c5bd8823e3156348f5bae6dacd436c919c6dd53e2b487da03fd02396306d248cda0e99f33420f577ee8ce54b67080a80d1ec69821bcb6a8839396f9652b6ff72a70';
var str2 = 'd131dd02c5e6eec4693d9a0698aff95c2fcab50712467eab4004583eb8fb7f8955ad340609f4b30283e4888325f1415a085125e8f7cdc99fd91dbd7280373c5bd8823e3156348f5bae6dacd436c919c6dd53e23487da03fd02396306d248cda0e99f33420f577ee8ce54b67080280d1ec69821bcb6a8839396f965ab6ff72a70';

var result1 = getHashResult(str1);
var result2 = getHashResult(str2);

if(result1 === result2) {
    console.log(`Got the same md5 result: ${result1}`);
}else{
    console.log(`Not the same md5 result`);
}
```

### hmac

```js
var crypto = require('crypto');
const hmac = crypto.createHmac('sha256', "secret");

var result = hmac.update("123456").digest('hex')
```

### 加密 / 解密

+ 加密：
    + crypto.createCipher(algorithm, password)
    + crypto.createCipheriv(algorithm, key, iv)

+ 解密：
    + crypto.createDecipher(algorithm, password)
    + crypto.createDecipheriv(algorithm, key, iv)

```js
const crypto = require("crypto")

const SECRET = 'secret'
const ALGORITHM = 'aes192'
const content = '123456'
const encoding = 'hex'

// 加密
const cipher = crypto.createCipher(ALGORITHM, SECRET)
cipher.update(content)
const output = cipher.final(encoding)

// 解密
const decipher = crypto.createDecipher(ALGORITHM, SECRET)
decipher.update(output, encoding)
const input = decipher.final('utf8')
```

```js
const crypto = require("crypto")

// 比如 aes128、aes192、aes256，长度分别是 128、192、256 位（16、24、32 字节）
const key = crypto.randomBytes(192 / 8)
// iv：初始化向量，都是 128 位（16 字节），也可以理解为密码盐的一种
const iv = crypto.randomBytes(128 / 8)
const algorithm = 'aes192'
const encoding = 'hex'

const encrypt = (text) => {
    const cipher = crypto.createCipheriv(algorithm, key, iv)
    cipher.update(text)
    return cipher.final(encoding)
}

const decrypt = (encrypted) => {
    const decipher = crypto.createDecipheriv(algorithm, key, iv)
    decipher.update(encrypted, encoding)
    return decipher.final('utf8')
}

const content = '123456'
const crypted = encrypt(content)

const decrypted = decrypt(crypted)
```

### 数字签名 / 签名校验

```js
const crypto = require('crypto');
const fs = require('fs');
const privateKey = fs.readFileSync('./private-key.pem');  // 私钥
const publicKey = fs.readFileSync('./public-key.pem');  // 公钥
const algorithm = 'RSA-SHA256';  // 加密算法 vs 摘要算法
const encoding = 'hex'

// 数字签名
function sign(text){
    const sign = crypto.createSign(algorithm);
    sign.update(text);
    return sign.sign(privateKey, encoding);
}

// 校验签名
function verify(oriContent, signature){
    const verifier = crypto.createVerify(algorithm);
    verifier.update(oriContent);
    return verifier.verify(publicKey, signature, encoding);
}

// 对内容进行签名
const content = '123456';
const signature = sign(content);

// 校验签名，如果通过，返回true
const verified = verify(content, signature);
```

### DH(DiffieHellman)

> DiffieHellman：Diffie–Hellman key exchange，缩写为 D-H，是一种安全协议，常用于密钥交换，让通信双方在预先没有对方信息的情况下，通过不安全通信信道，创建一个密钥。这个密钥可以在后续的通信中，作为对称加密的密钥加密传递的信息。

```js
const crypto = require('crypto');

const primeLength = 1024;  // 素数p的长度
const generator = 5;  // 素数a

// 创建客户端的DH实例
const client = crypto.createDiffieHellman(primeLength, generator);
// 产生公、私钥对，Ya = a^Xa mod p
const clientKey = client.generateKeys();

// 创建服务端的DH实例，采用跟客户端相同的素数a、p
const server = crypto.createDiffieHellman(client.getPrime(), client.getGenerator());
// 产生公、私钥对，Yb = a^Xb mod p
const serverKey = server.generateKeys();

// 计算 Ka = Yb^Xa mod p
const clientSecret = client.computeSecret(server.getPublicKey());
// 计算 Kb = Ya^Xb mod p
const serverSecret = server.computeSecret(client.getPublicKey());

// 由于素数p是动态生成的，所以每次打印都不一样
// 但是 clientSecret === serverSecret
console.log(clientSecret.toString('hex'));
console.log(serverSecret.toString('hex'));
```

### ECDH(Elliptic Curve Diffie-Hellma)

> ECDH 和 DH 原理类似，都是安全密钥协商协议。相对于 DH 协议，结合椭圆曲线密码学 ECC 加速，运算更节省 CPU 资源

```js
const crypto = require('crypto');

const G = 'secp521r1';
const encoding = 'hex'

const server = crypto.createECDH(G);
const serverKey = server.generateKeys();

const client = crypto.createECDH(G);
const clientKey = client.generateKeys();

const serverSecret = server.computeSecret(clientKey);
const clientSecret = client.computeSecret(serverKey);

console.log(serverSecret.toString(encoding));
console.log(clientSecret.toString(encoding));
```

## 应用场景

### 将图片转成datauri

```js
var fs = require('fs');
var filepath = './1.png';

var bData = fs.readFileSync(filepath);
var base64Str = bData.toString('base64');
var datauri = 'data:image/png;base64,' + base64Str;
```

## eggjs

+ [官网文档](https://www.eggjs.org/zh-CN/tutorials)

## 相关链接

- [Nodejs学习笔记](https://github.com/chyingp/nodejs-learning-guide/tree/master)
- [NodeJS加解密之Crypto](https://www.lebronchao.com/2021/12/22/NodeJS%E5%8A%A0%E8%A7%A3%E5%AF%86%E4%B9%8BCrypto/)