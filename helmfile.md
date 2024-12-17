---
title: helmfile
tags: [helm, helmfile]

---

# 介紹
helmfile 透過 helmfile.yaml 檔案幫助使用者管理和維護眾多的 helm chart。

# 安裝
macOS
```shell
brew install helmfile
```

# 指令
## sync
如果您發現由 helmfile 管理的資源在 Kubernetes 上消失了，而 values 文件沒有任何變動，您可以透過以下步驟將資源重新部署回去。

- 重新同步部署
  執行 `helmfile sync` 命令，這將根據您的 helmfile.yaml 定義，將所有的釋出（release）同步到 Kubernetes 集群。
  ```shell
  helmfile sync
  ```

- 強制更新部署
  如果資源沒有被更新，您可以使用 --force 選項強制重新部署所有釋出。
  ```shell
  helmfile sync --force
  ```



# 參考
[Helmfile](https://helmfile.readthedocs.io/en/latest/#helmfile)