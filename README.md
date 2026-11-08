# xht-images — 小海兔图床

小海兔 App 的图片图床 / 数据库同步 / 应用内更新源仓库。

## 目录结构（v3 · 每进化链聚合）

```
spirits/<编号>-<链首名>/chain.json            链共用数据：进化链ID、编号、链首、蛋组、推荐性格、成员
spirits/<编号>-<链首名>/egg.webp              链共用精灵蛋图
spirits/<编号>-<链首名>/fruit.webp            链共用精灵果实图
spirits/<编号>-<链首名>/<精灵名>/data.json    每精灵数值数据
spirits/<编号>-<链首名>/<精灵名>/avatar.webp  精灵图
spirits/<编号>-<链首名>/<精灵名>/art.webp     立绘
spirits/<编号>-<链首名>/<精灵名>/expression.webp 表情图
spirits/<编号>-<链首名>/<精灵名>/model.glb    模型数据（预留约定）
data/custom-base.json                         设备端差异数据库同步（含新增精灵与覆盖项）
apk/xiaohaitu_3.4.3.apk                       应用内更新安装包
version.json                                  更新检查（版本号固定 3.4.3）
tip-thanks.webp                               关于页图片
```

## data.json 字段说明

| 字段 | 含义 | 单位 |
|---|---|---|
| 编号 | 精灵编号（ci） | — |
| 精灵名称 | — | — |
| 进化阶段 | 1=I阶 2=II阶 3=III阶，null=未设定 | — |
| 身高下限/上限 | hi / ha | m |
| 体重下限/上限 | wi / wa | kg |
| 蛋高下限/上限 | ehi / eha | m |
| 蛋重下限/上限 | ewi / ewa | kg |
| 蛋重下限/上限精确 | ewt / ewb | kg |

- 链共用数据（进化链ID、蛋组、推荐性格、蛋图、果实图）只记录在 chain.json 与链根目录，不在每精灵文件中重复
- 复用精灵（同一名出现在多条链成员列表）的数据文件只写在它自身所属链的文件夹中
- 图片由 App「一键上传」写入；「一键整理仓库」可对旧结构做归一
