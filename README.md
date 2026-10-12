# xht-images · 小海兔

小海兔 APK 图片资源 & 版本发布仓库（CDN via jsDelivr）

---

## 当前版本

| 项目 | 值 |
|------|-----|
| 版本号 | v3.4.2 (versionCode 30402) |
| 发布日期 | 2026-10-12 |
| APK 下载 | [Releases v3.4.2](https://github.com/ElainaChina/xht-images/releases/download/v3.4.2/xiaohaitu_3.4.2.apk) |
| 强制更新 | 否 |

### 更新日志

```
小海兔 v3.4.2
1.修复部分精灵数据错误bug
2.新增待确认精灵蛋批量操作功能
3.优化待确认精灵蛋的显示，极限蛋会直接显示为红色名称
4.长按精灵蛋记录将支持编辑(便于进行备注与修改)
5.修复新版本无法下载问题（多镜像下载链接列表，避开浏览器拦截）
```

---

## 仓库结构

```
xht-images/
├── spirits/              # 精灵头像图片 (.webp)
├── expressions/          # 精灵表情大图 (.webp)
├── version.json          # 版本信息 & 完整性哈希白名单
└── README.md             # 本文件（开发者必读）
```

### CDN 路径

```
基础路径: https://cdn.jsdelivr.net/gh/ElainaChina/xht-images@main
精灵头像: {base}/spirits/{精灵名}.webp
表情大图: {base}/expressions/{精灵名}.webp
版本信息: {base}/version.json
```

CDN 配置文件位于 APK 内 `assets/public/cdn-config.js`，修改 `IMAGE_CDN_BASE` 即可切换 CDN 源。

---

## version.json 字段说明

```json
{
  "versionName": "3.4.2",          // 版本名（用户可见）
  "versionCode": 30402,            // 版本号（整数，递增）
  "downloadUrl": "...apk",         // APK 下载地址
  "changelog": "...",              // 更新日志（\n 分隔）
  "forceUpdate": false,            // 是否强制更新
  "publishDate": "2026-10-12",     // 发布日期
  "integrity": {                   // 完整性哈希白名单（SHA-256）
    "app.js": "...",
    "style.css": "...",
    "integrity.js": "...",
    ...
  }
}
```

`integrity` 中的哈希用于 APK 内 `integrity.js` 的运行时校验。修改任何白名单文件后，必须同步更新此处哈希。

---

## APK 内 public 目录文件清单

| 文件 | 用途 | 是否纳入白名单 |
|------|------|:---:|
| `app.js` | 主应用逻辑 | 是 |
| `style.css` | 全局样式 | 是 |
| `index.html` | 入口页面 | 是 |
| `viewer.html` | 查看器页面 | 是 |
| `integrity.js` | 完整性校验白名单（混淆存储） | 是 |
| `cdn-config.js` | CDN 基础路径配置 | 是 |
| `pcap-parser.js` | pcap 文件解析器 | 是 |
| `embedded_spirits.js` | 内置精灵数据库 | 是 |
| `embedded_evo_chains.js` | 进化链数据 | 是 |
| `embedded_nature_want.js` | 推荐性格数据 | 是 |
| `embedded_images.js` | 内置图片数据 | 是 |
| `软件基础信息.js` | 自定义基础数据（显示名/图片/性格） | 否 |
| `manifest.json` | PWA 清单 | 否 |
| `fontawesome/` | 图标字体库 | 否 |

---

## 发布流程

### 1. 修改代码

在 `apk_work/assets/public/` 下修改对应文件。

### 2. 更新完整性哈希

```bash
cd apk_work/assets/public

# 计算修改文件的 SHA-256
sha256sum app.js style.css

# 更新 integrity.js 中的哈希值
# （_ENCODED_HASHES 对象内的对应条目）

# 同时更新 version.json 中的 integrity 字段
```

### 3. 重新打包签名 APK

```bash
# 方式一：从 apk_work 完整打包（推荐，确保文件完整）
python3 -c "
import os, zipfile
src='apk_work'
out='xiaohaitu_3.4.2.apk'
files=[]
for root, dirs, fns in os.walk(src):
    for fn in fns:
        if fn.endswith('.bak') or fn.endswith('.idsig'): continue
        full=os.path.join(root, fn)
        rel=os.path.relpath(full, src)
        if rel.startswith('META-INF'+os.sep): continue
        files.append((full, rel))
with zipfile.ZipFile(out, 'w', zipfile.ZIP_DEFLATED) as z:
    for full, rel in files:
        z.write(full, rel.replace(os.sep, '/'))
"

# 方式二：从原 APK 更新文件（增量）
cp xiaohaitu_3.4.1.apk xiaohaitu_3.4.2_new.apk
zip -d xiaohaitu_3.4.2_new.apk "META-INF/*"
cd apk_work
zip /path/to/xiaohaitu_3.4.2_new.apk assets/public/app.js assets/public/style.css assets/public/integrity.js

# 签名（apksigner，推荐）
java -jar apksigner.jar sign \
  --ks xiaohaitu-release.p12 \
  --ks-pass "pass:密钥库密码" \
  --ks-key-alias xiaohaitu \
  --key-pass "pass:密钥密码" \
  --out xiaohaitu_3.4.2.apk xiaohaitu_3.4.2.apk

# 验证签名
java -jar apksigner.jar verify xiaohaitu_3.4.2.apk
```

### 4. 推送到 GitHub

```bash
# 更新 version.json
cd xht-images-repo
# （编辑 version.json 中的哈希和 changelog）

git add version.json
git commit -m "v3.4.2: 更新说明"
git push origin main
```

### 5. 更新 Release APK

```bash
# 删除旧 asset
ASSET_ID=$(curl -s -H "Authorization: token <TOKEN>" \
  "https://api.github.com/repos/ElainaChina/xht-images/releases/tags/v3.4.2" \
  | python3 -c "import sys,json; print(json.load(sys.stdin)['assets'][0]['id'])")
curl -X DELETE -H "Authorization: token <TOKEN>" \
  "https://api.github.com/repos/ElainaChina/xht-images/releases/assets/$ASSET_ID"

# 上传新 APK
curl -X POST \
  -H "Authorization: token <TOKEN>" \
  -H "Content-Type: application/vnd.android.package-archive" \
  --data-binary @xiaohaitu_3.4.2.apk \
  "https://uploads.github.com/repos/ElainaChina/xht-images/releases/<RELEASE_ID>/assets?name=xiaohaitu_3.4.2.apk"
```

### 6. 刷新 CDN 缓存

```bash
curl "https://purge.jsdelivr.net/gh/ElainaChina/xht-images@main/version.json"
```

---

## 关键注意事项

1. **哈希一致性**：每次修改白名单文件后，必须同步更新 `integrity.js` 和 `version.json` 中的哈希，否则 APK 运行时校验失败会回退到旧版本。
2. **签名一致性**：必须使用同一签名密钥（`xiaohaitu-release.p12`），否则无法覆盖安装。
3. **CDN 缓存延迟**：jsDelivr `@main` 分支标签缓存传播可能需要几分钟，`@<commit-hash>` 路径即时生效。
4. **图片资源更新**：新增/修改 `spirits/` 或 `expressions/` 中的图片后，需刷新 CDN 缓存。图片文件名须与精灵名完全一致（含括号等特殊字符）。
5. **自定义数据**：`软件基础信息.js`（CUSTOM_BASE_DATA）用于覆盖内置数据的差异部分，修改后不影响白名单校验。
6. **版本更新下载**：v3.4.2 起采用「多镜像下载链接列表」方案，点击「更新」弹出包含 jsDelivr CDN/Fastly/Gcore/TestingCF/ghproxy 等多个镜像的列表，用户可选择稳定镜像下载或复制链接到第三方浏览器打开，避开 vivo 等国产浏览器对 GitHub 的安全校验拦截。`downloadUrl` 建议使用 jsDelivr `@main` 形式（国内稳定可达）。
7. **版本检查地址**：`VERSION_CHECK_URL` 使用 `raw.githubusercontent.com`（无 CDN 缓存延迟），`integrity.js` 的 `REMOTE_INTEGRITY_URL` 同步使用 raw 地址，确保及时获取最新 version.json。

---

## 签名密钥

- 密钥库: `xiaohaitu-release.p12`
- 别名: `xiaohaitu`
- 签名算法: SHA256withRSA
- 有效期至: 2051-10-01

> 密钥库密码和密钥密码请妥善保管，切勿提交到仓库。
