# github actions尝鲜

### blank.yml文件说明
```
# 一个workflow，名为deploy to tengxunyun
name: deploy to tengxunyun

on: # 此CI/CD触发时的事件
  push: # 在代码提交时自动触发
    branches:
      - master

# 一个 CI/CD 的工作流有许多 jobs 组成，比如最典型的 job 是 lint，test，build。
jobs: 
  build: # 构建job
    runs-on: ubuntu-latest # 跑workflow的服务器系统
    steps: # job的一系列动作
      # 切换分支获取源码
      - name: Checkout # step的名称，将会在 github action 的控制台中显示
        uses: actions/checkout@master # 选择一个action，可以理解为若干 steps.run，有利于代码复用
      # 安装使用 node:10
      - name: use Node.js 10
        uses: actions/setup-node@v1
        with:
          node-version: 10
      # 运行命令，npm install && npm run build
      - name: npm install and build
        run: |
          npm install
          npm run build
        env:
          CI: true
      # 部署到腾讯云服务器
      - name: Deploy to Server
        uses: easingthemes/ssh-deploy@v2.0.7
        env:
            SSH_PRIVATE_KEY: ${{ secrets.TOKEN }} # 本地.ssh文件下的私钥id_rsa，存在secrets中
            ARGS: "-avzr --delete" # 复制操作的参数。"-avzr --delete"意味着部署时清空云服务器目标目录下的文件
            SOURCE: "dist/" # 源目录，相对于$GITHUB_WORKSPACE根目录的路径
            REMOTE_HOST: "49.233.195.71" # 服务器域名
            REMOTE_USER: "root" # 腾讯云默认用户名为root
            TARGET: "/usr/share/nginx/html" # 目标目录

```