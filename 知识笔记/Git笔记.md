---
tags:
  - Git
date: 2026-02-25 01:50:55
---
![[img/post-git-notes-cover.png|720]]

# 解决git clone很慢问题

如果电脑上开着Clash等科学上网工具，你要知道：Linux的终端默认是不会走代理的。即使浏览器已经可以访问国外的网站，但是终端里是没有连接代理的。此时想要git clone一个项目，速度会依旧很慢。那么如何给终端接上代理？

假设你的代理软件本地端口是7897，你需要在终端依次输入以下两行代码声明代理：

```bash
export http_proxy=http://127.0.0.1:7897
export https_proxy=http://127.0.0.1:7897
```

那么新的问题来了：如何查看代理软件`本地端口`？

1. 打开代理软件，在设置里面寻找代理端口，一般会放在更多设置里。
2. 懒得找，那么对号入座。Clash系列(Clash for Windows,Clash Verge等)：默认通常是7890(HTTP/Socks5混合端口)。V2Ray/Xray系列(v2rayN,Qv2ray等)：默认通常是10808(Socks5)和10809(HTTP)。Shadowsocks(小飞机)：默认通常是1080。
3. 不是上述三个系列的代理软件，那么请看1。

注意：这个代理设置是“临时生效”的。它只对当前这个终端窗口起作用，绝对不会弄乱系统其他的网络设置。如果你关掉了这个终端或者新开了一个终端，需要重新执行这两行才能再次走代理。

如果想要在终端里拔掉节点，依次输入：

```bash
unset http_proxy
unset https_proxy
```

如果想要省事一点，可以把以上命令在.bashrc中写成一个函数，以本地端口是7897且函数命名为proxy_on为例，打开.bashrc：
```bash
proxy_on() {
    local proxy_port="${1:-7897}"

    export HTTP_PROXY="http://127.0.0.1:${proxy_port}"
    export HTTPS_PROXY="http://127.0.0.1:${proxy_port}"
    export ALL_PROXY="socks5://127.0.0.1:${proxy_port}"

    export http_proxy="$HTTP_PROXY"
    export https_proxy="$HTTPS_PROXY"
    export all_proxy="$ALL_PROXY"

    export NO_PROXY="localhost,127.0.0.1,192.168.0.0/16,10.0.0.0/8,172.16.0.0/12,::1"
    export no_proxy="$NO_PROXY"
}

proxy_off() {
    unset HTTP_PROXY HTTPS_PROXY ALL_PROXY NO_PROXY
    unset http_proxy https_proxy all_proxy no_proxy
}
```
修改 `~/.bashrc` 后，在当前终端重新加载：

```bash
source ~/.bashrc
```
之后在日常使用时，是需要在终端输入
```bash
proxy_on
```
即可

关闭当前终端的代理：

```bash
proxy_off
```

如果 Clash 临时改用了其他端口，例如 `7890`：

```bash
proxy_on 7890
```
## 常见故障：

1. `Connection refused`：Clash 未启动，或本地端口填写错误。
2. 请求超时：远程节点不可用、分流规则错误或节点连接质量差。
3. 浏览器正常但终端失败：浏览器可能使用自己的代理扩展，而终端没有执行 `proxy_on`。
4. 切换节点后 Codex 断开：原长连接失效，重新启动 Codex。
5. 局域网服务访问异常：检查 `NO_PROXY` 是否包含目标地址或网段。

## 作用范围
代理设置只影响执行 `proxy_on` 的当前终端及其子进程：

- 在终端 A 执行 `proxy_on`，终端 A 中运行的程序走代理。
- 终端 B 不会因此改变。
- 关闭终端 A 后，其中临时导出的变量随之消失。
- `proxy_off` 只取消当前终端中的代理变量，不会关闭 Clash。

# Git 提示“干净的工作区”但文件未提交
* **误解**：“干净”不代表没文件，而是代表所有文件都已提交或被忽略。
* **常见原因 (ROS开发)**：**仓库套仓库 (Nested Git)**。
    * 如果在 `src` 下的子文件夹里还有一个 `.git` 文件夹，外层的 Git 会自动忽略该子文件夹的所有内容。
* **解决**：删除子目录下的 `.git` 文件夹：`rm -rf src/my_pkg/.git`。

# 为什么 GitHub 上看不到 README.md
* **原因**：GitHub 默认只渲染仓库**根目录**下的 README.md。
* **错误位置**：如果把 README 放在 `src/` 或其他子文件夹里，首页是不会显示的。
* **解决**：将 README.md 移动到仓库的最外层目录。

