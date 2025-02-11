---
title: IAM WorkLoad Identity
tags: [gcp, iam, wi]

---

# 將 Kubernetes ServiceAccount 關聯到 IAM

K8S 中建立 KSA

```shell
kubectl create serviceaccount <KSA_NAME> --namespace <NAMESPACE>
```

建立 IAM 服務帳號

```shell
gcloud iam service-accounts create <IAM_SA_NAME> \
    --project=<IAM_SA_PROJECT_ID>
```

建立 IAM 允許政策，以授予 Kubernetes ServiceAccount 模擬IAM 服務帳號的權限

```shell
gcloud iam service-accounts add-iam-policy-binding <IAM_SA_NAME>@<IAM_SA_PROJECT_ID>.iam.gserviceaccount.com \
    --role roles/iam.workloadIdentityUser \
    --member "serviceAccount:<PROJECT_ID>.svc.id.goog<NAMESPACE/KSA_NAME>"
```

查看 IAM Service Account bindings KSA

```shell
gcloud iam service-accounts get-iam-policy <GSA_NAME>@<PROJECT_ID>.iam.gserviceaccount.com
```

# 參考
[從GKE 工作負載向Google Cloud API 進行驗證](https://cloud.google.com/kubernetes-engine/docs/how-to/workload-identity?hl=zh-cn)