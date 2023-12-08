# PHP基础

## 安装

### 文档

* [官网](https://www.php.net/manual/zh/)
* [扩展 pecl](https://pecl.php.net/)
* [PHP8.2新特性](https://www.php.net/releases/8.2/zh.php)
* [PHP8新特性](https://www.php.net/manual/zh/migration80.new-features.php)
* [PHP7新特性](https://www.php.net/manual/zh/migration70.new-features.php)
* [PHP Package Repository](https://packagist.org/)
* [Laravel 10 中文文档](https://learnku.com/docs/laravel/10.x)
* [laravel-admin](https://explore-pu.github.io/laravel-admin-docs-cn/guide/)

### 下载

[下载地址](https://www.php.net/downloads)

### yum安装
- [install doc](https://computingforgeeks.com/how-to-install-php-8-2-on-centos-rhel-7/?expand_article=1)

```bash
# Enable EPEL repository on your CentOS 7 / RHEL 7 system:
sudo yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
sudo yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
sudo yum-config-manager --enable remi-php82
# Install PHP 8.2 packages
sudo yum -y install php
# check
php -v
# Install PHP 8.2 extensions
sudo yum install php-{common,pear,cgi,curl,mbstring,gd,mysqlnd,gettext,bcmath,json,xml,fpm,intl,zip,imap}
# Using PHP 8.2 with Nginx
sudo yum install vim nginx php-fpm -y
# Start and enable nginx and php-fpm services.
sudo systemctl enable --now nginx php-fpm
```

### 源码编译安装
```bash
# 下载适合的版本
wget https://www.php.net/distributions/php-8.2.12.tar.gz
# 解压
tar -zxvf php-8.2.12.tar.gz
# 安装依赖
yum -y install libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel libxml2 libxml2-devel zlib zlib-devel curl curl-devel openssl openssl-devel
yum -y install gcc gcc-c++ libxslt-devel* mod_ssl libtool-ltdl* sqlite-devel oniguruma-devel perl* libzip autoconf

cd php8
# 配置
./configure --prefix=/usr/local/php8 \
--enable-fpm \
--enable-pcntl \
--enable-mysqlnd \
--enable-opcache \
--enable-sockets \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-shmop \
--enable-zip \
--enable-soap \
--enable-xml \
--enable-mbstring \
--disable-rpath \
--disable-debug \
--disable-fileinfo \
--with-fpm-user=www \
--with-fpm-group=www \
--with-freetype \
--with-mysqli=mysqlnd \
--with-pdo-mysql=mysqlnd \
--with-pcre-regex \
--with-iconv \
--with-zlib \
--with-gd \
--with-openssl \
--with-mhash \
--with-curl 
# 编译
make
# 安装
make install
```
```bash
# 添加环境变量
vim /etc/profile
PATH=$PATH:/usr/local/php8/bin
export PATH
# 立即生效
source /etc/profile

# check
php -i
```

## 常量

### 自定义常量

| 比较项 | define | const |
| --- | --- | --- |
| 版本 | php 4.0 + | php 5.3.0 + |
| 定义位置 | 任意 | 作用域的最顶端，不能在函数内、循环内以及 `if` 语句之内 |
| 是否支持表达式 | 是 | 否 |
| 是否支持大小写不敏感 | 是, 第三个参数为 `true` , 表示不敏感 | 否 |
| 常量数组支持的版本 | php 7 + | php 5.6 + |

详细说明, 参考 [PHP 中 const 和 define() 定义常量的细节区别](http://blog.csdn.net/cscrazybing/article/details/46989749)

### 预定义常量

是在 `PHP` 的内核中就定义好了的常量。区分大小写。这些变量包括了以下这些东西：从外部变量到内置的环境变量，从最新的错误信息到最新收到的 `header`。

常用的预定义常量有：

| 名称 | 说明 |
| --- | --- |
| PHP_VERSION | 当前 `php` 的版本 |
| PHP_OS | 当前所使用的操作系统类型 |
| PHP_SAPI | `web` 服务器与 `php` 之间的接口 |
| DEFAULT_INCLUDE_PATH | `php` 默认的包含路径 |
| PHP_BINDIR | `php` 的执行路径 |
| PHP_LIBDIR | `php` 扩展模块的路径 |
| PEAR_INSTALL_DIR | `pear` 的安装路径 |
| PEAR_EXTENSION_DIR | `pear` 的扩展路径 |
| E_ERROR | 指向最近的错误处 |
| E_WARNING | 指向最近的警告处 |
| E_NOTICE | 指向最近的注意处 |
| PHP_INT_MAX | 最大的整型数 |
| M_E | 自然对数 `e`值 |
| M_PI | 数学上的圆周率的值 |
| TRUE | 逻辑真值 |
| FALSE | 逻辑假值 |

### 预定义变量

```html
$GLOBALS — 引用全局作用域中可用的全部变量
$_SERVER — 服务器和执行环境信息
$_GET — HTTP GET 变量
$_POST — HTTP POST 变量
$_FILES — HTTP 文件上传变量
$_REQUEST — HTTP Request 变量
$_SESSION — Session 变量
$_ENV — 环境变量
$_COOKIE — HTTP Cookies
$php_errormsg — 前一个错误信息
$HTTP_RAW_POST_DATA — 原生POST数据
$http_response_header — HTTP 响应头
$argc — 传递给脚本的参数数目
$argv — 传递给脚本的参数数组
```

- 超全局变量

PHP 中的许多预定义变量都是“超全局的”，这意味着它们在一个脚本的全部作用域中都可用。在函数或方法中无需执行 `global $variable`; 就可以访问它们
超全局变量：`$GLOBALS`、`$_SERVER`、`$_GET`、`$_POST`、`$_FILES`、`$_COOKIE`、`$_SESSION`、`$_REQUEST`、`$_ENV`

### 魔术常量

| 名称 | 说明 |
| --- | --- |
| `__DIR__` | 文件所在的目录。如果用在被包括文件中，则返回被包括的文件所在的目录。它等价于 `dirname(__FILE__)`。除非是根目录，否则目录中名不包括末尾的斜杠。 【PHP 5.3.0+】 |
| `__FILE__` | 文件的完整路径和文件名。如果用在被包含文件中，则返回被包含的文件名。自 `PHP 4.0.2` 起，`__FILE__` 总是包含一个绝对路径（如果是符号连接，则是解析后的绝对路径），而在此之前的版本有时会包含一个相对路径。 |
| `__LINE__` | 文件中的当前行号。 |
| `__NAMESPACE__` | 当前命名空间的名称（区分大小写）。此常量是在编译时定义的。 【PHP 5.3.0+】 |
| `__FUNCTION__` | 函数名称。【PHP 4.3.0+】 |
| `__TRAIT__` | `Trait` 的名字，包括其被声明的作用区域（例如 `Foo\Bar`）【PHP 5.4.0+】 |
| `__CLASS__` | 类的名称。【PHP 4.3.0+】 |
| `__METHOD__` | 类的方法名。返回该方法被定义时的名字（区分大小写）。【PHP 5.0.0+】 |

### 魔术方法

1. `__construct`，`__destruct`
 `__constuct`构建对象的时被调用；
 `__destruct`明确销毁对象或脚本结束时被调用；
2. `__get`，`__set`
 `__set`当给不可访问或不存在属性赋值时被调用
 `__get`读取不可访问或不存在属性时被调用
3. `__isset`，`__unset`
 `__isset`对不可访问或不存在的属性调用isset()或empty()时被调用
 `__unset`对不可访问或不存在的属性进行unset时被调用
4. `__call`，`__callStatic`
 `__call`调用不可访问或不存在的方法时被调用
 `__callStatic`调用不可访问或不存在的静态方法时被调用
5. `__sleep`，`__wakeup`
 `__sleep`当使用serialize时被调用，当你不需要保存大对象的所有数据时很有用
 `__wakeup`当使用unserialize时被调用，可用于做些对象的初始化操作
6. `__clone` 进行对象clone时被调用，用来调整对象的克隆行为
7. `__toString` 当一个类被转换成字符串时被调用
8. `__invoke` 当以函数方式调用对象时被调用
9. _`_set_state` 当调用var_export()导出类时，此静态方法被调用。用__set_state的返回值做为var_export的返回值。
10. `__debuginfo` 当调用var_dump()打印对象时被调用（当你不想打印所有属性）适用于PHP5.6版本
