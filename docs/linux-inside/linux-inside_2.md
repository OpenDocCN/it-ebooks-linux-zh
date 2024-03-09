# Data Structures in the Linux Kernel

# Linux 内核中的数据结构

Linux 内核对很多数据结构提供不同的实现方法，比如，双向链表，B+树，具有优先级的堆等等。

这部分考虑这些数据结构和算法。

*   [双向链表](https://github.com/MintCN/linux-insides/blob/master/DataStructures/dlist.md)
*   [基数树](https://github.com/MintCN/linux-insides/blob/master/DataStructures/radix-tree.md)

# Doubly linked list

# Linux 内核中的数据结构

## 双向链表

Linux kernel provides its own doubly linked list implementation which you can find in the [include/linux/list.h](https://github.com/torvalds/linux/blob/master/include/linux/list.h). We will start `Data Structures in the Linux kernel` from the doubly linked list data structure. Why? Because it is very popular in the kernel, just try to [search](http://lxr.free-electrons.com/ident?i=list_head)

First of all let's look on the main structure:

```
struct list_head {
    struct list_head *next, *prev;
}; 
```

You can note that it is different from many lists implementations which you have seen. For example this doubly linked list structure from the [glib](http://www.gnu.org/software/libc/):

```
struct GList {
  gpointer data;
  GList *next;
  GList *prev;
}; 
```

Usually a linked list structure contains a pointer to the item. Linux kernel implementation of the list does not. So the main question is - `where does the list store the data?`. The actual implementation of lists in the kernel is - `Intrusive list`. An intrusive linked list does not contain data in its nodes - A node just contains pointers to the next and previous node and list nodes part of the data that are added to the list. This makes the data structure generic, so it does not care about entry data type anymore.

For example:

```
struct nmi_desc {
    spinlock_t lock;
    struct list_head head;
}; 
```

Let's look at some examples to understand how `list_head` is used in the kernel. As I already wrote about, there are many, really many different places where lists are used in the kernel. Let's look for example in miscellaneous character drivers. Misc character drivers API from the [drivers/char/misc.c](https://github.com/torvalds/linux/blob/master/drivers/char/misc.c) is used for writing small drivers for handling simple hardware or virtual devices. This drivers share major number:

```
#define MISC_MAJOR              10 
```

but have their own minor number. For example you can see it with:

```
ls -l /dev |  grep 10
crw-------   1 root root     10, 235 Mar 21 12:01 autofs
drwxr-xr-x  10 root root         200 Mar 21 12:01 cpu
crw-------   1 root root     10,  62 Mar 21 12:01 cpu_dma_latency
crw-------   1 root root     10, 203 Mar 21 12:01 cuse
drwxr-xr-x   2 root root         100 Mar 21 12:01 dri
crw-rw-rw-   1 root root     10, 229 Mar 21 12:01 fuse
crw-------   1 root root     10, 228 Mar 21 12:01 hpet
crw-------   1 root root     10, 183 Mar 21 12:01 hwrng
crw-rw----+  1 root kvm      10, 232 Mar 21 12:01 kvm
crw-rw----   1 root disk     10, 237 Mar 21 12:01 loop-control
crw-------   1 root root     10, 227 Mar 21 12:01 mcelog
crw-------   1 root root     10,  59 Mar 21 12:01 memory_bandwidth
crw-------   1 root root     10,  61 Mar 21 12:01 network_latency
crw-------   1 root root     10,  60 Mar 21 12:01 network_throughput
crw-r-----   1 root kmem     10, 144 Mar 21 12:01 nvram
brw-rw----   1 root disk      1,  10 Mar 21 12:01 ram10
crw--w----   1 root tty       4,  10 Mar 21 12:01 tty10
crw-rw----   1 root dialout   4,  74 Mar 21 12:01 ttyS10
crw-------   1 root root     10,  63 Mar 21 12:01 vga_arbiter
crw-------   1 root root     10, 137 Mar 21 12:01 vhci 
```

Now let's have a close look at how lists are used in the misc device drivers. First of all let's look on `miscdevice` structure:

```
struct miscdevice
{
      int minor;
      const char *name;
      const struct file_operations *fops;
      struct list_head list;
      struct device *parent;
      struct device *this_device;
      const char *nodename;
      mode_t mode;
}; 
```

We can see the fourth field in the `miscdevice` structure - `list` which is a list of registered devices. In the beginning of the source code file we can see the definition of misc_list:

```
static LIST_HEAD(misc_list); 
```

which expands to definition of the variables with `list_head` type:

```
#define LIST_HEAD(name) \
    struct list_head name = LIST_HEAD_INIT(name) 
```

and initializes it with the `LIST_HEAD_INIT` macro which set previous and next entries:

```
#define LIST_HEAD_INIT(name) { &(name), &(name) } 
```

Now let's look on the `misc_register` function which registers a miscellaneous device. At the start it initializes `miscdevice->list` with the `INIT_LIST_HEAD` function:

```
INIT_LIST_HEAD(&misc->list); 
```

which does the same as the `LIST_HEAD_INIT` macro:

```
static inline void INIT_LIST_HEAD(struct list_head *list) {
    list->next = list;
    list->prev = list;
} 
```

In the next step after device created with the `device_create` function we add it to the miscellaneous devices list with:

```
list_add(&misc->list, &misc_list); 
```

Kernel `list.h` provides this API for the addition of new entry to the list. Let's look on it's implementation:

```
static inline void list_add(struct list_head *new, struct list_head *head) {
    __list_add(new, head, head->next);
} 
```

It just calls internal function `__list_add` with the 3 given parameters:

*   new - new entry;
*   head - list head after which the new item will be inserted
*   head->next - next item after list head.

Implementation of the `__list_add` is pretty simple:

```
static inline void __list_add(struct list_head *new,
                  struct list_head *prev,
                  struct list_head *next)
{
    next->prev = new;
    new->next = next;
    new->prev = prev;
    prev->next = new;
} 
```

Here we set new item between `prev` and `next`. So `misc` list which we defined at the start with the `LIST_HEAD_INIT` macro will contain previous and next pointers to the `miscdevice->list`.

There is still one question: how to get list's entry. There is a special macro:

```
#define list_entry(ptr, type, member) \
    container_of(ptr, type, member) 
```

which gets three parameters:

*   ptr - the structure list_head pointer;
*   type - structure type;
*   member - the name of the list_head within the structure;

For example:

```
const struct miscdevice *p = list_entry(v, struct miscdevice, list) 
```

After this we can access to any `miscdevice` field with `p->minor` or `p->name` and etc... Let's look on the `list_entry` implementation:

```
#define list_entry(ptr, type, member) \
    container_of(ptr, type, member) 
```

As we can see it just calls `container_of` macro with the same arguments. At first sight, the `container_of` looks strange:

```
#define container_of(ptr, type, member) ({                      \
    const typeof( ((type *)0)->member ) *__mptr = (ptr);    \
    (type *)( (char *)__mptr - offsetof(type,member) );}) 
```

First of all you can note that it consists of two expressions in curly brackets. Compiler will evaluate the whole block in the curly braces and use the value of the last expression.

For example:

```
#include <stdio.h>

int main() {
    int i = 0;
    printf("i = %d\n", ({++i; ++i;}));
    return 0;
} 
```

will print `2`.

The next point is `typeof`, it's simple. As you can understand from its name, it just returns the type of the given variable. When I first saw the implementation of the `container_of` macro, the strangest thing for me was the zero in the `((type *)0)` expression. Actually this pointer magic calculates the offset of the given field from the address of the structure, but as we have `0` here, it will be just a zero offset alongwith the field width. Let's look at a simple example:

```
#include <stdio.h>

struct s {
        int field1;
        char field2;
        char field3;
};

int main() {
    printf("%p\n", &((struct s*)0)->field3);
    return 0;
} 
```

will print `0x5`.

The next offsetof macro calculates offset from the beginning of the structure to the given structure's field. Its implementation is very similar to the previous code:

```
#define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER) 
```

Let's summarize all about `container_of` macro. `container_of` macro returns address of the structure by the given address of the structure's field with `list_head` type, the name of the structure field with `list_head` type and type of the container structure. At the first line this macro declares the `__mptr` pointer which points to the field of the structure that `ptr` points to and assigns `ptr` to it. Now `ptr` and `__mptr` point to the same address. Technically we don't need this line but its useful for type checking. First line ensures that that given structure (`type` parameter) has a member called `member`. In the second line it calculates offset of the field from the structure with the `offsetof` macro and subtracts it from the structure address. That's all.

Of course `list_add` and `list_entry` is not the only functions which `<linux/list.h>` provides. Implementation of the doubly linked list provides the following API:

*   list_add
*   list_add_tail
*   list_del
*   list_replace
*   list_move
*   list_is_last
*   list_empty
*   list_cut_position
*   list_splice

and many more.

# Radix tree

# Linux 内核中的数据结构

## 基数树

正如你所知道的 Linux 内核通过许多不同库以及函数提供各种数据结构以及算法实现。 这个部分我们将介绍其中一个数据结构 [Radix tree](http://en.wikipedia.org/wiki/Radix_tree)。Linux 内核中有两个文件与 `radix tree` 的实现和 API 相关：

*   [include/linux/radix-tree.h](https://github.com/torvalds/linux/blob/master/include/linux/radix-tree.h)
*   [lib/radix-tree.c](https://github.com/torvalds/linux/blob/master/lib/radix-tree.c)

首先说明一下什么是 `radix tree` 。Radix tree 是一种 `压缩 trie`，其中 [trie](http://en.wikipedia.org/wiki/Trie) 是一种通过保存关联数组（associative array）来提供 `关键字-值（key-value）` 存储与查找的数据结构。通常关键字是字符串，不过也可以是其他数据类型。

trie 结构的节点与 `n-tree` 不同，其节点中并不存储关键字，取而代之的是存储单个字符标签。关键字查找时，通过从树的根开始遍历关键字相关的所有字符标签节点，直至到达最终的叶子节点。下面是个例子：

```
+-----------+
               |           |
               |    " "    |
               |           |
        +------+-----------+------+
        |                         |
        |                         |
   +----v------+            +-----v-----+
   |           |            |           |
   |    g      |            |     c     |
   |           |            |           |
   +-----------+            +-----------+
        |                         |
        |                         |
   +----v------+            +-----v-----+
   |           |            |           |
   |    o      |            |     a     |
   |           |            |           |
   +-----------+            +-----------+
                                  |
                                  |
                            +-----v-----+
                            |           |
                            |     t     |
                            |           |
                            +-----------+ 
```

这个例子中，我们可以看到 `trie` 所存储的关键字信息 `go` 与 `cat`，压缩 trie 或 `radix tree` 与 `trie` 所不同的是，所有只存在单个孩子的中间节点将被压缩。

Linux 内核中的 Radix 树将值映射为整型关键字，Radix 的数据结构定义在 [include/linux/radix-tree.h](https://github.com/torvalds/linux/blob/master/include/linux/radix-tree.h) 文件中 :

```
struct radix_tree_root {
         unsigned int            height;
         gfp_t                   gfp_mask;
         struct radix_tree_node  __rcu *rnode;
}; 
```

上面这个是 radix 树的 root 节点的结构体，它包括三个成员：

*   `height` - 从叶节点向上计算出的树高度。
*   `gfp_mask` - 内存分配标识。
*   `rnode` - 子节点指针。

这里我们先讨论的结构体成员是 `gfp_mask` :

Linux 底层的内存申请接口需要提供一类标识（flag） - `gfp_mask` ，用于描述内存申请的行为。这个以 `GFP_` 前缀开头的内存申请控制标识主要包括，`GFP_NOIO` 禁止所有 IO 操作但允许睡眠等待内存，`__GFP_HIGHMEM` 允许申请内核的高端内存，`GFP_ATOMIC` 高优先级申请内存且操作不允许被睡眠。

接下来说的结构体成员是`rnode`：

```
struct radix_tree_node {
        unsigned int    path;
        unsigned int    count;
        union {
                struct {
                        struct radix_tree_node *parent;
                        void *private_data;
                };
                struct rcu_head rcu_head;
        };
        /* For tree user */
        struct list_head private_list;
        void __rcu      *slots[RADIX_TREE_MAP_SIZE];
        unsigned long   tags[RADIX_TREE_MAX_TAGS][RADIX_TREE_TAG_LONGS];
}; 
```

这个结构体中包括这几个内容，节点与父节点的偏移以及到树底端的高度，子节点的个数，节点的存储数据域，具体描述如下：

*   `path` - 从叶节点
*   `count` - 子节点的个数。
*   `parent` - 父节点的指针。
*   `private_data` - 存储数据内容缓冲区。
*   `rcu_head` - 用于节点释放的 RCU 链表。
*   `private_list` - 存储数据。

结构体 `radix_tree_node` 的最后两个成员 `tags` 与 `slots` 是非常重要且需要特别注意的。每个 Radix 树节点都可以包括一个指向存储数据指针的 slots 集合，空闲 slots 的指针指向 NULL。 Linux 内核的 Radix 树结构体中还包含用于记录节点存储状态的标签 `tags` 成员，标签通过位设置指示 Radix 树的数据存储状态。

至此，我们了解到 radix 树的结构，接下来看一下 radix 树所提供的 API。

## Linux 内核基数树 API

我们从数据结构的初始化开始看，radix 树支持两种方式初始化。

第一个是使用宏 `RADIX_TREE` ：

```
RADIX_TREE(name, gfp_mask);
` 
```

正如你看到，只需要提供 `name` 参数，就能够使用 `RADIX_TREE` 宏完成 radix 的定义以及初始化，`RADIX_TREE` 宏的实现非常简单：

```
#define RADIX_TREE(name, mask) \
         struct radix_tree_root name = RADIX_TREE_INIT(mask)

#define RADIX_TREE_INIT(mask)   { \
        .height = 0,              \
        .gfp_mask = (mask),       \
        .rnode = NULL,            \
} 
```

`RADIX_TREE` 宏首先使用 `name` 定义了一个 `radix_tree_root` 实例并用 `RADIX_TREE_INIT` 宏带参数 `mask` 进行初始化。宏 `RADIX_TREE_INIT` 将 `radix_tree_root` 初始化为默认属性并将 gfp_mask 初始化为入参 `mask` 。 第二种方式是手工定义 `radix_tree_root` 变量，之后再使用 `mask` 调用 `INIT_RADIX_TREE` 宏对变量进行初始化。

```
struct radix_tree_root my_radix_tree;
INIT_RADIX_TREE(my_tree, gfp_mask_for_my_radix_tree); 
```

`INIT_RADIX_TREE` 宏定义：

```
#define INIT_RADIX_TREE(root, mask)  \
do {                                 \
        (root)->height = 0;          \
        (root)->gfp_mask = (mask);   \
        (root)->rnode = NULL;        \
} while (0) 
```

宏 `INIT_RADIX_TREE` 所初始化的属性与 `RADIX_TREE_INIT` 一致

接下来是 radix 树的节点插入以及删除，这两个函数：

*   `radix_tree_insert`;
*   `radix_tree_delete`.

第一个函数 `radix_tree_insert` 需要三个入参：

*   radix 树 root 节点结构
*   索引关键字
*   需要插入存储的数据

第二个函数 `radix_tree_delete` 除了不需要存储数据参数外，其他与 `radix_tree_insert` 一致。

radix 树的查找实现有以下几个函数：The search in a radix tree implemented in two ways:

*   `radix_tree_lookup`;
*   `radix_tree_gang_lookup`;
*   `radix_tree_lookup_slot`.

第一个函数 `radix_tree_lookup` 需要两个参数：

*   radix 树 root 节点结构
*   索引关键字

这个函数通过给定的关键字查找 radix 树，并返关键字所对应的结点。

第二个函数 `radix_tree_gang_lookup` 具有以下特征：

```
unsigned int radix_tree_gang_lookup(struct radix_tree_root *root,
                                    void **results,
                                    unsigned long first_index,
                                    unsigned int max_items); 
```

函数返回查找到记录的条目数，并根据关键字进行排序，返回的总结点数不超过入参 `max_items` 的大小。

最后一个函数 `radix_tree_lookup_slot` 返回结点 slot 中所存储的数据。

## 链接

*   [Radix tree](http://en.wikipedia.org/wiki/Radix_tree)
*   [Trie](http://en.wikipedia.org/wiki/Trie)