# Git 分支与远程仓库 — 新手完全指南

> 本文档配合可视化管理工具使用，帮你彻底理解 Git 分支、远程仓库、推送机制。

---

## 一、核心概念图解

```
┌─────────────────────────────────────────────────────┐
│              你的电脑（本地仓库）                      │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    │
│  │  main    │    │ feature  │    │ 其他分支 │    │
│  │  (主分支) │    │ (功能分支)│    │          │    │
│  └────┬─────┘    └────┬─────┘    └────┬─────┘    │
│       │                │                │          │
│       └────────────────┴────────────────┘          │
│                         ↓ git push                  │
└─────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────┐
│             GitHub（远程仓库）                        │
│  ┌──────────┐    ┌──────────┐    ┌──────────┐    │
│  │origin/main│    │origin/fea│    │  其他    │    │
│  │  (主分支) │    │  ture   │    │  分支    │    │
│  └──────────┘    └──────────┘    └──────────┘    │
└─────────────────────────────────────────────────────┘
```

**关键理解：**
- **本地分支** = 你电脑上的代码版本
- **远程分支** = GitHub 上的代码版本
- **`origin`** = 远程仓库的别名（就是你的 GitHub 仓库地址）
- **跟踪关系** = 本地分支和远程分支的绑定关系

---

## 二、你的困惑解答

### Q1：怎么知道往哪个分支推？

**答：看跟踪关系**

```powershell
# 查看分支跟踪关系（最重要！）
git branch -vv
```

输出示例：
```
* main   abc1234 [origin/main] 提交说明
  feature   def5678 [origin/feature] 另一个提交
```

**`[origin/main]` 表示：** 本地 main 分支跟踪远程的 origin/main 分支
→ 所以你打 `git push`，就会推送到 `origin/main`

**如果没有跟踪关系，第一次要这样推：**
```powershell
git push -u origin main
# -u = 设置跟踪关系，以后直接 git push 就行
```

---

### Q2：分支和 VSCode 显示的一致吗？

**答：完全一致！**

| VSCode 显示 | 终端命令 | 含义 |
|-------------|---------|------|
| 左下角显示 `main` | `git branch` 输出 `* main` | 当前在 main 分支 |
| 左下角显示 `feature` | `git branch` 输出 `* feature` | 当前在 feature 分支 |

**VSCode 小技巧：**
- 点击左下角的分支名 → 可以快速切换分支
- 点击左下角的云图标 → 可以快速推送/拉取

---

### Q3：会不会不小心删除仓库推送不了？

**答：分两种情况**

#### 情况 1：删除的是"分支"（安全）
```powershell
git branch -d featuretest  # 删除本地分支
git push origin --delete newbranch  # 删除远程分支
```
- ✅ **安全**：只是删除一条版本线，主代码还在
- ✅ **可恢复**：分支删除后，代码不会丢失，可以恢复

#### 情况 2：删除的是"仓库"（危险）
在 GitHub 上点 `Settings → Delete repository`
- ❌ **危险**：整个项目、所有分支、所有历史记录都没了
- ⚠️ **可恢复**：GitHub 有回收站，30 天内可以恢复

**如何避免误删？**
1. 不要把 GitHub 仓库删除权限给不重要的人
2. 删除前，先备份整个项目文件夹
3. 重要项目，定期 clone 到本地备份

---

## 三、推送代码的正确流程

### 场景 1：推送到已跟踪的分支（最简单）
```powershell
# 1. 确保当前在正确的分支
git branch  # 看 * 号在哪

# 2. 直接推送
git push
```

### 场景 2：第一次推送到新分支
```powershell
# 1. 创建并切换到新分支
git checkout -b feature/新功能名

# 2. 开发完成后，第一次推送（设置跟踪）
git push -u origin feature/新功能名

# 3. 以后就可以直接 git push 了
git push
```

### 场景 3：推送到指定的远程和分支
```powershell
# 推送到 origin 这个远程的 main 分支
git push origin main

# 推送到 origin 这个远程的 feature 分支
git push origin feature/新功能名
```

---

## 四、检查清单（推送前必看）

每次推送前，检查这 3 件事：

```powershell
# ✅ 1. 当前在哪个分支？
git branch
# → 看哪个前面有 * 号

# ✅ 2. 远程仓库配置对了吗？
git remote -v
# → 应该看到 origin → 正确的 GitHub 地址

# ✅ 3. 分支跟踪关系对了吗？
git branch -vv
# → 应该看到 [origin/分支名]
```

**如果 3 个都是 ✅，直接打 `git push` 就行！**

---

## 五、常见错误和解决方案

### 错误 1：`fatal: The current branch has no upstream branch`
**原因：** 本地分支没有设置跟踪关系

**解决：**
```powershell
git push -u origin 当前分支名
```

---

