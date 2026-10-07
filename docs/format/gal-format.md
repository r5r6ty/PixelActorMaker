# .gal 容器格式规范

`.gal` 是 GraphicsGale 兼容的动画容器（本工具可读写；参考实现：MIT 许可的 [gale_ops](https://codeberg.org/frank-f-trafton/gale_ops) Lua 模块）。

## 1. 容器结构

```
"GaleX200"                      8 字节魔数
( uint32LE blockSize            ┐ 重复 N 次
  byte[blockSize] zlib 数据 )   ┘ 块体 = 原始 DEFLATE 流（zlib 头 + deflate + adler32）
```

- **第 1 块 = 头块**：zlib 解压后是 Latin-1 编码的伪 XML 元数据文本（见 §2）。
- **其后每块按 帧序 × 层序 严格排列**：对每帧的每层依次为 ①主像素块 ②alpha 块。
- 头 XML **不存块偏移**——读取方按上面顺序顺序游走解压即可；某层无 alpha 时其 alpha 块长度写 0。
- 帧内层数、每层宽高、bpp 都从头 XML 读。

## 2. 头块 XML

根元素与帧/层结构（属性值均为十进制数字或文本；工具写出的 XML 做了全角替换转义，第三方解析按普通 XML 属性处理即可）：

```xml
<Frames Version="…" Width="…" Height="…" Bpp="…" Count="帧数"
        SyncPal="0/1" Randomized="…" CompType="…" CompLevel="…"
        BGColor="…" BlockWidth="…" BlockHeight="…" NotFillBG="…">
  <Frame Name="…" TransColor="…" Delay="毫秒" Disposal="…">
    <Layers Count="层数" Width="层宽" Height="层高" Bpp="层位深">
      <RGB>BBGGRRBBGGRR…</RGB>          <!-- 可选：仅调色板帧（8bpp）有；每色 6 个 hex 字符 -->
      <Layer Left="…" Top="…" Visible="0/1" TransColor="…" Alpha="…"
             AlphaOn="0/1" Name="…" Lock="0/1" />
      …
    </Layers>
  </Frame>
  …
</Frames>
```

要点：

- `<RGB>` 调色板文本序 = **BBGGRR**（GraphicsGale 惯例），无 alpha 字节；色数由文件 bpp 决定（8bpp=256）。
- 根 `BGColor` 为 COLORREF 序（**低字节=R**）。
- 8bpp 帧 `TransColor` = 调色板**索引**；24bpp = 实际颜色（字节序历史样本对称，高位=R 的假定未遇反例）。
- 像素坐标 `(Left, Top)` = 层在帧画布内的左上角（可为负）。
- `Delay` 恒为毫秒；`SyncPal=1` 表示各帧共享同一调色板（读写方应把首帧色板视为全文档色板）。

## 3. 像素块布局

主块/alpha 块解压后均为**行填充**的平面像素阵列，行地址 = `y * 每行字节数`：

| Bpp | 主块布局 | 每行填充 |
|---|---|---|
| 1 | 1 字节/像素展开位（位序 MSB 在前：`byte >> (7 - bit&7)`） | 位填充到 32 的倍数 |
| 4 | 1 字节/像素展开半字节（高半字节在前：偶数列=高 4 位） | 半字节填充到 8 的倍数 |
| 8 | 1 字节/像素 = 调色板索引 | 填充到 4 字节倍数 |
| 15 | 2 字节/像素 `gggbbbbb 0rrrrrgg`（解出 R,G,B） | 有效 2W 字节 → 填 4 字节倍数 |
| 16 | 2 字节/像素 `gggbbbbb rrrrrggg`（6 位绿） | 同上 |
| 24 | 3 字节/像素，**文件字节序 B,G,R** | 有效 3W 字节 → 填 4 字节倍数 |

- alpha 块（若长度非 0）：1 字节/像素透明度，行填充 4 字节倍数；与主块同宽高。
- 本工具保存时**未修改的层块原样逐字节拷贝**（不会重编码旧块）；结构变更（增删帧/层）后头 XML 全量重生成。

## 4. 读取伪代码

```python
data = open(path, 'rb').read()
assert data[:8] == b'GaleX200'
pos = 8
blocks = []
while pos < len(data):
    n = int.from_bytes(data[pos:pos+4], 'little'); pos += 4
    blocks.append(zlib.decompress(data[pos:pos+n])); pos += n   # n==0 → 空块（无 alpha）
header_xml = blocks[0].decode('latin-1')
# 解析 <Frames>…：得每帧层数/每层(Left,Top,w,h,bpp,…)
# 之后按帧序×层序消费 blocks[1:]：每层先主块后 alpha 块，用 §3 表展开像素
```
