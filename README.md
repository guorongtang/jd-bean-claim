# 京东京豆 Skill

让具备浏览器操作能力的 AI Agent 帮你领取**购物返豆、每日签到奖励，并核对到账**。

中文操作说明见 [SKILL.md](SKILL.md)。这是平台无关的指令包，不是京东官方产品，也不是无人登录的抢豆脚本。无需上传 Cookie 或配置京东私有接口。

## 安装与使用

将本仓库作为名为 `jd-bean-claim` 的 Skill 文件夹，放入你的 Agent 支持的技能目录。具体目录以该 Agent 的文档为准；没有 Skill 加载机制时，把 [SKILL.md](SKILL.md) 及需要的脚本交给 Agent 读取也可以。

```sh
git clone https://github.com/huntingrin/jd-bean-claim.git
```

向 Agent 发出指令：

> 使用 jd-bean-claim，帮我领取京东购物返豆并完成今天的签到，核对实际到账。

也可以只说“检查待领京豆，不领取”或“只完成今天签到”。首次使用时在 Agent 的浏览器中自行登录京东；遇验证时本人处理。技能不会安装定时任务。

## 兼容性

| 运行能力 | 支持情况 |
| --- | --- |
| 浏览器导航、页面读取、点击 | 必需；通过你的 Agent 现有工具执行 |
| 截图识别 | 图片签到按钮没有可用文字时需要 |
| 页面 JavaScript / evaluate | 可选，用于结构化读取 |
| Node.js | 可选，仅用于导入辅助模块、运行对账和测试 |
| 仅文本聊天、无浏览器工具 | 可以理解流程，不能实际领取 |

不绑定 Ego、Playwright、MCP 或某家模型。实际端到端流程已在一个登录账户的电脑网页与 Ego 浏览器中验证；**其他 Agent/浏览器适配尚未逐一实测**。宿主 Agent 的浏览器权限、登录方式和规则仍然适用。“可移植”不等于每个平台都能零配置运行。

## 可选脚本

- `scripts/collect-state.mjs`：只读提取当前 DOM 的余额、待领项、签到卡片和流水。导出的 `collectBeanState` 是独立页面函数，可传给浏览器 evaluate。选择器变化时需要根据实际页面适配。
- `scripts/reconcile.mjs`：纯函数对账，保留重复流水次数，校验时间窗口和余额变化。不发请求、不领取。

```js
import { collectBeanState } from './scripts/collect-state.mjs';
// page 必须来自你的 Agent 已有且获准使用的浏览器适配器。
const before = await page.evaluate(collectBeanState);
// 按 SKILL.md 观察控件、执行已授权动作，再读取 after。
```

运行纯逻辑测试（Node.js 20 或更新版本）：

```sh
node --test tests/*.test.mjs
```

## 验证和限制

2026-10-12 已验证电脑网页购物返豆领取、图片按钮签到、成功弹层与到账流水核对。公开包不含实际账号、订单、Cookie、截图或执行记录。测试只使用合成数据。

每日奖励与账户资格不同，“最高+66”不是实际奖励保证。京豆用于消费抵扣，不能按现金提现收益计算。登录过期、风控、App专属入口或页面改版可能需要人工处理；点击超时后先对账，不盲目重试。

MIT License.
