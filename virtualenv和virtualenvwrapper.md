# virtualenv 和 virtualenvwrapper

内容来自[廖雪峰博客](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432712108300322c61f256c74803b43bfd65c6f8d0d0000) 和 [Python 开发必备神器之一：virtualenv 查看详情](<https://juejin.im/entry/578af3fec4c971005ee49e90>)

Python 的第三方包成千上万，在一个 Python 环境下开发时间越久、安装依赖越多，就越容易出现依赖包冲突的问题。为了解决这个问题，开发者们开发出了 virtualenv，可以搭建虚拟且独立的 Python 环境。这样就可以使每个项目环境与其他项目独立开来，保持环境的干净，解决包冲突问题。

## 安装 virtualenv

[virtualenv](https://link.juejin.im/?target=https%3A%2F%2Fvirtualenv.pypa.io%2Fen%2Fstable%2F)是一个第三方包，是管理虚拟环境的常用方法之一。此外，Python 3 中还自带了虚拟环境管理包。

我们可以用`easy_install`或者`pip`安装。

`pip install virtualenv`

## virtualenv基本用法

### 创建项目的虚拟环境:

```shell
cd my_project_folder
virtualenv venv # venv 可替换为别的虚拟环境名称
virtualenv --no-site-packages venv

```

命令`virtualenv`就可以创建一个独立的Python运行环境，我们还加上了参数`--no-site-packages`，这样，已经安装到系统Python环境中的所有第三方包都不会复制过来，这样，我们就得到了一个不带任何第三方包的“干净”的Python运行环境。

执行后，在本地会生成一个与虚拟环境同名的文件夹，包含 Python 可执行文件和 pip 库的拷贝，可用于安装其他包。

但是默认情况下，虚拟环境中不会包含也无法使用系统环境的global site-packages。比如系统环境里安装了 requests 模块，在虚拟环境里`import requests`会提示`ImportError`。如果想使用系统环境的第三方软件包，可以在创建虚拟环境时使用参数`–system-site-packages`。

```shell
virtualenv --system-site-packages venv
```

另外，你还可以自己指定虚拟环境所使用的 Python 版本，但前提是系统中已经安装了该版本：

```shell
virtualenv -p /usr/bin/python2.7 venv
```

### 使用虚拟环境

新建的Python环境被放到当前目录下的`venv`目录。有了`venv`这个Python环境，可以用`source`进入该环境：

```shell
source venv/bin/activate
```

注意到命令提示符变了，有个`(venv)`前缀，表示当前环境是一个名为`venv`的Python环境。

下面正常安装各种第三方包，并运行`python`命令：

```shell
(venv)Mac:myproject michael$ pip install jinja2
```

在`venv`环境下，用`pip`安装的包都被安装到`venv`这个环境下，系统Python环境不受任何影响。也就是说，`venv`环境是专门针对`myproject`这个应用创建的。

退出当前的`venv`环境，使用`deactivate`命令：

```shell
(venv)Mac:myproject michael$ deactivate 
```

此时就回到了正常的环境，现在`pip`或`python`均是在系统Python环境下执行。

完全可以针对每个应用创建独立的Python运行环境，这样就可以对每个应用的Python环境进行隔离。

virtualenv是如何创建“独立”的Python运行环境的呢？原理很简单，就是把系统Python复制一份到virtualenv的环境，用命令`source venv/bin/activate`进入一个virtualenv环境时，virtualenv会修改相关环境变量，让命令`python`和`pip`均指向当前的virtualenv环境。

如果项目开发完成后想删除虚拟环境，直接删除虚拟环境目录即可。

## 使用virtualenvwrapper

上述 virtualenv 的操作其实已经够简单了，但对于开发者来说还是不够简便，所以便有了 virtualenvwrapper。这是 virtualenv 的扩展工具，提供了一系列命令行命令，可以方便地创建、删除、复制、切换不同的虚拟环境。同时，使用该扩展后，所有虚拟环境都会被放置在同一个目录下。

### 安装virtualenvwrapper

```shell
pip install virtualenvwrapper
```

### 设置环境变量

把下面两行添加到`~/.bashrc`（或者`~/.zshrc`）里。

```shell
if [ -f /usr/local/bin/virtualenvwrapper.sh ]; then
   export WORKON_HOME=$HOME/.virtualenvs 
   source /usr/local/bin/virtualenvwrapper.sh
fi

#这里的路径需要检查一下不一定在/usr/local/bin/ 
#可以用 find . -name virtualenvwrapper.sh 找到所在路径
```

其中，`.virtualenvs` 是可以自定义的虚拟环境管理目录。

然后执行：`source ~/.bashrc`，就可以使用 virtualenvwrapper 了。Windows 平台的安装过程，请参考[官方文档](https://link.juejin.im/?target=http%3A%2F%2Fvirtualenvwrapper.readthedocs.io%2Fen%2Flatest%2Finstall.html)。

### 使用方法

创建虚拟环境：

```shell
mkvirtualenv venv
#mkvirtualenv [python版本]  [环境名]
#python版本：--python=[python路径]，不填则默认系统环境中的版本
#环境名：一般py开通用途结尾，比如以tensorflow为基础的python环境，名字可以定义为pytf。
```

注意：mkvirtualenv 也可以使用 virtualenv 的参数，比如 `–python` 来指定 Python 版本。创建虚拟环境后，会自动切换到此虚拟环境里。虚拟环境目录都在 WORKON_HOME 里。

其他命令如下：

```shell
lsvirtualenv -b # 列出虚拟环境

workon [虚拟环境名称] # 切换虚拟环境

lssitepackages # 查看环境里安装了哪些包

cdvirtualenv [子目录名] # 进入当前环境的目录

cpvirtualenv [source] [dest] # 复制虚拟环境

deactivate # 退出虚拟环境

rmvirtualenv [虚拟环境名称] # 删除虚拟环境
```



①     虚拟环境可以搭建多个。

②     在虚拟环境内使用跟正常的python环境一样，使用pip安装的包在不同环境间彼此独立。

③     只要记得自己的环境名，在不同环境下切换非常方便。

④     Pycharm可以直接关联到单独的虚拟环境

## virtualenv设置pip config文件 设置代理

[Virtualenv specific pip config files](https://stackoverflow.com/questions/35638576/virtualenv-specific-pip-config-files)

虚拟环境下`pip.config`路径

```shell
 ~/.virtualenvs/env1/pip.conf
 ~/.virtualenvs/env2/pip.conf
```

```shell

#linux:
#编辑~/.pip/目录下的pip.conf文件（如果没有则创建该文件~/.pip/pip.conf），然后编辑其内容如下
[global]
trusted-host=cmc-cd-mirror.rnd.huawei.com
index-url=http://cmc-cd-mirror.rnd.huawei.com/pypi/simple/

#Windows:
#在C:\Users\你的账号\pip 目录(没有这个pip文件夹就自己创建一个)下添加pip.ini 文件，然后编辑其内容如下

[global]
trusted-host=cmc-cd-mirror.rnd.huawei.com
index-url=http://cmc-cd-mirror.rnd.huawei.com/pypi/simple/
```

```shell
#临时使用
pip install -i <http://cmc-cd-mirror.rnd.huawei.com/pypi/simple/> package-name
```

### 各种环境下`pip.config`路径

来源：<https://pip.pypa.io/en/stable/user_guide/#config-file>

pip allows you to set all command line option defaults in a standard ini style config file.

The names and locations of the configuration files vary slightly across platforms. You may have per-user, per-virtualenv or site-wide (shared amongst all users) configuration:

**Per-user**:

- On Unix the default configuration file is: `$HOME/.config/pip/pip.conf` which respects the `XDG_CONFIG_HOME`environment variable.
- On macOS the configuration file is `$HOME/Library/Application Support/pip/pip.conf` if directory `$HOME/Library/Application Support/pip` exists else `$HOME/.config/pip/pip.conf`.
- On Windows the configuration file is `%APPDATA%\pip\pip.ini`.

There are also a legacy per-user configuration file which is also respected, these are located at:

- On Unix and macOS the configuration file is: `$HOME/.pip/pip.conf`
- On Windows the configuration file is: `%HOME%\pip\pip.ini`

You can set a custom path location for this config file using the environment variable `PIP_CONFIG_FILE`.

**Inside a virtualenv**:

- On Unix and macOS the file is `$VIRTUAL_ENV/pip.conf`
- On Windows the file is: `%VIRTUAL_ENV%\pip.ini`

**Site-wide**:

- On Unix the file may be located in `/etc/pip.conf`. Alternatively it may be in a “pip” subdirectory of any of the paths set in the environment variable `XDG_CONFIG_DIRS` (if it exists), for example `/etc/xdg/pip/pip.conf`.
- On macOS the file is: `/Library/Application Support/pip/pip.conf`
- On Windows XP the file is: `C:\Documents and Settings\All Users\Application Data\pip\pip.ini`
- On Windows 7 and later the file is hidden, but writeable at `C:\ProgramData\pip\pip.ini`
- Site-wide configuration is not supported on Windows Vista