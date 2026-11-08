# xht-images — 小海兔图床

小海兔 App 的图片图床 / 数据库同步 / 应用内更新源仓库。

## 目录结构（v2 · 每精灵聚合）

```
spirits/<编号>_<名>/avatar.webp      精灵图
spirits/<编号>_<名>/art.webp         立绘
spirits/<编号>_<名>/egg.webp         蛋图
spirits/<编号>_<名>/fruit.webp       果实图
spirits/<编号>_<名>/expression.webp  表情图
spirits/<编号>_<名>/model.glb        模型数据（预留约定）
data/custom-base.json                开发者差异数据库（数值数据集中于此）
apk/xiaohaitu_3.4.3.apk              应用内更新安装包
version.json                         更新检查（版本号固定 3.4.3）
tip-thanks.webp                      关于页图片
```

- 数值数据不做每精灵分片，统一存 data/custom-base.json（一键上传/下载合并的唯一数据文件）
- 图片由 App「一键上传」经 Contents API 直接写入；「一键整理仓库」可对新旧混合状态做结构归一
