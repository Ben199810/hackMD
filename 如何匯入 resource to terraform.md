---
title: 如何匯入 resource to terraform
tags: [terraform]

---

使用 `terraform import` 預先存在的雲端資源匯入 terraform state。

- provider.tf
  ```hcl
  provider "docker" {
  #   host    = "npipe:////.//pipe//docker_engine"
  }
  ```

- main.tf
  ```hcl
  resource "docker_container" "web" {}
  ```

- 執行下列 terraform import 指令，將現有的 Docker 容器連接至剛剛建立的 docker_container.web 資源。
  ```shell
  terraform import docker_container.web $(docker inspect -f {{.ID}} hashicorp-learn)
  ```

- 確認容器已匯入 Terraform 狀態
  ```shell
  terraform show
  ```
  
- 將 Terraform 狀態複製到 main.tf 檔案
  ```shell
  terraform show -no-color > docker.tf
  ```