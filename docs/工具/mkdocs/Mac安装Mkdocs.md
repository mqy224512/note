# Mac 安装Mkdocs

## 安装Mkdocs

1. 安装python3（可能已经预装好来）

```shell
python3 --version
```

2. 安装pip3（可能已经预装好来）

```shell
pip3 --version
```

```shell
# 安装新的pip3，过早的版本无法安装mkdocs
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
python3 get-pip.py
```

3. 安装mkdocs

```shell
pip3 install mkdocs
```

```shell
$ mkdocs --version                                                                                                            
zsh: command not found: mkdocs
```

4. 修改profile file

```shell
vim ~/.zshrc
```

```shell
export PYTHON_HOME=/Users/mqy/Library/Python/3.9
export PATH=$PATH:$PYTHON_HOME/bin
```

```shell
source ~/.zshrc
```

```shell
$ mkdocs --version 
mkdocs, version 1.4.2 from /Users/mqy/Librarsy/Python/3.9/lib/python/site-packages/mkdocs (Python 3.9)
```

5. 创建笔记目录

```shell
mkdocs new my_note
```

```shell
cd my_note
```

```shell
$ tree -L 3
.
├── docs
│   └── index.md
└── mkdocs.yml

2 directories, 2 files
```

6. 启动mkdocs server

```shell
$ mkdocs serve
INFO     -  Building documentation...
INFO     -  Cleaning site directory
INFO     -  Documentation built in 0.03 seconds
INFO     -  [18:47:24] Watching paths for changes: 'docs', 'mkdocs.yml'
INFO     -  [18:47:24] Serving on http://127.0.0.1:8000/
```

7. 访问页面

```shell
http://127.0.0.1:8000/
```

## 安装主题

1. 安装主题

```shell
pip3 install mkdocs-material
```

2. 使用主题

```shell
# 修改mkdocs.yml
site_name: mqy9174583
theme:
	name: "material"
```

## 设置index

> 参考 https://mkdocs-like-code.readthedocs.io/zh_CN/latest/MkDocs-advanced-operations/files-layout/

直接创建目录及文件即可，无需修改mkdocs.yml，docs下面的目录结构，页面上会原封不动的显示出来。

## 部署到github

```
# 前提:当前目录上是github上的一个参数
mkdocs gh-deploy
```



