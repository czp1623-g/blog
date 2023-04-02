---
title: Setup 相关
date: 2023-04-02 18:48:08
tags:
---

# Set up

### **主题**

**官方主题列表**

- https://hexo.io/themes/
- http://jekyllthemes.org/

**感觉还不错的**

- https://yunyoujun.github.io/

### 一些问题

**win 脚本执行**

```powershell
# 允许您在本地计算机上运行自己编写的脚本
Set-ExecutionPolicy RemoteSigned
# 将禁止在本地计算机上运行自己编写的脚本
Set-ExecutionPolicy Restricted
```

**Github 连接问题**

```powershell
# 10809是v2rayN的代理端口
git config --global http.proxy http://127.0.0.1:10809
git config --global https.proxy http://127.0.0.1:10809
```

