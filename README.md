# 文档站（GitHub Pages）

本目录是**自足的文档仓库内容**：把它整体推到一个新的公开 GitHub 仓库（或仓库根），Actions 会自动构建并发布到 Pages。

## 一次性发布步骤（只有你能做）

1. GitHub 新建**公开**仓库（例如 `PixelCharMaster`，同一个仓库以后也用来发 Releases 供更新检查）。
2. 把本目录**内容**（含 `.github/`）拷进新仓库根目录，提交推送：

   ```bash
   # 在 docs-site 目录里执行（<你> 换成你的 GitHub 用户名）
   git init && git remote add origin https://github.com/<你>/PixelCharMaster.git
   git add . && git commit -m "docs: init" && git push -u origin main
   ```

3. 仓库 Settings → Pages → Build and deployment → Source 选 **GitHub Actions**。
4. 等 Actions 跑完（约 1 分钟），文档就在 `https://<你>.github.io/PixelCharMaster/`。
5. 把站点地址填回 `mkdocs.yml` 的 `site_url`（以及编辑器「帮助」菜单的在线手册链接——后续版本接入）。

## 本地预览

```bash
pip install mkdocs-material mkdocs-static-i18n
mkdocs serve     # http://127.0.0.1:8000
```

## 以后怎么写

- 直接改 `docs/` 下的 Markdown，push 即自动发布。
- 多语言（2026-10-07 起）：中文=根 `docs/`（默认语言），English=`docs/en/`，한국어=`docs/ko/`（mkdocs-static-i18n folder 模式，语言切换器在页面顶栏）。**改中文内容后记得同步 en/ko 对应文件**；新增页面三份都要建（同名同相对路径）。
- 录制 2-3 分钟的操作 GIF（OBS → mp4 → 转 GIF），塞进对应手册页即可。
