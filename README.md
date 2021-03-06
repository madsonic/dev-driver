### 1a
module_init: when the module is loaded by the kernel

module_exit: when the module is removed from memory / unloaded

### 1b
This part assumes that your module is a driver otherwise it is a matter of building your module outside the kernel directory, with the target destination set to the kernel source directory
#### building
update modules Makefile to include your module. e.g. the hello module lives in `linux-4.15/drivers/hello/`

Append the `Makefile` in `drivers/` with `obj-m += hello/`

Then append the `Makefile` in `hello/` with `obj-m += hello.o`

finally build with
```shell
$ make modules
```

#### installing module
as per the previous assignment, we can install the module with
```shell
$ make modules_install
```

#### loading module
not sure if 'installing the module' meant 'loading' so this is included for completeness sake

```shell
$ cd /lib/modules/4.15.0/kernel/drivers/hello/ #or where ever your install your module
$ insmod hello.ko
```

### 1c
![printk message of hello module](https://i.imgur.com/UXn9lrf.png)
### 1d
```
#include <linux/kernel.h>
#include <linux/init.h>
#include <linux/module.h>
+ #include <linux/moduleparam.h>

MODULE_LICENSE("GPL");

+ static char *who = "world";
+ module_param(who, charp, S_IRUGO);

static int hello_init(void) {
-    printk(KERN_ALERT "Hello, world\n");
+    printk(KERN_ALERT "Hello, %s\n", who);
    return 0;
}

static void hello_exit(void) {
-    printk(KERN_ALERT "Goodbye, cruel world\n");
+    printk(KERN_ALERT "Goodbye, %s\n", who);
}

module_init(hello_init);
module_exit(hello_exit);
```
 ![parameterized module](https://i.imgur.com/0iaurdj.png)

 __Note:__ `dmesg -c` was called prior to loading the module

### 2a
```shell
$ mknod /dev/onebyte c 61 0
```

### 2b
![device list](https://i.imgur.com/3qexm4K.png)

### 2c
[Code for read](https://github.com/madsonic/dev-driver/blob/master/one.c#L42)
```c
static ssize_t onebyte_read(struct file *filep, char __user *buf, size_t count, loff_t *f_pos) {
    if (*f_pos + count > CAPACITY) {
        count = CAPACITY - *f_pos;
    }

    if (copy_to_user(buf, onebyte_data, count) != 0) {
        return -EFAULT;
    }
    *f_pos += count;
    return count;
}
```

[Code for write](https://github.com/madsonic/dev-driver/blob/master/one.c#L54)
```c
static ssize_t onebyte_write(struct file *filep, const char __user *buf, size_t count, loff_t *f_pos) {
    if (*f_pos > CAPACITY) {
        return 0;
    }

    if (copy_from_user(onebyte_data, buf, CAPACITY) != 0) {
        return -EFAULT;
    }


    if (count > CAPACITY) {
        return -ENOSPC;
    }

    return CAPACITY;
}
```

Test cases
![test cases](https://i.imgur.com/caYeesv.png)

Commits log

[View Github repo](https://github.com/madsonic/dev-driver/commits/master)

![commit log](https://i.imgur.com/EL1IKf6.png)
