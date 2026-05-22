# Typora+PicGo图床+Git+Github打造云笔记教程

适合需要在本地编辑、云端同步和版本管理的用户。

## 目标

- 使用 **Typora** 优雅地编写 Markdown 笔记

- 使用 **PicGo + GitHub 图床** 插入并自动上传图片

- 使用 **Git + GitHub 仓库** 同步笔记内容到云端

- 实现**跨设备同步、版本管理、图片可视化、无需担心本地图片丢失**

## 一、准备工作

1. **安装软件**

| 名称   | 作用            | 官网/下载地址                        |
| ------ | :-------------- | :----------------------------------- |
| Typora | Markdown 编辑器 | https://typora.io/                   |
| PicGo  | 图床上传工具    | https://github.com/Molunerfinn/PicGo |
| Git    | 版本控制        | https://git-scm.com/                 |

2. 注册 GitHub 账号

访问 https://github.com/	注册账号并登录

##  二、创建 GitHub 仓库

1. 登录 GitHub

2. 创建两个仓库（建议分开）：

   A. 云笔记仓库（文字内容）：

   - Repository name: `Note`
   - Description: 你的云笔记
   - 类型：Public 或 Private
   - ✅ 初始化仓库（Initialize with a README）——**不勾选**，我们从本地推送

   B. 图床仓库（存图片）：

   - Repository name：`my-images`
   - Description: my-images
   - 类型：**建议 Public**（否则图片外链无法访问）

3. 点击右上角 ➕号 → New repository(新建仓库)

4. 填写仓库信息：

   - Repository name: `Note`
   - Description: 你的云笔记
   - Public 或 Private（根据自己需求是否要公开）
   - ✅ 初始化仓库（Initialize with a README）——**不勾选**，我们从本地推送

5. 点击 “Create repository”

仓库的创建工作就完成了！

### 配置 PicGo 上传图片到 GitHub 图床

#### PicGo 安装与配置

- 下载 PicGo：https://github.com/Molunerfinn/PicGo/releases
- 安装后打开 PicGo，进入「图床设置」->「GitHub」

配置如下：

- 仓库名：`用户名/图床仓库名`（如：`yourname/img-bed`）
- 分支名：`main`（或 master，视你的 GitHub 默认而定）
- Token：GitHub -> Settings -> Developer settings -> Personal access tokens 创建一个带 repo 权限的 token
- 存储路径：设置为 `img/` 或其他（PicGo 会将图片存储在这个路径下）
- 自定义域名（可选）：填写 GitHub 图床的 CDN 地址或 raw 地址（如：`https://raw.githubusercontent.com/yourname/img-bed/main`）
- https://cdn.jsdelivr.net/gh/username/img-bed

#### 设置 Typora 调用 PicGo 上传

Typora -> 偏好设置 -> 图像：

- 插入图片时：上传图片
- 上传服务：选择 `PicGo (app)`
- 设置好 PicGo 路径（通常是 `/Applications/PicGo.app` 或 `C:\Program Files\PicGo\PicGo.exe`）

###  开始写作与同步

1. 打开 Typora 编辑笔记
2. 插入图片将自动上传到 GitHub 图床
3. 使用 Git 命令将笔记同步到远程仓库：

```
bash
git add .
git commit -m "更新笔记"
git push
```



如果你想在执行 `git push` 时 **忽略图片文件夹**（例如 `images/` 或 `assets/`），也就是不将它们加入版本控制，你可以通过设置 `.gitignore` 文件来实现。

------

✅ 步骤一：在 `.gitignore` 中忽略图片文件夹

### 假设你要忽略 `images/` 文件夹：

1. 打开仓库根目录下的 `.gitignore` 文件（如果没有就新建一个）；
2. 添加以下内容：

```
bash
# 忽略图片文件夹
images/
```

如果你的图片存储路径是 `assets/`，就写：

```

assets/
```

> ✅ 表示忽略整个文件夹及其内容。

------

⚠️ 注意：Git **只会忽略“未追踪”的文件**

如果图片文件夹 **已经被 Git 管理过了（已提交过）**，那么即使你添加到 `.gitignore`，Git 仍会继续跟踪这些文件。

