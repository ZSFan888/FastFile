# FastFile 文件快传

一个基于 Cloudflare Workers + KV 的轻量文件分享工具。

## ✨ 功能特点

- 📁 支持最大 20MB 文件上传
- 🔑 可设置提取口令保护
- ⏱️ 自定义有效期（最长 3 天）
- 🖼️ 图片 / 视频在线预览
- 📱 响应式设计，手机友好
- 🌙 自动适配深色模式
- 🔗 生成二维码便于分享
- 🛠️ 管理后台支持多接口调度

## 🚀 部署方法

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com)
2. 进入 **Workers & Pages** → 新建 Worker
3. 将 `worker.js` 代码复制粘贴进编辑器
4. 创建 **KV 命名空间**，绑定变量名为 `FILE_STORAGE`
5. 在 **Settings → Variables** 添加环境变量：
   - `ADMIN_PASSWORD`：管理员密码（勾选 Encrypt）
   - `API_SECRET`：API 密钥（勾选 Encrypt）
6. 保存并部署

## 📖 使用说明

| 路径 | 说明 |
|------|------|
| `/` | 文件上传页面 |
| `/d/{shareId}` | 文件下载页面 |
| `/admin` | 管理后台 |
| `/api/upload` | API 上传接口 |
| `/api/download/{shareId}` | API 下载接口 |

## 🛠️ 技术栈

- Cloudflare Workers（无服务器运行时）
- Cloudflare KV（文件存储）
- 原生 HTML / CSS / JavaScript（无框架）

## 📄 License

[MIT](LICENSE)
