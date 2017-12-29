---
layout: doc
title: "获取浏览器标签页的标题和链接构成 Markdown 链接"
---

用 Markdown 写文章的时候经常会引用网页，既要复制链接还要复制标题，并在应用间来回切换，这篇文章就是要通过 SpaceLauncher 实现由当前网页一键生成 Markdown 链接（`[标题](链接)`）并存入剪贴板。

SpaceLauncher 已经实现了获取当前网页的标题和链接，Safari 和 Chrome 的当前页面标题分别存储在 `SL_FRONTMOST_WINDOW_TITLE_VIA_ACCESSIBILITY` 和 `SL_FRONTMOST_WINDOW_TITLE_VIA_ACCESSIBILITY`，当前页面链接分别存储在 `SL_FRONTMOST_SAFARI_DOCUMENT_URL` 和 `SL_FRONTMOST_GOOGLE_CHROME_TAB_URL`。

在 `~/Library/Application Support/SpaceLauncher/actions` 目录创建 `current-web-page-markdown-link.yaml` 文件，复制粘贴下面的配置。

```
apps:
  com.apple.Safari:
    copy:
      '[$SL_FRONTMOST_WINDOW_TITLE_VIA_ACCESSIBILITY]($SL_FRONTMOST_SAFARI_DOCUMENT_URL)'
  com.google.Chrome:
    copy:
      '[$SL_FRONTMOST_WINDOW_TITLE_VIA_ACCESSIBILITY]($SL_FRONTMOST_GOOGLE_CHROME_TAB_URL)'

```

在 `~/Library/Application Support/SpaceLauncher/custom.yaml` 配置中调用这个动作，比如绑定到 `空格+C` 上。

```
...
  C:
    current-web-page-markdown-link.yaml:
      !!null "null"
```

重启 SpaceLauncher，打开一个网页，按下 `空格+C`，回到 Markdown 编辑器下，粘贴即可。

