---
title: AWS 初學筆記
tags: [aws]

---

# Install CLI
MacOS

:::warning
注意 MacOS 版本要在 13 以上，否則會報錯。
:::

```shell
brew install awscli
```

## 自動補齊指令
```shell
complete -C '/usr/local/bin/aws_completer' aws
```

AWS 在本機電腦存取 EKS 叢集跟其他 AWS 的資源。除了網路 IP 可以設置阻擋的白名單以外，也會對 IAM 使用者或者角色進行身份驗證。

AWS 需要使用 Access Key ID 和 Secret Access Key 來驗證。先透過 UI 產生自己帳號的 Access Key ID 和 Secret Access Key，才能夠有權限下指令。

## 建立存取密鑰
```shell
aws configure
```

# Get EKS kubeConfig file
```shell
aws eks --region <region> update-kubeconfig --name <cluster-name>
```

# Account 切換
路徑 ~/.aws 會存放 `credentials` 跟 `config` 管理 Account。

- config

```toml
[profile vsop]
region = ap-southeast-1
output = json
[profile fit-bear]
region = ap-southeast-1
output = json
```

- credentials

```toml
[vsop]
aws_access_key_id = key_id
aws_secret_access_key = access_key 
[fit-bear]
aws_access_key_id = key_id
aws_secret_access_key = access_key
```

## 新增 Account profile
```shell
aws configure --profile <Account>
```

## 查詢所有配置的 profiles
```shell
aws configure list-profiles
```

## configure get
從 AWS CLI 的配置檔案中獲取特定的配置值。

`configuration-item`: 指定要獲取的配置項目名稱。例如 region、output、aws_access_key_id、aws_secret_access_key 等。

```shell
aws configure get <configuration-item> [--profile <profile-name>]
```

## 透過環境變數切換 Account
```shell
export AWS_PROFILE=<Account>
```

# 遇到問題
更新 MacOS 後，發現執行 terragrunt 回報以下錯誤。

:::danger
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at:
/Library/Developer/CommandLineTools/usr/bin/xcrun
:::

請更新 CommandLineTools。

```shell
xcode-select --install
```

# 參考
[設定 AWS CLI 以及 AWS CLI 指令說明](https://pin-yi.me/blog/aws/aws-cli/)

[更新 Mac 出現 xcrun: error 解法](https://blog.clarence.tw/2021/05/22/update-mac-xcrun-error-solution-appears/)

