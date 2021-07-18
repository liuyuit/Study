# install xdebug3 on windows

## references

> https://stackoverflow.com/questions/65092543/how-can-i-connect-xdebug-3-to-phpstorm-on-windows-10
>
> https://www.jianshu.com/p/a4ec1947c326

## php-xdebug extension

[php_xdebug-3.0.3-7.4-vc15-nts.dll](https://xdebug.org/files/php_xdebug-3.0.3-7.4-vc15-nts.dll)

```
PHP Version 7.4.15

Architecture	x86

Zend Extension Build	API320190902,NTS,VC15
PHP Extension Build	API20190902,NTS,VC15
```

php.ini

```
zend_extension				=php_xdebug-3.0.3-7.4-vc15-nts.dll
xdebug.idekey				=PHPSTORM
xdebug.mode					=debug
; 远程调试时要填，填入开发机 ip（通过命令 ipconfig）。
;xdebug.client_host		= 10.3.25.7
xdebug.client_port			=9009
xdebug.start_with_request	=yes

# 在容器内部使用 host.docker.internal 表示宿主机 ip
# xdebug.client_host			=host.docker.internal
```

重启 ngixn 或其他 web 服务

## phpstorm setting

File-->Setting-->Language & FrameWorks-->Debug、Servces

- File-->Setting-->Language & FrameWorks-->Debug

Xdebug ( on the right side) 

  ​	debug prot : 9009 

- File-->Setting-->Language & FrameWorks-->Debug-->DbGp proxy

  ide key : PHPSTORM

  host:cps.ggxx.local

  port:9009

- File-->Setting-->Language & FrameWorks-->Servces

  name: cps.ggxx.local

  host: cps.ggxx.local

  port: 80

  debugger: xdebug

  

编辑调试配置服务器和程序

- Add Condfiation

- Add PHP Web Page

  name: web page cps.ggxx.local

  server:cps.ggxx.local

- 

## remote debug

File-->Setting-->Language & FrameWorks-->Servces

click use path mappings button

| File/Directory                                               | Absolute path on the server          |
| ------------------------------------------------------------ | ------------------------------------ |
| C:\phpStudy\PHPTutorial\WWW\xy_cps  （本地开发机项目根目录） | /data/www/xy_cps/ (远程机项目根目录) |
#### 远程调试

- File-->Setting-->Language & FrameWorks-->Servces

  choose use path mapping

  | File/Derectory                | Abusolut path on the server |
  | ----------------------------- | --------------------------- |
  | /Users/liuyu/var/www/material | /var/www                    |

  

#### docker

File-->Setting-->Language & FrameWorks->PHP

- CLI Interpreter

  add form docker

- Docker container

  | Container path | Host path                     |
  | -------------- | ----------------------------- |
  | /var/www       | /Users/liuyu/var/www/material |
- 

如果 nginx 端口映射为 82:80 那么

File-->Setting-->Language & FrameWorks-->Servces

port: 80
