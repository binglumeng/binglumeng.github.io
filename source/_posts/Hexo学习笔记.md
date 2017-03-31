---
title: Hexo使用说明笔记
date: 2017-03-21 16:26
tags:
    - Hexo
    - 博客
categories:
    - 网络相关
---

# Hexo使用说明笔记

本文简要介绍如何使用Hexo在github上搭建一个个人博客网站，关于Hexo的详细使用说明，还请参考[官方文档](https://hexo.io/zh-cn/docs/index.html)

## 1、文件简介

在Hexo生成的目录下，大致有一下几个文件夹/文件：

- public 生成的网站文件，发布的站点文件。

- source资源文件夹，用于存放内容。

- tag 标签文件夹。

- archive归档文件夹。

- category分类文件夹。

- downloads/code include code文件夹。

- :lang i18n_dir 国际化文件夹。

- _config.yml 配置文件

  - 配置说明

  ```yaml
  # Hexo Configuration
  ## Docs: https://hexo.io/docs/configuration.html
  ## Source: https://github.com/hexojs/hexo/

  # Site，参数说明：title 网站标题；subtitle 网站副标题；description 网站描述，在搜索引擎中可被搜索的信息，可以配置描述关键字；author 作者姓名；language 网站语言；timezone 网站时区；
  # 默认使用电脑本地时区，可以用时区表指定，如America/New_York，UTC等。
  title: Binglumeng's Blog
  subtitle: Just do what you want !
  author: 冰路梦
  language: 
  timezone: 

  # URL
  ## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
  # 参数说明：url 网址；root 网址根目录，要是网站存放在子目录，这里就要配置到子目录路径，如url是https://binglumeng.github.io ,而https://binglumeng.github.io/blog才是文件路径，那么root就要设置为/blog/；
  # permalink 文章永久链接格式，:year/:month/:day/:title/ ;permalink_defaults 永久链接中各部分的默认值。
  url: https://binglumeng.github.io/
  # root就是网址路径path
  root: /binglumeng/
  permalink: :year/:month/:day/:title/
  permalink_defaults:

  # Directory 目录参数说明：source_dir 资源文件夹，用于存放内容，默认值source；public_dir 公共文件夹，存放生成的站点文件，默认值public；
  # tag_dir 标签文件夹 默认值tag；archive_dir 归档文件夹 默认值archives;category_dir 分类文件夹，默认值categories;
  # code_dir include code 文件夹，默认值 downloads/code ;i18n_dir 国际化文件夹 ，默认值 :lang ；skip_render 跳过指定的渲染，可以使用glob表达式来匹配路径。

  source_dir: source
  public_dir: public
  tag_dir: tags
  archive_dir: i_dont_wanna_use_default_archives
  category_dir: categories
  code_dir: downloads/code
  i18n_dir: :lang
  skip_render:

  # Writing 文章相关参数：new_post_name 新文章文件名 ，默认值:title.md ;
  # default_category 预设布局，默认值 post ;
  # auto_spacing 中英文之间加入空格，默认 false；
  # titlecase 把标题转换为title case 默认值 false；
  # external_link 新标签中打开链接 ，默认值 true；
  # filename_case 把文件名转换为(1)小写或(2)大写。默认值0；
  # render_drafts 显示草稿，默认值false；
  # post_asset_folder 启动Asset文件夹，默认值false；
  # relative_link 把链接改为与根目录的相对路径位置，默认值false；
  # future 显示未来的文章，默认值true；
  # highlight 代码块设置
  new_post_name: :title.md # File name of new posts
  default_layout: post 
  titlecase: false # Transform title into titlecase
  external_link: true # Open external links in new tab
  filename_case: 0
  render_drafts: false
  post_asset_folder: true
  relative_link: false
  future: true
  highlight:
    enable: true
    line_number: true
    auto_detect: true
    tab_replace:

  # Category & Tag 分类和标签 ，default_categorty 默认分类，默认值 uncategorized;
  # category_map 分类别名；tag_map 标签别名
  default_category: uncategorized
  category_map:
  tag_map:

  # Date / Time format 日期时间格式 date_format 日期格式 ，默认是YYYY-MM-DD ；time_format 时间格式 ，H:mm:ss
  ## Hexo uses Moment.js to parse and display date
  ## You can customize the date format as defined in
  ## http://momentjs.com/docs/#/displaying/format/
  date_format: YYYY-MM-DD
  time_format: HH:mm:ss

  # Pagination 分页 per_page 每页显示的文章数，0表示关闭分页。默认值10；
  # pagination_dir 分页目录，默认值page
  ## Set per_page to 0 to disable pagination
  per_page: 10
  pagination_dir: page

  # Extensions 扩展，theme 当前主题的名称 ，false表示禁止使用主题。
  ## Plugins: https://hexo.io/plugins/
  ## Themes: https://hexo.io/themes/
  theme: binglumeng

  # Deployment 部署的设置
  ## Docs: https://hexo.io/docs/deployment.html
  deploy:
    type: git
    repo: https://github.com/
    branch: master
  ```

- package.json 包信息

# 指令说明

使用hexo首先要安装node.js和git。本文演示为windows系统下，linux和mac请参照官方文档。

- init

```shell
hexo init [folder] # 在cmd命令下，cd到你所需要建立博客的文件夹，执行此命令，其中folder为可选指令，若不写，则默认当前目录。
```

- new

```sh
hexo new [layout] <title> # layout为可选项，默认使用_config.yml中的default_layout。新建文章的指令
```

- generate

```sh
hexo generate # 生成静态文件，
# 可选参数：
-d ,--deploy 文件生成后立即部署网站
-w , --watch 件事文件变动
```

该指令可以简写为`hexo g`

- publish

```sh
hexo publish [layout] <filename> # 发布草稿
```

- server

```sh
hexo server # 启动server，就可以在本地预览效果。
# 参数，默认网址http://localhost:4000/
-p , --port 重设端口
-s , --static 只是用静态文件
-l , --log 启动日志记录，使用覆盖记录格式
-i , --ip 重新制定服务器ip
```

- deploy

```sh
hexo deploy # 发布到网站，这里就是发布到_config.yml中deploy中设置的网址上。
# 参数
-g , --generate 部署前生成静态文件
```

该命令可以简写`hexo g`

- render

```sh
hexo render <file1> [file2] ... # 渲染文件
# 参数
-o , --output 设置输出路径
```

- migrate

```sh
hexo migrate <type> # 从其他博客系统迁移内容
```

- clean

```sh
hexo clean # 清除缓存文件(db.json)和已生成的静态文件(public)，通常更换主题后，无效时，可以运行此命令。
```

- list

```sh
hexo list <type> # 列出网站资料
```

- version

```sh
hexo version # 显示hexo版本信息
```

#### 选项指令

> - 选项指令
>
> ```sh
> hexo --safe # 安全模式，会禁用插件和脚本。
> hexo --debug # debug 模式
> hexo --silent # 简洁模式，隐藏终端信息
> hexo --config custom.yml # 自定义配置文件路径，如此就不会使用_config.yml了。
> hexo --draft # 显示草稿，也就是source/_drafts
> hexo --cwd /path/to/cwd # 自定义当前工作目录
> ```
> - 迁移文章
>   - RSS 使用RSS需要安装`hexo-migrator-rss`插件
>
>     ```sh
>     npm install hexo-migrator-rss --save
>     #可使用如下命令，从RSS迁移所有文章
>     hexo migrate rss <source>
>     ```
>
>   - Jekyll 将`_post`文件夹内的文件复制到`source/posts`下，在`_config.yml`修改`new_post_name`
>
>     ```yaml
>     new_post_name: :year-:month-:day-:title.md
>     ```
>
>   - Octopress
>
>     将Octopress中`source/_posts`文件复制到Hexo的对应目录下，在修改`_config.yml`中`new_post_name`参数
>
>     ```yaml
>     new_post_name: :year-:month-:day-:title.md
>     ```
>
>   - WordPress
>
>     需要安装`hexo-migrator-wordpress`插件
>
>     ```sh
>     npm install hexo-migrator-wordpress --save
>     # wordpress导出数据,然后hexo迁移
>     hexo migrate wordpress <source> # source可以是网址，或者文件夹
>     ```
>
>   - Joomla
>
>     安装`hexo-migrator-joomla`插件，类似wordpress迁移。
>
>     ```sh
>     npm install hexo-migrator-joomla
>     hexo migrate joomla <source>
>     ```

## 3、写作

你可以执行下列命令来创建一篇新文章。

```
$ hexo new [layout] <title>
```

您可以在命令中指定文章的布局（layout），默认为 `post`，可以通过修改 `_config.yml` 中的 `default_layout` 参数来指定默认布局。

#### 布局（Layout）

Hexo 有三种默认布局：`post`、`page` 和 `draft`，它们分别对应不同的路径，而您自定义的其他布局和 `post` 相同，都将储存到 `source/_posts` 文件夹。

| 布局      | 路径               |
| ------- | ---------------- |
| `post`  | `source/_posts`  |
| `page`  | `source`         |
| `draft` | `source/_drafts` |

> **不要处理我的文章** 如果你不想你的文章被处理，你可以将 Front-Matter 中的`layout:` 设为 `false` 。

#### 文件名称

Hexo 默认以标题做为文件名称，但您可编辑 `new_post_name` 参数来改变默认的文件名称，举例来说，设为 `:year-:month-:day-:title.md` 可让您更方便的通过日期来管理文章。

| 变量         | 描述                   |
| ---------- | -------------------- |
| `:title`   | 标题（小写，空格将会被替换为短杠）    |
| `:year`    | 建立的年份，比如， `2015`     |
| `:month`   | 建立的月份（有前导零），比如， `04` |
| `:i_month` | 建立的月份（无前导零），比如， `4`  |
| `:day`     | 建立的日期（有前导零），比如， `07` |
| `:i_day`   | 建立的日期（无前导零），比如， `7`  |

#### 草稿

刚刚提到了 Hexo 的一种特殊布局：`draft`，这种布局在建立时会被保存到 `source/_drafts` 文件夹，您可通过 `publish` 命令将草稿移动到 `source/_posts` 文件夹，该命令的使用方式与 `new` 十分类似，您也可在命令中指定 `layout` 来指定布局。

```
$ hexo publish [layout] <title>
```

草稿默认不会显示在页面中，您可在执行时加上 `--draft` 参数，或是把 `render_drafts` 参数设为 `true`来预览草稿。

## 4、模版（Scaffold）

在新建文章时，Hexo 会根据 `scaffolds` 文件夹内相对应的文件来建立文件，例如：

```
$ hexo new photo "My Gallery"
```

在执行这行指令时，Hexo 会尝试在 `scaffolds` 文件夹中寻找 `photo.md`，并根据其内容建立文章，以下是您可以在模版中使用的变量：

| 变量       | 描述     |
| -------- | ------ |
| `layout` | 布局     |
| `title`  | 标题     |
| `date`   | 文件建立日期 |

## 5、Front-matter

Front-matter 是文件最上方以 `---` 分隔的区域，用于指定个别文件的变量，举例来说：

```
title: Hello World
date: 2013/7/13 20:46:25
---
```

以下是预先定义的参数，您可在模板中使用这些参数值并加以利用。

| 参数           | 描述         | 默认值    |
| ------------ | ---------- | ------ |
| `layout`     | 布局         |        |
| `title`      | 标题         |        |
| `date`       | 建立日期       | 文件建立日期 |
| `updated`    | 更新日期       | 文件更新日期 |
| `comments`   | 开启文章的评论功能  | true   |
| `tags`       | 标签（不适用于分页） |        |
| `categories` | 分类（不适用于分页） |        |
| `permalink`  | 覆盖文章网址     |        |

#### 分类和标签

只有文章支持分类和标签，您可以在 Front-matter 中设置。在其他系统中，分类和标签听起来很接近，但是在 Hexo 中两者有着明显的差别：分类具有顺序性和层次性，也就是说 `Foo, Bar` 不等于 `Bar, Foo`；而标签没有顺序和层次。

```
categories:
- Diary
tags:
- PS3
- Games
```

> **分类方法的分歧**
>
> 如果您有过使用WordPress的经验，就很容易误解Hexo的分类方式。WordPress支持对一篇文章设置多个分类，而且这些分类可以是同级的，也可以是父子分类。但是Hexo不支持指定多个同级分类。下面的指定方法：
> categories:DiaryLife
> 会使分类`Life`成为`Diary`的子分类，而不是并列分类。因此，有必要为您的文章选择尽可能准确的分类。

#### JSON Front-matter

除了 YAML 外，你也可以使用 JSON 来编写 Front-matter，只要将 `---` 代换成 `;;;` 即可。

```
"title": "Hello World",
"date": "2013/7/13 20:46:25"
;;;
```

标签插件和 Front-matter 中的标签不同，它们是用于在文章中快速插入特定内容的插件。

## 6、 标签插件（Tag Plugins）

标签插件和 Front-matter 中的标签不同，它们是用于在文章中快速插入特定内容的插件。

- #### 引用块

在文章中插入引言，可包含作者、来源和标题。

**别号：** quote

```
{% blockquote [author[, source]] [link] [source_link_title] %}
content
{% endblockquote %}
```

#### 样例

**没有提供参数，则只输出普通的 blockquote**

```
{% blockquote %}
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque hendrerit lacus ut purus iaculis feugiat. Sed nec tempor elit, quis aliquam neque. Curabitur sed diam eget dolor fermentum semper at eu lorem.
{% endblockquote %}
```

> Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque hendrerit lacus ut purus iaculis feugiat. Sed nec tempor elit, quis aliquam neque. Curabitur sed diam eget dolor fermentum semper at eu lorem.

**引用书上的句子**

```
{% blockquote David Levithan, Wide Awake %}
Do not just seek happiness for yourself. Seek happiness for all. Through kindness. Through mercy.
{% endblockquote %}
```

> Do not just seek happiness for yourself. Seek happiness for all. Through kindness. Through mercy.
>
> **David Levithan**Wide Awake

**引用 Twitter**

```
{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}
```

> NEW: DevDocs now comes with syntax highlighting. [http://devdocs.io](http://devdocs.io/)
>
> **@DevDocs**[twitter.com/devdocs/status/356095192085962752](https://twitter.com/devdocs/status/356095192085962752)

**引用网络上的文章**

```
{% blockquote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}
Every interaction is both precious and an opportunity to delight.
{% endblockquote %}
```

> Every interaction is both precious and an opportunity to delight.
>
> **Seth Godin**[Welcome to Island Marketing](http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html)

- #### 代码块

在文章中插入代码。

**别名：** code

```
{% codeblock [title] [lang:language] [url] [link text] %}
code snippet
{% endcodeblock %}
```

#### 样例

**普通的代码块**

```
{% codeblock %}
alert('Hello World!');
{% endcodeblock %}
```

```
alert('Hello World!');
```

**指定语言**

```
{% codeblock lang:objc %}
[rectangle setX: 10 y: 10 width: 20 height: 20];
{% endcodeblock %}
```

```
[rectangle setX: 10 y: 10 width: 20 height: 20];
```

**附加说明**

```
{% codeblock Array.map %}
array.map(callback[, thisArg])
{% endcodeblock %}
```

```
Array.map
array.map(callback[, thisArg])
```

**附加说明和网址**

```
{% codeblock _.compact http://underscorejs.org/#compact Underscore.js %}
_.compact([0, 1, false, 2, '', 3]);
=> [1, 2, 3]
{% endcodeblock %}
```

```
_.compactUnderscore.js
_.compact([0, 1, false, 2, '', 3]);
=> [1, 2, 3]
```

- #### 反引号代码块

另一种形式的代码块，不同的是它使用三个反引号来包裹。

\``` [language] [title] [url] [link text] code snippet ```

- #### Pull Quote

在文章中插入 Pull quote。

```
{% pullquote [class] %}
content
{% endpullquote %}
```

- #### jsFiddle

在文章中嵌入 jsFiddle。

```
{% jsfiddle shorttag [tabs] [skin] [width] [height] %}
```

- #### Gist

在文章中嵌入 Gist。

```
{% gist gist_id [filename] %}
```

- #### iframe

在文章中插入 iframe。

```
{% iframe url [width] [height] %}
```

- #### Image

在文章中插入指定大小的图片。

```
{% img [class names] /path/to/image [width] [height] [title text [alt text]] %}
```

- #### Link

在文章中插入链接，并自动给外部链接添加 `target="_blank"` 属性。

```
{% link text url [external] [title] %}
```

- #### Include Code

插入 `source` 文件夹内的代码文件。

```
{% include_code [title] [lang:language] path/to/file %}
```

- #### Youtube

在文章中插入 Youtube 视频。

```
{% youtube video_id %}
```

- #### Vimeo

在文章中插入 Vimeo 视频。

```
{% vimeo video_id %}
```

- #### 引用文章

引用其他文章的链接。

```
{% post_path slug %}
{% post_link slug [title] %}
```

- #### 引用资源

引用文章的资源。

```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

- #### Raw

如果您想在文章中插入 Swig 标签，可以尝试使用 Raw 标签，以免发生解析异常。

```
{% raw %}
content
{% endraw %}
```



> ==其上上面那么一大段，主要是用于编辑文章时候的文档语法，如果有自己的markdown编辑器，则可以用md文件，那么md语法就相对便捷。==[Markdown语法简介](http://blog.csdn.net/binglumeng/article/details/52949717)，注意有时候hexo不完全支持所有markdown的语法显示。



## 7、Asset资源文件夹

Asset用于存放除了`source`文件夹中的文章意外的文件，比如图片、CSS/JS文件等。若仅有少量的图片，可以在`source/images`中存放，在文章中用`![](images/img.jpg)`引用。

如果在`_config.yml`中配置了`post_asset_folder:true`那么Hexo创建新文章时候，会同时创建一个文件夹，存放相关资源。

**注意：**有时候的markdown相对引用在文章中显示正常，但是发布后不一定可以，新版Hexo 3 中可以使用标签

```yaml
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

比如使用相对引用图片资源时候

```yaml
{% asset_img img.jpg avaster %} # 这就可以正常的显示相对引用的图片资源
```

## 8、数据文件

Hexo 3中增加了`数据文件`的功能，便于资源复用。通过加载`source/_data`下的`yaml 或json`文件。

```yaml
# 例如在source/_data下menu.yaml
Home: /
Gallery: /gallery/
Archives: /archivers/
#那么就可以在模板中使用
{% for link in site.data.menu %}
	<a href="{{ link }}">{{ loop.key }}</a>
{% endfor %}
```

## 9、部署

在`_config.yml`中配置参数，就可以部署到服务器。

```yaml
deploy:
	type: git 
# 可以多个type,通过缩进表示层级 ,冒号后面要有空格！
deploy:
- type: git
	repo:
- type :heroku
	repo:
```

- git

安装`hexo-deploy-git`

```yaml
npm install hexo-deployer-git --save
```

配置`_config.yml`

```yaml
deploy:
	type: git
	repo: <repository url>
    branch: [branch]
    message: [message] #用与git自动提交的信息说明，默认格式Site updated:{{now('YYYY-MM-DD HH:mm:ss)}}
```



## 10、永久链接

`_config.yml`配置调用永久链接或在每篇文章的Front-matter中指定

- 变量

| 变量          | 描述                                      |
| ----------- | --------------------------------------- |
| `:year`     | 文章的发表年份（4 位数）                           |
| `:month`    | 文章的发表月份（2 位数）                           |
| `:i_month`  | 文章的发表月份（去掉开头的零）                         |
| `:day`      | 文章的发表日期 (2 位数)                          |
| `:i_day`    | 文章的发表日期（去掉开头的零）                         |
| `:title`    | 文件名称                                    |
| `:id`       | 文章 ID                                   |
| `:category` | 分类。如果文章没有分类，则是 `default_category` 配置信息。 |

修改`permalink_defaluts:`参数为空，就可以永久变量。

- 多语言支持

修改`new_post_name`和`permalink`参数：

```yaml
new_post_name: :lang/:title.md
permalink: :lang/:title/
#新建文章时候
hexo new "hello world" --lang tw
# 则输出 => source/_posts/tw/hello-world.md,访问网址也会变成url/tw/hello-world/
```



## 11、主题

创建Hexo主题，在`themes`文件夹内，新增任意文件夹，修改`_config.yml`内`theme`设置

```yaml
# 主题的结构
.
|--- _config.yml
|--- languages
|--- layout
|--- scripts
|___ source
```

## 12、模板

每个主题都必须有个`index`的模板，用于决定网站内容的呈现方式

| 模板         | 用途   | 回调        |
| ---------- | ---- | --------- |
| `index`    | 首页   |           |
| `post`     | 文章   | `index`   |
| `page`     | 分页   | `index`   |
| `archive`  | 归档   | `index`   |
| `category` | 分类归档 | `archive` |
| `tag`      | 标签归档 | `archive` |

