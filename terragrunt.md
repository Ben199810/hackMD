---
title: terragrunt
tags: [terraform]

---

# terragrunt
terragrunt 是一套可以更簡潔的管理 terraform 的套件。

Terragrunt 允許您在 terragrunt.hcl 中的任何位置使用[內建函數](https://terragrunt.gruntwork.io/docs/reference/built-in-functions/)，就像 Terraform 一樣！

## get_path_to_repo_root()
回傳 git 儲存庫的根目錄的相對路徑
```
terraform {
  source = "${get_path_to_repo_root()}/modules/gke"
}
```

## find_in_parent_folders()
在父資料夾中尋找 `terragrunt.hcl`，如果找不到此類文件，則返回錯誤並退出

###### tags: `terraform`