### 错误 2：`fatal: 'origin' does not appear to be a git repository`
**原因：** 没有配置远程仓库，或者远程仓库名字不是 `origin`

**解决：**
```powershell
# 查看当前远程仓库配置
git remote -v

# 如果没有 origin，重新添加
git remote add origin https://github.com/你的用户名/仓库名.git

# 如果远程仓库名字不对，重命名
git remote rename 错误的名字 origin
```

---

### 错误 3：有多个远程仓库（aa、second、gitdemo1）
**原因：** 之前添加远程时用了不同的名字

**解决：**
```powershell
# 删除错误的远程
git remote remove aa
git remote remove second

# 保留正确的，并重命名为 origin
git remote rename gitdemo1 origin
```

---

### 错误 4：`! [rejected] main -> main (fetch first)`
**原因：** 远程有你本地没有的提交，需要先拉取

**解决：**
```powershell
# 先拉取远程最新代码
git pull

# 如果有冲突，解决冲突后再推送
git push
```

---

## 六、清理不需要的分支（实操）

### 第 1 步：删除本地不需要的分支
```powershell
# 先切换到 main（不能删除当前所在的分支）
git checkout main

# 删除本地 featuretest 分支
git branch -d featuretest
# -d = 安全删除（有未合并代码会警告）

# 如果上面报错，确定要删除，用强制删除
git branch -D featuretest
# -D = 强制删除（不管有没有合并）
```

### 第 2 步：删除远程不需要的分支
```powershell
# 删除远程的 newbranch 分支
git push origin --delete newbranch

# 删除远程的 rebranch 分支
git push origin --delete rebranch
```

### 第 3 步：清理本地记录的远程分支
```powershell
# 清理那些在远程已经删除的分支的本地记录
git fetch --prune
```

---

## 七、紧急情况恢复指南

### 如果不小心删了远程仓库怎么办？

**别慌！有 3 种恢复方法：**

#### 方法 1：从本地推送恢复（如果本地还有代码）
```powershell
# 重新添加远程仓库
git remote add origin https://github.com/你的用户名/仓库名.git

# 推送所有分支
git push -u origin main
```

#### 方法 2：从 GitHub 回收站恢复（如果是刚删除）
1. 登录 GitHub
2. 进入 `Settings → Repositories → Deleted repositories`
3. 找到被删除的仓库，点击 `Restore` 恢复

#### 方法 3：重新创建仓库（如果恢复不了）
1. 在 GitHub 上新建同名仓库
2. 本地重新关联并推送
```powershell
git remote set-url origin https://github.com/你的用户名/仓库名.git
git push -u origin main
```

---

## 八、日常工作流程（推荐）

### 标准流程：
```powershell
# 1. 确保当前在 main 分支
git checkout main

# 2. 拉取远程最新代码
git pull

# 3. 创建新分支开发新功能
git checkout -b feature/功能名

# 4. 开发完成后，提交代码
git add .
git commit -m "添加新功能"

# 5. 推送到远程（第一次需要 -u）
git push -u origin feature/功能名

# 6. 在 GitHub 上创建 Pull Request，合并到 main

# 7. 合并后，删除本地和远程的功能分支
git checkout main
git branch -d feature/功能名
git push origin --delete feature/功能名
```

---

## 九、5 个最重要的命令（记住这些！）

```powershell
git status          # 查看当前状态（最常用！）
git branch -a       # 查看所有分支（本地+远程）
git remote -v       # 查看远程仓库
git branch -vv      # 查看分支跟踪关系
git push -u origin main  # 第一次推送，设置跟踪
# 以后直接：
git push            # 推送代码（已设置跟踪）
```

---

## 十、总结：Git 分支和推送的核心逻辑

1. **本地分支** → 你电脑上的代码版本
2. **远程分支** → GitHub 上的代码版本
3. **跟踪关系** → 本地分支和远程分支的绑定
4. **`git push`** → 把本地分支的代码推送到跟踪的远程分支
5. **`git pull`** → 把远程分支的代码拉取到本地分支

**记住一句话：**
> 只要设置了跟踪关系，`git push` 就会推送到正确的远程分支！

---

## 附录：Windows PowerShell 注意事项

1. **不要加 `$` 前缀**（那是 Linux 教程的写法）
   ```powershell
   # ❌ 错误
   $ git config --global user.name "xixi"
   
   # ✅ 正确
   git config --global user.name "xixi"
   ```

2. **路径用反斜杠 `\`**
   ```powershell
   # ✅ 正确
   cd E:\Study\typescrpt\gitdemo1
   ```

3. **查看当前路径**
   ```powershell
   pwd  # 显示当前路径
   ```

---

> 📅 最后更新：2026-05-02
> 📧 如有问题，检查 `git status`、`git branch -vv`、`git remote -v` 这 3 个命令！
