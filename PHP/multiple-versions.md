### windows下设置多个版本PHP 以及多个composer的方法

在php官网下载编译好的PHP，是免安装的，分别配置在不同目录, 并且设置环境变量，例如:
- C:\Application\php8.0.30
- C:\Application\php8.2.10

把目录下的 php.exe 改成 php80.exe、php82.exe 即可
```shell
	PS C:\Users\username> php80 -v
	PHP 8.0.30 (cli) (built: Sep  1 2023 14:08:48) ( NTS Visual C++ 2019 x64 )
	Copyright (c) The PHP Group
	Zend Engine v4.0.30, Copyright (c) Zend Technologies

	PS C:\Users\username> php82 -v
	PHP 8.2.10 (cli) (built: Aug 30 2023 09:46:43) (NTS Visual C++ 2019 x64)
	Copyright (c) The PHP Group
	Zend Engine v4.2.10, Copyright (c) Zend Technologies
```


### 设置多composer对应多个PHP版本
多版本composer的情况下不能全局安装，请下载 [composer.phar|阿里云](https://mirrors.aliyun.com/composer/composer.phar) 
<br/>

把下载的 composer.phar 单独放一个目录例如: C:\Application\compoer\composer.phar
```
// PATH环境变量添加:
C:\Application\compoer
```

在该目录下新建 com80.bat 、 com82.bat文件做转发，com80.bat文件内容如下:
```
@php80 "%~dp0composer.phar" %*
```

com82.bat文件内容如下:
```
@php80 "%~dp0composer.phar" %*
```