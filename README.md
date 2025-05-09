放置在“plugins”插件文件夹中的插件将自动托管在此仓库的 GitHub Pages 站点上，提交到 master 分支将自动更新库。

## 步骤：

1、复制此模板并新建仓库
https://github.com/mklauber/tw5-plugins-template

点击："Use this template"-"Create a new repository"，填写仓库名 Repository name-点击"Create repository"

2、gui/Library.tid 修改链接指向自己的仓库

url: https://{github 用户名}.github.io/{仓库名}/library/index.html

示例：https://dyp1121054136.github.io/dyp-plugins-library/library/index.html

3、新建github动作配置文件.github/workflows/main.yml

```
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
