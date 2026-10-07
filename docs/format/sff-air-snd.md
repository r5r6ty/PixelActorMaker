# .sff / .air / .snd 与 MugenSdk

三个文件都是**普通 JSON 文本**（UTF-8，缩进格式），Unity/引擎侧用任意 JSON 库直接反序列化。配套的纯数据结构 SDK：仓库 `Tools/MugenSdk/MugenDataSdk.cs`（单文件、零方法、零依赖；`check_fields.py` 做字段漂移核对）。

## .sff（精灵/图集）

- `baseMap` / `normalMap`：图集 PNG **字节**。Newtonsoft 下 `byte[]` 序列化为 **base64 字符串**。
- 精灵元数据含图集内矩形；**`SpriteMeta.rect` 的 y 轴自底向上**（Unity 约定），引擎侧注意翻转。
- 调色板为独立条目（RGBA）。

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

- 类名与你的工程冲突时可以随意改名，JSON `"type"` 判别值不变。
- 三个文件由编辑器以「同目录同名三件套」整体保存（Ctrl+S）。
- 格式由 `Tools/MugenSdk/check_fields.py` 账本核对；游戏侧 Mugen.cs 字段变更会同步 SDK。
