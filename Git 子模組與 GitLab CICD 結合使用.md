---
title: Git 子模組與 GitLab CICD 結合使用
tags: [cicd]

---

# Git 子模組與 GitLab CICD 結合使用
使用 git 子模組時，專案底下通常會有 .gitmodules

如果要用 git 子模組在 CICD 作業中正常運作：
變數設定為 `normal` 或 `recursive` 來告訴執行程式 job 執行前取得子模組
```shell
variables:
  GIT_SUBMODULE_STRATEGY: recursive
```

1. none 取得代碼項目不包含子模塊，預設值。
2. normal

```shell
git submodule sync
git submodule update --init
```
3. recursive

```shell
git submodule sync --recursive
git submodule update --init --recursive
```


參考連結：https://docs.gitlab.com/ee/ci/git_submodules.html

###### tags: `cicd`