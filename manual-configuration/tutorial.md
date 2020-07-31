---
layout: doc
title: "SpaceLauncher Configuration File Tutorial"
---

**Requires SpaceLauncher 1.5.0-beta.1 or later**

SpaceLauncher provides complex settings via the configuration file at `~/Library/Application Support/SpaceLauncher/custom.yaml`.

After being launched, SpaceLauncher tries to load the configuration file first. If the file doesn't exist, then the actions settings in Preferences Windows take effect.

The configuration file's format is [YAML](http://yaml.org), but YAML is not prerequisite for understanding this tutorial.

If you find any unexplained YAML terminologies or ambiguous words, please [email me](mailto:guochen@live.com) (guochen@live.com).

This tutorial tries to cover all features about SpaceLauncher configuration files. It is not necessary to finish the whole tutorial before using SpaceLauncher configuration files.

---

* TOC
{:toc}

---

# Structure

The spaces indentations at the head of every line in configuration files are used to indicate lines' hierarchy.

```
Root Level:
  # This is a comment
  First Level:
    Second Level:
      Leaf Level
    Second Level:
      Third Level:
        Leaf Level
```

The levels which have sub-levels must be followed by `:`.

The lines which have the same spaces indentations are in a same level, such as the `Second Level` above.

The leaf level could be text, a value, etc.

The line starting with `#` is a comment. Comments are not parsed as a part of the configuration.

The above is almost all you need to use the configuration file.

Let's get started with a simple configuration file.

First, you'll need a plain text editor (such as [CotEditor](https://coteditor.com)).

Open the plain text editor, copy and paste the following text.

```
actionsByKey:
  H:
    say:
      hello
  W:
    say:
      world
```

Save, in the dialog window, press `Shift-Command-G` and go to `~/Library/Application Support/SpaceLauncher/` folder, enter the file name `custom.yaml`，save.

Restart SpaceLauncher, holding space bar and press `H`, you will hear "hello", press `H` for "world".

In this configuration file, the root level is `actionsByKey`, under which the relationships between keys and actions are defined.

`actionsByKey`'s sub-level has to be keys' names, such as `H`, `W`.

The sub-level of keys' names includes their corresponding actions (e.g., `say` in the above example). Actions' details will be given later.

The sub-level of an action has to be its action argument, which provides the information to execute the action. In the above example, the two `say` actions have `hello` and `world` as their action arguments correspondingly. In the example, they are text, but action arguments may be a value or other types. An action argument's type depends on the action's requirement.

## Available Keys

In addition to the above `H` and `W`, SpaceLauncher supports most keys on a keyboard.

