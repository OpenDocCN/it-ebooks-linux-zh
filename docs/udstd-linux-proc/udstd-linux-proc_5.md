# 注意事项

## 第五章 注意事项

对进程有了深入理解后，我们编写实际应用可能遇到这些坑，这里总结一下。

# 创建目录权限

## 创建目录权限

如果你想创建一个目录并授予 777 权限，你需要怎么做？查看 Go 的 API 文档我们可以这样写。

源文件为 mkdir.go。

```
package main

import (
  "fmt"
  "os"
)

func main() {
    err := os.MkdirAll("/tmp/gotest/", 0777)
    if err != nil {
      panic(err)
    }

    fmt.Println("Mkdir /tmp/gotest/")
} 
```

## 运行结果

```
➜  understand_linux_process_examples git:(master) ✗ ll /tmp/
drwxr-xr-x   2 tobe  wheel    68B Dec 30 10:06 gotest
➜  understand_linux_process_examples git:(master) ✗ umask
022 
```

## 正确做法

代码在 mkdir_umask.go 中。

```
package main

import (
  "fmt"
  "os"
  "syscall"
)

func main() {
    mask := syscall.Umask(0)
    defer syscall.Umask(mask)

    err := os.MkdirAll("/tmp/gotest/", 0777)
    if err != nil {
      panic(err)
    }

    fmt.Println("Mkdir /tmp/gotest/")
} 
```

## 注意事项

这并不是 Go 的 Bug，包括 Linux 系统调用都是这样的，创建目录除了给定的权限还要加上系统的 Umask，Go 也是如实遵循这种约定。

如果你想达到你的预期权限，知道 Umask 及其用法是必须的。

# 捕获 SIGKILL

## 捕获 SIGKILL

SIGKILL 是常见的 Linux 信号，我们使用`kill`命令杀掉进程也就是像进程发送 SIGKILL 信号。

和其他信号不同，[SIGKILL](https://en.wikipedia.org/wiki/Unix_signal#SIGKILL)和 SIGSTOP 是不可被 Catch 的，因此下面的代码是能编译通过但也是无效的，更多细节可以参考[golang/go#9463](https://github.com/golang/go/issues/9463).

```
c := make(chan os.Signal, 1)
signal.Notify(c, syscall.SIGKILL, syscall.SIGSTOP) 
```

## 注意事项

这是 Linux 内核的限制，这种限制也是为了让操作系统有可能控制进程的生命周期，理解后我们也不应该去尝试捕获 SIGKILL。

不过还是有人这样去做，最后结果也不符合预期，这需要我们对底层有足够的理解。

# Sendfile 系统调用

## 系统调用 sendfile

[Sendfile](http://man7.org/linux/man-pages/man2/sendfile.2.html)是 Linux 实现的系统调用，可以通过避免文件在内核态和用户态的拷贝来优化文件传输的效率。

其中大名鼎鼎的分布式消息队列服务 Kafka 就使用 sendfile 来优化效率，具体用法可参见其[官方文档](http://kafka.apache.org/documentation.html)。

## 优化策略

在普通进程中，要从磁盘拷贝数据到网络，其实是需要通过系统调用，进程也会反复在用户态和内核态切换，频繁的数据传输在此有效率问题。因此我们必须意识到 Linux 给我们提供了 sendfile 这样的系统调用，可以提高进程的数据传输效率。