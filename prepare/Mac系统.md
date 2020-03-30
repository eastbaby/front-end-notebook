mac中程序一般位于三个目录，按照覆盖优先级是`/usr/local/bin `> `/usr/bin` > `/bin`，优先级其实是由PATH中的设置决定的，上面的顺序是系统默认的设置

brew会下载源代码，然后执行 `./configure` && `make install` ，将软件安装到单独的目录（`/usr/local/Cellar`）下，然后软链（symlink）到 `/usr/local/bin` 目录下，同时会自动检测下载相关依赖库，并自动配置好各种环境变量。

