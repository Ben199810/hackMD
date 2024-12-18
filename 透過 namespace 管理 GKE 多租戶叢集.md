---
title: 透過 namespace 管理 GKE 多租戶叢集
tags: [gcp]

---

# 建立 namespace
```shell
kubectl create namespace <team-a>
kubectl create namespace <team-b>
```

# 部署服務
```shell
kubectl run app-server --image=centos --namespace=team-a -- sleep infinity
kubectl run app-server --image=centos --namespace=team-b -- sleep infinity
```

# 服務帳戶授予角色權限
```shell
gcloud projects add-iam-policy-binding ${GOOGLE_CLOUD_PROJECT} --member=serviceAccount:team-a-dev@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com --role=roles/container.clusterViewer
```

# RBAC
在叢集內，任何資源類型(Pod、服務、部署作業等)的存取權都是透過角色或叢集角色加以定義，其中只有角色可套用於命名空間。角色可指定資源及每項資源適用的動作，而角色繫結則會指定向哪些使用者帳戶或群組指派該權限。

:::success
kubectl api-resources 將列出所有支援的資源類型和 api-group
:::

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: team-a
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods", "services", "serviceaccounts"]
  verbs: ["update", "create", "delete", "get", "watch", "list"]
- apiGroups:["apps"]
  resources: ["deployments"]
  verbs: ["update", "create", "delete", "get", "watch", "list"]
```

# Google serviceAccount 與 role 建立 roleBinding
```shell
kubectl create rolebinding team-a-developers --role=developer --user=team-a-dev@${GOOGLE_CLOUD_PROJECT}.iam.gserviceaccount.com
```

# 參考
[kubenetes resource-type](https://kubernetes.io/zh-cn/docs/reference/kubectl/#resource-types)