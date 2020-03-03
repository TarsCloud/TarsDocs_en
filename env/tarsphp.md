# TarsPHP
> * [Intro](#chapter-1)
> * [Install PHP](#chapter-2)
> * [Install swoole](#chapter-3)
> * [Install tarsphp extension](#chapter-4)


# <a id="chapter-1"></a>Intro

Proposed installation: php7+swoole2

# <a id="chapter-2"></a>Install PHP

```text
https://github.com/php/php-src 
```

# <a id="chapter-3"></a>Install swoole

```text
swoole: https://github.com/swoole/swoole-src
install: https://wiki.swoole.com/wiki/page/6.html
PECL Auto click download and installation
    pecl install swoole
```

PHP needs to install the extension，Please in [https://github.com/TarsPHP/tars-extension](https://github.com/TarsPHP/tars-extension) compiler and add extension into php.ini, see follows:

# <a id="chapter-4"></a>Install tarsphp extension

project: [https://github.com/TarsPHP/tars-extension](https://github.com/TarsPHP/tars-extension)

```text
git clone https://github.com/TarsPHP/tars-extension.git
cd tars-extension
sudo phpize 
sudo ./configure
sudo make 
sudo make install
```

In php.ini add: extension=phptars.so