Click this link [spacelauncher://available-keys](spacelauncher://available-keys), then SpaceLauncher will generate available keys based on current keyboard layout.

All generated keys are enclosed by single or double quotes, which are recommended for all non-alphanumeric keys. For alphanumeric keys, it is safe not to use quotes.

# Common Actions

The above `say` is an action. I will show you more common actions in this section.

## Open URL `openurl`

This action receives a link as its argument. For example, we want to open Google via `Space+G`.

```
...
  G:
    openurl:
      https://google.com
```

Restart SpaceLauncher and have a try.

The link could also a local file, folder or application. e.g., `Space+F` to open Finder, `Space+S` to open Safari.

```
...
  F:
    openurl:
      file:///System/Library/CoreServices/Finder.app
  S:
    openurl:
      file:///Applications/Safari.app
```

After changes on the configuration file, we have to restart SpaceLauncher manually. We can create a shortcut for restarting SpaceLauncher via opening a special URL.

```
...
  R:
    ## Restart SpaceLauncher
    openurl:
      spacelauncher://relaunch
```

After restarting SpaceLauncher manually, then we can restart SpaceLauncher by `Space+R`.

Note: If the comment line below a key name starts with `##`, the comment content will be displayed in the hint window (hold space bar to show the hint window).

## Look Up in Dictionary `dict`

Look up selected text by Dictionary.app. This action can get the current selected text. It does not need any argument, but for consistence, we have to pass it an empty argument.

```
...
  D:
    ## Loop up selected text by Dictionary.app
    dict:
      !!null "null"
```

Now press `Space+R` to restart SpaceLauncher, then select a text, `Space+D`, SpaceLauncher will look up the text in Dictionary.app. If there is no text selected, Dictionary app will also be launched.

## Insert Text `insert-text`

In the above example of `dict` action, we need to type `!!null "null"`. It is boring and error-prone. The action `insert-text` can help us insert the text.

```
...
  E:
    ## Insert YAML empty argument
    insert-text:
      '!!null "null"'
```

Note the inserted text need to be enclosed by single quotes.

Don't forget to `Space+R` to restart SpaceLauncher.

## Simulate Keystroke `keystroke`

All the actions in the previous examples are followed by an argument. How can we pass more information to an action? The action `keystroke` is an example of passing more information.

For example, we want to simulate the keystroke `Shift-Command-4` to take a screenshot. The necessary information includes a main key `4` and two modifier keys `Shift` and `Command`. We add sub-levels `key` and `modifiers` with their corresponding values.

```
...
  4:
    ## Take a screenshot
    keystroke:
      key:
        4
      modifiers:
        [ shift, command ]
```

Note the `modifiers`'s value `[ shift, command ]` is special, which is a sequence in YAML. What does the sequence mean is not important. Follow the format: separate necessary modifiers (`shift`/`command`/`option`/`control`) by comma, and enclose all the modifiers with brackets.

`Space+R` to relaunch SpaceLauncher, then press `Space+4` and select an area to take a screenshot.

## Cycle Windows in a Specified App `launch-and-cycle-windows.yaml`

Launch or switch to a specified app. If the app is already frontmost, cycle the windows in the app.

Now change the previous configuration for `F` to the following.

```
  F:
    ## Launch Finder and cycle windows in Finder
    launch-and-cycle-windows.yaml:
      file:///System/Library/CoreServices/Finder.app
```

After restarting SpaceLauncher, switch to Finder manually, then `Command-N` to create a few windows, and then switch to another app. Now press `Space+F` to switch to Finder, without releasing space bar, continue to type `F`, the Finder windows will be cycled.

Note the action name ends with `.yaml`, which can not be elided. `launch-and-cycle-windows.yaml` is a template action. Template actions' details will be given later.

## Execute Menu Command `menu`

The menu in the top of the screen provides many commands. We can execute these commands by the `menu` action.

```
...
  U:
    ## Summarize selected text
    menu:
      - ThisApp
      - Services
      - Summarize
```

Note the argument's type of `menu` action is the same as that of `keystroke` action, which is a sequence. The difference is that we use another sequences' format. Note that every menu item name starts with a dash and a whitespace `- `.

`Space+R` to relaunch SpaceLauncher, then select some long text, `Space+U` to summarize the text.

## Open Terminal with current Finder window's path `open-in-terminal`

```
...
  T:
    ## Open Terminal.app with the directory changed to current Finder window's path
    open-in-terminal:
      !!null "null"
```

## Close an application `quit-app`

```
...
  P:
    ## Close Finder
    quit-app:
      com.apple.finder
```

## Execute a command `shell`

```
...
  H:
    ## Open Google Chrome with all extensions disabled.
    shell:
      '/Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --disable-extensions'
```


# Space+X+Y+... to trigger an action `group`

The handy keys on a keyboard is limited. The action `group` make it possible to group similar actions with a prefix. For example, I bound keys for several Google services, `Space+G+M` to open Gmail, `Space+G+C` for Google Calendar, `Space+G+D` for Google Drive.

See our configuration file at the time. `actionsByKey`'s sub-level is the mapping from keys to actions. Similarly, mappings could also be sub-levels of the action `group` as its argument, so that the multiple actions are grouped.

At the same time, `group` itself is an action, so it should be a key's sub-level.

Delete the previous definded `Space+G` open Goolge action.

```
  G:
    openurl:
      https://google.com
```

Change it to,

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

Relaunch SpaceLauncher, try the above actions.

## Nested Group

If we want `Space+G+D+R` to open Google Drive recent files, and `Space+G+D+S` to open Google Drive starred files, we can nest groups.

```
  G:
    ## Google Services
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
            ## recent files
            openurl:
              https://drive.google.com/drive/recent
          S:
            ## starred files
            openurl:
              https://drive.google.com/drive/starred
```

This solution can be improved in these ways.

- `Space+G+D+R` open recent files, without releasing the space bar, continue to press `S` to open starred files.

- `Space+G+D` open Google Drive, without releasing the space bar, continue to press `R` to open the recent files.

- `Space+G+D` open Google Drive, without releasing the space bar, continue to press `R` to open the recent files in the same tab page.

These improvements will be introduced later.

# Advanced Actions

## Execute multiple actions `actions-one-by-one` `actions-at-once`

```
  actions-one-by-one:
    - action1:
        argument
    - action2:
        argument
    - action3:
        argument
```

Note the argument format of `actions-one-by-one` and `actions-at-once`. The argument is a pair in YAML. Every action starts with a dash and a whitespace `- `.

The example will be given later.

## Execute different actions in different apps

The shortcuts for Bookmark Manager in Safari and Firefox are different. We will set `Space+B` to simulate the corresponding shortcuts in Safari and Firefox.

```
...
  B:
    ## Bookmark Manager
    apps:
      org.mozilla.firefox:
        keystroke:
          key:
            B
          modifiers:
            [ command, shift ]
      com.apple.Safari:
        keystroke:
          key:
            B
          modifiers:
            [ command, option ]
      com.google.Chrome:
        keystroke:
          key:
            B
          modifiers:
            [ command, option ]
```

You can set action for all other applications with `default` as the app identifier.

# Template Action

Template actions provide a easy way to compose actions. You can use some special placeholders in a template action.

We implemented the function that summarizes selected text in the previous example. Now we can simplify it via template actions.

Create a new file, copy and paste the following configuration in the file.

```
## Summarize selected text
menu:
  - ThisApp
  - Services
  - Summarize
```

Save as `~/Library/Application Support/SpaceLauncher/actions/summarize.yaml`. Template action files must be YAML format, so the file name ends with `.yaml`.

Custom action files (including custom template actions) are stored in `~/Library/Application Support/SpaceLauncher/actions/`.

Change `custom.yaml`,

```
...
  U:
    ## Summarize selected text
    summarize.yaml:
      !!null "null"
```

Restart SpaceLauncher.

Note `summarize.yaml` is named as "template action" rather than "action template". It is essentially an action. So it should be followed by an action argument. The `summarize.yaml` in the above example does not need an informative action argument, so we pass it an empty argument `!!null "null"`. We will see the examples that require non-empty arguments.

---

The last example for template actions has no real benefits except for clarity.

As I metioned in [Nested Group](#nested-group), we want to improve the group action like this.

`Space+G+D` open Google Drive, without releasing the space bar, continue to press `S` to open the starred files in the same tab page.

To implement this feature, let's start with a part of it. Press a key to open a link in the current tab.

To do this manually, we need to do these steps.

- `Command-L` to focus in address text field
- Type an address
- Type enter key

These steps can be done via some actions metioned before. For example, with the following configuration, SpaceLauncher can 
这几个操作都可以分别用之前提到的动作实现。比如，下面的配置实现了在当前标签页下打开 `https://drive.google.com/drive/starred`。

```
# 在当前标签页下打开链接
actions-one-by-one:
  - keystroke:
      key:
        L
      modifiers:
        [ command ]
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
        [ command ]
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
        [ command ]
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
        [ command ]
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
        [ command ]
  default:
    openurl:
      file:///System/Library/CoreServices/Finder.app
```

最后，动作开始执行，如果当前应用程序已经是 Finder，则通过 `keystroke` 动作模拟切换窗口的快捷键 `` Command-` ``，否则，如果当前应用程序是其它程序，则通过执行 `openurl` 动作跳转到 Finder。

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

注：对于可执行文件类型的动作，和计算机进程中的环境变量一样，在动作被调用时传入动作所在的进程。对于模板动作，由于是文本文件，在被调用时会替换其中的环境变量文本。

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

  如果动作及其参数本身已经以 YAML 格式的字符串表示，可直接传入。比如 `$SL_EXE actionYAML 'keystroke: { key: V, modifiers: [ shift ] }'`。

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
