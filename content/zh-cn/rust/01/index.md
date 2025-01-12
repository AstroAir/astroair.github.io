---
title: 'Day1 - Rust 安装与入门指南'
---

## 安装 Rust

第一步是安装 Rust。我们将通过 `rustup` 下载 Rust，这是一个用于管理 Rust 版本及相关工具的命令行工具。你需要联网才能下载。

**注意：** 如果出于某些原因你不想使用 `rustup`，请参阅 [Other Rust Installation Methods](https://forge.rust-lang.org/infra/other-installation-methods.html) 页面以获取更多选项。

以下步骤将安装最新稳定版的 Rust 编译器。Rust 的稳定性保证确保本书中所有能够编译的示例在较新的 Rust 版本中仍然可以编译。不同版本之间的输出可能会略有不同，因为 Rust 经常改进错误信息和警告。换句话说，使用这些步骤安装的任何较新的稳定版 Rust 都应该与本书的内容兼容。

### 命令行符号

在本章及全书中，我们将展示一些在终端中使用的命令。你应该在终端中输入的行都以 `$` 开头。你不需要输入 `$` 字符；它是命令行提示符，用于指示每个命令的开始。不以 `$` 开头的行通常显示上一个命令的输出。此外，PowerShell 特定的示例将使用 `>` 而不是 `$`。

### 在 Linux 或 macOS 上安装 rustup

如果你使用的是 Linux 或 macOS，打开终端并输入以下命令：

```bash
curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
```

该命令下载一个脚本并启动 `rustup` 工具的安装，该工具将安装最新稳定版的 Rust。你可能会被提示输入密码。如果安装成功，将出现以下行：

```txt
Rust is installed now. Great!
```

你还需要一个链接器，这是 Rust 用来将其编译输出合并为一个文件的程序。你可能已经有一个链接器。如果你遇到链接器错误，你应该安装一个 C 编译器，它通常会包含一个链接器。C 编译器也很有用，因为一些常见的 Rust 包依赖于 C 代码，因此需要 C 编译器。

在 macOS 上，你可以通过运行以下命令获取 C 编译器：

```bash
xcode-select --install
```

Linux 用户通常应根据其发行版的文档安装 GCC 或 Clang。例如，如果你使用 Ubuntu，可以安装 `build-essential` 包。

### 在 Windows 上安装 rustup

在 Windows 上，访问 [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install) 并按照安装 Rust 的说明进行操作。在安装过程中的某个时刻，你将被提示安装 Visual Studio。这将提供一个链接器和编译程序所需的本地库。如果你需要更多帮助，请参阅 [https://rust-lang.github.io/rustup/installation/windows-msvc.html](https://rust-lang.github.io/rustup/installation/windows-msvc.html)。

本书的其余部分使用在 `cmd.exe` 和 PowerShell 中都有效的命令。如果有特定的差异，我们会解释使用哪个。

### 故障排除

要检查你是否正确安装了 Rust，请打开一个 shell 并输入以下命令：

```bash
rustc --version
```

你应该看到最新稳定版本的版本号、提交哈希和提交日期，格式如下：

```txt
rustc x.y.z (abcabcabc yyyy-mm-dd)
```

如果你看到这些信息，说明你已经成功安装了 Rust！如果你没有看到这些信息，请检查 Rust 是否在你的 `%PATH%` 系统变量中。

在 Windows CMD 中，使用：

```cmd
> echo %PATH%
```

在 PowerShell 中，使用：

```powershell
> echo $env:Path
```

在 Linux 和 macOS 中，使用：

```bash
echo $PATH
```

如果这些都正确，但 Rust 仍然无法工作，你可以在多个地方寻求帮助。了解如何与其他 Rustaceans（我们对自己的一个有趣的昵称）联系，请访问 [社区页面](https://www.rust-lang.org/community)。

### 更新和卸载

通过 `rustup` 安装 Rust 后，更新到新发布的版本非常简单。在你的 shell 中运行以下更新脚本：

```bash
rustup update
```

要卸载 Rust 和 `rustup`，请在 shell 中运行以下卸载脚本：

```bash
rustup self uninstall
```

### 本地文档

Rust 的安装还包括一份本地文档副本，以便你可以离线阅读。运行 `rustup doc` 以在浏览器中打开本地文档。

任何时候，如果你不确定标准库提供的类型或函数的作用或如何使用它，请使用应用程序编程接口（API）文档来查找答案！

---

## 你好，世界

现在你已经安装了 Rust，是时候编写你的第一个 Rust 程序了。学习新语言时，传统上会编写一个在屏幕上打印 `Hello, world!` 的小程序，所以我们在这里也这样做！

**注意：** 本书假设你对命令行有基本的了解。Rust 对你的编辑器、工具或代码存放位置没有特定要求，因此如果你更喜欢使用集成开发环境（IDE）而不是命令行，请随意使用你喜欢的 IDE。许多 IDE 现在都有一定程度的 Rust 支持；请查看 IDE 的文档以获取详细信息。Rust 团队一直致力于通过 `rust-analyzer` 提供出色的 IDE 支持。更多详情请参见附录 D。

### 创建项目目录

首先，你需要创建一个目录来存放你的 Rust 代码。Rust 并不关心你的代码存放在哪里，但为了本书的练习和项目，我们建议在你的主目录下创建一个 `projects` 目录，并将所有项目存放在那里。

打开终端并输入以下命令，以在 `projects` 目录下创建一个 `projects` 目录和一个用于“Hello, world!”项目的目录。

#### 对于 Linux、macOS 和 Windows 上的 PowerShell，请输入以下内容

```bash
mkdir ~/projects
cd ~/projects
mkdir hello_world
cd hello_world
```

#### 对于 Windows CMD，请输入以下内容

```cmd
> mkdir "%USERPROFILE%\projects"
> cd /d "%USERPROFILE%\projects"
> mkdir hello_world
> cd hello_world
```

### 编写并运行 Rust 程序

接下来，创建一个新的源文件并将其命名为 `main.rs`。Rust 文件总是以 `.rs` 扩展名结尾。如果你在文件名中使用多个单词，惯例是使用下划线来分隔它们。例如，使用 `hello_world.rs` 而不是 `helloworld.rs`。

现在打开你刚刚创建的 `main.rs` 文件，并输入清单 1-1 中的代码。

**文件名：** `main.rs`

```rust
fn main() {
    println!("Hello, world!");
}
```

**清单 1-1：** 一个打印 `Hello, world!` 的程序

保存文件并返回到 `~/projects/hello_world` 目录中的终端窗口。在 Linux 或 macOS 上，输入以下命令来编译并运行文件：

```bash
rustc main.rs
./main
Hello, world!
```

在 Windows 上，输入命令 `.\main.exe` 而不是 `./main`：

```cmd
> rustc main.rs
> .\main.exe
Hello, world!
```

无论你的操作系统是什么，字符串 `Hello, world!` 都应该打印到终端。如果你没有看到这个输出，请参考安装部分的“故障排除”部分以获取帮助。

如果 `Hello, world!` 打印出来了，恭喜你！你已经正式编写了一个 Rust 程序。这使你成为了一名 Rust 程序员——欢迎加入！

### Rust 程序的结构

让我们详细回顾一下这个“Hello, world!”程序。以下是第一个关键部分：

```rust
fn main() {
}
```

这些行定义了一个名为 `main` 的函数。`main` 函数是特殊的：它始终是每个可执行 Rust 程序中首先运行的代码。在这里，第一行声明了一个名为 `main` 的函数，它没有参数且不返回任何内容。如果有参数，它们将放在括号 `()` 内。

函数体用 `{}` 包裹。Rust 要求所有函数体都用花括号括起来。将左花括号放在函数声明的同一行，并在两者之间添加一个空格，这是一种良好的风格。

**注意：** 如果你想在 Rust 项目中坚持标准风格，可以使用一个名为 `rustfmt` 的自动格式化工具来以特定风格格式化你的代码（更多关于 `rustfmt` 的内容请参见附录 D）。Rust 团队已经将这个工具包含在标准 Rust 发行版中，就像 `rustc` 一样，所以它应该已经安装在你的电脑上了！

`main` 函数的主体包含以下代码：

```rust
    println!("Hello, world!");
```

这行代码在这个小程序中完成了所有工作：它将文本打印到屏幕上。这里有四个重要的细节需要注意。

1. **缩进：** Rust 风格是使用四个空格进行缩进，而不是制表符。
2. **宏调用：** `println!` 调用了一个 Rust 宏。如果它调用的是一个函数，那么它将输入为 `println`（没有 `!`）。我们将在第 19 章详细讨论 Rust 宏。现在，你只需要知道使用 `!` 意味着你正在调用一个宏而不是普通函数，并且宏并不总是遵循与函数相同的规则。
3. **字符串参数：** 你看到了 `"Hello, world!"` 字符串。我们将这个字符串作为参数传递给 `println!`，然后字符串被打印到屏幕上。
4. **分号：** 我们以分号（`;`）结束这行代码，表示这个表达式结束，下一个表达式准备开始。大多数 Rust 代码行都以分号结尾。

### 编译和运行是分开的步骤

你刚刚运行了一个新创建的程序，让我们来检查一下这个过程。

在运行 Rust 程序之前，你必须使用 Rust 编译器通过输入 `rustc` 命令并传递源文件的名称来编译它，如下所示：

```bash
rustc main.rs
```

如果你有 C 或 C++ 背景，你会注意到这与 `gcc` 或 `clang` 类似。成功编译后，Rust 会输出一个二进制可执行文件。

在 Linux、macOS 和 Windows 上的 PowerShell 中，你可以通过在 shell 中输入 `ls` 命令来查看可执行文件：

```bash
ls
main  main.rs
```

在 Linux 和 macOS 上，你会看到两个文件。在 Windows 上的 PowerShell 中，你会看到与使用 CMD 时相同的三个文件。在 Windows 上的 CMD 中，你将输入以下内容：

```cmd
> dir /B %= /B 选项表示只显示文件名 =%
main.exe
main.pdb
main.rs
```

这将显示带有 `.rs` 扩展名的源代码文件、可执行文件（在 Windows 上是 `main.exe`，但在其他平台上为 `main`），以及在使用 Windows 时包含调试信息的 `.pdb` 扩展名的文件。从这里，你可以运行 `main` 或 `main.exe` 文件，如下所示：

```bash
./main # 或在 Windows 上为 .\main.exe
```

如果你的 `main.rs` 是“Hello, world!”程序，这行代码将 `Hello, world!` 打印到你的终端。

如果你更熟悉动态语言，如 Ruby、Python 或 JavaScript，你可能不习惯将编译和运行程序作为分开的步骤。Rust 是一种提前编译的语言，这意味着你可以编译一个程序并将可执行文件交给其他人，他们可以在没有安装 Rust 的情况下运行它。如果你给某人一个 `.rb`、`.py` 或 `.js` 文件，他们需要安装 Ruby、Python 或 JavaScript 实现（分别）。但在这些语言中，你只需要一个命令来编译和运行你的程序。语言设计中的一切都是权衡。

对于简单的程序来说，仅使用 `rustc` 编译是可以的，但随着项目的增长，你将需要管理所有选项并使其易于共享代码。接下来，我们将向你介绍 Cargo 工具，它将帮助你编写实际的 Rust 程序。

---

## 你好，Cargo

Cargo 是 Rust 的构建系统和包管理器。大多数 Rust 开发者使用这个工具来管理他们的 Rust 项目，因为 Cargo 为你处理了许多任务，比如构建代码、下载代码依赖的库以及构建这些库。（我们将代码所需的库称为依赖项。）

最简单的 Rust 程序，比如我们目前编写的这个，没有任何依赖项。如果我们使用 Cargo 构建“Hello, world!”项目，它只会使用 Cargo 中处理代码构建的部分。随着你编写更复杂的 Rust 程序，你会添加依赖项，而如果你使用 Cargo 启动项目，添加依赖项会变得容易得多。

因为绝大多数 Rust 项目都使用 Cargo，本书的其余部分也假设你正在使用 Cargo。如果你使用了“安装”部分中讨论的官方安装程序，Cargo 会随 Rust 一起安装。如果你通过其他方式安装了 Rust，可以通过在终端中输入以下内容来检查是否安装了 Cargo：

```bash
cargo --version
```

如果你看到一个版本号，说明你已经安装了 Cargo！如果你看到一个错误，比如 `command not found`，请查看你安装方法的文档，以确定如何单独安装 Cargo。

### 使用 Cargo 创建项目

让我们使用 Cargo 创建一个新项目，并看看它与我们最初的“Hello, world!”项目有何不同。导航回你的 `projects` 目录（或你决定存放代码的任何地方）。然后，在任何操作系统上运行以下命令：

```bash
cargo new hello_cargo
cd hello_cargo
```

第一个命令创建了一个名为 `hello_cargo` 的新目录和项目。我们将项目命名为 `hello_cargo`，Cargo 会在同名目录中创建其文件。

进入 `hello_cargo` 目录并列出文件。你会看到 Cargo 为我们生成了两个文件和一个目录：一个 `Cargo.toml` 文件和一个包含 `main.rs` 文件的 `src` 目录。

它还初始化了一个新的 Git 仓库，并附带了一个 `.gitignore` 文件。如果你在现有的 Git 仓库中运行 `cargo new`，则不会生成 Git 文件；你可以通过使用 `cargo new --vcs=git` 来覆盖此行为。

**注意：** Git 是一个常见的版本控制系统。你可以通过使用 `--vcs` 标志来更改 `cargo new` 以使用不同的版本控制系统或不使用版本控制系统。运行 `cargo new --help` 以查看可用的选项。

在你选择的文本编辑器中打开 `Cargo.toml`。它应该类似于清单 1-2 中的代码。

**文件名：** `Cargo.toml`

```toml
[package]
name = "hello_cargo"
version = "0.1.0"
edition = "2021"

# 更多键及其定义请参见 https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

**清单 1-2：** 由 `cargo new` 生成的 `Cargo.toml` 内容

这个文件采用 TOML（Tom's Obvious, Minimal Language）格式，这是 Cargo 的配置格式。

第一行 `[package]` 是一个部分标题，表示以下语句正在配置一个包。随着我们向该文件添加更多信息，我们将添加其他部分。

接下来的三行设置了 Cargo 编译程序所需的配置信息：名称、版本和使用的 Rust 版本。我们将在附录 E 中讨论 `edition` 键。

最后一行 `[dependencies]` 是一个部分的开始，用于列出项目的任何依赖项。在 Rust 中，代码包被称为 crate。我们在这个项目中不需要其他 crate，但在第 2 章的第一个项目中会需要，因此我们将在那时使用这个 `dependencies` 部分。

现在打开 `src/main.rs` 并查看：

**文件名：** `src/main.rs`

```rust
fn main() {
    println!("Hello, world!");
}
```

Cargo 为你生成了一个“Hello, world!”程序，就像我们在清单 1-1 中编写的那个一样！到目前为止，我们的项目与 Cargo 生成的项目之间的区别在于，Cargo 将代码放在了 `src` 目录中，并且我们在顶层目录中有一个 `Cargo.toml` 配置文件。

Cargo 期望你的源文件位于 `src` 目录中。顶级项目目录仅用于 README 文件、许可证信息、配置文件以及其他与代码无关的内容。使用 Cargo 有助于你组织项目。每样东西都有其位置，每样东西都在其位置上。

如果你启动了一个不使用 Cargo 的项目，就像我们使用“Hello, world!”项目那样，你可以将其转换为使用 Cargo 的项目。将项目代码移动到 `src` 目录中，并创建一个适当的 `Cargo.toml` 文件。获取该 `Cargo.toml` 文件的一种简单方法是运行 `cargo init`，它会自动为你创建该文件。

### 构建和运行 Cargo 项目

现在让我们看看使用 Cargo 构建和运行“Hello, world!”程序时有什么不同！在你的 `hello_cargo` 目录中，通过输入以下命令来构建你的项目：

```bash
cargo build
   Compiling hello_cargo v0.1.0 (file:///projects/hello_cargo)
    Finished dev [unoptimized + debuginfo] target(s) in 2.85 secs
```

此命令在 `target/debug/hello_cargo`（或在 Windows 上为 `target\debug\hello_cargo.exe`）中创建一个可执行文件，而不是在当前目录中。因为默认构建是调试构建，Cargo 将二进制文件放在名为 `debug` 的目录中。你可以使用以下命令运行可执行文件：

```bash
./target/debug/hello_cargo # 或在 Windows 上为 .\target\debug\hello_cargo.exe
Hello, world!
```

如果一切顺利，`Hello, world!` 应该会打印到终端。首次运行 `cargo build` 还会导致 Cargo 在顶层创建一个新文件：`Cargo.lock`。此文件跟踪项目中依赖项的确切版本
