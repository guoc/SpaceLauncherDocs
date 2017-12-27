---
layout: doc
title: "SpaceLauncher 自定义配置文件教程"
---

**注：仅适用于 SpaceLauncher 1.5.0-alpha.14 及其以上版本**

SpaceLauncher 使用配置文件实现更复杂的按键动作。

配置文件位于 `~/Library/Application Support/SpaceLauncher/custom.yaml`。

SpaceLauncher 启动后，优先使用该文件中的配置，如果该文件不存在，则调用之前在图形化界面下进行的设置。

配置文件使用 [YAML](http://yaml.org) 格式，阅读这篇教程不需要了解 YAML。

如果你发现文中使用了 YAML 的术语或者其它不清晰的词汇，请发邮件告诉我（guochen42@live.com）。

这篇教程尽可能多地覆盖了 SpaceLauncher 配置文件的各个方面，尽量由易到难，你并不需要全部读完。

# 预备知识

每行配置用行首的空格缩进表示层级关系，如下，

```
顶级:
  # 这是注释
  一级:
    二级:
      最低级
    二级:
      三级:
        最低级
```

如果一个级别有子级，那它后面需要有英文冒号。

相同空格缩进的行表示同一级别，如上面例子中的「二级」。

最低级可以是一些值，比如一段文字、一个数值等。

以 # 开始的部分是注释，不起作用。

以上就是使用 `custom.yaml` 所需的绝大部分知识了。

# 基本结构

让我们从简单的配置开始。

首先，你需要有一个纯文本编辑器，如果没有，推荐 [CotEditor](https://coteditor.com)。

打开纯文本编辑器，复制粘贴以下文字。

```
actionsByKey:
  H:
    say:
      hello
  W:
    say:
      world
```

保存，在弹出的保存窗口下，按 `Shift-Command-G` 跳转到目录 `~/Library/Application Support/SpaceLauncher/`，然后输入文件名 `custom.yaml`，保存。

重启 SpaceLauncher 后，调整系统音量，避免吓着身边的人。按住空格，按 `H`，就会听到「hello」的发音，按 `W` 就会听到「world」的发音。

这个配置文件中，顶级是 `actionsByKey`，按键和动作的关系都必须在它的下级定义，所以需要注意空格缩进。

`H`、`W` 都是 `actionsByKey` 的下一级，这一级只能是按键的名称。

在它们各自的下一级是该按键所对应的动作，上面例子中都是 say，其它动作会在后面介绍。

动作的下一级必须是该动作的参数，也就是执行该动作所需要的信息。上面的例子中，两个 `say` 的参数分别是 `hello` 和 `world`，它们都是一段文字。对于其它动作，参数也可能是一个数值或者其它，这取决于具体的动作。

## 可用的按键

除了上面用到的 `H` 和 `W`，SpaceLauncher 支持键盘上的大部分按键。

点击这个链接 [spacelauncher://available-keys](spacelauncher://available-keys) 会使 SpaceLauncher 根据当前键盘布局生成可用按键。

这里的按键中都包含了引号，推荐除了字母和单个数字以外的其它按键都使用这里的格式。

# 打开链接

上面提到的 `say` 是一个动作，这里再介绍一个动作，`openurl`，它接受一个链接作为参数。比如我们想通过 `空格+G` 打开 Google 的主页。

```
...
  G:
    openurl:
      https://google.com
```

重启 SpaceLauncher，尝试上面的动作。

这个链接也可以是本地文件、文件夹或者应用程序，比如，`空格+F` 打开 Finder，`空格+S` 打开 Safari。

```
...
  F:
    openurl:
      file:///System/Library/CoreServices/Finder.app
  S:
    openurl:
      file:///Applications/Safari.app
```

每次修改配置之后都需要重启 SpaceLauncher，很麻烦。这里我们通过打开一个特殊的链接来重启 SpaceLauncher。

```
...
  R:
    ## 重启 SpaceLauncher
    openurl:
      spacelauncher://relaunch
```

最后一次手动重启 SpaceLauncher，之后就可以通过 `空格+R` 重启 SpaceLauncher 了。

注意，按键名称所在行的下一行如果是以 `##` 开始的注释，注释的内容会出现在提示窗口中，长按空格就会出现。

# 更多动作

让我们再来看几个动作。

## dict 查字典

用系统自带的字典查询当前选中的文字。这个动作能自己获取当前选中的文字，本身不需要任何参数，但为了保持一致，我们需要给它一个空参数。

```
...
  D:
    ## 在系统字典中查询当前选中的文字
    dict:
      !!null "null"
```

按 `空格+R` 重启 SpaceLauncher，选中一个单词，`空格+D`，SpaceLauncher 就会用系统自带词典查询该单词，如果没有选中文字，也会打开字典。

## insert-text 插入文字

上面 `dict` 动作的例子中需要输入 `!!null "null"`，每次输入很麻烦，还容易输错，insert-text 这个动作可以帮助我们插入一段文字。

```
...
  E:
    ## 插入 YAML 空参数
    insert-text:
      '!!null "null"'
```

注意要插入的文字得用英文单引号括住。

别忘了 `空格+R` 重启 SpaceLauncher。

## keystroke 模拟按键

上面例子中的动作后面都跟了一个参数，如果一个动作需要更多的信息该怎么办？动作 `keystroke` 就是这样一个例子。

比如我们要模拟截屏快捷键 `Shift-Command-4`，这里包括主键 `4`、修饰键 `Shift` 和 `Command`。我们给 `keystroke` 又加了两个下级，`key` 表示主键，`modifiers` 表示修饰键，它们又有各自的值。

```
...
  4:
    ## 截屏
    keystroke:
      key:
        4
      modifiers:
        { shift, command }
```

注意这里 `modifiers` 下的值 `{ shift, command }` 比较特殊，是一个集合。它是什么不重要，重要的是按照这个格式把需要的修饰符（`shift`/`command`/`option`/`control`）用逗号分割，放在大括号里即可。

`空格+R` 重启 SpaceLauncher，按下 `空格+4`，就可以拖拽鼠标进行截屏了。

## launch-and-cycle-windows.yaml 切换到指定应用程序，重复按键在应用内多个窗口间切换

把之前绑定到 F 的配置改成如下，

```
  F:
    ## 打开 Finder 并切换应用内窗口
    launch-and-cycle-windows.yaml:
      file:///System/Library/CoreServices/Finder.app
```

重启 SpaceLauncher 后，先手动切换到 Finder，`Command-N` 多创建几个窗口，再切换到其它应用。这时按下 `空格+F`，会切换到 Finder，不松空格，继续按 `F`，会在多个 Finder 窗口间切换。

注意动作名字末尾的 `.yaml` 不可省略。该动作是一个模板动作，下面会有介绍。

## menu 执行菜单操作

屏幕顶部的菜单栏提供了很多操作，我们可以通过 `menu` 动作代替手动点击或者预设快捷键。

```
...
  U:
    ## 对选中文字做摘要
    menu:
      - ThisApp
      - Services
      - Summarize
```

注意 menu 的参数是一个 sequence，不用在意这个概念，我们只需要注意它的格式，每个菜单项的名字前面都有一个连字符「- 」。

`空格+R` 重启 SpaceLauncher，任意选中一长段文字，`空格+U` 试试效果。

## open-in-terminal 根据当前 Finder 窗口路径打开终端

```
...
  T:
    ## 根据当前 Finder 窗口路径打开终端
    open-in-terminal:
      !!null "null"
```

## quit-app 关闭指定应用程序

```
...
  P:
    ## 关闭 Finder
    quit-app:
      com.apple.finder
```

## shell 执行命令行

```
...
  H:
    ## 禁用所有插件打开 Chrome
    shell:
      '/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --disable-extensions'
```


# group 依次按多个键执行动作

键盘上按得顺手的键就那么多，比如给 Google 家的各项服务都绑定了快捷键，很快就会发现键不够用了。如果能这样就好了，`空格+G+M` 打开 Gmail，`空格+G+C` 打开 Google Calendar，`空格+G+D` 打开 Google Drive。这就是 `group` 这个动作要做的。

先看下我们当前的配置文件，`actionsByKey` 的下级是各个按键到动作的对应，这些对应同样可以放在 `group` 的下级作为其参数，从而把多个动作归为一组。

`group` 本身是一个动作，它同时又需要作为一个按键的下级。

现在删掉之前定义的 `空格+G` 打开 Google 主页的动作。

```
  G:
    openurl:
      https://google.com
```

替换为，

```
  G:
    group:
      M:
        openurl:
          https://mail.google.com
      C:
        openurl:
          https://calendar.google.com
      D:
        openurl:
          https://drive.google.com
```

重启 SpaceLauncher，尝试上面的动作。

## 嵌套多个 group

上面的例子中，`空格+G+D` 是打开 Google Drive，如果我们想让 `空格+G+D+R` 打开最近使用过的的 Google Drive 文档、`空格+G+D+S` 打开加星标的文档，只需要嵌套使用 `group` 即可。

```
  G:
    ## Google 服务
    group:
      M:
        ## Gmail
        openurl:
          https://mail.google.com
      C:
        ## Google Calendar
        openurl:
          https://calendar.google.com
      D:
        ## Google Drive
        group:
          R:
            ## 最近的文档
            openurl:
              https://drive.google.com/drive/recent
          S:
            ## 加星标的文档
            openurl:
              https://drive.google.com/drive/starred
```

你可能对这个方案并不满意，你可能更希望，

- `空格+G+D+R` 打开最近的文档，不松空格接着按 `S` 打开加星标的文档。

- `空格+G+D` 打开 Google Drive，接着按 `R` 打开最近的文档。

- `空格+G+D` 打开 Google Drive，接着按 `R` 直接在之前打开的 Google Drive 的标签页下打开最近的文档。

这些 SpaceLauncher 都能做到，后面会提。

# 高级动作

这里开始，可能需要一些相关知识，但并不复杂。

## actions-one-by-one actions-at-once 一次按键执行多个动作

前者是依次执行多个动作，后者是同时执行。

```
  actions-one-by-one:
    - 动作一:
        参数
    - 动作二:
        参数
    - 动作三:
        参数
```

需要注意，`actions-one-by-one` 和 `actions-at-once` 这两个动作需要跟 pair 作为其参数，不必在意什么是 pair，我们只需要注意它的格式，每个动作前面都有连字符「- 」。

后面会有具体的例子。

## 相同按键在不同应用程序下执行不同动作

Safari 和 Firefox 的书签管理器的快捷键不一样，我们可以用 `空格+B` 把它们统一。

```
...
  B:
    ## 浏览器的书签管理器
    apps:
      org.mozilla.firefox:
        keystroke:
          key:
            B
          modifiers:
            { command, shift }
      com.apple.Safari:
        keystroke:
          key:
            B
          modifiers:
            { command, option }
      com.google.Chrome:
        keystroke:
          key:
            B
          modifiers:
            { command, option }
```

应用标识符的位置处如果设置为「default」，可以对应所有其它的应用程序。

# 模板动作

就像「IMHO」是「in my humble opinion」的缩写一样，模板动作就像是动作的缩写。模板动作中还可以使用一些特殊的占位符。

上面的例子中，我们用 menu 动作实现了对选中文字做摘要的操作，我们可以通过模板动作对它做「缩写」。

新建一个文件，复制粘贴下面的配置，

```
## 对选中文字做摘要
menu:
  - ThisApp
  - Services
  - Summarize
```

保存为 `~/Library/Application Support/SpaceLauncher/actions/summarize.yaml`，所有模板动作都是 YAML 文件，必须以 `.yaml` 作为文件后缀。

所有的自定义动作都保存在 `~/Library/Application Support/SpaceLauncher/actions/` 目录下。

把刚才在 `custom.yaml` 中的配置改为，

```
...
  U:
    ## 对选中的文字做摘要
    summarize.yaml:
      !!null "null"
```

重启 SpaceLauncher。

注意 `summarize.yaml` 被称作「模板动作」而非「动作模板」，说明它本质是动作，所以后面需要跟参数，上面例子中不需要参数，所以只需要传空参数 `!!null "null"` 即可，下面会有需要参数的例子。

---

上面的例子更多是为了展示，除了让配置文件更清晰以外并没有直接的好处。

在前面我们提到了这样一个效果，`空格+G+D` 打开 Google Drive，不松空格接着按 `S` 在当前标签页下打开 Google Drive 中加星标的文档。

为了实现这个效果，在这一章节中我们首先实现一个子功能，按下快捷键在当前标签页中打开指定链接。

通常我们要在当前标签页下打开链接需要做这几个操作，

- `Command-L` 将光标聚焦到地址栏
- 输入地址
- 回车

这几个操作都可以分别用之前提到的动作实现。比如，下面的配置实现了在当前标签页下打开 `https://drive.google.com/drive/starred`。

```
# 在当前标签页下打开链接
actions-one-by-one:
  - keystroke:
      key:
        L
      modifiers:
        { command }
  - insert-text:
      https://drive.google.com/drive/starred
  - keystroke:
      key:
        Return (Enter)
```

我们可以把这段代码放到一个单独的模板动作文件中，但是这个模板动作只能打开一个固定的链接，意义不大。这时我们就可以使用模板动作的参数了。把上面例子中的固定链接替换为 `$1`，并保存为一个模板动作，`openurl-in-current-tab.yaml`，注意不要忘了 `.yaml` 的文件后缀。文件内容如下，

```
# 在当前标签页下打开链接
actions-one-by-one:
  - keystroke:
      key:
        L
      modifiers:
        { command }
  - insert-text:
      $1
  - keystroke:
      key:
        Return (Enter)
```

在 `custom.yaml` 中给这个模板动作一个链接作为参数，比如，

```
...
  按键甲:
    openurl-in-current-tab.yaml:
      https://drive.google.com/drive/starred
  按键乙:
    openurl-in-current-tab.yaml:
      https://drive.google.com/drive/recent
```

重启 SpaceLauncher，打开一个 Chrome 标签页，`空格+按键甲` 就会在当前页面下打开加星标的 Google Drive 页面，`空格+按键乙` 是最近打开的 Google Drive 文档。（Safari 下可能会由于受到空格键状态的影响不能达到预期效果，下面有更稳定的实现。）

按下 `空格+按键甲` 时，SpaceLauncher 收到了动作 `openurl-in-current-tab.yaml` 和它的参数 `https://drive.google.com/drive/recent`，SpaceLauncher 会把 `openurl-in-current-tab.yaml` 文件内容中的 `$1` 替换为 `https://drive.google.com/drive/recent` 并执行。这里的 `$1` 就是一个占位符，表明这里要被这个模板动作的参数替换掉。

除了 `$1` 这个占位符，SpaceLauncher 还提供了一些其它的占位符，`$SL_PASTEBOARD_TEXT`（剪贴板中的文字）、`$SL_SELECTED_TEXT_VIA_ACCESSIBILITY`（当前选中的文字，仅限支持 Accessibility 的应用）、`$SL_FRONTMOST_FINDER_WINDOW_PATH`（当前 Finder 窗口所在路径）等。

其实如果只使用 Safari 和 Google Chrome，可以通过 AppleScript 设置当前标签页的链接，不需要模拟按键的过程。

```
# 通过 AppleScript 在当前标签页下打开链接
# 保存在 actions/openurl-in-current-tab-via-applescript.yaml
shell: |
  osascript -e 'tell application "$SL_FRONTMOST_APP_LOCALIZED_NAME" to set URL of active tab of window 1 to "$1"'
  osascript -e 'tell application "$SL_FRONTMOST_APP_LOCALIZED_NAME" to set the URL of the front document to "$1"'
```

# 转换器

上面的例子中，对于所有的应用程序，我们都使用了完整的路径，比如 `file:///System/Library/CoreServices/Finder.app`，我们也可以通过一个特殊的转换器 `APP_URL` 来获取完整路径。

```
...
  F:
    ## 打开 Finder 并切换应用内窗口
    launch-and-cycle-windows.yaml:
      APP_URL(Finder)
```

**注意**：`Finder` 的前后没有任何引号。

这里的 `APP_URL` 就是一个转换器，它接受括号里的 `Finder` 作为参数，计算出 Finder 的路径，然后替换原来的位置。

虽然可以用这样的方法获取每个应用程序的路径，但并不推荐这样做。比起直接给出一个准确的路径，使用转换器需要额外的计算，而这可能会在使用时导致延时。

可每次输入完整路径确实很麻烦，这里给出一个动作，可以在编写配置文件时，将选中的应用程序的名字替换为相应的 URL 链接。

```
...
  L:
    ## 将选中的应用程序名称替换为相应的 URL 链接
    shell: |
      $SL_EXE action insert-text `$SL_EXE converter APP_URL $SL_SELECTED_TEXT_VIA_ACCESSIBILITY`
```

打开一个文本编辑器，输入并选中 `Finder`，`空格+L`，就会自动替换为 Finder 的 URL 链接。

这个动作通过 SpaceLauncher 的命令行接口执行了转换器 `APP_URL` 和 动作 `insert-text`，之后会有详细介绍。

另一个常见的转换器是 `APP_ID`，可以用来获取一个应用程序的 ID，动作 `apps`、`quit-app` 等须接受 ID 作为参数。

# 转换器和模板动作的混合使用

前文提到了 `launch-and-cycle-windows.yaml` 动作，通过这个动作，在按键切换到某个应用程序之后，重复按键可以在该应用程序内的多个窗口间切换。这是个一个内置的模板动作，位于 `/Applications/SpaceLauncher.app/Contents/SharedSupport/actions/launch-and-cycle-windows.yaml`，文件内容如下。

```
apps:
  APP_ID($1):
    keystroke:
      key: '`'
      modifiers:
        { command }
  default:
    openurl:
      APP_URL($1)
```

当我们如下使用这个动作时，

```
  F:
    ## 打开 Finder 并切换应用内窗口
    launch-and-cycle-windows.yaml:
      Finder
```

模板动作中的 `$1` 被替换为这个动作接收到的参数 `Finder`。

```
apps:
  APP_ID(Finder):
    keystroke:
      key: '`'
      modifiers:
        { command }
  default:
    openurl:
      APP_URL(Finder)
```

接着，转换器开始求值并替换相应位置。

```
apps:
  com.apple.finder:
    keystroke:
      key: '`'
      modifiers:
        { command }
  default:
    openurl:
      file:///System/Library/CoreServices/Finder.app
```

最后，动作开始执行，如果当前应用程序已经是 Finder，则通过 `keystroke` 动作模拟切换窗口的快捷键 `Command-\``，否则，如果当前应用程序是其它程序，则通过执行 `openurl` 动作跳转到 Finder。

可以看到模板动作和转换器的使用极大地减少了重复配置，如果不使用它们，我们得为每个需要设置快捷键的应用程序重复上面的配置。

# group 的子参数

之前在 [嵌套多个 group](#嵌套多个-group) 部分提到了三个更好的使用方案。

- 方案一：`空格+G+D+R` 打开最近的文档，不松空格接着按 `S` 打开加星标的文档。

- 方案二：`空格+G+D` 打开 Google Drive，接着按 `R` 打开最近的文档。

- 方案三：`空格+G+D` 打开 Google Drive，接着按 `R` 直接在之前打开的 Google Drive 的标签页下打开最近的文档。

## reset-to-previous-group-state-after-following-action

让我们从第一个方案开始看起。

之前的配置是这样的，

```
  G:
    group:
      M:
        openurl:
          https://mail.google.com
      C:
        openurl:
          https://calendar.google.com
      D:
        group:
          R:
            openurl:
              https://drive.google.com/drive/recent
          S:
            openurl:
              https://drive.google.com/drive/starred
```

`空格+G+D+R` 之后必须按完整的 `空格+G+D+S` 才能触发相应动作。

`group` 动作在成功触发动作或者遇到了未设定的按键时，会回到初始状态。

我们可以通过子参数 `reset-to-previous-group-state-after-following-action` 使其在成功触发动作之后回到之前的某个 `group` 的状态。

如果我们在下面这个位置的 `group` 中添加一行设置该参数为 `-1`，

```
...
      D:
        group:
          reset-to-previous-group-state-after-following-action:
            -1
          R:
            openurl:
              https://drive.google.com/drive/recent
          S:
            openurl:
              https://drive.google.com/drive/starred
```

这时，`空格+G+D+R` 打开最近的文档，不松空格接着按 `S` 就可以打开加星标的文档。

你可能还想实现下面的效果，

`空格+G+D+R` 打开最近的文档，不松空格接着按 `M` 就可以打开 Gmail，接着按 `D+R` 打开最近的文档，接着按 `D+S` 打开加星标的文档。

只需要把上面的 `-1` 改为 `-2` 即可。

## additional-action

方案二中我们希望 `空格+G+D` 打开 Google Drive，不松空格接着按 `R` 打开最近的文档。可以通过 `group` 的另一个子参数 `additional-action` 在 `D` 所对应的 `group` 下为这个 `group` 设置一个额外的动作，在这里就是打开 Google Drive。

```
...
      D:
        group:
          additional-action:
            openurl:
              https://drive.google.com
          R:
            openurl:
              https://drive.google.com/drive/recent
          S:
            openurl:
              https://drive.google.com/drive/starred
```

## 结合 openurl-in-current-tab.yaml

方案三在方案二的基础上，要求 `空格+G+D` 打开 Google Drive 之后，不松空格接着按 `R` 能够在刚刚打开的 Google Drive 标签页上打开最近的文档。

因为我们之前已经实现了 `openurl-in-current-tab.yaml`，将 `R` 和 `S` 对应的动作 `openurl` 改为 `openurl-in-current-tab.yaml` 即可。

```
...
      D:
        group:
          additional-action:
            openurl:
              https://drive.google.com
          R:
            openurl-in-current-tab.yaml:
              https://drive.google.com/drive/recent
          S:
            openurl-in-current-tab.yaml:
              https://drive.google.com/drive/starred
```

# 制作动作

模板动作方便我们把已有的动作组合起来，而要想实现新的功能，就需要制作新的动作了。

每个动作本质上都是一个可执行文件，可以使用任何编程语言编写。SpaceLauncher 在配置文件中读到一个动作和它后面的参数时，会找到这个动作所对应的可执行文件，传入参数并执行这个可执行文件。

一开始我们提到的动作 `say` 可以用来朗读指定文字。接下来，我们以 `say` 为例，介绍 SpaceLauncher 如何执行动作。

如果之前的配置还保留着，可以看到，

```
...
  W:
    say:
      world
```

当按下 `空格+W` 时，SpaceLauncher 检测到这组按键，查询配置文件看到需要执行 `say` 动作和相应的参数 `world`。

首先，SpaceLauncher 依次在 `/Applications/SpaceLauncher.app/Contents/MacOS/actions/`、`/Applications/SpaceLauncher.app/Contents/SharedSupport/actions/` 和 `~/Library/Application Support/SpaceLauncher/actions/` 目录下找到名为 `say` 的可执行文件 `/Applications/SpaceLauncher.app/Contents/SharedSupport/actions/say`。

然后，执行该文件，同时将参数 `world` 传入。

以上操作和在终端中执行以下命令效果完全相同。

```
/Applications/SpaceLauncher.app/Contents/SharedSupport/actions/say world
```

至于 `say` 动作所对应文件的具体实现，其实只是一个 Bash 脚本，调用了系统的 say 命令。

关于 SpaceLauncher 执行动作的具体细节，见 [SpaceLauncher 外部动作执行细节]({% link docs/cn/manual-configuration/action-execution-detail.md %})。

## 环境变量

SpaceLauncher 在执行一个动作所对应的可执行文件时，除了传入参数，还会传入一些特殊的环境变量，用于为动作提供可能需要的信息。

- `SL_EXE`

  SpaceLauncher 的命令行接口的可执行文件路径，目前位于 `/Applications/SpaceLauncher.app/Contents/MacOS/spacelauncher-cli`

- `SL_FRONTMOST_APP_PATH`

  当前应用程序路径

- `SL_FRONTMOST_APP_LOCALIZED_NAME`

  当前应用程序名称

- `SL_FRONTMOST_APP_ID`

  当前应用程序 ID

- `SL_PRESSED_KEY`

  触发该动作的按键名

- `SL_PRESSED_KEY_CODE`

  触发该动作的按键码

- `SL_FRONTMOST_WINDOW_TITLE_VIA_ACCESSIBILITY`

  通过 Accessibility 接口获取的当前应用程序窗口标题

- `SL_SELECTED_TEXT_VIA_ACCESSIBILITY`

  通过 Accessibility 接口获取的当前选中的文本

- `SL_PASTEBOARD_TEXT`

  当前剪贴板中的文本

- `SL_FRONTMOST_FINDER_WINDOW_URL`

  如果当前应用程序是 Finder，返回最前端的窗口所显示的文件夹的路径 URL。

- `SL_FRONTMOST_FINDER_WINDOW_PATH`

  如果当前应用程序是 Finder，返回最前端的窗口所显示的文件夹的路径。

- `SL_FRONTMOST_SAFARI_DOCUMENT_URL`

  如果当前应用程序是 Safari，返回最前端的文档的 URL。
  
- `SL_FRONTMOST_GOOGLE_CHROME_TAB_URL`

  如果当前应用程序是 Google Chrome，返回最前端的标签页的 URL。

欢迎提出建议，添加你所需要的环境变量。

## 命令行接口

在一个动作的执行过程中，免不了要执行其它的动作，SpaceLauncher 的命令行接口可以被调用以执行其它动作。

接口文件位于 `/Applications/SpaceLauncher.app/Contents/MacOS/spacelauncher-cli`，建议通过环境变量 `SL_EXE` 获取。

注意，执行该文件时需保证 SpaceLauncher 主程序已运行。

执行该文件有以下两种格式。

- `$SL_EXE action [--sync] *action_name* *action_argument*`

  执行 `*action_name*` 所指的动作并传入 `*action_argument*` 作为其参数。

- `$SL_EXE actionYAML [--sync] *action_and_argument_in_YAML*`

  如果动作及其参数本身已经以 YAML 格式的字符串表示，可直接传入。比如 `$SL_EXE actionYAML 'keystroke: { key: V, modifiers: { shift } }'`。

以上两种格式默认都是执行后立即返回，如果需要等待动作执行结束后返回，可以传入 `--sync` 选项。

# 从其它应用程序执行 SpaceLauncher 动作

SpaceLauncher 中的动作，不管是内置的，还是用户自己创建的，都可以通过上面介绍的[命令行接口](#命令行接口)从其它应用程序调用，这样极大地扩展了动作的使用范围。

# 其它设置

## 修改热键

空格键作为热键除了本身的优势以外，本身又是一个常用按键，不可避免地会发生冲突，SpaceLauncher 使用了多种方法避免这个冲突，但带来了少量延迟，或者在少数情况下不能正常使用。

### 设置空格以外的热键

除了空格，还可以设置通过其它按键触发，但是注意，这个额外设置的热键将会失去本来的功能，所以我们需要选择那些用不到的按键。

```
extraHotKey:
  F20
...
```

然后通过 [Karabiner-Elements](https://pqrs.org/osx/karabiner/) 将那些 SpaceLauncher 不支持的按键（比如 CapsLock）再映射到上面的 `F20`。

### 禁用空格热键

```
spaceHotKeyDisabled:
  true
...
```

这样就可以只使用额外设置的热键触发动作了。
