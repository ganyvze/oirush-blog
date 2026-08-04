---
title: VSCode环境配置指南
published: 2026-01-02
description: "如何配置VSCode环境？"
image: "./cover.jpeg"
tags: ["博客"]
category: 指南
draft: false
---

这是一篇专门针对**信息学竞赛**选手的 VSCode C++ 环境配置保姆级教程。从环境安装、插件配置到竞赛专属快捷模板和自动化测试插件，帮助你打造最流畅的刷题与竞赛环境。

在信息学竞赛中，许多同学早期使用的是 Dev-C++ 或 Code::Blocks。虽然它们开箱即用，但在代码补全、界面颜值、插件生态以及现代 C++ 标准支持上，早已跟不上时代。

**VSCode（Visual Studio Code）** 凭其轻量、强大、高颜值和丰富的扩展生态，成为了当下最受 ACM / OI 选手青睐的代码编辑器。本文将教你从零开始，搭建一套适合信息学竞赛的完美 VSCode 配置。

---

## 一、准备工作：下载与安装

整个配置过程分为两部分：**编译器（GCC）** 和 **编辑器（VSCode）**。

### 1. 下载并安装 VSCode
* 官网下载：[https://code.visualstudio.com/Download/](https://code.visualstudio.com/Download/)
* 安装时建议**全选**“添加到上下文菜单”（这样可以在文件夹右键直接用 VSCode 打开）。

### 2. 安装 C++ 编译器 (MinGW-w64)
*Windows 系统本身不带 C++ 编译器，我们需要安装 MinGW-w64。*

* **推荐下载**：在 GitHub 或 WinLibs 下载打包好的 MinGW-w64（推荐 GCC 12 或更高版本，支持最新的 C++ 标准）。

  **下载链接：**[https://github.com/brechtsanders/winlibs_mingw/releases](https://github.com/brechtsanders/winlibs_mingw/releases/)

  > **Windows 10/11 (64位)：** 建议选择 **UCRT runtime** 版本的 **x86_64**
  >
  > 线程模型（Thread model）通常选择默认的 **posix**，异常处理（Exception handling）选择 **seh**

* **配置环境变量**：
  > 下载并放置好编译器后，必须将其 `bin` 路径加入到系统的环境变量中，这样系统才能在任意位置调用 `gcc` 或 `g++` 命令。
  1. 将下载好的 MinGW 压缩包解压到一个**不含中文和空格**的路径（例如 `C:\mingw64`）。
  2. 找到 `mingw64\bin` 目录路径，复制该路径。
  3. 在 Windows 搜索栏中输入 **“环境变量”**，并选择 **“编辑系统环境变量”**（或按 `Win + R` 输入 `sysdm.cpl`，切到 **“高级”** 标签页 -> 点击 **“环境变量”**）。
  4. 找到名为 **`Path`** 的变量，双击打开，点击 **“新建”**，将前面复制的路径粘贴进去。
* **验证安装**：
  1. 按下快捷键 `Win + R`，输入 `cmd` 并回车，打开命令提示符。
  2. 依次输入以下命令并回车：
     ```bash
     gcc --version
     g++ --version
     gdb --version
     ```
  3. 如果正确显示了对应的版本信息，则说明 MinGW-w64 已经成功下载、安装并配置完毕！

> *macOS 用户直接在终端运行 `xcode-select --install` 安装 Clang/GCC*
>
> *Linux 用户直接 `sudo apt install build-essential` 即可。*

---

## 二、VSCode 必备插件安装

打开 VSCode，点击左侧边栏的 **“扩展（Extensions）”图标（快捷键 `Ctrl + Shift + X`）**，搜索并安装以下插件：

1. **Chinese (Simplified) Language Pack**：官方汉化插件。
2. **C/C++**：提供 C++ 语法高亮、智能提示、跳转与调试。
3. **Code Runner**：一键运行单个 C++ 代码文件（必装！）。
4. **Competitive Programming Helper (CPH)**：**竞赛神器！** 自动抓取题解样例，一键对拍测试。

---

## 三、关键运行与编译配置

对于竞赛选手来说，配置过重的 `.vscode` 调试文件并不高效，我们推荐**两种最适合竞赛的运行方案**。

### 方案 A：使用 Code Runner（一键运行 + 自定义编译参数）

Code Runner 可以做到按 `F1` 或点右上角 ▶️ 按钮瞬间编译运行代码。

1. 打开 VSCode 设置（`Ctrl + ,`），搜索 `code-runner.runInTerminal`，勾选 **Run In Terminal**（*必须勾选，否则无法在控制台进行 `cin`/`scanf` 输入*）。
2. 在设置中搜索 `code-runner.executorMap`，点击 **Edit in settings.json**。
3. 找到 `"cpp"` 这一行，将其修改为包含 `-O2` 优化和 `-std=c++14`（或 c++17）的命令：

**Windows 环境设置示例：**
```json
"cpp": "cd $dir && g++ -O2 -std=c++14 $fileName -o $fileNameWithoutExt && .\\$fileNameWithoutExt.exe",
```

**macOS/Linux 环境设置示例：**
```json
"cpp": "cd $dir && g++ -O2 -std=c++14 $fileName -o $fileNameWithoutExt && ./$fileNameWithoutExt",
```
> **竞赛小贴士**：NOIP 目前已全面支持 C++14 标准，并在测评中开启 `-O2` 优化。开启这些参数可以让你在本地体验与赛场一致的编译环境。

---

### 方案 B：使用 CPH (Competitive Programming Helper) 插件（强烈推荐）

CPH 是专门为 CP/OI 选手打造的插件。

1. **如何使用**：
   打开任何 `.cpp` 文件，左侧边栏会多出一个 CPH 图标（一个 Trophy 奖杯或者斜杠图标）。
2. **自动测试样例**：
   你可以手动添加 `Input`（输入样例）和 `Expected Output`（期望输出）。点击 `Run Test Cases`，它会自动运行你的程序并对比结果，显示绿色的 **ACCEPTED** 或红色的 **WRONG ANSWER**，还会精确测量运行毫秒数！
3. **搭配浏览器插件（Web Extension）**：
   在 Chrome/Edge 浏览器安装 **Competitive Companion** 插件。在洛谷、Codeforces、AtCoder 打开题目页面，点一下浏览器插件图标，题目样例会**秒传**到 VSCode 内的 CPH 中，无需手动复制粘贴样例！

---

## 四、信息学竞赛专属高级技巧

### 1. 配置高效的 C++ 头文件与代码模板 (Snippets)

竞赛中每次新建文件都要敲 `#include <bits/stdc++.h>` 和 `int main()` 很费时间。我们可以配置代码片段（Snippets）。

1. 点击左下角齿轮（设置） -> **配置用户代码片段 (User Snippets)** -> 选择 `cpp` 。
2. 粘贴以下模板代码：

```json
{
	"CSP Template": {
		"prefix": "csp",
		"body": [
			"#include <bits/stdc++.h>",
			"using namespace std;",
			"using ll = long long;",
            "",
			"int main() {",
            "    ios::sync_with_stdio(0);cin.tie(0);cout.tie(0);",
            "    $0",
			"    return 0;",
			"}"
		],
		"description": "CSP Example Template"
	}
}
```

**使用方法**：新建 `.cpp` 文件，输入 `csp` 然后按 `Tab` 键，就能瞬间生成整洁的竞赛模板！

---

### 2. 万能头文件 `<bits/stdc++.h>` 找不到补全的问题

如果在 VSCode 中使用 `#include <bits/stdc++.h>` 提示波浪线警告（找不到头文件）：

1. 按 `Ctrl + Shift + P` 输入 `C/C++: Edit Configurations (UI)` 打开配置界面。
2. 在 **Compiler path**（编译器路径）中，确保填入了正确的 `g++.exe` 路径（例如 `C:/mingw64/bin/g++.exe`）。
3. **C++ Standard** 选为 `c++14` 或 `c++17`。
4. VSCode 会自动识别 GCC 内置的 `<bits/stdc++.h>`，波浪线即可消除。

> **如果问题仍无法解决：**
>
> **1. 下载 MinGW-w64 后解压，在文件夹中搜索 `stdc++.h` 并复制文件**
>
> **2. 新建 `.cpp` 文件，输入 `#include<iostream>`**
>
> **3. 按住 `Ctrl` ，单击 `iostream`**
>
> **4. 右键文件标签页，点击 “在文件资源管理器中显示”**
>
> **5. 在弹出的目录中，双击打开 `bits` 文件夹，粘贴文件**

---

### 3. 文件输入输出（`freopen`）与本地调试

在 CSP-J/S 中，要求使用文件读写。在本地编写代码时，建议使用预编译指令做隔离：

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
#ifndef ONLINE_JUDGE
    // 只在本地生效，提交到 OJ 时 ONLINE_JUDGE 宏会被自动定义，无需手动删掉这两行
    freopen("data.in", "r", stdin);
    freopen("data.out", "w", stdout);
#endif

    int a, b;
    cin >> a >> b;
    cout << a + b << "\n";
    return 0;
}
```

---

## 五、常见问题与踩坑指南 (FAQ)

### Q1：控制台中文乱码怎么办？
这是 Windows 常见的 GBK 与 UTF-8 冲突问题。
* **解决方法**：
  点击 VSCode 右下角的 `UTF-8` -> 选择 **通过编码重新打开 (Reopen with Encoding)** -> 选择 `GBK`；
  或者在终端运行命令 `chcp 65001` 将终端临时切换为 UTF-8 编码。

### Q2：使用 Code Runner 运行代码时无法在终端输入？
* **解决方法**：
  进入设置（`Ctrl + ,`），搜索 `code-runner.runInTerminal`，将其**打勾**。

### Q3：比赛赛场没有 VSCode 怎么办？
* 竞赛选手的基本功：虽然平时训练推荐用舒适的 VSCode，但也要熟练掌握赛场常见环境（如 **Dev-C++**, **Code::Blocks** 或 **Vim / Linux 终端命令行** 编译运行 `g++ a.cpp -o a -O2`）。VSCode 用来提效日常刷题，赛场上要做到 **“有啥用啥”**。

---

## 六、总结

通过本文的配置，你已经拥有了一套包含 **高版本 GCC + `-O2` 极速编译 + 自动化样例测试 (CPH) + 一键模版生成** 的现代 C++ 竞赛开发环境。这能帮你省去繁琐的测试流程，把更多精力专注在算法与逻辑本身上。

祝你在 CSP-J/S 和各大赛事中顺利 AC，AK 全场！