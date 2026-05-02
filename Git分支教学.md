# Git 分支与远程仓库完整教学

## 一、核心概念理解

### 1. 本地分支 vs 远程分支

```
本地分支（你电脑上的）
├── main          ← 主分支，默认工作分支
├── featuretest   ← 你创建的测试分支
└── 其他自定义分支

远程分支（GitHub 上的）
├── origin/main      ← 远程的 main 分支
├── origin/newbranch  ← 远程的 newbranch 分支（你不需要的）
└── origin/rebranch   ← 远程的 rebranch 分支（你不需要的）
```

**关键理解：**
- 本地分支 = 你电脑上的代码版本
- 远程分支 = GitHub 上的代码版本
- `origin` = 远程仓库的别名（就是 https://github.com/DAISY123x/gitdemo1.git）
- 本地分支可以"跟踪"远程分支，这样 `git push` 就知道往哪推

---

## 二、查看和理解当前状态

### 命令 1：查看所有分支
```powershell
git branch -a
```

输出示例：
```
* main                 ← 前面有 * 号，表示你当前在这个分支
  featuretest         ← 本地分支
  remotes/origin/main ← 远程分支（remotes/ 开头）
  remotes/origin/newbranch
  remotes/origin/rebranch
```

### 命令 2：查看分支跟踪关系
```powershell
git branch -vv
```

输出示例：
```
* main    dfa0990 [origin/main] 提交说明
  featuretest    abc1234 另一个提交
```

**`[origin/main]` 表示：** 本地 main 分支跟踪远程的 origin/main 分支

### 命令 3：查看远程仓库配置
```powershell
git remote -v
```

输出：
```
origin  https://github.com/DAISY123x/gitdemo1.git (fetch)
origin  https://github.com/DAISY123x/gitdemo1.git (push)
```

---

## 三、推送代码到哪？怎么推？

### 场景 1：推送到默认跟踪的远程分支
```powershell
git push
```
**前提：** 本地分支已经设置了跟踪关系（如 main → origin/main）

### 场景 2：第一次推送到新分支
```powershell
git push -u origin 分支名
```
**`-u` 的作用：** 设置跟踪关系，下次直接 `git push` 就行

### 场景 3：推送到指定的远程和分支
```powershell
git push origin main
```
**含义：** 推送到 origin 这个远程的 main 分支

---

## 四、清理不需要的分支（实操）

### 步骤 1：删除本地不需要的分支
```powershell
# 先切换到 main（不能删除当前所在的分支）
git checkout main

# 删除本地 featuretest 分支
git branch -d featuretest
```

### 步骤 2：删除远程不需要的分支
```powershell
# 删除远程的 newbranch 分支
git push origin --delete newbranch

# 删除远程的 rebranch 分支
git push origin --delete rebranch
```

### 步骤 3：清理本地记录的远程分支
```powershell
# 清理那些在远程已经删除的分支的本地记录
git fetch --prune
```

---

## 五、常见问题和解决方案

### 问题 1：git push 报错 "no upstream branch"
**原因：** 本地分支没有设置跟踪关系

**解决：**
```powershell
git push -u origin 当前分支名
```

### 问题 2：不小心删除了远程仓库配置
**现象：**
```powershell
git remote -v
# 输出为空，或者不是 origin
```

**解决：**
```powershell
# 重新添加远程仓库
git remote add origin https://github.com/DAISY123x/gitdemo1.git

# 设置本地分支跟踪远程
git branch -u origin/main
```

### 问题 3：有多个远程仓库（aa、second、gitdemo1）
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

## 六、日常工作流程（推荐）

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

## 七、检查清单

每次推送前，检查这 3 件事：

✅ **1. 当前在哪个分支？**
```powershell
git branch    # 看哪个前面有 * 号
```

✅ **2. 远程仓库配置对了吗？**
```powershell
git remote -v    # 应该看到 origin 指向正确的 GitHub 地址
```

✅ **3. 分支跟踪关系对了吗？**
```powershell
git branch -vv    # 应该看到 [origin/分支名]
```

---

## 八、紧急情况恢复指南

### 如果搞砸了，想回到干净状态：

```powershell
# 1. 备份当前代码（复制整个文件夹）

# 2. 重新克隆远程仓库
cd ..
git clone https://github.com/DAISY123x/gitdemo1.git gitdemo1-backup

# 3. 用干净的仓库替换当前的
# （这样所有错误的分支和配置都会消失）
```

---

## 总结：记住这 5 个命令

```powershell
git status          # 查看当前状态（最常用！）
git branch -a       # 查看所有分支
git remote -v       # 查看远程仓库
git branch -vv      # 查看分支跟踪关系
git push            # 推送到远程（前提：已设置跟踪）
```
