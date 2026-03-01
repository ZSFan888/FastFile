# 🚀 FastFile - 文件快传

<div align="center">

![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform](https://img.shields.io/badge/platform-Cloudflare%20Workers-orange.svg)
![Status](https://img.shields.io/badge/status-active-brightgreen.svg)

**基于 Cloudflare Workers + KV 的轻量级文件快速分享工具**

无需服务器 · 无需数据库 · 一键部署 · 全球加速

</div>

---

## 📸 功能预览

| 功能 | 描述 |
|------|------|
| 📤 文件上传 | 拖拽或点击上传，支持多文件同时上传 |
| 🔒 口令保护 | 可设置提取口令，防止文件被陌生人下载 |
| ⏱️ 自定义有效期 | 10分钟 ~ 3天，过期自动删除 |
| 🖼️ 在线预览 | 图片、视频无需下载直接预览 |
| 📱 移动端友好 | 响应式设计，手机电脑均可流畅使用 |
| 🌙 深色模式 | 自动跟随系统深色/浅色模式切换 |
| 📲 二维码分享 | 上传成功后自动生成二维码，手机扫码即可下载 |
| 🛠️ 管理后台 | 支持多 Worker 接口调度，统一管理配额 |
| 🌐 API 接口 | 提供标准 REST API，方便第三方集成 |

---

## ✨ 特点

- **零成本部署**：Cloudflare Workers 免费版每天 10 万次请求，完全够个人使用
- **全球 CDN 加速**：Cloudflare 全球 300+ 节点，无论在哪里访问都很快
- **无需维护**：Serverless 架构，不需要管理服务器、数据库、SSL 证书
- **数据安全**：文件存储在 Cloudflare KV，过期自动清除，不留残留
- **单文件部署**：整个项目只有一个 `worker.js`，极简架构

---

## 📦 文件结构

```
FastFile/
├── worker.js      # 全部代码（Worker 逻辑 + 前端页面）
├── README.md      # 项目说明
└── LICENSE        # MIT 开源协议
```

---

## 🚀 部署教程

### 前置条件

- 一个 [Cloudflare 账号](https://dash.cloudflare.com/sign-up)（免费注册）

### 第一步：创建 KV 命名空间

1. 登录 [Cloudflare Dashboard](https://dash.cloudflare.com)
2. 左侧菜单点击 **Workers & Pages** → **KV**
3. 点击 **Create a namespace**
4. 名称填写 `FILE_STORAGE`，点击 **Add**
5. 记下生成的 **Namespace ID**（后面要用）

### 第二步：创建 Worker

1. 左侧菜单点击 **Workers & Pages** → **Create**
2. 点击 **Create Worker**
3. Worker 名称随意填写（例如 `fastfile`），点击 **Deploy**
4. 进入 Worker 后，点击 **Edit Code**
5. 删除默认代码，将本项目 `worker.js` 的全部内容粘贴进去
6. 点击右上角 **Deploy** 保存部署

### 第三步：绑定 KV 命名空间

1. 进入 Worker 的 **Settings** 页面
2. 找到 **Bindings** → **KV Namespace Bindings**
3. 点击 **Add binding**
4. 填写：
   - **Variable name**：`FILE_STORAGE`
   - **KV namespace**：选择刚才创建的 `FILE_STORAGE`
5. 点击 **Save**

### 第四步：配置环境变量

1. 在 Worker **Settings** 页面
2. 找到 **Variables** → **Environment Variables**
3. 点击 **Add variable**，添加以下两个变量：

| 变量名 | 值 | 说明 |
|--------|-----|------|
| `ADMIN_PASSWORD` | 自定义密码 | 管理后台登录密码，**务必勾选 Encrypt** |
| `API_SECRET` | 自定义密钥 | API 接口调用密钥，**务必勾选 Encrypt** |

4. 点击 **Save and deploy**

### 第五步：访问使用

部署完成后，访问你的 Worker 地址：

```
https://fastfile.你的用户名.workers.dev
```

---

## 📖 使用说明

### 上传文件

1. 打开首页，拖拽文件或点击选择文件
2. 可选填提取口令（不填则无需口令即可下载）
3. 选择有效期（10分钟 ~ 3天）
4. 点击 **开始上传**
5. 上传成功后复制链接或扫描二维码分享

### 下载文件

1. 打开分享链接
2. 如有口令则输入口令
3. 点击 **下载** 或 **预览**

### 管理后台

访问 `/admin` 路径进入管理后台：

```
https://fastfile.你的用户名.workers.dev/admin
```

功能包括：
- 查看今日调用量和剩余配额
- 添加 / 删除子 Worker 接口
- 重置接口今日计数
- 查看 API 使用说明

---

## 🌐 API 文档

### 上传文件

```
POST /api/upload
```

请求头：

```
X-API-Key: 你设置的 API_SECRET
```

请求体（FormData）：

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `files` | File[] | ✅ | 上传的文件，支持多个 |
| `password` | string | ❌ | 提取口令 |
| `expiration` | number | ❌ | 有效期（分钟），默认 60 |

响应示例：

```
{
  "success": true,
  "shareId": "abc123",
  "downloadUrl": "https://xxx.workers.dev/d/abc123",
  "expirationTime": 1709999999000,
  "fileCount": 1
}
```

### 下载文件

```
GET /api/download/{shareId}?fileId={fileId}&password={password}
```

---

## ⚙️ 配置说明

在 `worker.js` 顶部可修改以下配置：

```
const MAX_FILE_SIZE = 20 * 1024 * 1024;  // 单文件最大限制，默认 20MB
const MAX_DAILY_DEFAULT = 200;            // 每个接口每日默认配额
```

---

## 📋 路由说明

| 路由 | 方法 | 说明 |
|------|------|------|
| `/` | GET | 文件上传页面 |
| `/upload` | POST | 上传接口 |
| `/d/{shareId}` | GET | 文件下载页面 |
| `/download/{shareId}` | GET | 下载文件内容 |
| `/delete/{shareId}` | DELETE | 删除分享 |
| `/admin` | GET | 管理后台页面 |
| `/admin/login` | POST | 管理员登录 |
| `/admin/endpoints` | GET/POST | 获取/添加子接口 |
| `/api/upload` | POST | 对外 API 上传 |
| `/api/download/{id}` | GET | 对外 API 下载 |

---

## 🛠️ 技术栈

| 技术 | 说明 |
|------|------|
| Cloudflare Workers | Serverless 运行时，处理所有请求逻辑 |
| Cloudflare KV | 键值存储，用于保存文件数据和分享信息 |
| 原生 HTML/CSS/JS | 前端页面，无任何第三方框架依赖 |
| QRCode.js | 二维码生成库 |

---

## ⚠️ 注意事项

- 单文件最大 **20MB**（受 Cloudflare Workers 限制）
- 文件以 Base64 编码存储在 KV，实际占用空间约为原文件的 **1.33 倍**
- Cloudflare KV 免费版存储上限为 **1GB**
- Workers 免费版每天 **10 万次**请求，超出后需升级付费版
- 建议不要用于存储敏感数据

---

## 📄 License

本项目基于 [MIT License](LICENSE) 开源，欢迎 Fork 和 Star ⭐

---

<div align="center">

Made with ❤️ by ZSFan

</div>
