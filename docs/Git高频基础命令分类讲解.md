# Git 高频基础命令分类讲解
## 说明
本文档按**使用场景**分类整理 Git 命令，每个命令附带简短说明和常用示例。
分类原则：不是学一遍所有命令，而是遇到什么场景用什么命令。
---
## 一、仓库操作
### `git init` — 初始化一个新的 Git 仓库
```powershell
git init                    # 在当前目录创建仓库
git init 项目名              # 创建目录并初始化仓库
```
### `git clone` — 克隆远程仓库到本地
```powershell
git clone git@github.com:用户名/仓库名.git          # SSH 方式
git clone https://github.com/用户名/仓库名.git       # HTTPS 方式
```
### `git remote` — 管理远程仓库地址
```powershell
git remote -v                                      # 查看远程仓库列表
git remote add origin git@github.com:用户名/仓库名.git  # 添加远程仓库
git remote remove origin                           # 删除远程仓库
```
---
## 二、日常提交
### `git status` — 查看当前工作区状态
```powershell
git status     # 完整状态（推荐）
git status -s  # 精简模式
```
### `git add` — 将文件加入暂存区
```powershell
git add 文件名              # 添加单个文件
git add .                  # 添加所有变更（新文件+修改+删除）
git add -p                 # 交互式分段添加（精细控制每次提交的内容）
```
### `git commit` — 提交暂存区的变更
```powershell
git commit -m "提交信息"     # 直接写提交信息
git commit                  # 打开编辑器写多行提交信息
git commit --amend          # 修改上一次提交（未推送时使用）
```
### `git log` — 查看提交历史
```powershell
git log                        # 完整历史
git log --oneline              # 每行一条
git log --oneline --graph      # 带分支图
git log --oneline -5           # 只看最近 5 条
```
---
## 三、分支管理
### `git branch` — 查看和管理分支
```powershell
git branch                    # 查看本地分支（* 标记当前分支）
git branch -a                 # 查看所有分支（含远程）
git branch 分支名              # 创建新分支
git branch -d 分支名           # 删除已合并的分支
git branch -D 分支名           # 强制删除未合并的分支（谨慎）
```
### `git checkout` / `git switch` — 切换分支
```powershell
git checkout 分支名            # 切换到已存在的分支
git checkout -b 分支名         # 创建并切换到新分支
git switch 分支名              # 新版切换命令（Git 2.23+）
git switch -c 分支名           # 创建并切换到新分支
```
### `git merge` — 合并分支
```powershell
git merge 分支名                # 将指定分支合并到当前分支
git merge --no-ff 分支名        # 强制保留分支历史
```
**合并冲突处理流程：**
1. `git status` 查看哪些文件冲突
2. 搜索 `<<<<<<<` `=======` `>>>>>>>`
3. 手动保留需要的部分，删除标记行
4. `git add 文件名`
5. `git commit`
---
## 四、远程同步
### `git push` — 推送到远程仓库
```powershell
git push                          # 推送到默认远程
git push -u origin main           # 首次推送，设置上游分支
git push origin 本地分支名          # 推送本地分支到远程
git push origin --delete 分支名    # 删除远程分支
```
### `git pull` — 拉取远程并合并到本地
```powershell
git pull                          # 拉取当前分支远程并自动合并
git pull origin main              # 拉取远程 main 分支
git pull --rebase                 # 拉取后使用 rebase（历史更干净）
```
### `git fetch` — 仅拉取远程信息，不自动合并
```powershell
git fetch                         # 获取远程所有分支的最新提交
git fetch --prune                 # 拉取并清理已不存在的远程分支引用
```
> `fetch` 与 `pull` 的区别：`fetch` 只下载不改变工作区，`pull` = `fetch` + `merge`。
---
## 五、撤销与回退
### `git restore` — 撤销工作区/暂存区修改（Git 2.23+）
```powershell
git restore 文件名                # 撤销工作区的修改
git restore --staged 文件名       # 将文件从暂存区移除
```
### `git reset` — 回退提交（修改历史，谨慎使用）
```powershell
git reset --soft HEAD~1          # 撤销最近一次 commit，改动保留在暂存区
git reset --mixed HEAD~1         # 撤销最近一次 commit，改动保留在工作区
git reset --hard HEAD~1          # 撤销最近一次 commit，改动全部丢弃（不可恢复！）
```
### `git revert` — 新增一个提交来撤销历史（安全，推荐）
```powershell
git revert HEAD                  # 撤销最近一次提交
git revert 提交ID                # 撤销指定提交
```
> `reset` vs `revert`：已推送到远程的分支用 `revert`，绝对不要用 `reset --hard`。
### `git stash` — 暂存工作区，稍后恢复
```powershell
git stash                       # 暂存当前修改，工作区回到干净状态
git stash list                  # 查看所有暂存记录
git stash pop                   # 恢复最近一次暂存，并从列表移除
git stash drop                  # 删除最近一次暂存
```
**典型场景**：正在开发功能时，需要临时切到其他分支修 Bug，先把未提交的修改暂存起来。
---
## 六、比较与查看
### `git diff` — 查看文件具体差异
```powershell
git diff                        # 工作区 vs 暂存区（未 add 的改动）
git diff --staged               # 暂存区 vs 最近一次提交
git diff 分支A..分支B            # 两个分支之间的差异
```
### `git show` — 查看某次提交的详情
```powershell
git show                        # 查看最近一次提交的详情
git show 提交ID                 # 查看指定提交的详情
```
### `git blame` — 查看每行代码的最后修改者
```powershell
git blame 文件名                # 显示每行谁在什么时候改的
git blame -L 10,20 文件名       # 只看第 10 到 20 行
```
---
## 七、团队协作场景速查
### 开发一个新功能
```powershell
git checkout -b feature/xxx     # 从 main 创建功能分支
# 写代码...
git add .
git commit -m "feat: 完成 xxx"
git push -u origin feature/xxx  # 推送到远程
# 在 GitHub 上发起 Pull Request
```
### 同步 main 到功能分支
```powershell
# 方式一：merge（推荐新手使用）
git checkout main
git pull origin main
git checkout feature/xxx
git merge main

# 方式二：rebase（历史更干净）
git checkout feature/xxx
git rebase main
```
### 处理 PR review 意见
```powershell
# 在 feature/xxx 分支上修改 reviewer 指出的问题
git add .
git commit -m "fix: 根据 review 意见修改 xxx"
git push origin feature/xxx
# PR 页面自动更新，无需重新创建
```
## 八、常用命令速查表
| 场景 | 命令 |
|------|------|
| 查看当前状态 | `git status` |
| 添加所有变更 | `git add .` |
| 提交 | `git commit -m "消息"` |
| 推送 | `git push` |
| 拉取最新代码 | `git pull` |
| 创建并切换分支 | `git checkout -b 分支名` |
| 合并分支 | `git merge 分支名` |
| 查看提交历史 | `git log --oneline --graph` |
| 撤销本地修改 | `git restore 文件名` |
| 暂存当前工作 | `git stash` |
| 恢复暂存 | `git stash pop` |
| 撤销已提交的内容（安全） | `git revert HEAD` |
| 删除已合并的分支 | `git branch -d 分支名` |
| 强制删除分支 | `git branch -D 分支名` |
