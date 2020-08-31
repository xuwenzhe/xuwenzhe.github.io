# 使用 Hugo + Github Pages 搭建个人博客


Hugo: 一个静态网页生成器，将markdown格式的博文编译为HTML，CSS，JavaScript

Github Pages: 静态网页托管服务，将hugo转化后的博客公开给读者访问

reference: [Mogeko博客《使用 Hugo + GitHub Pages 搭建个人博客》](https://mogeko.me/2018/018/)

## Step 1 - 创建Github Pages对应的repo
repo name: [username].github.io

reference: [Github Pages official page](https://pages.github.com/)

## Step 2 - 安装Hugo

(Mac系统)

```
brew install hugo
```

reference: [Hugo official quickstart](https://gohugo.io/getting-started/quick-start/)

## Step 3 - 使用Hugo创建本地博客文件夹

```bash
hugo new site myBlog
cd myBlog
```

创建`myBlog`文件夹来存放博客站点。其中：

* `content`存放markdown博文
* `themes`存放皮肤主题
* `config.toml`配置站点

## Step 4 - 安装皮肤主题

此博客使用`mogege`主题，[原repo](https://github.com/Mogeko/mogege)

安装主题至`themes`下


```bash
git clone https://github.com/Mogeko/mogege.git themes/mogege
```

## Step 5 - 创建页面

```bash
hugo new about.md
hugo new posts/my-first-post.md
```

在`content`下自动生成页面，meta data包括：

* `title`: 页面标题
* `date`: 创建时间
* `draft`: 是否为草稿。建立server时，草稿需要加上`--buildDrafts`参数才能在public正确显示
* `description`: 描述 (optional)
* `tags:`: 标签，用于文章分类 (optional)


## Step 6 - 配置网站

修改配置文件`config.toml`

```toml
baseURL = "https://xuwenzhe.github.io" # <head> 里面的 baseurl 信息，填你的博客地址
title = "Wenzhe's Blog" # 浏览器的标题
languageCode = "zh-cn" # 语言
hasCJKLanguage = true # 开启可以让「字数统计」统计汉字
theme = "mogege" # 主题 (需要自己下载)

paginate = 11 # 每页的文章数
enableEmoji = true # 支持 Emoji
enableRobotsTXT = true # 支持 robots.txt
googleAnalytics = "" # Google 统计 id

preserveTaxonomyNames = true

[blackfriday]
  hrefTargetBlank = true
  nofollowLinks = true
  noreferrerLinks = true

[Permalinks]
 posts = "/:year/:filename/"

[menu]
  [[menu.main]]
    name = "Blog"
    url = "/posts/"
    weight = 1

  [[menu.main]]
    name = "Categories"
    url = "/categories/"
    weight = 2

  [[menu.main]]
    name = "Tags"
    url = "/tags/"
    weight = 3

  [[menu.main]]
    name = "About"
    url = "/about/"
    weight = 4

[params]
    since = 2020
    author = "Wenzhe"                          # Author's name
    avatar = "/images/me/avatar.png"           # Author's avatar
    subtitle = "Just for Fun"                  # Subtitle
    cdn_url = ""           # Base CDN URL
    home_mode = "" # post or other
    enableGitalk = true # gitalk 评论系统

    google_verification = ""

    description = "" # (Meta) 描述
    keywords = "" # site keywords

    beian = ""
    baiduAnalytics = ""

    license= '本文采用<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/" target="_blank">知识共享署名-非商业性使用 4.0 国际许可协议</a>进行许可'

[params.social]
    GitHub = "https://github.com/xuwenzhe"
    Twitter = "xxoo"
    Email   = "xxoo"
    Instagram = "xxoo"
    Wechat = "/images/me/wechat.png"  # Wechat QRcode image
    Facebook = "xxoo"
    Telegram = "xxoo"
    Dribbble = "xxoo"
    Medium = "xxoo"

[params.gitalk] # Github: https://github.com/gitalk/gitalk
    clientID = "" # Your client ID
    clientSecret = "" # Your client secret
    repo = "" # The repo to store comments
    owner = "" # Your GitHub ID
    admin= "" # Required. Github repository owner and collaborators. (Users who having write access to this repository)
    id= "location.pathname" # The unique id of the page.
    labels= "gitalk" # Github issue labels. If you used to use Gitment, you can change it
    perPage= 15 # Pagination size, with maximum 100.
    pagerDirection= "last" # Comment sorting direction, available values are 'last' and 'first'.
    createIssueManually= false # If it is 'false', it is auto to make a Github issue when the administrators login.
    distractionFreeMode= false # Enable hot key (cmd|ctrl + enter) submit comment.
```

## Step 7 - 本地预览

```bash
hugo server --buildDrafts -w
```

* `--buildDrafts`: 生成draft博文
* `-w`: 监控修改，自动更新修改过的站点

此时，在`http://localhost:1313`已经可以访问到博客啦

## Step 8 - 编译发布

在myBlog文件夹中编译

```bash
hugo
```

此时，编译后的静态文件会存放在`public`文件夹

```bash
cd public # 进入public文件夹
git init # 建立本地git仓库
git remote add origin https://github.com/[username]/[username].github.io.git # 关联
git add . # add
git commit -m "[commit message]" # commit
git push -u origin master # push
```

之后发布新博文可以在`public`直接执行后三条命令`add`,`commit`,`push`即可。

记得`draft`改为`false`



