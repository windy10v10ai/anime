---
name: online-hero-assets
description: >-
  Use when configuring online-environment hero avatars, hero image overrides,
  or custom ability icons in this Dota project. Keep the required Panorama
  hero-image mappings synchronized and map custom spell icons to the
  windyanime flash3 path used by DOTAAbilities.
---

# Online Hero Assets

维护线上环境可见的英雄头像和自定义技能图标。只要请求涉及这两类资源，先按本技能确认映射、资源路径和运行时覆盖范围，再进行最小必要修改。

## 英雄头像

用户约定的必同步脚本为：

- `content/panorama/scripts/custom_game/multiteam_hero_select_overlay.js`
- `content/panorama/scripts/custom_game/replaceHeroImage.js`
- `content/panorama/scripts/custom_game/end_screen_2.js`

新增或调整英雄头像时，三个文件中的 `imagefile` 必须使用同一组 key/value：

- key：`npc_dota_hero_<hero_name>`
- value：`file://{images}/heroes/npc_dota_hero_<hero_name>_custom.png`

不要只改其中一个脚本，也不要改变未覆盖英雄的回退逻辑。配置后逐文件核对 key 和 value，确保选人界面、游戏内英雄图标以及结算界面都能命中同一张图片。

头像资源还需要验证对应的 Panorama 源图和编译产物：

- 源图：`content/panorama/images/heroes/npc_dota_hero_<hero_name>_custom.png`
- 编译结果：`game/panorama/images/heroes/npc_dota_hero_<hero_name>_custom_png.vtex_c`

如果任务明确包含 Flyout scoreboard，额外检查
`content/panorama/layout/custom_game/flyout_scoreboard/shared_scoreboard_updater.js` 中的同类映射；它不替代上面三个必同步脚本。

## 线上技能图标

自定义技能图标固定放在：

```text
game/resource/flash3/images/spellicons/custom/windyanime/<ability_name>.png
```

在技能配置根块 `DOTAAbilities` 下对应的技能块中，使用不带 `.png` 后缀的纹理名：

```kv
"<ability_name>"
{
	"AbilityTextureName"	"custom/windyanime/<ability_name>"
}
```

配置时逐项核对技能名、文件名和大小写完全一致。原版已有 texture 时直接复用原名，不为它复制自定义 PNG；不要把技能图标套用物品图标的 content 副本或 `images_items.xml` 登记流程。

## 验证清单

- 三个必同步脚本的 `imagefile` 条目一致。
- 头像源 PNG、同名编译入口（如项目已有）和 `.vtex_c` 产物均可找到，且路径大小写一致。
- `DOTAAbilities` 对应技能块的 `AbilityTextureName` 与 `spellicons/custom/windyanime/<ability_name>.png` 一一对应。
- 运行时分别检查选人界面、游戏内英雄图标、结算界面和技能栏；涉及 Flyout scoreboard 时再单独检查该界面。
- 不把玩家 Steam 头像组件 `DOTAAvatarImage` 与英雄头像替换资源混淆。
