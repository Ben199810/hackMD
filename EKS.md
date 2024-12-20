---
title: EKS
tags: [aws, eks, terraform]

---

# ARN
ARN（Amazon Resource Name）是一種唯一標識符，用於指定 AWS 資源。每個 ARN 都有一個標準格式，這使得它可以在 AWS 的不同服務和區域中唯一地標識某個資源。

`arn:partition:service:region:account-id:resource`

- partition (分區)
- service  (AWS 服務)
- region (資源所在的區域代碼)
- account-id (AWS Account ID)
- resource (資源標識符)

# terraform
source terraform-aws-modules/eks/aws 詳細內容點擊參考連結。

- Require Variable
  - name 叢集名稱。
  - role_arn IAM 角色的 ARN，該角色為 Kubernetes 控制平面提供代表您呼叫 AWS API 操作的權限。
  - vpc_config 與叢集關聯的 VPC 的配置區塊。

- cluster_endpoint_public_access

控制 EKS 叢集端點是否可以公開訪問，決定 Kubernetes API 伺服器是否可以從公共網際網路訪問。

- enable_irsa

開 OIDC 等同於 gcp Worklaod Identity 功能。

- cluster_addons

要為叢集啟用的叢集插件配置的對應。插件名稱可以是 map keys 或設定 name

# 參考
[terraform-aws-modules](https://registry.terraform.io/modules/terraform-aws-modules/eks/aws/latest)

[github terraform-aws-modules](https://github.com/terraform-aws-modules/terraform-aws-eks)

[[GKE] Workload Identity 試用小記](https://danielsig727.tw/posts/201907/190720_gke_workload_identity/)