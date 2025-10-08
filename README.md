# FRC_Start

## Github setup

### 1. Windows Git - Downloading Package

下载并安装 Windows Git：https://git-scm.com/downloads/win

---

### 2. 在 git 终端执行如下命令

```bash
cd D:\Visual_Studio\FRC_Start
echo "# FRC_Start" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/Anthonyzou101/FRC_Start.git
git push -u origin main
```

---

### 3. 在 Visual Studio 编辑 README.md

使用 Visual Studio 编辑 `README.md` 文件。

---

### 4. 推送到 GitHub

```bash
git status           # 检查文件状态，查看 README.md 是否已被修改并等待提交
git add README.md    # 添加修改到暂存区
# 或者添加所有修改:
git add .
git commit -m "update README.md"
git push origin main # 提交修改
```

---

### 5. 添加 .gitignore 文件

```bash
touch .gitignore  # 新建 .gitignore 文件
```

示例 `.gitignore` 内容：

```gitignore
# Visual Studio
.vs/
*.vsidx
*.suo
*.user
*.userosscache
*.sln.docstates

# Visual Studio 2015/2017 cache/options
/.vscode/
```

将 `.gitignore` 文件添加并推送到仓库：

```bash
git add .
git commit -m "add .gitignore file"
git push origin main # 提交修改
```

Setting up Android Studio and Github | FTC 16441 Pretty Smart Robotics
 
Java download: Java Downloads | Oracle
Andoird Studio download: Download Android Studio & App Tools - Android Developers
FTC Robot Control: FIRST Robotics Competition Control System — FIRST Robotics Competition documentation
