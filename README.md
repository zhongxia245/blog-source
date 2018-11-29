## 一、介绍

这个是 hexo 生成的 blog 代码，这里主要保存的是 Markdown 的文章文件。

## 二、如何部署起来

```bash
# 下载代码
git clone https://github.com/zhongxia245/blog-source.git

# 安装依赖
yarn install

# 下载主题
git clone https://gitee.com/zhongxia245/hexo-theme-polarbear-update.git themes/polarbear

# _config.yml 修改 theme
# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: polarbear

# 启动
hexo s

# 生成静态文件，并且部署到服务器上
hexo d -g
# or
npm run deploy

# 部署使用 gitee pages 服务进行部署，很简单，并且打开速度杠杠的。
```

## 三、新建文章

```
hexo new "postName"
```

## 四、使用的插件

使用插件 `hexo-abbrlink` 来生成唯一的 hexo 地址链接

```yaml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://www.izhongxia.com
root: ''
permalink: posts/:abbrlink.html   # :abbrlink 表示生成的唯一id， hexo clean 也不会把地址变了
permalink_defaults:
```

## 五、文章加密访问

post 头部添加 `password: 123456` 即可开启密码功能

### 1. 如果使用 algolia 搜索，则替换 depoly

```bash
"depoly": "export HEXO_ALGOLIA_INDEXING_KEY=a36beb21c35137004e52f72f1912c715 && rm -rf .deploy_git && hexo algolia && hexo d -g",
```

next 主题的 `algolia_search` 改为 `true` ，关闭 `local_search` 即可。