### 🚫 错误例子

```
bash复制编辑git add images/
git commit -m "加了图片"
# 即使现在 .gitignore 中写了 images/，Git 也还会继续推送
```

------

✅ 步骤二：移除已追踪的图片文件夹（如果之前已提交）

如果你已经提交过图片文件夹，需要手动“取消跟踪”：

```
bash

git rm -r --cached images/
```

然后再提交这个操作：

```
bash
git commit -m "移除图片目录并加入 .gitignore 忽略"
git push
```

这样 Git 就不会再跟踪 `images/` 文件夹，后续推送时也不会上传图床图片。





笔记自动同步脚本

##  脚本功能亮点

自动拉取远程更新，避免冲突

自动 `add + commit + push`

自动设置 `upstream`（首次推送）

显示详细执行过程与错误提示

可用于 macOS / Linux（兼容 `bash` 环境）

手动关闭终端窗口，避免自动退出

一键运行



## 图床测试

![01](https://raw.githubusercontent.com/Sylen-X/PicGo/main/images/20251206182322802.jpg)





## 配置忽略文件

```
# 忽略自动更新脚本
*.sh
# 忽略本文件
*.gitignore
# 忽略图片文件目录
assets/
```



------

## 📜 智能同步脚本 

请将此文件保存为你仓库根目录下的 `sync.sh` 并赋予执行权限（`chmod +x sync.sh`）：

### 简易版本

```
#!/bin/bash
git add .
git commit -m "自动同步：$(date '+%Y-%m-%d %H:%M:%S')"
git push
```

### 复杂版本

```
#!/bin/bash
# 脚本文件名：sync_notes.sh

# 设置颜色变量（用于美化输出）
GREEN='\033[0;32m'
RED='\033[0;31m'
NC='\033[0m' # 无颜色，重置终端颜色

# 打印提示
echo -e "${GREEN}📁 当前目录：$(pwd)${NC}"

# 拉取远程仓库最新更新（防止冲突）
echo -e "${GREEN}📥 正在拉取远程更新（git pull --rebase）...${NC}"
git pull --rebase
if [ $? -ne 0 ]; then
    echo -e "${RED}❌ 拉取失败！可能存在冲突，请手动解决后再同步。${NC}"
    read -p "按回车键退出..." dummy
    exit 1
fi

# 添加所有更改（包括新增/修改文件）
echo -e "${GREEN}📦 添加文件中（git add .）...${NC}"
git add .

# 生成提交信息（包含时间戳）
commit_msg="自动同步：$(date '+%Y-%m-%d %H:%M:%S')"
echo -e "${GREEN}📝 提交更改：${commit_msg}${NC}"
git commit -m "$commit_msg"

# 如果没有改动可提交，git commit 会返回非 0
if [ $? -ne 0 ]; then
    echo -e "${RED}⚠️ 没有文件变动可提交，跳过提交步骤。${NC}"
fi

# 推送到远程仓库，如果是第一次推送（没有 upstream），自动设置
echo -e "${GREEN}🚀 正在推送到远程仓库（git push）...${NC}"
git push

# 如果失败（通常是第一次推送），尝试设置 upstream 后再推
if [ $? -ne 0 ]; then
    current_branch=$(git symbolic-ref --short HEAD)
    echo -e "${RED}⚠️ 检测到未设置 upstream，正在自动设置并推送...${NC}"
    git push -u origin "$current_branch"
    
    if [ $? -ne 0 ]; then
        echo -e "${RED}❌ 推送失败，请检查网络连接或远程仓库权限。${NC}"
        read -p "按回车键退出..." dummy
        exit 1
    fi
fi

# 同步完成提示
echo -e "${GREEN}✅ 同步成功完成！${NC}"

# 等待用户按键后手动关闭终端窗口
read -p "🔚 按回车键退出脚本..." dummy

```

------

## 🧪 使用方式

1. 把 `sync.sh` 放到你的 Typora 本地仓库目录中；
2. 赋予执行权限：

```
bash

chmod +x sync.sh
```

1. 每次更新笔记后运行：

```
bash

./sync.sh
```









