---
title: GitLab CICD 基礎
tags: [cicd]

---

# GitLab CICD 基礎
⭐️ 當專案越趨龐大的時候，Gitlab 提供 template、繼承等特性可以更靈活的運用。

:::info
image 👉 決定 gitlab-runner 執行時使用的環境。

script 👉 撰寫指令或腳本。
:::

一般的 CICD 工作流程可以分為以下三個部分，概略分類：
1. Verify
2. Package
3. Release

:::info
如果有 Script 是正式的 Script 執行前或執行後所有 Job 均需要完成的要怎麼設定？
:::

在 GitLab CI 語法中有 `before_script` 及 `after_script` 兩個設置值，可以讓你設計在正式的 script 前後的動作 

如果不想要到 Gitlab Setting 設定變數，可以在 yaml 中使用 variable 👈 這項參數來設置變數。

除了自己自定義的變數，也有一些是系統預設的變數可以直接使用!
:::info
CI_COMMIT_REF_NAME 👉 job目前取得的 GitRepo 所在的 Tag 或 Branch 名稱

CI_COMMIT_REF_SLUG 👉 如上，但透過方法將名稱精簡至最長63個字元並調整為小寫

CI_COMMIT_SHA 👉 Git Repo 目前所在的 Commit Hash Code

CI_COMMIT_SHORT_SHA 👉 Git Repo 目前所在的 Commit Hash Code 的前 8 字元
:::

###### tags: `cicd`