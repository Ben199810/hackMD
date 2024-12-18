---
title: Ingress-nginx setting gcsProxy point to GCS
tags: [k8s, gcp, ingress-nginx, proxy]

---

# 建立 ExternalName Type Service
external 的 service 可以協助 ingress-nginx 訪問外部域名。

```yaml
kind: Service
apiVersion: v1
metadata:
  name: proxy-to-gcs
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  type: ExternalName
  externalName: storage.googleapis.com
```

# 建立 Ingress for Proxy GCS Buckets
`nginx.ingress.kubernetes.io/upstream-vhost` 控制 Host 的值，在 location block 設定以下設定。`proxy_set_header Host $host`

`nginx.ingress.kubernetes.io/secure-backends`使用具有安全後端的 ingress-nginx。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: gcs-asset
  namespace: ingress-nginx
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /[BUCKET NAME]
    nginx.ingress.kubernetes.io/secure-backends: "true"
    nginx.ingress.kubernetes.io/upstream-vhost: "storage.googleapis.com"
spec:
  rules:
    - http:
        paths:
          - path: /*
            backend:
              serviceName: google-storage-buckets-service
              servicePort: 443
          - path: /c/
            backend:
              serviceName: hello-world-service
              servicePort: 8080
```

# 參考
[Use NGINX ingress controller with GCS bucket as a backend](https://github.com/kubernetes/ingress-nginx/issues/1809)

[Kubernetes ingress-nginx LoadBalancer pointing to cloud bucket](https://stackoverflow.com/questions/55779048/kubernetes-ingress-nginx-loadbalancer-pointing-to-cloud-bucket)

[How to use ingress-nginx with secure backend?](https://discuss.kubernetes.io/t/how-to-use-ingress-nginx-with-secure-backend/7347/1)

[Custom NGINX upstream vhost](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#custom-nginx-upstream-vhost)

[Backend Protocol](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/#backend-protocol)