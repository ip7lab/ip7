# ip7-overlay — 线上反代层与静态页（非镜像内容）

本站源码构建的镜像之外，线上还有一层 OpenResty 反代配置与静态页，放在这里备查。它们不进应用镜像，随反代配置部署。

## 文件

- `openresty/ip7.de5.net.conf`：线上 OpenResty vhost（80/443）。含：反代到应用容器回环端口（当前为 18968，随容器端口变化而调整）、ACME 与 ZeroSSL 验证路径、场景页与隐私页静态路由、首页底部品牌横条注入（场景入口、隐私入口、联盟返佣披露、AGPL 声明与源代码链接）。证书文件路径仅作位置说明，证书与私钥本身只存于部署服务器，不进本仓库。
- `scenarios/*.html`：四个静态场景页（跨境电商注册、AI 工具、金融与加密账户、境外电话办理）。
- `privacy.html`：隐私政策页。

## 部署位置（悉尼机）

- vhost：`/opt/1panel/www/conf.d/ip7.de5.net.conf`（改动后 `nginx -t` 通过再重载）。
- 静态页：`/opt/1panel/www/sites/ip7.de5.net/index/scenarios/` 与 `/opt/1panel/www/sites/ip7.de5.net/index/privacy.html`。
- 应用容器：源码构建镜像，见仓库 README「上游与部署版本」一节；反代目标端口跟随当前容器端口。

## 密钥与隐私

本目录不含任何密钥凭据：MaxMind 密钥、环境变量、证书私钥一律不在内；证书文件只存于部署服务器。
