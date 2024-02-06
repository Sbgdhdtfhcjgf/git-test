## 安装 Node.js

1. 访问 [Node.js](https://nodejs.org/#download)，下载最新的 Node.js LTS 版本

1. 运行 Node.js 安装包按照安装指导完成安装，此处不再赘述

> **说明**
> 安装的时候会安装 Node.js 本身和 npm 包管理器，都是后面需要用到的工具

1. 安装完成后运行 cmd，执行以下命令查看 Node.js 和 npm 版本

```bash
node -v
npm -v
```

## 安装 Hexo

> **说明**
> 新旧版本 Hexo 安装命令有所区别，安装时以[官网](https://hexo.io/zh-cn/)为准

1. 运行 cmd，执行以下命令安装 Hexo

```
d:
cd blog
npm install hexo-cli -g
```

1. 安装完成后执行以下命令查看 Hexo 版本

```
hexo version
```

# 创建并初始化项目

------

1. 新建一个文件夹，用于存放博客的相关文件

> **说明**
> 文件夹位置自定义即可，如果后面出现问题想推倒重来，直接删除这个文件夹即可

1. 运行 cmd，进入文件夹

```
d:
cd blog
```

1. 运行以下命令初始化项目，初始化需要花费一些时间

```
hexo init
```

> **说明**
> 后面有关博客的所有的命令都要通过 cmd 在博客文件夹下执行，本文中为 `blog` 文件夹下

1. 启动服务

```
hexo server
```

1. 用浏览器打开 [http://localhost:4000](http://localhost:4000/) 或者 [http://127.0.0.1:4000](http://127.0.0.1:4000/) 就能看到你的博客雏形了，接下来将对你的博客进行一系列自定义配置。

> **说明**
> 按 Ctrl+C 可以停止本地预览服务

## 目录结构

```
.
├── .deploy       #需要部署的文件
├── node_modules  #Hexo插件
├── public        #生成的静态网页文件
├── scaffolds     #模板
├── source        #博客正文和其他源文件，404、favicon、CNAME 都应该放在这里
|   ├── _drafts   #草稿
|   └── _posts    #文章
├── themes        #主题
├── _config.yml   #全局配置文件
└── package.json
```

```bash
git clone https://github.com/theme-next/hexo-theme-next themes/next
npm install hexo-generator-searchdb --save
```

## 

```bash
# 生成静态文件：
hexo generate
hexo new [layout] <title>
hexo new  day01
```







