---
title: 將 Kubernetes ServiceAccount 關聯到 GCP ServiceAccount
tags: [gcp, iam, wi]

---

1. Create K8S KSA

```shell
kubectl create serviceaccount <KSA_NAME> --namespace <NAMESPACE>
```

2. Create GSA

```shell
gcloud iam service-accounts create <IAM_SA_NAME> \
    --project=<IAM_SA_PROJECT_ID>
```

3. 建立 IAM 允許政策，以授予 Kubernetes ServiceAccount 模擬 GCP ServiceAccount 服務帳號的權限

```shell
gcloud iam service-accounts add-iam-policy-binding <IAM_SA_NAME>@<IAM_SA_PROJECT_ID>.iam.gserviceaccount.com \
    --role roles/iam.workloadIdentityUser \
    --member "serviceAccount:<PROJECT_ID>.svc.id.goog<NAMESPACE/KSA_NAME>"
```

4. Check GCP ServiceAccount bindings Kubernetes ServiceAccount

```shell
gcloud iam service-accounts get-iam-policy <GSA_NAME>@<PROJECT_ID>.iam.gserviceaccount.com
```

# 參考
[從GKE 工作負載向Google Cloud API 進行驗證](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity?hl=zh-cn)