### 功能：

放置在“plugins”插件文件夹中的tiddlywiki插件将自动托管在此仓库的 GitHub Pages 站点上，提交到 master 分支将自动更新tiddlywiki插件库。

### 步骤：

1、复制此模板并新建仓库
https://github.com/mklauber/tw5-plugins-template

点击："Use this template"-"Create a new repository"，填写仓库名 Repository name-点击"Create repository"

2、gui/Library.tid 修改链接指向自己的仓库

url: https://{github 用户名}.github.io/{仓库名}/library/index.html

示例：https://dyp1121054136.github.io/dyp-plugins-library/library/index.html

3、新建github动作配置文件.github/workflows/main.yml

```yaml
name: Node.js CI

on:
  push:
    branches: [ master ]
  workflow_dispatch:  # 添加手动触发事件

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: 'recursive'
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '22'
      - run: npm install tiddlywiki
      - run: mkdir -p node_modules/tiddlywiki/plugins/dyp/
      - run: cp -r plugins/* node_modules/tiddlywiki/plugins/dyp/
      - run: ./node_modules/.bin/tiddlywiki .  --output output/library --build library
      - run: ./node_modules/.bin/tiddlywiki .  --output output --build gui
      - name: Deploy to GitHub Pages
        if: success()
        uses: crazy-max/ghaction-github-pages@v2
        with:
          target_branch: gh-pages
          build_dir: output
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

4、修改仓库权限

"Settings"-"Actions"-"General"-"Workflow permissions"，勾选"Read and write permissions"，工作流在所有范围的存储库中均具有读写权限。

5、设置为中文

tiddlywiki.info 中增加

```
	"languages": [
                "zh-Hans"
	],
```

新建文件gui/$__language.json

```
[{"created":"20250508112256704","text":"$:/languages/zh-Hans","title":"$:/language","modified":"20250508112643825"}]
```

6、修改master分支中的内容会自动部署gh-pages分支，如果没有，可以手动部署

"Actions"-"Node.js CI"-"Run workflow"-"Branch: master"-"Run workflow"

7、GitHub Pages指向gh-pages分支

"Settings"-"Pages"，选"Deploy from a branch"，"gh-pages"-"/ (root)"-"Save"

8、部署成功后访问：
{github 用户名}.github.io/{仓库名}

示例：dyp1121054136.github.io/dyp-plugins-library


### 其他技巧：

1、想要预装tiddlywiki官方插件，可以修改tiddlywiki.info文件，插件名格式为"tiddlywiki/插件名"，用英文逗号隔开多个插件

示例：

```
	"plugins": [
		"tiddlywiki/pluginlibrary",
		"tiddlywiki/highlight"
	],
```

tiddlywiki官方插件库：https://github.com/TiddlyWiki/TiddlyWiki5/tree/master/plugins/tiddlywiki

2、个人常用配置和新条目可以上传到 gui 文件夹

可以直接上传json文件

例如：
网站标题、网站副标题

$__SiteTitle.json、$__SiteSubtitle.json

3、禁用自动部署，只需注释掉 push 触发器，只保留 workflow_dispatch 手动触发器，避免文件修改或上传到一半的时候就自动部署了

```yaml
  # push:  
  #   branches: [ master ]
```

4、字体插件有未知bug，如果字体不生效，可能是安装的时候woff2文件打包状态被破坏了，修改条目类型为 application/font-woff2 或 font/woff2 或 application/octet-stream (一种MIME类型，通用的二进制文件类型)，可使字体重新生效

### Deepwiki AI 分析本仓库：

https://deepwiki.com/dyp1121054136/dyp-plugins-library
