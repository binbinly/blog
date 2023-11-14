# PHP运行机制

## PHP设计理念及特点

- 多进程模型：由于PHP是多进程模型，不同请求间互不干涉，这样保证了一个请求挂掉不会对全盘服务造成影响，PHP也早支持多线程模型。
- 弱类型语言：一个变量的类型并不是一开始就确定不变的，运行中才会确定并可能发生隐式或显示的类型转换。
- 引擎（`Zend`）+ 组件（`ext`）的模式降低内部耦合。中间层（`sapi`）隔绝web server和PHP。

## PHP的四层体系

1. `Zend引擎`整体用纯c实现，是PHP的内核部分，主要功能：将PHP代码翻译成可以执行的`opcode`以及实现相关的处理方法，实现了基础的数据结构（如：hashtable）,内存分配机制以及管理，同时提供相关的API方法供外部去调用。
2. `Extensions`围绕着Zend引擎，通过组件化的方式，提供各种基础服务，比如常见的php内置函数（array()等等），以及一系列的标准库，同时也可以自定义实现自己想要的扩展应用
3. `Sapi` 全称Server Application Programming Interface，也就是服务端应用编程接口，Sapi通过一系列钩子函数，使得PHP可以和外围交互数据，这是PHP非常优雅和成功的设计，通过sapi成功的将PHP本身和上层应用解耦隔离，PHP可以不再考虑如何针对不同应用进行兼容，而应用本身也可以针对自己的特点实现不同的处理方式。
4. `上层应用`平时编写的PHP程序，通过不同的spai方式得到各种各样的应用模式，如何通过webserver实现web应用、在命令行下已脚本方式运行等等。

## PHP常见的SAPI

- CGI（通用网关接口 - Common Gateway Interface)
- FastCGI（常驻型CGI Long-Live-CGI)
- Web模块模式（Apache等Web服务器运行的模式)
- CLI（命令行运行 - Comman-Line-Interface)

### CGI 模式

CGI模式是最原始最通用的一种运行模式，他其实是一个广泛使用的通讯接口，事实上早期的PHP是不能提供真正的WebServer的，所以主要依赖Apache 和Nginx 以及IIS等WebServer。

当通过Http访问WebServer时，由WebServer根据其配置去调启PHP解析器执行用户当前访问的.php文件，当解释器执行完毕时会将内容通过CGI接口返回给WebServer，随后WebServer根据其内容展示给访问者。

这便是简单最原始的访问过程，但是现在太多的PHP开发者完全使用框架开发，逐渐忘记了实际的执行过程，我认为合格的开发者是需要认真的了解的。

到今天为止CGI实际上已经基本趋于淘汰了，因为CGI解释器启动时会重新加载几乎所有的配置，然后才开始解析 `.php` 文件。目前已经被  `FastCGI` 取代。

### FastCGI 模式

FastCGI实际上是CGI的升级版本，他通过由一个常驻的Master管理进程管理其他的CGI解析器进程~~(关于多进程的问题我会在后续的文章中给出)~~，也就是说在FastCGI模式中，大部分全局配置都会在第一次启动Master进程时被加载。

在后续的处理过程中，并不会再次去加载配置文件。事实上PHP-FPM便是PHP的FastCGI进程管理器，正如你必须启动PHP-FPM后WebServer才能正常的解析 `.php` 文件，而你修改了 `php.ini` 文件内容时，又需要通过重启PHP-FPM来更新配置。

有部署LNMP环境的开发者应该很清楚必须在Nginx.conf文件中加入一段**反向代理**代码才能让WebServer正确的解析，所以和CGI模式不同，FastCGI模式下，请求到达WebServer后会通过反向代理转发给指定端口的PHP-FPM由他再次指定空闲的CGI子进程解析PHP代码并返回给WebServer(正如你猜想的那样，我们可以通过反向代理非本机的PHP-FPM来实现分布式)。

### Model 模式

Model模式其实是CGI模式的一种特例化版本，他通过集成到WebServer中使得WebServer拥有了直接解析.php文件的能力。最为常见的便是Apache中使用PHP，但是其本质并没有任何区别，都是由WebServer将 `.php` 文件交给PHP解析器处理。然后返回给WebServer的模式。

如果说和CGI、FastCGI模式有什么区别，那就是Model模式下PHP会随着WebServer(Apache)的运行而运行，这可能会导致PHP或是Apache出现问题导致整个WebServer挂掉。

目前 `Apache` 也支持了FastCGI 模式使得其有了更好的性能。

### CLI 模式

CLI模式其实是一个较为新鲜的玩意，他在PHP5.4的时候才加入，在你使用命令行去执行.php文件时就是CLI模式；但是互联网环境的发展，短生命周期的PHP-FPM方式已经逐渐无法很好的胜任商业上更高的需求了，高并发、长连接、分布式等等都对PHP提出了更高的挑战，而CLI的出现和许多优秀开发者的共同努力下，像workerman这类纯PHP开发的基于CLI模式框架，以及仅支持CLI模式的Swoole扩展，都给PHP带来了更高更快更强的新体验。