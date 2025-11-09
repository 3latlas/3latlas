### 1.关闭Steam的GPU加速可选项

<img width="1068" height="900" alt="Image" src="https://github.com/user-attachments/assets/b939c7ac-47b5-4c6c-8e8c-2cb3038c31eb" />
如图设置即可。

###  2.启动项加上环境变量
飞蚊话博客里边讲了这个事情：

> 这可能不关是不是用Wayland的事儿，只是凑巧找到了解决办法。但因为是在wayland下面发现解决了的，暂且也算是Wayland参与了一些帮助吧…
> 只需在启动器增加环境变量：LANG=zh_CN.UTF-8

具体做法就是找到这行代码：`Exec=LANG=zh_CN.UTF-8 /usr/bin/steam %U`
修改成这样：`Exec=LANG=zh_CN.UTF-8 /usr/bin/steam %U`
