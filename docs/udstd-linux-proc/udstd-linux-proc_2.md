# Go 编程实例

## 第二章 Go 编程实例

学习完进程基础知识，我们通过几个 Go 编程实例介绍如果使用 Go 运行外部进程。

这章主要是编程练习，学习完这章后对进程的使用和 Go 对进程的使用应该都有更深的理解。

# 衍生新进程

## 衍生(Spawn)新进程

这是来自 GoByExample 的例子，代码在[`gobyexample.com/spawning-processes`](https://gobyexample.com/spawning-processes)。

它能够执行任意 Go 或者非 Go 程序，并且等待放回结果，外部进程结束后继续执行本程序。

## 代码实现

```
package main

import "fmt"
import "io/ioutil"
import "os/exec"

func main() {
    dateCmd := exec.Command("date")
    dateOut, err := dateCmd.Output()
    if err != nil {
        panic(err)
    }
    fmt.Println("> date")
    fmt.Println(string(dateOut))

    grepCmd := exec.Command("grep", "hello")
    grepIn, _ := grepCmd.StdinPipe()
    grepOut, _ := grepCmd.StdoutPipe()
    grepCmd.Start()
    grepIn.Write([]byte("hello grep\ngoodbye grep"))
    grepIn.Close()
    grepBytes, _ := ioutil.ReadAll(grepOut)
    grepCmd.Wait()
    fmt.Println("> grep hello")
    fmt.Println(string(grepBytes))

    lsCmd := exec.Command("bash", "-c", "ls -a -l -h")
    lsOut, err := lsCmd.Output()
    if err != nil {
        panic(err)
    }
    fmt.Println("> ls -a -l -h")
    fmt.Println(string(lsOut))
} 
```

## 运行结果

```
$ go run spawning-processes.go
> date
Wed Oct 10 09:53:11 PDT 2012
> grep hello
hello grep
> ls -a -l -h
drwxr-xr-x  4 mark 136B Oct 3 16:29 .
drwxr-xr-x 91 mark 3.0K Oct 3 12:50 ..
-rw-r--r--  1 mark 1.3K Oct 3 16:28 spawning-processes.go 
```

## 归纳总结

因此如果你的程序需要执行外部命令，可以直接使用`exec.Command()`来 Spawn 进程，并且根据需要获得外部程序的返回值。

# 执行外部程序

## 执行(Exec)外部程序

这是来自 GoByExample 的例子，代码在[`gobyexample.com/execing-processes`](https://gobyexample.com/execing-processes)。

把新程序加载到自己的内存。

与 Spawn 不同，执行外部程序并不会返回到原进程中，也就是让外部程序完全取代本进程。

## 代码实现

```
package main

import "syscall"
import "os"
import "os/exec"

func main() {
    binary, lookErr := exec.LookPath("ls")
    if lookErr != nil {
        panic(lookErr)
    }
    args := []string{"ls", "-a", "-l", "-h"}
    env := os.Environ()
    execErr := syscall.Exec(binary, args, env)
    if execErr != nil {
        panic(execErr)
    }
} 
```

## 运行结果

```
$ go run execing-processes.go
total 16
drwxr-xr-x  4 mark 136B Oct 3 16:29 .
drwxr-xr-x 91 mark 3.0K Oct 3 12:50 ..
-rw-r--r--  1 mark 1.3K Oct 3 16:28 execing-processes.go 
```

## 归纳总结

如果你的程序就是用来执行外部程序的，例如后面提到的项目实例 Run，那使用`syscall.Exec`执行外部程序就最合适了。注意调用该函数后，本进程后面的代码将不可能再执行了。

# 复制进程

## 复制(Fork)进程

如果我们仅仅想复制父进程的堆栈空间呢，很遗憾 Go 没有提供这样的接口，因为使用 Spawn、Exec 和 Goroutine 已经能覆盖绝大部分的使用案例了。

事实上无论是 Spawn 还是 Exec 都是通过实现 Fork 系统调用来实现的，后面将会详细介绍它的实现原理。