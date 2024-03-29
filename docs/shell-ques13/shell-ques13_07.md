# 7.()与{}差在哪 _

## shell 十三问之 7：()与{}差在哪？

* * *

嗯，这次轻松一下，不讲太多... ^_^

先说一下，为何要用()或者{}好了。

许多时候，我们在 shell 操作上，需要在 一定的条件下执行多个命令，也就是说， 要么不执行，要么就全执行，而不是每次 依序的判断是否要执行下一个命令。

或者，要从一些命令执行的先后次序中得到结果， 如算术运算的 2*(3+4)那样...

这时候，我们就可以引入"`命令群组`"(`command group`) 的概念：将许多命令集中处理。

在 shell `command line`中，一般人或许不太计较`()`与 `{}`这两对符号的差异，虽然两者都可以将多个命令当作群组处理， 但若从技术细节上，却是很不一样的：

*   `()` 将`command group`置于`sub-shell`(`子 shell`)中去执行，也称 `nested sub-shell`。
*   `{}` 则是在同一个`shell`内完成，也称`non-named command group`。

若你对上一章的 fork 与 source 的概念还记得的话， 那就不难理解两者的差异了。

要是在 `command group`中扯上变量及其他环境的修改， 我们可以根据不同的需求来使用`()`或`{}`。 通常而言, 若所作的修改是临时的，且不想影响原有或以后的设定， 那我们就使用`nested sub-shell`, 即`()`; 反之，则用`non-named command group`, 即`{}`。

是的，光从`command line`来看，`()` 与 `{}`差别就讲完了，够轻松吧~~~, ^_^

然而，这两个`meta`用在其他`command meta`或领域中(如 Regular Expression)， 还是有很多差别的。 只是，我不打算再去说明了，留给读者慢慢发掘好了...

我这里只想补充一个概念，就是`function`。 所谓`function`，就是用一个名字去命名一个`command group`, 然后再调用这个名字去执行`command group`。

从`non-named command group`来推断， 大概你也可以推测到我要说的是`{}`了吧？(yes! 你真聪明 ^_^)

在 bash 中，function 的定义方式有两种：

*   方式一：

    ```
    function function_name {
      command1
      command2
      command3
      .....
    } 
    ```

*   方式二：

    ```
    function_name () {
      command1
      command2
      command3
      ......
    } 
    ```

用哪一种方式无所谓， 只是碰到所定义的名称与现有的命令或者别名冲突的话， 方式二或许会失败。 但方式二起码可以少打个`function`这一串英文字符， 对懒人来说(如我)，有何乐而不为呢？...^_^

`function` 在一定程度上来说，也可以称为"函数"， 但请不要与传统编程所使用的"函数"(library)搞混了， 毕竟两者差异很大。 唯一相同的是，我们都可以随时用"已定义的名称"来调用它们...

若我们在 shell 操作中，需要不断地重复某些命令， 我们首先想到的，或许是将命令写成 shell 脚本(shell script)。 不过，我们也可以写成 function, 然后在 command line 中打上 function_name 就可当一般的 shell script 使用了。

若只是你在 shell 中定义的`function`, 除了用`unset` function_name 取消外， 一旦你退出 shell， function 也跟着消失。 然而，在 script 中使用 function 却有许多好处， 除了提高整体 script 的执行性能外(因为已经载入)， 还可以节省许多重复的代码......

简单而言，若你会将多个命令写成 script 以供调用的话， 那你可以将 function 看成 script 中 script。... ^_^

而且通过上一章节介绍的`source`命令， 我们可以自行定义许许多多好用的 function， 在集中写在特定文件中， 然后，在其他的 script 中用`source`将它们载入，并反复执行。

若你是`RedHat Linux`的使用者， 或许，已经猜出 `/etc/rc.d/init.d/functions`这个文件时啥作用了~~~ ^_^

okay，说要轻松点的嘛，那这次就暂时写到这吧。 祝大家学习愉快，^_^