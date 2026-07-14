---
tags:
  - MOC
  - Dataview
---

# 📊 Dataview 查询

> 本页用 **Dataview 插件**动态生成笔记索引（每次打开自动刷新）。
> ⚠️ 需先安装并启用 Dataview 插件，下面的查询才会渲染成表格/列表，否则只显示原始代码。
>
> **安装**：设置（`Ctrl+,`）→ 左侧 `第三方插件 / Community plugins` → `Browse` → 搜 `Dataview` → `Install` → `Enable`。

## 🔥 最近修改的 20 篇

```dataview
TABLE file.mtime AS "修改时间", tags AS "标签"
FROM ""
WHERE file.name != "首页" AND file.name != "Dataview 查询"
SORT file.mtime DESC
LIMIT 20
```

## 📂 笔记 /（技术学习）

```dataview
LIST
FROM "笔记"
SORT file.name ASC
```

## 📂 code /（运维账号与脚本）

```dataview
LIST
FROM "code"
SORT file.name ASC
```

## 🏷️ 按标签：运维

```dataview
TABLE file.folder AS "位置"
FROM #运维
SORT file.folder, file.name
```

## 🏷️ 按标签：账号密码

```dataview
TABLE file.folder AS "位置"
FROM #账号密码
SORT file.folder, file.name
```

## 🏷️ 按标签：数据库

```dataview
TABLE file.folder AS "位置"
FROM #数据库
SORT file.name
```

## 📊 各文件夹笔记数量

```dataview
TABLE length(rows) AS "笔记数"
FROM ""
GROUP BY file.folder
SORT file.folder ASC
```

---

> 💡 Dataview 相当于"给笔记写 SQL"。想要别的查询（如"所有含 Spring 的笔记"、"本周改过的"、"code 下按 tag 分组"），告诉我，我往上加。
