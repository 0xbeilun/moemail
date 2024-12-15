<p align="center">
  <img src="public/icons/icon-192x192.png" alt="MoeMail Logo" width="100" height="100">
  <h1 align="center">MoeMail</h1>
</p>

<p align="center">
  一个基于 NextJS + Cloudflare 技术栈构建的可爱临时邮箱服务🎉
</p>

<p align="center">
  <a href="#在线演示">在线演示</a> •
  <a href="#特性">特性</a> •
  <a href="#技术栈">技术栈</a> •
  <a href="#本地运行">本地运行</a> •
  <a href="#部署">部署</a> •
  <a href="#贡献">贡献</a> •
  <a href="#许可证">许可证</a> •
  <a href="#交流群">交流群</a> •
  <a href="#支持">支持</a>
</p>

## 在线演示
[https://moemail.app](https://moemail.app)

![首页](https://pic.otaku.ren/20241209/AQADwsUxG9k1uVZ-.jpg "首页")


![邮箱](https://pic.otaku.ren/20241209/AQADw8UxG9k1uVZ-.jpg "邮箱")

## 特性

- 🔒 **隐私保护**：保护您的真实邮箱地址，远离垃圾邮件和不必要的订阅
- ⚡ **实时收件**：自动轮询，即时接收邮件通知
- ⏱️ **灵活过期**：支持 1 小时、24 小时或 3 天的过期时间选择
- 🎨 **主题切换**：支持亮色和暗色模式
- 📱 **响应式设计**：完美适配桌面和移动设备
- 🔄 **自动清理**：自动清理过期的邮箱和邮件
- 📱 **PWA 支持**：支持 PWA 安装
- 💸 **免费自部署**：基于 Cloudflare 构建, 可实现免费自部署，无需任何费用
- 🎉 **可爱的 UI**：简洁可爱萌萌哒 UI 界面

## 技术栈

- **框架**: [Next.js](https://nextjs.org/) (App Router)
- **平台**: [Cloudflare Pages](https://pages.cloudflare.com/)
- **数据库**: [Cloudflare D1](https://developers.cloudflare.com/d1/) (SQLite)
- **认证**: [NextAuth](https://authjs.dev/getting-started/installation?framework=Next.js) 配合 GitHub 登录
- **样式**: [Tailwind CSS](https://tailwindcss.com/)
- **UI 组件**: 基于 [Radix UI](https://www.radix-ui.com/) 的自定义组件
- **邮件处理**: [Cloudflare Email Workers](https://developers.cloudflare.com/email-routing/)
- **类型安全**: [TypeScript](https://www.typescriptlang.org/)
- **ORM**: [Drizzle ORM](https://orm.drizzle.team/)

## 本地运行

### 前置要求

- Node.js 18+
- pnpm
- Wrangler CLI
- Cloudflare 账号

### 安装

1. 克隆仓库：
```bash
git clone https://github.com/beilunyang/moemail.git
cd moemail
```

2. 安装依赖：
```bash
pnpm install
```

3. 设置 wrangler：
```bash
cp wrangler.example.toml wrangler.toml
cp wrangler.email.example.toml wrangler.email.toml
cp wrangler.cleanup.example.toml wrangler.cleanup.toml
```
并设置 Cloudflare D1 数据库名以及数据库 ID

4. 设置环境变量：
```bash
cp .env.example .env.local
```
设置 AUTH_GITHUB_ID, AUTH_GITHUB_SECRET, AUTH_SECRET

5. 创建本地数据库表结构
```bash
pnpm db:migrate-local
```

### 开发

1. 启动开发服务器：
```bash
pnpm dev
```

2. 测试邮件 worker：
目前无法本地运行并测试，请使用 wrangler 部署邮件 worker 并测试
```bash
pnpm deploy:email
```

3. 测试清理 worker：
```bash
pnpm dev:cleanup
pnpm test:cleanup
```

4. 生成 Mock 数据（邮箱以及邮件消息）
```bash
pnpm generate-test-data
```

## 部署

### 本地 Wrangler 部署

1. 设置 wrangler：
```bash
cp wrangler.example.toml wrangler.toml
cp wrangler.email.example.toml wrangler.email.toml
cp wrangler.cleanup.example.toml wrangler.cleanup.toml
```
设置 Cloudflare D1 数据库名以及数据库 ID

2. 创建云端 D1 数据库表结构
```bash
pnpm db:migrate-remote
```

2. 部署主应用到 Cloudflare Pages：
```bash
pnpm deploy:pages
```

3. 部署邮件 worker：
```bash
pnpm deploy:email
```

4. 部署清理 worker：
```bash
pnpm deploy:cleanup
```

### Github Actions 部署

本项目可使用 GitHub Actions 实现自动化部署。当推送新的 tag 时会触发部署流程。

1. 在 GitHub 仓库设置中添加以下 Secrets：

- `CLOUDFLARE_API_TOKEN`: Cloudflare API 令牌
- `CLOUDFLARE_ACCOUNT_ID`: Cloudflare 账户 ID
- `DATABASE_NAME`: D1 数据库名称
- `DATABASE_ID`: D1 数据库 ID

2. 创建并推送新的 tag 来触发部署：

```bash
# 创建新的 tag
git tag v1.0.0

# 推送 tag 到远程仓库
git push origin v1.0.0
```

3. GitHub Actions 会自动执行以下任务：

- 构建并部署主应用到 Cloudflare Pages
- 检测并部署更新的 Email Worker
- 检测并部署更新的 Cleanup Worker

4. 部署进度可以在仓库的 Actions 标签页查看

注意事项：
- 确保所有 Secrets 都已正确设置
- tag 必须以 `v` 开头（例如：v1.0.0）
- 只有推送 tag 才会触发部署，普通的 commit 不会触发
- 如果只修改了某个 worker，只会部署该 worker

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/beilunyang/moemail)


### 初次部署完成后
初次通过本地 Wrangler 或者 Github Actions 部署完成后，请登录到 Cloudflare 控制台，添加 AUTH 认证 相关 SECRETS
- 登录 [Cloudflare 控制台](https://dash.cloudflare.com/) 并选择你的账户
- 选择 Workers 和 Pages
- 在 Overview 中选择刚刚部署的 Cloudflare Pages
- 在 Settings 中选择变量和机密
- 添加 AUTH_GITHUB_ID, AUTH_GITHUB_SECRET, AUTH_SECRET

## 贡献

欢迎提交 Pull Request 或者 Issue来帮助改进这个项目

## 许可证

本项目采用 [MIT](LICENSE) 许可证

## 交流群
<img src="https://pic.otaku.ren/20241215/AQADXcMxGyDw-FZ-.jpg" style="width: 400px;"/>
<br />
如二维码失效，请添加我的个人微信（hansenones），并备注 “MoeMail” 加入微信交流群

## 支持

如果你喜欢这个项目，欢迎给它一个 Star ⭐️
或者进行赞助
<br />
<br />
<img src="https://pic.otaku.ren/20240212/AQADPrgxGwoIWFZ-.jpg" style="width: 400px;"/>
<br />
<br />
<a href="https://www.buymeacoffee.com/beilunyang" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-blue.png" alt="Buy Me A Coffee" style="width: 400px;" ></a>
