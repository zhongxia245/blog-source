## 一、介绍
这个是 hexo 生成的 blog 代码，这里主要保存的是 Markdown 的文章文件。

## 二、如何部署起来
```bash
# 下载代码
git clone https://github.com/zhongxia245/blog-source.git

# 安装依赖
yarn install 

# 下载主题
git clone https://github.com/zhongxia245/hexo-theme-BlueLake.git themes/BlueLake

# 启动
hexo s

# 生成静态文件，并且部署到服务器上
hexo d -g

```