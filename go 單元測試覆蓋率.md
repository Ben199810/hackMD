---
title: go 單元測試覆蓋率
tags: [talkto]

---

# go 單元測試覆蓋率

```shell
go test ./... -coverprofile=coverage.out 2>&1 | go-junit-report > report.xml
```
上述的指令，因為 gitlab ci 在執行時連續的指令不會印出錯誤。所以要先改成以下。

```shell
go test ./... -coverprofile=coverage.out 2>&1
```
之後就可以看到以下錯誤

![image](https://hackmd.io/_uploads/SJRroweM0.png)
RD 說這個錯誤他們之前有遇過，api/docs 這個錯誤是因為 `swag` 這個套件產生的

所以在執行 go test 之前先安裝套件。
```shell
go intsall github.com/swaggo/swag/cmd/swag@latest
```



###### tags: `talkto`