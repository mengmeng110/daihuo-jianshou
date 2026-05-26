# 带货剪手 Railway 部署指南

## 📋 前置准备

- GitHub 账号（用来 Fork 项目）
- Railway 账号（用 GitHub 登录即可，免费 $5/月额度）

---

## 第一步：Fork 项目到你的 GitHub

1. 打开 https://github.com/witty-suckerpunch492/daihuo-jianshou
2. 点击右上角 **Fork** 按钮
3. 选择你的 GitHub 账号，点击 **Create fork**
4. 等待几秒，项目就复制到你的仓库了

---

## 第二步：注册 Railway

1. 打开 https://railway.app
2. 点击 **Login** → 选择 **Login with GitHub**
3. 授权 Railway 访问你的 GitHub 仓库

---

## 第三步：部署项目

1. 登录 Railway 后，点击 **New Project**
2. 选择 **Deploy from GitHub Repo**
3. 找到你刚才 Fork 的 `daihuo-jianshou` 仓库，点击它
4. Railway 会自动检测到 `Dockerfile`，开始构建部署
5. 等待 3-5 分钟构建完成

---

## 第四步：添加持久化存储（重要！）

SQLite 数据库需要持久化，否则每次重新部署数据会丢失。

1. 在项目页面，点击 **+ New** → **Volume**
2. 设置挂载路径为 `/app/data`
3. 点击 **Deploy**

---

## 第五步：配置环境变量

在项目页面，点击你的服务 → **Variables** 标签，添加：

| 变量名 | 说明 | 示例 |
|--------|------|------|
| `DATABASE_URL` | SQLite 数据库路径 | `file:/app/data/sqlite.db` |
| `OPENAI_API_KEY` | OpenAI API Key（脚本生成用） | `sk-xxx` |
| `FAL_API_KEY` | fal.ai API Key（图片/视频生成） | `xxx` |

> 💡 至少配置一个 AI 平台的 API Key 才能使用。推荐 fal.ai，模型最全。

---

## 第六步：获取访问地址

1. 部署完成后，在项目页面点击你的服务
2. 点击 **Settings** → **Networking** → **Generate Domain**
3. Railway 会生成一个 `xxx.up.railway.app` 的域名
4. 手机浏览器直接访问这个 URL 就能用了！

---

## ⚠️ 注意事项

1. **免费额度**：Railway 每月 $5 免费额度，个人使用够了
2. **休眠机制**：免费版 30 分钟无请求会休眠，首次访问需要等 10-30 秒冷启动
3. **数据库备份**：定期在 Railway 控制台导出 Volume 数据备份
4. **API 费用**：AI 模型调用费用另计，由各平台（fal.ai 等）收取

---

## 🚀 快速命令（本地测试）

如果想先在本地测试：

```bash
cd daihuo-jianshou
pnpm install
pnpm dev
# 访问 http://localhost:3000
```

---

## 常见问题

**Q: 构建失败怎么办？**
A: 检查 Railway 的 Deploy Logs，通常是依赖安装问题。确保 Dockerfile 中的 `pnpm install --frozen-lockfile` 能正常执行。

**Q: FFmpeg 找不到？**
A: Dockerfile 已经安装了 FFmpeg。如果报错，检查 Railway 的 Shell 中执行 `ffmpeg -version` 是否正常。

**Q: 数据库报错？**
A: 确保 Volume 已正确挂载到 `/app/data`，且 `DATABASE_URL` 环境变量已设置。
