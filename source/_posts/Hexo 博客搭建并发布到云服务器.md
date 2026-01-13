### 一.本地计算机搭建Hexo 环境

#### 1.安装node.js

#### 2.安装git

#### 3.安装hexo

```plain
npm install -g hexo-cli
hexo init everwin-notes
cd everwin-notes
npm install
hexo server

卸载hexo
 npm uninstall hexo-cli -g
再删除文件夹
```

#### 4.本地仓库推送至github

```plain
git init
git add .
git commit -m "init hexo blog"
git branch -M main
git remote add origin git@github.com:linxux001-beep/****.git
git pull origin main --allow-unrelated-histories
git push -u origin main
```

#### 5.初始化github仓库

服务器生成密钥

```plain
ssh-keygen -t rsa -b 4096 -m PEM -C "your_email@example.com"
cat id_rsa.pub >> authorized_keys
```

配置仓库secret

参考https://cloud.tencent.com/developer/article/1720500

配置github actions

```plain
# deploy.yml
name: deploy blog to aliyun
on:
  push:
    branches:
      - main
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # 切换分支
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: 'recursive' # This replaces the need for srt32/git-actions
          persist-credentials: false
      # 使用 node:24
      - name: use Node.js v24.12.0
        uses: actions/setup-node@v3
        with:
          node-version: v24.12.0
      # npm install
      - name: npm install
        run: |
          npm install -g hexo-cli
          npm install
        env:
          CI: true
      # build
      - name: hexo build
        run: |
          hexo clean
          hexo generate
        env:
          CI: true

      # Deploy
      - name: Deploy
        uses: easingthemes/ssh-deploy@v2.0.7
        env:
          SSH_PRIVATE_KEY: ${{ secrets.ACCESS_TOKEN }}
          ARGS: "-avz --delete"
          SOURCE: "public/"
          REMOTE_HOST: ${{ secrets.REMOTE_HOST }}
          REMOTE_USER: ${{ secrets.REMOTE_USER }}
          TARGET: ${{ secrets.TARGET }}
```

#### 6.配置nginx

省略

#### 7.安全告警

参考https://www.owo.cab/212/

```plain
curl.exe -L -H "Accept: application/vnd.github+json" `
   -H "Authorization: Bearer <YOUR-TOKEN>" `
   -H "X-GitHub-Api-Version: 2022-11-28" `
   https://api.github.com/meta | 
   ConvertFrom-Json | 
   Select-Object -ExpandProperty actions | 
   Out-File actions.txt -Encoding utf8
```

#### 8.流程解析

本地博客仓库---推送---->github仓库------触发----->github actions------自动部署----->云服务器

#### 9.扩展

使用腾讯云CICD工具完全避免安全告警