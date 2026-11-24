# 二次元版与原版同步规则

二次元版 = 原版玩法 + 动漫外观（模型、皮肤、图标、音效、头像）+ 自己的地图。

- 原版：`windy10v10ai/game`（本地 remote `upstream`），创意工坊 `2307479570`
- 二次元版：`windy10v10ai/anime`（`origin`），创意工坊 `3788870952`，只有 `develop` 分支，正式服从它编译发布

## 以谁为准

| 内容 | 以谁为准 |
|---|---|
| 玩法、数值、代码（`src/`、技能物品逻辑、商店） | 原版 |
| 外观字段（`Model`、`ModelScale`、`AbilityTextureName`、皮肤、音效、粒子） | 二次元版，叠加在原版最新内容上 |
| 地图（`content/maps`、`game/maps`、`overviews`） | 二次元版，地形升级单独做 |
| 原版已删除或改名的技能、物品 | 跟原版删除或改名，二次元版对应的外观改动一起删或改名，不复活旧数值 |
| 两边都改过的二进制编译产物 | 以源文件重新编译的结果为准，没有源文件的取二次元版 |
| 文档、skill（`.claude/`、`AGENTS.md`） | 原版，只保留下面差异清单里的行 |

## 二次元版与原版的差异清单

新增差异时同步更新本清单。

- `package.json` 的 `name` 为 `windyanime`（本地 addon 目录 `dota_addons/windyanime`，与原版互不冲突）
- 地图：`content/maps/**`、`game/maps/*.vpk`、`content/maps/prefabs/anime/`、`game/resource/overviews/dota.txt`
- 4 个 KV 的外观字段：`npc_abilities_custom.txt`、`npc_abilities_override.txt`、`npc_heroes_custom.txt`、`npc_units_creep.txt`（小兵 `ModelScale` 为原版的 1.26 倍）
- 4 个头像映射脚本：`replaceHeroImage.js`、`end_screen_2.js`、`multiteam_hero_select_overlay.js`、`flyout_scoreboard/shared_scoreboard_updater.js`
- 素材：动漫模型、材质、音效、头像视频（`game/panorama/videos/heroes/`）、技能图标（`spellicons/custom/windyanime/`）
- 加载界面互跳按钮：二次元版显示「10v10AI 原版」，指向 `2307479570`
- `.gitignore`：粒子编译产物 `*.vpcf_c` 不进仓库，只有没有源文件的几个成品单独放行
- `AGENTS.md` 多一行 `online-hero-assets`

## 同步流程

1. 从 `develop` 切 `feature/<issue>-sync-upstream`，`git merge upstream/develop`（不 rebase、不 squash）
2. 按「以谁为准」解决冲突，拿不准的写进 PR「待你决定」
3. `npm run build`、`npm test`、`npm run lint` 通过，Tools 整体编译后进图验证（商店、模型、加载界面）
4. 编译产物和合并放在同一个 PR
5. 同步 PR 用 **merge commit** 合并，保留原版提交历史；审查后追加的修改另开 PR，用 squash 合并

## 本地环境

- `src/vscripts/api/api-client.local.ts`：仓库里放原版的空占位，本地用与原版同一把 key 并设 `skip-worktree`；原版轮替 key 时同步复制一份过来，二次元版也要在下次轮替前发一次版
- 离线玩家数据 `game/scripts/kv/`（不进仓库）：从原版复制，原版导出后会顺手复制
- 发布：用 Workshop Tools 以 `-addon windyanime` 上传到 `3788870952`；原版测试服 `2636824668` 有原版开发者在用，不借用

## 已知问题

- `eyeherodemo.css` 第 198、373 行是空的 `url("s2r://")`，新编译器会报错（原版也有）
- Tools 启动会自动重编译一批已跟踪的 `game/` 编译产物，与本次改动无关的不提交
- Tools 刚启动、还在重编译时，商店等界面可能暂时显示不全，重启 Dota 后再判断
