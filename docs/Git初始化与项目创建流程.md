# Git 初始化与 GitHub 项目创建流程


## 零、初始化一键配置
```powershell
# === Git 基础配置 ===
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub邮箱"
git config --global init.defaultBranch main
git config --global core.autocrlf true
# === SSH Key ===
ssh-keygen -t ed25519 -C "你的GitHub邮箱"
# 手动复制公钥到 GitHub Settings → SSH and GPG keys
cat ~/.ssh/id_ed25519.pub
# 验证ssh授权
ssh -T git@github.com
# === 首次推送已有项目 ===
cd 项目目录
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com:你的用户名/仓库名.git
git pull origin main --allow-unrelated-histories   # 如果远程已有文件，需要这一步
git push -u origin main
```

## 一、安装 Git 后的基础配置

首次安装 Git 必须先设置用户信息，否则无法提交。
```powershell
# 1. 配置用户名（将显示在每次提交记录中）
git config --global user.name "你的GitHub用户名"
# 2. 配置邮箱（必须和 GitHub 账号邮箱一致）
git config --global user.email "你的GitHub邮箱"
# 3. 设置默认分支名为 main（与 GitHub 保持一致）
git config --global init.defaultBranch main
# 4. Windows 换行符设置（避免团队协作时大量文件显示被修改）
git config --global core.autocrlf true
# 5. 控制 Git 对含中文 / 空格 / 特殊字符文件名的显示规则）
git config --global core.quotepath false
# 6. 查看所有配置
git config --list
# 7. 查看单条配置
git config user.name
git config user.email
```
> **可选**：设置默认编辑器为 VS Code
>
> ```powershell
> git config --global core.editor "code --wait"
> ```

### SSH Key 配置（GitHub 认证）
1. **生成 SSH Key**

```powershell
ssh-keygen -t ed25519 -C "你的GitHub邮箱"
```
一路回车即可，生成位置：
```
C:\Users\你的用户名\.ssh\id_ed25519      （私钥，不要泄露）
C:\Users\你的用户名\.ssh\id_ed25519.pub  （公钥，需要上传到 GitHub）
```
2. **查看并复制公钥**

```text
cat ~/.ssh/id_ed25519.pub
输出类似：ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your@email.com
```
3. 全选复制添加到 GitHub

```
浏览器打开 GitHub → 右上角头像 → Settings → 左侧 SSH and GPG keys → New SSH key → 粘贴公钥 → Add SSH key
```

4. **git bash测试连接**

```text
ssh -T git@github.com
成功响应：Hi username! You've successfully authenticated
```

## 二、GitHub 仓库创建
1. 登录 GitHub，点击右上角 **+** → **New repository**
2. 填写仓库名（例如 `SRAMS`）
3. 可选：添加描述
4. **核心选择**——是否勾选 "Add a README file"
| 选项 | 后续操作 |
|------|---------|
| **不勾选** README（推荐） | 本地 `git init` → `git add` → `git commit` → `git push`，一次成功 |
| **勾选** README | 需要先 `git pull` 拉取远程内容，再 `git push`，否则会报错 |

5. 本地项目推送到 GitHub

- 前提：GitHub 仓库为空，无任何文件**（新建仓库不勾选 README）**

```powershell
# 1. 进入项目目录
cd D:\xxx
# 2. 初始化本地仓库
git init
# 3. 添加所有文件到暂存区
git add .
# 4. 首次提交
git commit -m "初始化项目文档与设计稿"
# 5. 添加远程仓库
git remote add origin git@github.com:你的用户名/SRAMS.git
# 6. 推送到 GitHub
git push -u origin main
```
6. 验证远程仓库
```text
git remote -v
输出：
origin  git@github.com:你的用户名/SRAMS.git (fetch)
origin  git@github.com:你的用户名/SRAMS.git (push)
```

## 三、.gitignore 配置
项目根目录创建 `.gitignore` 文件，告诉 Git 哪些文件不需要跟踪。

### 推荐模板
```gitignore
# Node.js
node_modules/
# 构建产物
dist/
build/
target/
*.jar
*.war
# IDE
.idea/
*.iml
.vscode/
# 环境配置
.env
.env.local
# 日志
*.log
# 系统文件
.DS_Store
Thumbs.db
# Maven
log/
```
