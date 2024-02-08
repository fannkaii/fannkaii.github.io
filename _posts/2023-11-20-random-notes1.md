---
layout: post
title: "随手记1"
author: "Kai"
header-style: text
tags:
  - 随手记
---

- [macOS 上 的Bundler](#macos-上-的bundler)
- [图床](#图床)
- [里氏替换与多态的相同点和不同点？](#里氏替换与多态的相同点和不同点)
  - [相同点：](#相同点)
  - [不同点：](#不同点)
- [Ruby](#ruby)
- [brew是什么命令](#brew是什么命令)
- [curl 命令](#curl-命令)
- [在终端里能用命令看到当前代理吗](#在终端里能用命令看到当前代理吗)
- [如何用代理启动终端](#如何用代理启动终端)
  - [1. 在终端中直接设置代理：](#1-在终端中直接设置代理)
  - [2. 启动终端：](#2-启动终端)
  - [3. 验证代理是否生效：](#3-验证代理是否生效)
- [keg-only是什么意思](#keg-only是什么意思)
- [如何看keg-only的ruby的版本](#如何看keg-only的ruby的版本)
- [如何让keg-only的ruby替代系统正在使用的ruby](#如何让keg-only的ruby替代系统正在使用的ruby)
- [如何查看当前max os的环境变量](#如何查看当前max-os的环境变量)
- [为啥关闭终端，再打开，刚才export的东西都没了](#为啥关闭终端再打开刚才export的东西都没了)
- [能否用文本编辑器打开bash\_profile](#能否用文本编辑器打开bash_profile)
- [在终端里如何更新bundle](#在终端里如何更新bundle)
- [bundle自身需要更新吗](#bundle自身需要更新吗)
- [在终端里安装完jekyll后，输入jekyll命令，提示command not found](#在终端里安装完jekyll后输入jekyll命令提示command-not-found)
- [如何用在终端执行一个命令，在访达打开命令里的路径](#如何用在终端执行一个命令在访达打开命令里的路径)
- [source 'https://gems.ruby-china.com'是什么意思](#source-httpsgemsruby-chinacom是什么意思)
- [执行后，怎么看设置的source](#执行后怎么看设置的source)
- [安装的gem包都存储在哪里 maxos](#安装的gem包都存储在哪里-maxos)



## macOS 上 的Bundler

在 macOS 上，Bundler 是一个 Ruby 程序，用于管理 Ruby 项目的依赖关系。它通过一个名为 `Gemfile` 的配置文件来定义项目的依赖项，并确保这些依赖项的版本是一致的，以避免潜在的兼容性问题。

以下是 Bundler 的一些主要功能：

1. **Gemfile 定义：** 通过在项目根目录下创建一个名为 `Gemfile` 的文件，你可以列出项目所需的 Ruby gems，并指定它们的版本号或版本范围。

2. **依赖解析：** Bundler 分析 Gemfile 中定义的依赖关系，并确保安装的 gems 的版本是一致的。这有助于避免在不同环境中出现不同版本的 gems 导致的问题。

3. **安装依赖：** 运行 `bundle install` 命令会根据 Gemfile 安装所需的 gems。这些 gems 通常存储在项目的 `vendor` 目录中，而不是系统范围的 gem 安装路径中。

4. **依赖环境隔离：** Bundler 提供了一种将项目的依赖项与系统范围的 Ruby gems 隔离开来的方法。这意味着每个项目都可以有自己的 gem 环境，而不会影响其他项目。

5. **更新依赖：** 运行 `bundle update` 命令可以更新 Gemfile 中指定的 gems 到最新兼容的版本。

要在 macOS 上安装 Bundler，你可以使用以下命令：

```bash
gem install bundler
```
然后，你可以在项目目录中创建一个 Gemfile 文件，并运行 `bundle install` 来安装项目的依赖项。

## 图床

图床（Image hosting service），也称为图片托管服务，是一种在线服务，允许用户上传、存储和分享图像。通常，用户可以将图像上传到图床，然后获取一个链接，通过该链接可以在网页、论坛、社交媒体等地方分享图像。这种服务对于在互联网上分享图片很有用，特别是在需要在不同平台上发布图片时。

图床有一些常见的用途：

1. **在论坛或博客中插入图片：** 论坛和博客通常限制了直接上传大型图片的大小，因此可以使用图床来存储并在帖子中插入图片。

2. **在社交媒体上分享图片：** 图床可以用于存储并分享在社交媒体平台上发布的图片，而不受限于平台对图片大小和格式的限制。

3. **网站和应用程序开发：** 在开发网站或应用程序时，有时需要在页面上显示图片。图床可以作为一个临时或永久的存储解决方案，确保图片可以在需要时被访问。

4. **减轻网站负担：** 将图片存储在图床上可以减轻网站服务器的负担，因为图片可以从外部服务加载，而不占用网站服务器的带宽和存储空间。

图床服务通常提供免费和付费的存储选项，用户可以根据其需求选择适当的服务。流行的图床服务包括Imgur、Flickr、Google Photos等。注意，使用图床时要确保遵守相应服务的使用规定和法律法规。

## 里氏替换与多态的相同点和不同点？
里氏替换原则（Liskov Substitution Principle，LSP）和多态性（Polymorphism）是面向对象编程中的两个重要概念，它们有一些相似之处，但也存在一些不同之处。

### 相同点：

1. **关联性：** 里氏替换原则和多态性都与继承有关，是面向对象编程中的基本概念之一。

2. **继承：** 两者都依赖于继承。在实现里氏替换原则时，子类必须能够替代其父类，而多态性通过继承和方法重写实现。

3. **增加灵活性：** 两者都有助于代码的灵活性。通过继承和多态，可以更容易地扩展和维护代码。

### 不同点：

1. **定义：**
   - **里氏替换原则：** 由巴巴拉·利斯科夫（Barbara Liskov）在1987年提出，强调子类型必须能够替代基类型，即在任何使用基类型的地方，都应该能够使用子类型而不引起错误。
   - **多态性：** 多态性是一种能够以不同的方式呈现相同的接口或方法的能力。这包括方法重载和方法重写。

2. **关注点：**
   - **里氏替换原则：** 强调的是类之间的继承关系，侧重于子类的行为能否完全替代父类。
   - **多态性：** 侧重于对象的多种形态，即一个对象可以以多种形式存在，可以根据上下文表现出不同的行为。

3. **具体性：**
   - **里氏替换原则：** 是一种设计原则，关注的是类的结构和行为，侧重于类的设计。
   - **多态性：** 是一种编程概念，强调同一接口可以有多种实现，侧重于代码的实际运行时表现。

总的来说，里氏替换原则是一种更广泛的设计原则，而多态性则是一种编程实现的概念。里氏替换原则强调的是类的继承关系和结构，而多态性强调的是对象的多态表现和代码的动态行为。

## Ruby
Ruby 是一种简单而强大的面向对象的编程语言。它具有清晰的语法和灵活性，广泛应用于 Web 开发、脚本编程、自动化任务等领域。以下是一些关于 Ruby 的基本信息：

1. **创始人：** Ruby 由日本程序员松本行弘（Yukihiro Matsumoto）于1995年首次发布。Matz，作为他的绰号，设计 Ruby 时的目标是创造一种既具有实用性又令程序员愉悦的编程语言。

2. **特点：**
   - **面向对象：** Ruby 是一种纯粹的面向对象编程语言，一切皆为对象，包括基本数据类型。
   - **动态类型：** 变量的类型是动态确定的，你不需要在声明变量时指定类型。
   - **自动内存管理：** Ruby 使用垃圾收集机制进行内存管理，开发者无需手动管理内存。
   - **闭包和块：** Ruby 支持闭包和块，这使得它在编写灵活的、可读性强的代码方面很有优势。
   - **元编程：** Ruby 是一种具有强大元编程（metaprogramming）能力的语言，允许程序在运行时修改自身的结构。

3. **语法：** Ruby 的语法简洁而灵活，强调代码的可读性。它采用面向表达式的风格，可以使代码更具表现力和自然。

4. **库和框架：** Ruby 社区拥有丰富的库和框架，其中最著名的是 Ruby on Rails，一个用于构建 Web 应用程序的 MVC 框架。此外，RubyGems 是 Ruby 的包管理器，用于安装和管理 Ruby 的软件包。

5. **用途：** Ruby 在 Web 开发中得到了广泛的应用，特别是在构建动态、灵活的网站和应用程序方面。Ruby on Rails作为一个流行的Web框架，大大简化了Web应用的开发。此外，Ruby 也用于脚本编程、自动化任务、数据分析等领域。

6. **示例代码：**
   ```ruby
   # Hello World 示例
   puts "Hello, World!"

   # 定义类和对象
   class Person
     attr_accessor :name

     def initialize(name)
       @name = name
     end

     def say_hello
       puts "Hello, #{@name}!"
     end
   end

   person = Person.new("Alice")
   person.say_hello
   ```

总的来说，Ruby 是一门强大而富有表达力的编程语言，它注重简洁、灵活和开发者友好的设计，使其成为许多开发者喜欢使用的语言之一。

## brew是什么命令
`brew` 是 Homebrew 包管理器的命令行工具。Homebrew 是 macOS 上的一个包管理系统，它允许用户从命令行轻松安装、升级和管理软件包。

以下是一些常用的 `brew` 命令：

1. **安装软件包：**
   ```bash
   brew install <package_name>
   ```
   用于安装指定的软件包。

2. **查看已安装的软件包：**
   ```bash
   brew list
   ```
   显示当前已经安装的软件包列表。

3. **查看可用的软件包：**
   ```bash
   brew search <search_term>
   ```
   搜索可用的软件包。

4. **更新 Homebrew 及已安装的软件包：**
   ```bash
   brew update
   brew upgrade
   ```
   `brew update` 用于更新 Homebrew 本身，`brew upgrade` 用于升级已安装的软件包。

5. **显示已安装软件包的信息：**
   ```bash
   brew info <package_name>
   ```
   显示有关指定软件包的信息。

6. **删除已安装的软件包：**
   ```bash
   brew uninstall <package_name>
   ```
   用于卸载已安装的软件包。

7. **查看已安装软件包的依赖关系：**
   ```bash
   brew deps <package_name>
   ```
   显示指定软件包的依赖关系。

8. **清理不需要的文件：**
   ```bash
   brew cleanup
   ```
   清理 Homebrew 缓存和不再需要的文件。

9. **显示 Homebrew 帮助：**
   ```bash
   brew help
   ```
   显示有关 Homebrew 命令的帮助信息。

这些命令是 Homebrew 提供的一些基本功能，使得在 macOS 上管理软件包变得更加方便。

## curl 命令
在 macOS 终端中，你可以使用 `curl` 命令来查看一个 HTTPS 地址的内容。以下是使用 `curl` 的基本语法：

```bash
curl https://example.com
```

这会向指定的 HTTPS 地址发送一个 GET 请求，并将服务器的响应打印到终端。

如果你只是想查看内容而不保存到文件，上述命令足够了。如果你想将内容保存到文件，你可以使用 `-o` 选项，如下所示：

```bash
curl -o output.html https://example.com
```

这将把服务器的响应保存到 `output.html` 文件中。

如果你需要查看详细的 HTTP 请求和响应头信息，你可以使用 `-v` 选项：

```bash
curl -v https://example.com
```

请注意，使用 `curl` 命令需要你的系统上安装了该工具。在 macOS 上，`curl` 通常是预安装的，但如果你发现没有安装，你可以通过运行 `man curl` 来查看 `curl` 的手册页面，或者通过运行以下命令来安装：

```bash
brew install curl
```

这会使用 Homebrew 包管理器来安装 `curl`。

## 在终端里能用命令看到当前代理吗
在 macOS 终端中，你可以通过查看环境变量来确定当前系统代理的设置。系统代理的环境变量通常是 `http_proxy` 和 `https_proxy`。以下是在终端中查看这些变量的方法：

```bash
echo $http_proxy
echo $https_proxy
```

如果代理已设置，上述命令将显示代理服务器的地址和端口号。如果这两个环境变量的值为空，表示没有设置代理。

另外，你还可以使用 `networksetup` 命令来查看当前网络设置，包括代理设置。以下是一个示例：

```bash
networksetup -getwebproxy Wi-Fi
networksetup -getsecurewebproxy Wi-Fi
```

上述命令会显示 Wi-Fi 连接的代理设置，你可以将 `Wi-Fi` 替换为你当前连接的网络服务名称，例如 `Ethernet`。如果代理已设置，这两个命令将显示代理服务器的地址和端口号。

请注意，这些命令可能会因网络连接类型和系统设置而有所不同。

## 如何用代理启动终端
如果你想在终端中使用代理，你可以在启动终端时设置环境变量来指定代理。以下是一种使用代理启动终端的方法：

### 1. 在终端中直接设置代理：

在终端中运行以下命令，将代理服务器地址和端口号替换为你实际使用的代理信息：

```bash
export http_proxy=http://proxy.server.address:proxy_port
export https_proxy=http://proxy.server.address:proxy_port
```

### 2. 启动终端：

```bash
open -a Terminal
```

这将启动一个新的终端窗口，并且该终端窗口将使用你设置的代理。在这个终端窗口中，任何使用网络的命令，如 `curl`、`wget` 或者其他需要网络连接的命令，都会走通过设置的代理。

### 3. 验证代理是否生效：

在新的终端窗口中运行以下命令：

```bash
echo $http_proxy
echo $https_proxy
```

这将显示当前终端窗口中的 http 和 https 代理设置。

请注意，这种设置仅在当前终端窗口中生效，如果你打开新的终端窗口，它们将不会继承这些代理设置。如果你想在系统范围内设置代理，可以考虑在配置文件中永久设置这些环境变量，或者使用其他代理工具如 `clash`、`v2ray` 等。


## keg-only是什么意思
在 Homebrew 中，"keg-only" 表示一个软件包的安装目录（也称为 "keg"）不会被加入系统的默认路径中，而是被保留在软件包自己的目录中。这通常是因为软件包与系统中已经存在的类似工具或库有冲突，而 Homebrew 为了避免系统混乱，将这个软件包安装到了一个独立的目录中。

当你通过 Homebrew 安装一个 keg-only 软件包时，Homebrew 会输出警告信息，指示该软件包是 keg-only 的。例如：

```bash
Warning: [Formula] is keg-only and must be linked with `brew link [Formula]`
```

这表示你需要手动执行 `brew link [Formula]` 命令来链接这个软件包，将其可执行文件添加到系统的 `PATH` 中。但在进行这样的链接时，需要注意潜在的冲突，因为这可能会影响系统中其他软件包的正常运行。

这种安排通常用于避免与系统预装的工具或库发生冲突，或者是因为软件包有一些特殊的需求，需要自行管理它的可执行文件、库文件等。

## 如何看keg-only的ruby的版本
在 Homebrew 安装的 Ruby 中，通常 Homebrew 会安装一个 keg-only 版本，而不是使用系统预装的 Ruby。你可以使用以下命令来查看 keg-only 版本的 Ruby：

```bash
brew info ruby
```

在输出中，你会看到类似于以下的信息：

```bash
ruby: stable 3.x.x (bottled), HEAD
Interpreted object-oriented scripting language
https://www.ruby-lang.org/
/usr/local/Cellar/ruby/3.x.x (4,508 files, 22MB) *
  Poured from bottle on 2022-01-01 at 12:00:00
From: https://github.com/Homebrew/homebrew-core/blob/main/Formula/ruby.rb
```

在这个例子中，`/usr/local/Cellar/ruby/3.x.x` 是 keg-only Ruby 的安装路径。你可以进入该路径来查看 Ruby 的详细信息，例如：

```bash
ls /usr/local/Cellar/ruby/3.x.x
```

你也可以使用以下命令查看该版本的 Ruby：

```bash
/usr/local/Cellar/ruby/3.x.x/bin/ruby -v
```

请注意，具体路径和版本号可能会因你的实际安装情况而有所不同，所以请替换成你自己的路径和版本号。

## 如何让keg-only的ruby替代系统正在使用的ruby
如果你想让 Homebrew 安装的 keg-only 版本的 Ruby 替代系统默认安装的 Ruby，你需要调整环境变量 `PATH`，以便将 Homebrew 安装的 Ruby 的可执行文件路径添加到前面。

以下是一些步骤，你可以尝试进行这个设置：

1. **查找 keg-only Ruby 的路径：**
   使用以下命令查看 keg-only Ruby 的安装路径：

   ```bash
   brew info ruby
   ```

   输出中包含了安装路径，类似于 `/usr/local/Cellar/ruby/3.x.x`。

2. **更新环境变量 `PATH`：**
   将 keg-only Ruby 的可执行文件路径添加到环境变量 `PATH` 中。在你的 shell 配置文件中（例如 `.bashrc`, `.zshrc` 等），添加类似以下的行：

   ```bash
   export PATH="/usr/local/Cellar/ruby/3.x.x/bin:$PATH"
   ```

   请确保将 `/usr/local/Cellar/ruby/3.x.x/bin` 替换为你实际的 keg-only Ruby 安装路径。

3. **重启终端或重新加载配置文件：**
   使配置文件生效，可以通过重新启动终端或者运行以下命令重新加载配置文件：

   ```bash
   source ~/.bashrc   # 或者 .zshrc, .bash_profile 等，根据你的 shell 配置文件来替换
   ```

   如果一切设置正确，现在终端中运行 `ruby -v` 应该显示 keg-only Ruby 的版本。

请注意，这种修改可能会影响到你系统中其他依赖于系统默认 Ruby 版本的工具或应用程序，因此请确保你明白这个修改可能带来的潜在影响。

## 如何查看当前max os的环境变量
在 macOS 终端中，你可以使用 `echo` 命令查看当前环境变量的值。下面是一些常见的用法：

1. **查看所有环境变量：**

   ```bash
   env
   ```

   这会列出当前终端会话中的所有环境变量及其值。

2. **查看特定环境变量的值：**

   ```bash
   echo $VARIABLE_NAME
   ```

   将 `VARIABLE_NAME` 替换为你要查看的具体环境变量的名称，例如：

   ```bash
   echo $PATH
   ```

   上述命令将显示当前的 `PATH` 环境变量的值。

3. **查看所有环境变量的值（详细格式）：**

   ```bash
   set
   ```

   这个命令将显示详细的环境变量信息，包括各个变量的名称和值。

如果你想了解某个特定的系统环境变量（例如 `PATH`）的默认值，你可以查看相关的系统配置文件。例如，`PATH` 的默认设置通常在 `~/.bash_profile`、`~/.zshrc` 或 `/etc/profile` 等文件中进行。

```bash
cat ~/.bash_profile   # 或者 .zshrc, /etc/profile，根据你的 shell 配置文件来替换
```

这样你就可以查看在新的终端会话中默认加载的环境变量配置。

## 为啥关闭终端，再打开，刚才export的东西都没了
当你在终端中执行 `export` 命令设置环境变量时，这个变量仅在当前终端会话中生效。一旦你关闭终端窗口，该终端会话结束，环境变量也就失效了。

要使环境变量在系统中的所有终端会话中持久生效，你需要将这些设置添加到你的 shell 配置文件中。对于 Bash，这可能是 `~/.bashrc` 或 `~/.bash_profile`；对于 Zsh，可能是 `~/.zshrc`。你可以使用文本编辑器打开相应的文件，然后在文件中添加你的 `export` 语句。

例如，在 Bash 中，你可以编辑 `~/.bashrc` 文件：

```bash
nano ~/.bashrc
```

然后在文件的末尾添加你的 `export` 语句：

```bash
export MY_VARIABLE="some_value"
```

保存并关闭文件，然后在终端中运行以下命令使配置文件生效：

```bash
source ~/.bashrc
```

或者你可以注销并重新登录，新的设置将在下次登录时生效。

确保你添加的配置语句符合 shell 的语法要求，并注意不同的 shell 使用不同的配置文件。

在 macOS 中，通常默认使用的是 Bash shell。因此，你可以编辑 `~/.bash_profile` 文件来设置环境变量，并使其在终端会话中持久生效。如果该文件不存在，你可以创建它。

1. 打开或创建 `~/.bash_profile` 文件：

    ```bash
    nano ~/.bash_profile
    ```

2. 在文件中添加你的 `export` 语句，例如：

    ```bash
    export MY_VARIABLE="some_value"
    ```

3. 保存并关闭文件。

4. 使配置文件生效：

    ```bash
    source ~/.bash_profile
    ```

或者你可以注销并重新登录，新的设置将在下次登录时生效。

请注意，如果你使用的是 Zsh shell，你应该编辑 `~/.zshrc` 文件，而不是 `~/.bash_profile`。 macOS Catalina 和更新版本默认使用 Zsh 作为默认 shell。

## 能否用文本编辑器打开bash_profile
是的，你可以使用文本编辑器打开 `~/.bash_profile` 文件。在 macOS 中，你可以使用命令行下的文本编辑器（如 `nano`、`vim`）或者图形界面下的文本编辑器（如 TextEdit）来进行编辑。

使用 `nano` 编辑器的例子：

```bash
nano ~/.bash_profile
```

这将打开 `~/.bash_profile` 文件以进行编辑。在 `nano` 中，你可以使用箭头键移动光标，进行编辑，然后按下 `Ctrl` + `X`，按照提示保存并退出。

如果你想使用 macOS 图形界面中的 TextEdit 编辑器，可以使用以下命令：

```bash
open -e ~/.bash_profile
```

这将使用 TextEdit 打开 `~/.bash_profile` 文件。编辑完成后，保存并关闭文本编辑器即可。

无论你选择使用命令行还是图形界面的文本编辑器，都可以方便地编辑 `~/.bash_profile` 文件。

## 在终端里如何更新bundle
在终端中，你可以使用 `bundle` 命令来更新你项目中的 Ruby gems。以下是一些常用的 `bundle` 命令来更新不同的依赖：

1. **更新 Gemfile.lock 中的所有依赖项（推荐）：**

    ```bash
    bundle update
    ```

    这将更新 `Gemfile.lock` 中的所有依赖项，并确保它们满足 Gemfile 中指定的版本要求。

2. **更新指定 gem：**

    ```bash
    bundle update <gem_name>
    ```

    将 `<gem_name>` 替换为你要更新的具体 gem 的名称。

3. **安装所有依赖项的最新版本：**

    ```bash
    bundle install
    ```

    这将根据 Gemfile.lock 安装所有依赖项的最新版本。

确保在执行这些命令之前，你在项目目录中打开了终端，并且已经安装了 Bundler。如果没有安装 Bundler，你可以使用以下命令安装：

```bash
gem install bundler
```

然后，切换到包含 `Gemfile` 的项目目录，并使用上述 `bundle` 命令更新依赖项。

## bundle自身需要更新吗
是的，Bundler 本身也会时常更新。你可以使用以下命令来更新 Bundler：

```bash
gem update bundler
```

这将会检查 RubyGems 仓库以获取 Bundler 的最新版本，并将其安装在你的系统中。

如果你遇到权限问题，可能需要在前面加上 `sudo`：

```bash
sudo gem update bundler
```

然后输入管理员密码以完成更新。请注意，在使用 `sudo` 时要格外小心，确保你知道你正在做什么，因为它涉及到系统级别的更改。

在更新 Bundler 后，你可以继续使用 `bundle update` 命令更新项目中的 gem 依赖。

## 在终端里安装完jekyll后，输入jekyll命令，提示command not found
如果你在终端中安装完 Jekyll 后，执行 `jekyll` 命令时提示 "command not found"，有几个可能的原因：

1. **Jekyll 安装路径未包含在 PATH 环境变量中：** 当你安装 Jekyll 时，它会被安装到一个特定的路径，你需要确保这个路径被包含在系统的 PATH 环境变量中。

   你可以尝试使用以下命令检查是否已正确设置 PATH：

   ```bash
   echo $PATH
   ```

   如果 Jekyll 的安装路径没有包含在其中，你需要将其添加到 PATH。

2. **Jekyll 安装路径：** 默认情况下，Jekyll 通常会被安装在 Ruby 的 gem 路径中。你可以通过以下命令查找 Jekyll 的安装路径：

   ```bash
   gem environment
   ```

   在输出中查找 "EXECUTABLE DIRECTORY" 一行，这就是 gem 可执行文件的存储路径。确保这个路径被包含在 PATH 中。

3. **尝试重新安装 Jekyll：** 有时候，重新安装 Jekyll 也能解决问题。可以使用以下命令：

   ```bash
   gem uninstall jekyll
   gem install jekyll
   ```

   请注意，如果你使用了 bundler，确保你在正确的 gemset 或者环境中进行操作。

4. **系统版本问题：** 如果你使用的是 macOS Catalina 或更高版本，由于系统默认使用了 Zsh shell，你可能需要将 gem 的执行路径添加到 Zsh 的 `$PATH` 中。编辑你的 `~/.zshrc` 文件，添加类似以下的行：

   ```bash
   export PATH=$PATH:/path/to/your/gem/bin
   ```

   请将 `/path/to/your/gem/bin` 替换为实际的 gem 执行路径。

5. **检查 Jekyll 是否正确安装：** 确保 Jekyll 成功安装，你可以运行以下命令检查 Jekyll 的版本：

   ```bash
   jekyll -v
   ```

   如果成功安装，这应该会显示 Jekyll 的版本号。

通过这些步骤，你应该能够解决 "command not found" 的问题。如果还有问题，请检查终端输出，看是否有任何错误消息。

## 如何用在终端执行一个命令，在访达打开命令里的路径
你可以使用 `open` 命令在终端中打开 Finder（访达），并定位到指定的路径。以下是具体的命令：

```bash
open /path/to/your/folder
```

将 `/path/to/your/folder` 替换为你想要打开的文件夹的实际路径。这将在 Finder 中打开指定的文件夹。

例如，如果要打开当前目录，可以使用：

```bash
open .
```

或者，如果要打开 `/Users/YourUsername/Documents` 文件夹，可以使用：

```bash
open /Users/YourUsername/Documents
```

这会在 Finder 中打开指定的文件夹，并让你可以直观地查看其中的内容。

## source 'https://gems.ruby-china.com'是什么意思
`source 'https://gems.ruby-china.com'` 是一个 RubyGems 的源（source）配置语句，它告诉 RubyGems 在寻找和安装 Ruby gems（宝石）时使用特定的源。在这个例子中，源被设置为 Ruby China 的 gem 镜像地址。

具体而言：

- `source` 关键字用于指定 RubyGems 的源。
- `'https://gems.ruby-china.com'` 是 RubyGems 的镜像地址。镜像是一个包含 Ruby gems 的服务器，它提供了一个替代的下载源，加快了 gem 的下载速度。

在这个例子中，`https://gems.ruby-china.com` 是 Ruby China 提供的一个 RubyGems 镜像地址。使用镜像可以有效提高 gem 下载的速度，尤其是在某些地区访问原始源较慢的情况下。

你可以根据你的地理位置或网络状况选择使用合适的 gem 镜像。其他一些常用的 gem 镜像包括 `https://rubygems.org`（默认源），以及其他一些全球性的源。

## 执行后，怎么看设置的source
在 RubyGems 中，你可以使用以下命令来查看当前 gem 源的设置：

```bash
gem sources
```

这将列出当前配置的所有 gem 源。如果你之前使用了 `source 'https://gems.ruby-china.com'` 来配置源，那么在执行 `gem sources` 后，你应该会看到输出中包含 `https://gems.ruby-china.com`。

如果你想添加、删除或切换 gem 源，可以使用以下命令：

- **添加一个新的 gem 源：**

  ```bash
  gem sources --add https://new-gem-source.com
  ```

- **删除一个 gem 源：**

  ```bash
  gem sources --remove https://old-gem-source.com
  ```

- **切换到特定的 gem 源：**

  ```bash
  gem sources --clear-all
  gem sources --add https://chosen-gem-source.com
  ```

请记得替换上述命令中的 `https://new-gem-source.com`、`https://old-gem-source.com` 和 `https://chosen-gem-source.com` 为你实际要使用的 gem 源。

## 安装的gem包都存储在哪里 maxos
在 macOS 系统上，通过 Ruby 的包管理器（Gem）安装的 gem 包通常会被存储在用户的 home 目录下的 `.gem` 文件夹中。默认情况下，该目录在用户的 home 目录下的隐藏文件夹中。

通常，`.gem` 目录的完整路径是：`/Users/your_username/.gem`。

你可以通过终端查看该目录。在终端中执行以下命令：

```bash
ls -la ~/.gem
```

这将显示 `.gem` 目录下的所有文件和文件夹。你应该能够找到安装的 gem 包。

如果你想查看当前 Ruby 的 gem 安装路径，可以使用以下命令：

```bash
gem environment
```

在输出中，你会找到 `GEM PATHS` 部分，其中列出了 gem 的安装路径。