# 晨会记录 · Morning Brief

面向证券销售的 AI 工作台。粘贴晨会纪要或研报，由 DeepSeek 提炼成可直接发送微信群的摘要；另含存储板块专项分析与私人投资日记。

**在线地址**：https://yangaikaka.github.io/morning-meeting/

---

## 功能

**📋 晨会** — 输入晨会资料，AI 按行业、个股分层提炼核心观点，重点保留行业空间测算与估值依据，上市公司自动标注股票代码，输出为无 Markdown 符号的纯文本，便于直接转发。

**💾 存储热点** — 针对存储行业（NAND / DRAM / HBM / SSD / 存储控制芯片）的专项分析，输出板块概览、个股订单情况、市场观点、目标价与估值、催化剂与风险。

**📔 投资日记** — 记录个人投资心得，可调用 AI 以资深投资家视角对全部历史心得作综合点评。查看历史需密码。

三个标签页的历史记录均存于云端，多设备共享。

输入支持粘贴文字与上传文档（TXT、文字版 PDF 经 PDF.js 解析、图片交由 AI 识别）。

---

## 技术架构

```
GitHub Pages (静态页面)
        │
        ├──→ DeepSeek API        文本分析，Key 存用户浏览器本地
        │
        └──→ 阿里云函数计算 FC ──→ 阿里云 OSS
                （鉴权中转）        （数据存储）
```

前端为单个 HTML 文件，无框架、无构建步骤，CSS 与 JS 全部内联。

数据读写经由函数计算中转，OSS 的 AccessKey 保存在函数环境变量中，前端不含任何云服务凭证。函数侧设有文件名白名单与 CORS 来源限制。

DeepSeek API Key 由用户自行填写，仅保存在各自浏览器的 localStorage，不上传服务器。

---

## 数据存储

OSS Bucket 内四个 JSON 文件：

| 文件 | 内容 |
|---|---|
| `morning.json` | 晨会摘要历史 |
| `storage.json` | 存储热点分析历史 |
| `journal.json` | 投资日记 |
| `visitor.json` | 访问统计 |

后端接口：

- `GET /read?file=<name>` — 读取，文件不存在时返回 `{"records":[]}`
- `POST /write` — 写入，body 为 `{ file, data }`

---

## 部署

**前端**：将 `index.html` 推送至本仓库，GitHub Pages 自动发布。

**后端**：阿里云函数计算 Web 函数（Node.js 20），需配置以下环境变量：

```
OSS_REGION       OSS 地域，如 oss-cn-chengdu
OSS_BUCKET       Bucket 名称
OSS_KEY_ID       RAM 用户 AccessKey ID
OSS_KEY_SECRET   RAM 用户 AccessKey Secret
```

函数最小实例数设为 0 以节省费用，首次访问存在冷启动延迟，前端已内置退避重试。

---

## 相关项目

- [存储芯片监控台](https://yangaikaka.github.io/memory-watch/)
- [Alibaba](https://yangaikaka.github.io/Alibaba/)

---

## 说明

本项目输出内容由 AI 生成，仅供内部整理参考，不构成任何投资建议。
