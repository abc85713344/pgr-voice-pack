# 版本说明与发版流程

每个版本的 Release 说明保存在本目录，**文件名与 tag 一致**，例如 `v2026.09.24.md`。

## 发新版：三步

### 1. 更新内容

- `README.md` 顶部的版本号
- `文件校验.json` 与 `校验记录/`（覆盖为新版的校验结果）
- 新增本目录下的 `v新版本号.md`（Release 说明，格式照抄上一版）
- 如果网盘分享换了，同步更新 `README.md` 和新版说明里的链接与提取码

### 2. 提交并推送

```powershell
cd 'F:\战双配音包\公开仓库-pgr-voice-pack'
git add -A
git commit -m "更新到 2026.XX.XX"
git push
```

### 3. 打 tag 并推送 —— 这一步会自动创建 Release

```powershell
git tag -a v2026.XX.XX -m "版本说明"
git push origin v2026.XX.XX
```

推送 tag 后，`.github/workflows/release.yml` 会在 GitHub Actions 里自动读取
`release-notes/<tag>.md`，用它作为 Release 说明创建 Release。

**不需要配置任何 Personal Access Token** —— workflow 用的是 GitHub 自动注入的 `GITHUB_TOKEN`。

## ⚠️ 两个踩过的坑

### 1. Release 附件名只能用英文，不能用中文

通过网页上传附件时，GitHub 会把文件名里的非 ASCII 字符替换成点。实测：

| 上传时你看到的文件名 | GitHub 实际存成 |
|---|---|
| `01-播放器-Windows-x64.zip` | `01-.-Windows-x64.zip` |
| `02-OCR组件-可选.zip` | `02-OCR.-.zip` |

**所以上传前先把文件复制成英文名**，把副本拖上去：

```powershell
$src='F:\战双配音包\网络发布包-XXXXXX'
$dst='F:\战双配音包\github上传用'
New-Item -ItemType Directory -Force -Path $dst | Out-Null
Copy-Item "$src\01-播放器-Windows-x64.zip" "$dst\01-player-windows-x64.zip"
Copy-Item "$src\02-OCR组件-可选.zip"  "$dst\02-ocr-optional.zip"
```

压缩包**内部**的文件夹名不受影响，仍是中文的「战双剧情配音播放器」，玩家解压后体验一样。

> 注意：git 仓库里的中文文件名**完全正常**（如 `使用说明.md`），这个限制只针对 Release 附件。

### 2. 别删 tag —— 用 force 更新它

在 GitHub 上**删除** tag，关联的 Release 会被置为 **draft**：列表页看不见、Latest 标记也会掉（附件本身仍在，但对外不可见）。

**正确做法是 force 更新 tag** —— tag 名一直存在，Release 不会变 draft，附件也不受影响：

```powershell
git tag -f -a v2026.XX.XX -m "版本说明"
git push --force origin v2026.XX.XX
```

如果已经误删了 tag，workflow 里带了 `--draft=false` 兜底，重新推同名 tag 就能自动恢复到已发布状态。

## 注意

- 本仓库**只放文本、JSON 和截图**，不放大文件。程序（约 210 MiB）放 Release 附件，章节包（约 24 GiB）走网盘。
- GitHub 单文件上限 100 MB，本仓库任何文件都不应接近这个量级。
- 若某个 tag 没有对应的说明文件，workflow 会退回到自动生成的说明，不会失败。
- 想更新某个版本的说明（比如补个链接），用 force 更新 tag 即可，附件不受影响：

```powershell
git tag -f -a v2026.XX.XX -m "版本说明"
git push --force origin v2026.XX.XX
```
