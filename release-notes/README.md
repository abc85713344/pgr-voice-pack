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

## 注意

- 本仓库**只放文本、JSON 和截图**，不放大文件。程序与音频（约 24 GiB）走网盘。
- GitHub 单文件上限 100 MB，本仓库任何文件都不应接近这个量级。
- 若某个 tag 没有对应的说明文件，workflow 会退回到自动生成的说明，不会失败。
- 想重新发布某个版本，删掉对应 Release 后重推 tag 即可：

```powershell
git push --delete origin v2026.XX.XX
git push origin v2026.XX.XX
```
