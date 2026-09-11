# GitHub Pages 网站部署流程总结

## 一、部署步骤

### 1. 新建GitHub仓库，上传本地项目

```text
本地项目
→ 初始化 Git
→ 推送到 GitHub 的 main
→ GitHub Actions 构建
→ GitHub Pages 发布
→ 得到网站地址
```

以个人网站为例：

1. 准备项目结构，网站入口通常是根目录的 `index.html`

```text
portfolio/
├─ index.html
├─ style.css
├─ games/
├─ assets/
└─ .github/
   └─ workflows/
      └─ deploy.yml
```

2. 初始化并推送到 GitHub（如果远程仓库已经连接，就不需要 `git init` 或 `git remote add origin`）

```powershell
cd "D:\portfolio"
git init
git add .
git commit -m "创建个人网站"
git branch -M main
git remote add origin https://github.com/用户名/仓库名.git
git push -u origin main
```

3. 部署源选择：GitHub Actions

Pages 设置：

```text
Settings → Pages → Source → GitHub Actions
```

GitHub 虚拟机部署流程是：

```text
main
→ Actions 读取项目文件
→ 上传网站构建产物
→ GitHub Pages 发布
```

以后更新网站：

```powershell
git add .
git commit -m "更新网站"
git push origin main
```

当前个人网站的 `deploy.yml` 使用的是：

```yaml
actions/checkout
actions/configure-pages
actions/upload-pages-artifact
actions/deploy-pages
```

4. 以后更新网站：

```powershell
git add .
git commit -m "更新网站"
git push origin main
```

推送后 Actions 自动运行部署流程，成功后网站会自动更新，不需要手动执行部署命令。

### SpaceShip 购买域名，添加 DNS 解析记录，等待生效

| 主机（全称）                                           | 类型  | 值（解析到目标 IP ）     |
| ------------------------------------------------------ | ----- | ------------------------ |
| `@`（裸域名 `.paratroopery.space`）                    | CNAME | `paratroopery.github.io` |
| `portfolio`（二级域名 `portfolio.paratroopery.space`） | CNAME | `paratroopery.github.io` |
| `note`（二级域名 `note.paratroopery.space`）           | CNAME | `paratroopery.github.io` |

### 2. 新建仓库部署网站（GitHub Pages）

1. 上传网站到仓库 → Settings → Pages

2. 部署源选择：Deploy from a branch，选择分支+网站所在文件夹（一般`main`+`/root`）

3. 部署源选择：Github Actions，仓库内准备 `.github/workflows/deploy.yml`

   ```yml
   name: Deploy MkDocs
   on:
     push:
       branches: [main]
   
   permissions:
     contents: read
     pages: write
     id-token: write
   
   jobs:
     deploy:
       runs-on: ubuntu-latest
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
   
       steps:
         - uses: actions/checkout@v5
         - uses: actions/setup-python@v6
           with:
             python-version: "3.x"
         - run: pip install mkdocs-material
         - run: mkdocs build
         - uses: actions/configure-pages@v5
         - uses: actions/upload-pages-artifact@v3
           with:
             path: site
         - id: deployment
           uses: actions/deploy-pages@v4
   
   ```

4. Custom domain 填入对应子域名，保存

5. 成功访问后勾选 `Enforce HTTPS`（GitHub会自动申请Let’s Encrypt证书，就绪后可勾选）

### 3. GitHub Pages 部署过程出现的问题

- **配置文件位置错误**

  MkDocs 工作流执行 `mkdocs build` 时，默认在仓库根目录寻找 `mkdocs.yml`

  如果文件被删除或放进了子目录，就会出现： `Error: Config file 'mkdocs.yml' does not exist.`

- **中文文件编码不是 UTF-8**
  
  Markdown 文件统一保存为 `UTF-8`，GBK 文件在 Actions 或 MkDocs 中可能报：`'utf-8' codec can't decode byte ...`

- **Actions 权限不足**
  
  Pages artifact 工作流需要：
  
  ```yaml
  permissions:
    contents: read
    pages: write
    id-token: write
  ```

- **裸域名只能绑定一个GitHub仓库**

  多站点必须用子域名隔离，每个子域名单独在对应GitHub仓库配置**Custom Domain**
