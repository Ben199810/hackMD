---
title: GO 單元測試整合 CICD
tags: [cicd, go]

---

# GO 單元測試整合 CICD
產生覆蓋率文件
```shell
go test -v ./... -coverprofile=coverage.out
```

coverage.out
```go
mode: set
go_traning/unit_test/db/get_name.go:15.51,18.2 1 0
go_traning/unit_test/db/get_name.go:20.39,22.2 1 1
go_traning/unit_test/db/get_name_mock.go:25.49,29.2 3 1
go_traning/unit_test/db/get_name_mock.go:32.47,34.2 1 1
go_traning/unit_test/db/get_name_mock.go:37.51,42.2 4 1
go_traning/unit_test/db/get_name_mock.go:45.78,48.2 2 1
go_traning/unit_test/validator/validator.go:5.33,8.2 2 1
```

轉換為sonar可解讀呈現的檔案格式
需安裝 `gocov` `gocov-xml`
```shell
gocov convert cover.out
gocov-xml > report.xml
```


```shell
go test "./..." -coverprofile="coverage.out" -covermode=count -json > report.json
```


###### tags: `cicd` `go`