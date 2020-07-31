---
layout: doc
title: "SpaceLauncher 外部动作执行细节"
---

**注：仅适用于 SpaceLauncher 1.5 及其以上版本**

1. 查找 `custom.yaml` 中的转换器，依次求值并替换，作为转换后的 `custom.yaml`。

2. 解析转换后的 `custom.yaml` 中的 `actionsByKey`。

3. 用户按下 `空格+预设按键` 时，查找 `actionsByKey` 下相应按键对应的动作及其参数。

4. 执行动作：

  * 如果动作是 `openurl`、`keystroke`、`menu` 或者 `group`，直接执行。

  * 如果是其它动作（包括以 `.yaml` 结尾的模板动作），依次在 `/Applications/SpaceLauncher.app/Contents/MacOS/actions/` 和 `~/Library/Application Support/SpaceLauncher/actions/` 目录下查找同名文件。如果两个目录下都存在，则弹出提示。

    * 如果是模板动作，

      * 读取其内容，将其中的 `$1` 替换为该动作的参数（如果有参数的话），获取最新的环境变量的值并替换模板动作出现的环境变量。

      * 将替换后的内容解析为一个动作并按照步骤 4 执行。

    * 如果不是模板动作，

      * 获取最新的环境变量的值，创建新进程执行该动作文件并传入环境变量和参数。

      * 读取标准错误输出，如果有，则弹出提示。