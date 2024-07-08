# shelter

Shelter是一个用于CVM Launcher的工具。

## Usage

**运行Shelter不需要root权限，也不建议使用root权限运行Shelter(防止损坏host)。**

### 命令行选项

~~~txt
./shelter
---
Usage: shelter {subcommand}
Available SubCommands:
    build   Build the shelter
    start   Start the shelter
    stop    Stop the shelter
    exec    exec a shell commad in shelter
    status  Query the status of shelter
    clean   Remove output image and cache

Options:
    -h, --help  Show this help message and exit
~~~

Shelter默认构建的initramfs的发行版版本与host一致，支持的发行版有`debian`，`arch`，`opensuse`，`ubuntu`，`centos`，`rocky`，`alma`，`fedora`，`rhel-ubi`，`mageia`和`openmandriva`。

通过配置[build.conf](./build.conf)文件，可以实现在执行Shelter构建时将指定的文件和二进制文件复制到initramfs中，并且可以自动复制其依赖的.so文件。

### 日志输出

可通过`LOG_LEVEL`选项指定Shelter的日志级别，例如`LOG_LEVEL=4`设置为DEBUG级别。默认日志级别为INFO。

支持的级别和对应数字如下：

```txt
ERROR=1
WARN=2
INFO=3
DEBUG=4
```

## 开发者使用步骤

本节介绍在各种主机上编译、安装和运行shelter的步骤。

### Ubuntu 22.04

1. 安装编译时和运行时依赖
    ~~~sh
    make prepare
    ~~~

2. 安装shelter和相关依赖
    ~~~sh
    make install
    ~~~

3. 执行demo测试
    ~~~sh
    make test
    ~~~

4. 配置./build.conf文件
    该sh文件中包含了要被拷贝到guest系统的程序/文件。
    - `binary=()`：指定要拷贝的可执行文件列表，程序依赖的动态库也会自动被拷贝到initrd中
    - `file=()`：指定要拷贝的普通文件列表
    具体的配置说明请查看[./build.conf](./build.conf)文件中的注释
    
    在当前的实现中，用户可以按照以上格式增加新路径以实现将程序/文件拷贝到initrd中。

5. 构建shelter镜像
    ~~~sh
    shelter build
    ~~~

6. 直接运行shelter镜像中的指定命令（相当于start、exec、stop的组合）
    ~~~sh
    shelter run cat /proc/cpuinfo
    ~~~

    也可以分步执行：
    - 创建shelter实例
      ~~~sh
      shelter start
      ~~~

    - 在创建的shelter实例中运行镜像中的指定命令
      ~~~sh
      shelter exec cat /proc/cpuinfo
      ~~~

    - 停止shelter实例
      ~~~sh
      shelter stop
      ~~~