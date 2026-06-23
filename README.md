# Apple Refurb Watcher

自托管仪表板，监控 Apple 官方翻新商店的产品库存变化。支持**中国区**及 9 个其他国家，覆盖 **Mac / iPad / iPhone / Watch / 配件** 五个品类。

> 基于 [drajvver/apple-refurb-watcher](https://github.com/drajvver/apple-refurb-watcher)，新增中国区支持和多品类监控。

## 功能

- 🌍 **10 个国家** — 中国（默认）、波兰、美国、英国、德国、法国、西班牙、意大利、加拿大、澳大利亚
- 📦 **5 个品类** — Mac、iPad、iPhone、Watch、配件
- 🟢 **新品上架** — 绿色标记新出现的产品
- 🔴 **已下架** — 红色标记不再可用的产品
- 🟡 **价格变动** — 黄色标记价格变化，显示原价
- 🏷️ **智能筛选** — 按品类、型号、屏幕尺寸、芯片、内存、存储、颜色筛选
- ⏱️ **自动刷新** — 可配置 15 分钟到 6 小时的自动抓取间隔
- 🌙 **深色模式** — 自动适应系统主题
- 💰 **含税/不含税** — 一键切换价格显示方式（中国区增值税 13%）
- 🐳 **Docker 支持** — 一条命令即可部署

## 快速开始

### 方式一：开发模式（WSL / Linux / macOS）

要求 Node.js >= 20.9

```bash
npm install
npm run dev
```

浏览器打开 `http://localhost:3000`。

### 方式二：Docker

```bash
docker build -t apple-refurb-watcher .
docker run -d --name apple-watcher -p 3000:3000 -v apple-data:/app/data apple-refurb-watcher
```

### 方式三：Windows + WSL

```bash
# 进入 WSL
wsl

# 安装 Node.js 22.x
curl -fsSL https://deb.nodesource.com/setup_22.x | bash -
apt install -y nodejs

# 启动
cd /mnt/d/projects/apple-refurb-watcher
npm install
npm run dev
```

## 使用说明

1. 打开 `http://localhost:3000`，默认显示**中国区全部品类**翻新产品
2. 选择**地区**和**品类**，点击 **Refresh** 抓取数据
3. 首次运行建立基线（不触发变动通知），后续抓取开始检测变化
4. 开启 **Auto** 并选择间隔时间，即可后台自动监控
5. 使用品类下拉框和规格标签快速筛选目标产品

## 支持的地区

| 地区 | 代码 | 货币 | 域名 |
|------|------|------|------|
| 中国 | `cn` | CNY (¥) | apple.com.cn |
| 波兰 | `pl` | PLN | apple.com |
| 美国 | `us` | USD | apple.com |
| 英国 | `uk` | GBP | apple.com |
| 德国 | `de` | EUR | apple.com |
| 法国 | `fr` | EUR | apple.com |
| 西班牙 | `es` | EUR | apple.com |
| 意大利 | `it` | EUR | apple.com |
| 加拿大 | `ca` | CAD | apple.com |
| 澳大利亚 | `au` | AUD | apple.com |

## 支持的品类

| 品类 | URL 路径 |
|------|----------|
| Mac | `/shop/refurbished/mac` |
| iPad | `/shop/refurbished/ipad` |
| iPhone | `/shop/refurbished/iphone` |
| Watch | `/shop/refurbished/watch` |
| 配件 | `/shop/refurbished/accessories` |

## 项目结构

```
src/
├── app/
│   ├── api/
│   │   ├── refresh/route.ts    # 手动刷新 API
│   │   └── settings/route.ts   # 自动刷新设置 API
│   ├── globals.css
│   ├── layout.tsx
│   └── page.tsx                # 主页面（服务端组件）
├── components/
│   ├── Dashboard.tsx           # 仪表板 UI
│   └── ThemeToggle.tsx         # 暗色模式切换
└── lib/
    ├── config.ts               # 地区 & 品类配置
    ├── types.ts                # TypeScript 类型定义
    ├── scraper.ts              # Apple 商店抓取（JSON + Cheerio 双策略）
    ├── watcher.ts              # 变动检测 & 状态持久化
    ├── scheduler.ts            # 后台自动刷新调度
    └── settings.ts             # 设置读写
```

## 抓取原理

1. 请求 Apple 翻新商店页面（带 Chrome UA 和对应地区的 Accept-Language）
2. 优先从页面嵌入式 JSON（`window.REFURB_GRID_BOOTSTRAP`）提取结构化产品数据
3. 如 JSON 不可用，回退到 Cheerio DOM 解析
4. 通过零件编号（part number）对比新旧快照，检测新增、下架、价格变动
5. 状态持久化到 `data/state-{country}.json`，无需外部数据库

## 数据存储

所有数据保存在 `data/` 目录（Docker 中为 `/app/data`）的 JSON 文件中：

- `state-{country}.json` — 每个国家的产品快照和变动记录
- `settings.json` — 自动刷新配置

## 技术栈

- [Next.js 16](https://nextjs.org/) (App Router / Turbopack)
- [React 19](https://react.dev/)
- [TypeScript](https://www.typescriptlang.org/)
- [Tailwind CSS v4](https://tailwindcss.com/)
- [Cheerio](https://cheerio.js.org/)（HTML 回退解析）

## 免责声明

本项目是**非官方工具**，与 Apple Inc. 无任何关联。所有数据来自 Apple 公开页面，仅供个人学习研究使用。请遵守 Apple [服务条款](https://www.apple.com/legal/internet-services/terms/site.html)。

## License

MIT
