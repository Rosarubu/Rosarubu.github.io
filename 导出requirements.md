# 导出requirements.txt

## pipreqs

[pipreqs](https://pypi.org/project/pipreqs/)> 用于输出目前project用到的package

进到project代码所在目录

```shell
pipreqs  --encoding=utf-8 --use-local --force ./
```

`--use-local` 只用本地的包

## pip freeze

```shell
pip freeze > requirements.txt
```

这段将会导出所有当前环境下用到的包

## 导出后安装

```shell
pip install -r requirements.txt
```

参考链接：

[Generating a requirements.txt File with pip and pipreqs](http://www.furnaceai.com/python/generating-a-requirements-txt-file-with-pip/)

## 遇到的问题：

```shell
pipreqs  --encoding=utf-8 --use-local /code/

Traceback (most recent call last):
  File "d:\python36\lib\runpy.py", line 193, in _run_module_as_main
    "__main__", mod_spec)
  File "d:\python36\lib\runpy.py", line 85, in _run_code
    exec(code, run_globals)
  File "D:\Python36\Scripts\pipreqs.exe\__main__.py", line 9, in <module>
  File "d:\python36\lib\site-packages\pipreqs\pipreqs.py", line 396, in main
    init(args)
  File "d:\python36\lib\site-packages\pipreqs\pipreqs.py", line 386, in init
    generate_requirements_file(path, imports)
  File "d:\python36\lib\site-packages\pipreqs\pipreqs.py", line 117, in generate_requirements_file
    with open(path, "w") as out_file:
FileNotFoundError: [Errno 2] No such file or directory: '/code/requirements.txt'

```

创建`requirements.txt`文件，进入到`/code/`路径下运行同样的命令成功了