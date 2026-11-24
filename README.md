# 鲸鱼屋 · dsh-whalehouse

把一间隔音的小屋装进 DSH。左上角侧栏多一个「鲸鱼屋」，点开就是一整页聊天窗，跟**你自己的 AI** 说话。

入口在 `sidebar.panellist`（DSH 侧栏图标列表），`order: 2`，排在插件市场下面、日程上面。点开是中间列整页，左侧栏和会话都还在，随时切走。
1.4.0 之前是右下角的浮标加全屏遮罩；现在是侧栏一行，切到别的侧栏项时 iframe 不卸载，切回来聊天记录还在。

包里**不带任何人设卡**：屋里的人是谁、叫什么、什么脾气，装的人自己在页面里写；说话用的是他那台 DSH 上正在跑的模型（`/whalehouse/llm`），不是谁预设的设定。

> 反馈、报 bug：QQ 群 **1135328051**

## 它是什么

一个 DSH 插件，三部分：

- 宿主侧 `index.js`：起和管理鲸鱼屋进程，注册 `/whalehouse/{status,llm,restart,open,peer,feedback,update…}` 路由，Windows 下顺手生成桌面快捷方式
- 客户端 `client.js`：往 `sidebar.panellist` 注册一行入口（`id: whalehouse`、`order: 2`），往 `main` keyed 槽注册整页面板
- `app/server.mjs`：屋子本体，独立 node 进程，端口随机；`/whalehouse/open` 是固定入口，302 到当前端口并现取 pin

手机打开同一个页面，浏览器里「添加到主屏幕」，走 `manifest.webmanifest`（standalone，有自己的图标）。

## 屋子里的东西

- **聊天**：先落盘再回填，切页不丢对话；断线重连能捞回进行中的回合
- **屋里的人**：最多 8 个，各配名字、头像、颜色、设定文字、会不会自己开口、在不在群里说话
- **公园**：AI 自己溜达换区，天气面板（晴阴雨加灯），发图气泡
- **衣柜、物品**：11 槽位同槽替换、215 件目录 tag 全覆盖、回收箱（日封顶，钱没进账自动回滚）
- **情绪**：十维情绪引擎本地跑（`engine/`），情绪组合出语气和动作自动档；功能面板有情绪小卡
- **生活**：吃饭睡觉、日记、相册、养成数值
- **小游戏**：RPG、gal、音乐播放器、桌面挂件
- **画画**：接 ComfyUI，投单带配方（底模、步数、cfg、尺寸、放大、负向、正面模板、seed、LoRA 链）
- **语音**：ASR、按句切分、预取、Live2D 动作联动
- **记忆**：四态召回、作废索引不删原文、超长段先压再丢、按月归档
- **本体、分身**：发版那台是 `host`，别的机器填三个地址就自动推反馈、拉更新包（sha256 校验、备份、覆盖）

## 装

```bash
mkdir -p ~/.dsh/profiles/<profile>/node_modules/dsh-whalehouse
# 解压 whalehouse-<版本>.zip 到这个目录
# 那个 profile 的 package.json 里挂上：
#   "dsh": { "profile": { "bundles": [ ..., "dsh-whalehouse" ] } }
# 重启 DSH，或 plugin_manager set_bundle dsh-whalehouse enabled=true
```

装好之后左上角侧栏出现「鲸鱼屋」，桌面上多一个 `鲸鱼屋.url`（Windows）。
口令（pin）第一次启动随机生成，在自己那台的 `~/.dsh/whalehouse/config.json` 里。

## 默认值（新装就能用，也不替你做主）

| 项 | 默认 | 说明 |
|---|---|---|
| `host` | `127.0.0.1` | 只在本机开，改 `0.0.0.0` 才对外 |
| `pin` | 首启随机 | 空口令等于门没关，插件自己补 |
| `port` | `0` | 不跟已经跑着的端口抢 |
| `control.allowShell` | `false` | 她能不能在这台机器上跑命令 |
| `control.allowPower` | `[]` | 锁屏、睡眠、关机要哪个写哪个 |
| `onebot.enabled` | `false` | 公园的 OneBot 口子免 pin，默认关 |
| `autoTalk.enabled` | `true` | 她自己开口，这是屋子的意思 |
| `parkRemote.enabled` | `false` | 不连任何人的公园 |
| `dsh.houseCard` | `none` | 屋子不给人设，宿主那台 DSH 的人格说了算 |
| `persona.autoAdopt` / `autoImport` | `false` | 不抄卡、不后台覆盖你的卡 |
| `memBridge` | `false` | 不把别家 agent 的记忆摘要拼进上下文 |

## 隐私

- `_private/`（私人邀请卡和发版者自己的角色卡）永远不打包，`app/roles/` 只剩中性模板
- 发版有一道闸门：压缩前扫全部文本，命中公园域名、QQ 号、屋子口令、本机用户目录、数据盘路径、人格名就拒绝出包
- zip 内路径统一用正斜杠，Windows 打出来也不会让 Mac、Linux 装不上
- 反馈只带 `kind/message/stack/version/platform/page`，不带任何聊天内容

## 反馈

QQ 群 **1135328051**。也可以直接在页面「🐣 分身 · 反馈 · 更新」里提，会推到本体。

## 版本

当前 `1.9.6`，包在 [Releases](https://github.com/sadhuashdu/dsh-whalehouse/releases) 里。
