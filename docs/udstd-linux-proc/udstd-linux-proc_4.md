# 项目实例 Run

## 第四章 项目实例 Run

Run 是开源的脚本管理工具，官方网站[`runscripts.org`](http://runscripts.org)，项目地址[`github.com/runscripts/run`](https://github.com/runscripts/run)。

Run 可以执行任意的脚本，当然使用到 Go 库提供的系统调用程序。

# 项目架构

## Run 项目架构

Run 是一个命令行工具，没有复杂的 CS 或 BS 架构，只是通过解析命令行或者配置文件来下载运行相应的脚本。

## Flock

Run 使用了前面提到的进程文件锁，避免同时运行同一个脚本。同时运行同一个脚本会有什么问题呢？例如我们`run pt-summary`，同时另一个终端执行`run -u pt-summary`，这样前一个命令有可以使用旧脚本也可能使用新脚本，这需我们规避这样的问题。

# 代码实现

## 实现 Run

## 实现 Flock

前面提到进程的文件锁，实际上 Run 也用到了，可以试想下以下的场景。

用户 A 执行`run pt-summary`，由于本地已经缓存了所以会直接运行本地的脚本。同时用户 B 执行`run -u pt-summary`，加上`-u`或者`--update`参数后 Run 会从远端下载并运行最新的脚本。如果不加文件锁的话，用户 A 的行为就不可预测了，而文件锁很好得解决了这个问题。

具体使用方法如下，我们封装了以下的接口。

```
var lockFile *os.File

// Lock the file.
func Flock(path string) error {
    return fcntlFlock(syscall.F_WRLCK, path)
}

// Unlock the file.
func Funlock(path string) error {
    err := fcntlFlock(syscall.F_UNLCK)
    if err != nil {
      return err
    } else {
        return lockFile.Close()
    }
}

// Control the lock of file.
func fcntlFlock(lockType int16, path ...string) error {
    var err error
    if lockType != syscall.F_UNLCK {
      mode := syscall.O_CREAT | syscall.O_WRONLY
      mask := syscall.Umask(0)
      lockFile, err = os.OpenFile(path[0], mode, 0666)
      syscall.Umask(mask)
      if err != nil {
        return err
      }
    }

    lock := syscall.Flock_t{
      Start:  0,
      Len:    1,
      Type:   lockType,
      Whence: int16(os.SEEK_SET),
    }
    return syscall.FcntlFlock(lockFile.Fd(), syscall.F_SETLK, &lock)
} 
```

在运行脚本前就调用锁进程的方法。

```
// Lock the script.
lockPath := cacheDir + ".lock"
err = flock.Flock(lockPath)
if err != nil {
  utils.LogError("%s: %v\n", lockPath, err)
  os.Exit(1)
} 
```

## 实现 HTTP 请求

使用 Run 时它会自动从网上下载脚本，走的 HTTP 协议，具体实现方法如下。

```
// Retrieve a file via HTTP GET.
func Fetch(url string, path string) error {
  response, err := http.Get(url)
  if err != nil {
    return err
  }
  if response.StatusCode != 200 {
    return Errorf("%s: %s", response.Status, url)
  }

  defer response.Body.Close()
  body, err := ioutil.ReadAll(response.Body)
  if err != nil {
    return err
  }
  if strings.HasPrefix(url, MASTER_URL) {
    // When fetching run.conf, etc.
    return ioutil.WriteFile(path, body, 0644)
    } else {
      // When fetching scripts.
      return ioutil.WriteFile(path, body, 0777)
    }
}
` 
```

Run 的总体代码是很简单的，主要是通过解析 run.conf 下载相应的脚本并执行。