# 如何强制覆盖 GitHub 历史 (清除旧存档)
* **场景**：想要彻底删除所有历史提交，让仓库变成全新的“第一次提交”状态。
* **步骤**：
    1.  删除本地历史：`rm -rf .git`。
    2.  重新初始化：`git init` -> `git add .` -> `git commit -m "Init"`。
    3.  强制推送：`git push -f origin main` (注意 `-f` 参数是强制覆盖的关键)。

# Git 中 `origin` 的含义
* **定义**：`origin` 是远程仓库地址（URL）的**别名**。
* **作用**：代替输入冗长的 `https://github.com/user/repo.git`。
* **操作**：
    * 查看：`git remote -v`
    * 修改：`git remote set-url origin <新地址>`
    * 删除：`git remote remove origin`

# 解决 GitHub README 图片不显示的问题 

## 现象描述
在 Windows 电脑上使用 Markdown 编写 `README.md` 时，使用相对路径插入的图片在本地编辑器（如 VS Code）中可以正常显示，但推送到 GitHub 后图片裂开、无法显示。

## 根本原因
这是典型的 **Windows和Web 路径分隔符差异**：
* **Windows 系统**：默认使用**反斜杠 `\`** 作为文件夹路径的分隔符。
* **Web 端与 Git (Linux/macOS)**：标准路径分隔符必须是**正斜杠 `/`**。

当 GitHub 的网页解析器遇到反斜杠 `\` 时，会将其视作普通字符或转义符，无法正确识别文件层级目录，从而导致找不到图片。

## 解决方案
在编写 Markdown 时，**统一将路径中的所有反斜杠 `\` 替换为正斜杠 `/`**。

**错误写法（仅本地 Windows 可见）**：
```markdown
![[img/Fig5_3_路径规划总览.png]]
```

# Git常用指令

#### 一、 建库与克隆
* `git init` 
    * 在这个文件夹里开启游戏存档功能。（把一个普通文件夹变成 Git 仓库）
* `git clone <网址>` 
    * 从云端（GitHub等）把别人的存档原封不动地下载到我的电脑里。

#### 二、 日常存档三步曲
* **第一步：检查状态**
    `git status`
    * 看看我今天改了哪些文件？哪些还没存档？（标红的说明还没准备好，标绿的说明准备好存档了）。
* **第二步：放入暂存区**
    `git add .`
    * 把当前目录下所有修改过的文件，统统放进暂存区，准备存档。 （注意 `.` 代表所有文件，也可以换成具体文件名）。
* **第三步：正式存档**
    `git commit -m "新增了水下机器人的阻力参数"`
    * 正式生成一个游戏存档，`-m` 后面带的引号里是给这次存档写的“备注名”，方便以后回滚的时候知道这次干了啥。

#### 三、 后悔药
* `git log`
    * 查看历史存档记录。能看到谁在什么时间提交了什么代码，以及每次存档的唯一串号（哈希值）。
* `git reset --hard <存档串号>`
    * 时光倒流，直接把代码恢复到那个特定的历史存档状态。**（警告：这个操作很危险，时光倒流后，未来的代码就没了！）**

#### 四、 联机对战
* `git push origin master` (或者 main)
    * 把我在本地做好的存档，**上传**到云端服务器。让队友也能看到。
* `git pull origin master` (或者 main)
    * 把队友在云端更新的最新存档，**下载**到我的电脑里，并和我的代码合并。每次写代码前最好先执行一下，防止冲突。

#### 五、 分支管理
* `git branch`
    * 查看当前有几个平行宇宙（分支）。打星号 `*` 的是你现在所在的宇宙。
* `git branch -M master`
    * 强制把当前分支的名字改成 `master`。
* `git checkout -b dev`  (较新版本的Git推荐用 `git switch -c dev`)
    * 创建一个名叫 `dev` 的新平行宇宙，并立马穿越过去。在这个宇宙里乱改代码，**绝对不会影响**原来 `master` 宇宙里的主线剧情。非常适合用来测试新算法。

#### 六、 日常更新操作
* `git commit -am "巴拉巴拉"`
   * 适用于如果文件之前已经被 Git 追踪过，你可以加上 -a 参数直接打包提交：这种方式不会提交那个显示为 Untracked files 的 .vscode/ 文件夹
