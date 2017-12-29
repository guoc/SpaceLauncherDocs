---
layout: doc
title: "在选中文字的前后插入反引号"
---

在 `~/Library/Application Support/SpaceLauncher/actions` 目录创建 `enclose-selected-text.yaml` 文件，复制粘贴下面的配置。

```
insert-text:
  "$1$SL_SELECTED_TEXT_VIA_ACCESSIBILITY$1"
```

在 `~/Library/Application Support/SpaceLauncher/custom.yaml` 配置中调用这个动作，比如绑定到 `` 空格+` `` 上。

```
  '`':
    enclose-selected-text.yaml:
      '`'
```

重启 SpaceLauncher，选中编辑中的文字，`` 空格＋` ``，就会在选中文字的前后插入 `` ` ``。

也可以把参数设置为其它字符，比如下面例子中的双引号和单引号，需要注意转义字符。

```
  Q:
    enclose-selected-text.yaml:
      '\"'
  W:
    enclose-selected-text.yaml:
      '\'''
```

当然，你也可以根据需求把上面的 `Q` 和 `W` 改成 `'"'` 和 `"'"` 使其与插入的字符一致。

