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
![printk message of hello module](https://i.imgur.com/aUBwQ6m.png)
