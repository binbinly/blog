# 深入 Laravel 核心

## 文档

- [中文文档-10.x](https://learnku.com/docs/laravel/10.x)

## 前言

laravel 优缺点
+ 优点
    + 使用composer包管理工具，大量的第三方开源库，方便使用丰富的扩展包，开发效率高，维护方便
    + 文档丰富，社区活跃，github star 在 php 分类排名第一
+ 缺点
    + 基于组件式的框架，加载组件多，牺牲了一些运行效率
    + 新手上手相对较难，laravel 采用了 php 比较新的特性，闭包等等，IOC 容器，中间件，事件等

## 控制反转，依赖注入，反射

```php
// 定义写日志的接口规范
interface Log
{
    public function write();   
}

// 文件记录日志
class FileLog implements Log
{
    public function write(){
        echo 'file log write...';
    }   
}


// 数据库记录日志
class DatabaseLog implements Log
{
    public function write(){
        echo 'database log write...';
    }   
}

// 程序操作类
class User 
{
    protected $fileLog;

    public function __construct()
    {
        $this->fileLog = new FileLog();   
    }

    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->fileLog->write();
    }

}

$user = new User();
$user->login();
```

上面的写法可以实现记录日志的功能，但是有一个问题，假设现在想用数据库记录日志的话，我们就得修改 `User` 类，这份代码没达到解耦合，

```php
class User 
{
    protected $log;

    public function __construct(Log $log)
    {
        $this->log = $log;   
    }

    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->log->write();
    }

}

$user = new User(new DatabaseLog());
$user->login();
```

这样想用任何方式记录操作日志都不需要去修改 `User` 类了，只需要通过构造函数参数传递就可以实现，其实这就是 **控制反转**。

> 不需要自己修改内容，改成由外部传递。这种由外部负责其依赖需求的行为，我们可以称其为 **控制反转（IoC）**。

> 不是由自己内部 `new` 对象或者实例，通过构造函数，或者方法传入的都属于 **依赖注入（DI）** 。上面的例子也算是依赖注入，

### laravel 依赖注入

```php
// routes/web.php
Route::get('/post/store', 'PostController@store');

// App\Http\Controllers
class PostController extends Controller {

    public function store(Illuminate\Http\Request $request)
    {
        $this->validate($request, [
            'category_id' => 'required',
            'title' => 'required|max:255|min:4',
            'body' => 'required|min:6',
        ]);
    }

}
```

### 反射理解

+ 反射常用的 api

```php
// 获取User的reflectionClass对象
$reflector = new ReflectionClass(User::class);

// 拿到User的构造函数
$constructor = $reflector->getConstructor();

// 拿到User的构造函数的所有依赖参数
$dependencies = $constructor->getParameters();

// 创建user对象
$user = $reflector->newInstance();

// 创建user对象，需要传递参数的
$user = $reflector->newInstanceArgs($dependencies = []);
```

+ 具体代码实现

```php
// 注意我们这里需要修改一下User的构造函数，如果不去修改。反射是不能动态创建接口的，那如果非要用接口该怎么处理呢？下一节我们讲Ioc容器的时候会去解决。

class User 
{
    protected $log;

    public function __construct(FileLog $log)
    {
        $this->log = $log;   
    }

    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->log->write();
    }

}

function make($concrete){

    $reflector = new ReflectionClass($concrete);
    $constructor = $reflector->getConstructor();
    // 为什么这样写的? 主要是递归。比如创建FileLog不需要传入参数。
    if(is_null($constructor)) {
        return $reflector->newInstance();
    }else {
        // 构造函数依赖的参数
        $dependencies = $constructor->getParameters();
        // 根据参数返回实例，如FileLog
        $instances = getDependencies($dependencies);
        return $reflector->newInstanceArgs($instances);
    }

}

function getDependencies($paramters) {
    $dependencies = [];
    foreach ($paramters as $paramter) {
        $dependencies[] = make($paramter->getClass()->name);
    }
    return $dependencies;
}

$user = make('User');
$user->login();
```

## Ioc容器和服务提供者

### 什么是 `Ioc` 容器和服务提供者？

> `Ioc` 容器 就是管理类的依赖和执行依赖注入的工具
>
> `服务提供者` 就是把服务注册、绑定到容器上的地方

### 具体代码实现的思路

1. Ioc 容器维护 `binding` 数组记录 `bind` 方法传入的键值对如:`log=>FileLog`, `user=>User`
2. 在 `ioc->make ('user')` 的时候，通过反射拿到 `User` 的构造函数，拿到构造函数的参数，发现参数是 `User` 的构造函数参数 `log`, 然后根据 `log` 得到 `FileLog。`
3. 这时候我们只需要通过反射机制创建 `$filelog = new FileLog()`;
4. 通过 `newInstanceArgs` 然后再去创建 `new User ($filelog)`;

```php

//实例化ioc容器
$ioc = new Ioc();
$ioc->bind('Log','FileLog');
$ioc->bind('user','User');
$user = $ioc->make('user');
$user->login();
```

> 这里的容器就是指 Ioc 容器，服务提供者就是 User。

### 核心的 Ioc 容器代码编写

```php
interface Log
{
    public function write();
}

// 文件记录日志
class FileLog implements Log
{
    public function write(){
        echo 'file log write...';
    }
}

// 数据库记录日志
class DatabaseLog implements Log
{
    public function write(){
        echo 'database log write...';
    }
}

class User
{
    protected $log;
    public function __construct(Log $log)
    {
        $this->log = $log;
    }
    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->log->write();
    }
}

class Ioc
{
    public $binding = [];

    public function bind($abstract, $concrete)
    {
        //这里为什么要返回一个closure呢？因为bind的时候还不需要创建User对象，所以采用closure等make的时候再创建FileLog;
        $this->binding[$abstract]['concrete'] = function ($ioc) use ($concrete) {
            return $ioc->build($concrete);
        };

    }

    public function make($abstract)
    {
        // 根据key获取binding的值
        $concrete = $this->binding[$abstract]['concrete'];
        return $concrete($this);
    }

    // 创建对象
    public function build($concrete) {
        $reflector = new ReflectionClass($concrete);
        $constructor = $reflector->getConstructor();
        if(is_null($constructor)) {
            return $reflector->newInstance();
        }else {
            $dependencies = $constructor->getParameters();
            $instances = $this->getDependencies($dependencies);
            return $reflector->newInstanceArgs($instances);
        }
    }

    // 获取参数的依赖
    protected function getDependencies($paramters) {
        $dependencies = [];
        foreach ($paramters as $paramter) {
            $dependencies[] = $this->make($paramter->getClass()->name);
        }
        return $dependencies;
    }

}

//实例化IoC容器
$ioc = new Ioc();
$ioc->bind('Log','FileLog');
$ioc->bind('user','User');
$user = $ioc->make('user');
$user->login();
```

### laravel 中的服务容器和服务提供者

> 可以在 `config` 目录找到 `app.php` 中 `providers`, 这个数组定义的都是已经写好的服务提供者

```php
$providers = [
    Illuminate\Auth\AuthServiceProvider::class,
    Illuminate\Broadcasting\BroadcastServiceProvider::class,
    Illuminate\Bus\BusServiceProvider::class,
    Illuminate\Cache\CacheServiceProvider::class,
    ...
]
...

// 随便打开一个类比如CacheServiceProvider，这个服务提供者都是通过调用register方法注册到ioc容器中，其中的app就是Ioc容器。singleton可以理解成我们的上面例子中的bind方法。只不过这里singleton指的是单例模式。

class CacheServiceProvider{
    public function register()
    {
        $this->app->singleton('cache', function ($app) {
            return new CacheManager($app);
        });

        $this->app->singleton('cache.store', function ($app) {
            return $app['cache']->driver();
        });

        $this->app->singleton('memcached.connector', function () {
            return new MemcachedConnector;
        });
    }
}
```

## Contracts契约之面向接口编程

> 契约就是所谓的面向接口编程。

```php

// 文件记录日志
class FileLog
{
    public function write(){
        echo 'file log write...';
    }
}

// 数据库记录日志
class DatabaseLog
{
    public function write(){
        echo 'database log write...';
    }
}

class User
{
    protected $log;
    public function __construct(FileLog $log)
    {
        $this->log = $log;
    }
    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->log->write();
    }
}

$user = new User(new FileLog());
$user->login();
```

就说上面的，看似没有什么问题，那如果随着我们日后需求的变更，想更换数据库作为记录日志的方式呢？那就得去改 User 类，没有解偶。

所以才有了面向接口编程，也就是 laravel 中的契约。代码修改如下。

```php

// 定义日志的接口规范
interface log
{
    public function write();   
}

// 文件记录日志
class FileLog implements Log
{
    public function write(){
        echo 'file log write...';
    }   
}

// 数据库记录日志
class DatabaseLog implements Log
{
    public function write(){
        echo 'database log write...';
    }   
}

class User 
{
    protected $log;

    public function __construct(Log $log)
    {
        $this->log = $log;   
    }

    public function login()
    {
        // 登录成功，记录登录日志
        echo 'login success...';
        $this->log->write();
    }

}

$user = new User(new DatabaseLog());
$user->login();
```

### 在 Laravel 中契约是什么样子的？

> 比如 `Cache`，定义的契约规范在 `Illuminate\Contracts\Cache\Repository` 文件中。

## Facades外观模式背后实现原理

### 外观模式 Facade 理解

上文需要 `$ioc->make ('user')` 才能拿到 `User` 的实例，再去使用 `$user->login ()`; 那能不能更方便点，比如下面的用法，是不是很方便。

```php
UserFacade::login(); 
```

### Facade 工作原理

- Facade 核心实现原理就是在 UserFacade 提前注入 Ioc 容器。
- 定义一个服务提供者的外观类，在该类定义一个类的变量，跟 ioc 容器绑定的 key 一样，
- 通过静态魔术方法__callStatic 可以得到当前想要调用的 login
- 使用 static::$ioc->make ('user');

### 具体实现 Facade

```php

class UserFacade
{
    // 维护Ioc容器
    protected static $ioc;

    public static function setFacadeIoc($ioc)
    {
        static::$ioc = $ioc;
    }

    // 返回User在Ioc中的bind的key
    protected static function getFacadeAccessor()
    {
        return 'user';
    }

    // php 魔术方法，调用不可访问或不存在的静态方法时被调用
    public static function __callStatic($method, $args)
    {
        $instance = static::$ioc->make(static::getFacadeAccessor());
        return $instance->$method(...$args);
    }

}

//实例化IoC容器

$ioc = new Ioc();
$ioc->bind('Log','FileLog');
$ioc->bind('user','User');

UserFacade::setFacadeIoc($ioc);

UserFacade::login();
```

##  Laravel中间件

> Laravel 中间件提供了一种方便的机制来过滤进入应用的 HTTP 请求

我们也采用面向接口编程的形式，来定义我们的中间件

```php

interface Milldeware {
    public static function handle(Closure $next);
}

class VerfiyCsrfToekn implements Milldeware {

    public static function handle(Closure $next)
    {
        echo '验证csrf Token <br>';
        $next();
    }
}

class VerfiyAuth implements Milldeware {

    public static function handle(Closure $next)
    {
        echo '验证是否登录 <br>';
        $next();
    }
}

class SetCookie implements Milldeware {
    public static function handle(Closure $next)
    {
        $next();
        echo '设置cookie信息！';
    }
}

function call_middware() {
    SetCookie::handle(function (){
        VerfiyAuth::handle(function() {
            $handle = function() {
                echo '当前要执行的程序!';
            };
            VerfiyCsrfToekn::handle($handle);
        });
    });
}

call_middware();
```

明白了其中原理，有些同学想这样代码肯定不会很好维护和扩展啊。那我们应该怎么去修改我们的代码呢？

> php 两个核心函数 `call_user_func()` `array_reduce()`

```php

    interface Milldeware {
        public static function handle(Closure $next);
    }

    class VerfiyCsrfToekn implements Milldeware {

        public static function handle(Closure $next)
        {
            echo '验证csrf Token <br>';
            $next();
        }
    }

    class VerfiyAuth implements Milldeware {

        public static function handle(Closure $next)
        {
            echo '验证是否登录 <br>';
            $next();
        }
    }

    class SetCookie implements Milldeware {
        public static function handle(Closure $next)
        {
            $next();
            echo '设置cookie信息！';
        }
    }

    $handle = function() {
        echo '当前要执行的程序!';
    };

    $pipe_arr = [
        'VerfiyCsrfToekn',
        'VerfiyAuth',
        'SetCookie',
    ];

    $callback = array_reduce($pipe_arr,function($stack,$pipe) {
        return function() use($stack,$pipe){
            return $pipe::handle($stack);
        };
    },$handle);

    call_user_func($callback);
```

 ## Laravel生命周期

 ```php
 
// 定义了laravel一个请求的开始时间
define('LARAVEL_START', microtime(true));

// composer自动加载机制
require __DIR__.'/../vendor/autoload.php';

// 这句话你就可以理解laravel,在最开始引入了一个ioc容器。
$app = require_once __DIR__.'/../bootstrap/app.php';

// 打开__DIR__.'/../bootstrap/app.php';你会发现这段代码，绑定了Illuminate\Contracts\Http\Kernel::class，
// 这个你可以理解成之前我们所说的$ioc->bind();方法。
// $app->singleton(
//     Illuminate\Contracts\Http\Kernel::class,
//    App\Http\Kernel::class
// );

// 这个相当于我们创建了Kernel::class的服务提供者
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

// 获取一个 Request ，返回一个 Response。以把该内核想象作一个代表整个应用的大黑盒子，
// 输入 HTTP 请求，返回 HTTP 响应。
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

// 就是把我们服务器的结果返回给浏览器。
$response->send(); 

// 这个就是执行我们比较耗时的请求，
$kernel->terminate($request, $response);
 ```

走到这里你会发现，是不是在我们学会了 ioc，服务提供者理解起来就比较简单了。那 $middleware，服务提供者都是在哪个文件注册运行的呢？

打开 `App\Http\Kernel::class` 这个文件，你会发现定义了一堆需要加载的 `$middleware`。这个 kernel 的主要方法还是在他的父类里面 Illuminate\Foundation\Http\Kernel 中。

打开 Illuminate\Foundation\Http\Kernel，你会发现定义了启动时需要做的事呢？

```php
protected $bootstrappers = [
    \Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables::class,
    \Illuminate\Foundation\Bootstrap\LoadConfiguration::class,
    \Illuminate\Foundation\Bootstrap\HandleExceptions::class,
    \Illuminate\Foundation\Bootstrap\RegisterFacades::class,
    \Illuminate\Foundation\Bootstrap\RegisterProviders::class,
    \Illuminate\Foundation\Bootstrap\BootProviders::class,
];
```

> $`bootstrappers` 就定义了我们的 `RegisterFacades.class`,`RegisterProviders.class` 这两个类的意思就是要将我们在 `app.config` 中的 `Providers`,`Facades` 注入到我们的 `Ioc` 容器中

## Laravel事件之观察者模式

```php
/**
 * 观察者接口类
 * Interface Observer
 */
interface Observer
{
    public function update($event_info = null);
}

/**
 * 观察者1
 */
class Observer1 implements Observer
{
    public function update($event_info = null)
    {
        echo "观察者1 收到执行通知 执行完毕！\n";
    }
}

/**
 * 观察者2
 */
class Observer2 implements Observer
{
    public function update($event_info = null)
    {
        echo "观察者2 收到执行通知 执行完毕！\n";
    }
}

/**
 * 事件
 * Class Event
 */
class Event
{
    //用于观察者注册的数组
    protected $this->observers = [];

    //增加观察者
    public function add(Observer $observer)
    {
        $this->observers[] = $observer;
    }

    //事件通知
    public function notify()
    {
        foreach ($this->observers as $observer) {
            $observer->update();
        }
    }

    /**
     * 触发事件
     */
    public function trigger()
    {
        //通知观察者
        $this->notify();
    }
}

//创建事件
$event = new Event();
//为事件增加旁观者
$event->add(new Observer1());
$event->add(new Observer2());
//执行事件
$event->trigger();
```

————————————————
> 原文作者：cxp1539
> 转自链接：https://learnku.com/docs/laravel-core-concept/5.5
> 版权声明：著作权归作者所有。商业转载请联系作者获得授权，非商业转载请保留以上作者信息和原文链接。