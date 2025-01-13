---
title: VSCode 中解決 gitlab-ci yaml !reference 錯誤 lint
tags: [gitlab, vscode, setting]

---

# 如何設定

Mac: cmd + shift + R 開啟 `setting.json`。

在使用者設定中加入此設定。

定義了一個名為 !reference 的自定義標籤，並將其類型設置為 sequence。

在這裡，!reference 標籤被定義為一個序列（sequence），這意味著當 YAML 解析器遇到 !reference 標籤時，會將其內容解析為一個序列（例如一個列表）。

```json
"yaml.customTags": [ "!reference sequence" ],
```