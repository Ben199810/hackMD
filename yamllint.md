---
title: yamllint
tags: ["開源", "小工具", "實用"]

---

YAML 的 linter。檢查語法有效性，還檢查按鍵重複等奇怪現像以及行長、尾隨空格、縮排等外觀問題。

# 安裝
```shell
pip install --user yamllint
```

# 用法

```shell
# 一個或多個檔案
yamllint my_file.yml my_other_file.yaml ...
```

```shell
# 所有 YAML 檔案在此資料夾
yamllint .
```

```shell
# 使用預先定義的 lint 配置
yamllint -d relaxed file.yaml

# 使用自訂的 lint 配置
yamllint -c /path/to/myconfig file-to-lint.yaml
```

```shell
# 輸出可解析的格式(用於編輯器的檢查，像 Vim, emacs...)
yamllint -f parsable file.yaml
```

# 設定檔範例

```yaml
extends: default

rules:
  # 80 字元應該足夠了，如果一行太長也不會發生失敗
  line-length:
    max: 80
    level: warning

  # 別用這個規則干擾我
  indentation: disable
```

在 YAML 檔案中，可以使用特殊註解停用對單行檢查。

```yaml
This line is waaaaaaaaaay too long  # yamllint disable-line
```

或者對於整個區塊。

```yaml
# yamllint disable rule:colons
- Lorem       : ipsum
  dolor       : sit amet,
  consectetur : adipiscing elit
# yamllint enable
```

可以使用 `.gitignore` 樣式模式忽略特定檔案(完全忽略或僅忽略某些規則)。

```yaml
# 套用在所有 rules
ignore: |
  *.dont-lint-me.yaml
  /bin/
  !/bin/*.lint-me-anyway.yaml

rules:
  key-duplicates:
    ignore: |
      generated
      *.template.yaml
  trailing-spaces:
    ignore: |
      *.ignore-trailing-spaces.yaml
      /ascii-art/*
```

# 參考
[yamllint](https://github.com/adrienverge/yamllint)

[yamllint 文檔](https://yamllint.readthedocs.io/en/stable/)