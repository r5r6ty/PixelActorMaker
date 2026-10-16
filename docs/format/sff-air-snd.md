# .sff / .air / .snd 与 PAMSDK_Unity

三个文件都是**普通 JSON 文本**（UTF-8，缩进格式），Unity/引擎侧用任意 JSON 库直接反序列化。配套的纯数据结构 SDK：仓库 `Tools/FrameEventEditor.SDK/PAMSDK_Unity.cs`（单文件、零方法、零依赖；发布包内位于 `SDK/PAMSDK_Unity.cs`）。

## .sff（精灵/图集）

- `baseMap` / `normalMap`：图集 PNG **字节**。Newtonsoft 下 `byte[]` 序列化为 **base64 字符串**。
- 精灵元数据含图集内矩形；**`SpriteMeta.rect` 的 y 轴自底向上**（Unity 约定），引擎侧注意翻转。
- 调色板为独立条目（RGBA）。

## 部件层（武器变体，modelDispMask）

一个角色展示不同武器**不需要多套动作**：gal 里把武器画在名为 `layer_N`（N=1..9）的图层上（`layer_0` 与其它名字=基础层），导入时自动拆层：

- 基础组保持 gal 文件名（如 `Body`），不含任何 `layer_N` 像素；每个 `layer_N` 拆出部件组 **`Body+1`、`Body+2`…**
- 部件组与基础组**帧号严格 1:1**（该帧武器没画=全透明占位精灵）——部件帧号永远跟着主体事件的帧号走，同步免费
- 各部件组独立裁切（有多少切多少，不包透明），对齐全靠 Center 层算出的 pivot——绘制端按 pivot 叠加即自然合成，无需 rect 一致
- **部件组在图组列表里隐藏**，由基础组全自动管理：导入/重导入/删除/重命名基础组时 `+N` 家族联动；图像事件的组下拉也只列基础组
- 预览区的部件勾选条（1..9）实时开关各部件，默认全拼上——纯预览态，不写数据
- **约定即清单**：`组名+N` 这个命名本身就是部件定义，没有任何清单文件

引擎侧口径（PAMSDK_Unity 同构实现）：

- `SpriteEvent` 应用后按**同帧号**查 `组名+N` 的精灵叠加显示；角色掩码 `modelDispMask` 的 **bit N = 部件 N 显隐**（`SetMaskBit(n, on)` 即时生效，切武器不用换动画）
- 组不存在 / 位关闭 / 部件缺帧 = 该部件不显示；旧角色没有 `layer_N` 图层则行为与从前完全一致（air/sff/snd 格式零改动）

## .air（动作与帧事件）

- 动作（action）= 帧序列；每帧可挂多类事件。
- **事件多态判别 = `"type"` 类名字段**：

```json
{ "type": "AttackEvent", …攻击框字段… }
```

  五类：`SpriteEvent`（显示）/ `CollisionEvent`（受击框）/ `BodyEvent`（身体框）/ `AttackEvent`（攻击框）/ `SoundEvent`（音效）。按类名实例化对应事件类并填充其余字段。

## .snd（音效）

- `byte[]`（WAV 字节）用的是 **Unity JsonUtility 的数字数组格式**（`[82,73,70,70,…]`），**不是 base64**——与 .sff 的 byte[] 口径不同，解析时按字段所在文件区分。

## 注意事项

- **与 MUGEN 的 `.air`/`.sff`/`.snd` 不互通**：扩展名相同纯属巧合——本工具三件套是自有 JSON 格式，MUGEN 引擎读不了；MUGEN 的素材文件本工具同样读不了。
- 类名与你的工程冲突时可以随意改名，JSON `"type"` 判别值不变。
- 三个文件由编辑器以「同目录同名三件套」整体保存（Ctrl+S）。
- **Unity 导入**：`.sff/.air/.snd` 扩展名 Unity 不认（进不了 TextAsset）。编辑器另存为时「保存类型」选 **JSON 角色（Unity）**，即得 `角色.sff.json/.air.json/.snd.json` 三件——TextAsset 直读 `text` 字段后交给 Newtonsoft + PAMSDK_Unity 数据类即可。
- 格式由 `Tools/FrameEventEditor.SDK/check_fields.py` 账本核对；游戏侧 Mugen.cs 字段变更会同步 SDK。
