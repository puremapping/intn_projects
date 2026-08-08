# intn_projects 仓库维护教训

> 记录维护本仓库（git / GitHub）踩过的坑，随仓库同步。
> by puremapping & pi

## 教训 1：git 身份缺失（2026-08-08）

- 现象：commit 报 `Author identity unknown`
- 原因：仓库未配置 user.name/email，全局也未配置
- 结论：仓库级配置 `git config user.name "zhe"` / `git config user.email "15963919133@qq.com"`，沿用历史提交身份

## 教训 2：GitHub 直连被重置，走 Clash 混合端口代理（2026-08-08）

- 现象：`git push` 报 `Connection was reset`；TUN 模式下 curl github.com 仍超时（baidu 正常、google/github 不通）
- 原因：国内网络直连 GitHub 不稳定；TUN 分流对 GitHub 走了直连而失败
- 结论：git 仓库级配置 `git config http.proxy http://127.0.0.1:7897`（Clash 混合端口，需 GUI 运行后才监听）。**TUN 已开时仍显式端口代理最稳**

## 教训 3：push 的 libpng 警告是无害噪音（2026-08-08）

- 现象：push 时刷大量 `libpng warning: iCCP: known incorrect sRGB profile`
- 原因：Git for Windows HTTPS 层加载 GitHub 资源图标所致，与提交内容无关
- 结论：无害，过滤输出（`grep -v libpng`）即可；**push 是否成功以 `git ls-remote origin master` 与本地 HEAD 比对确认**（或再次 push 显示 Everything up-to-date）
