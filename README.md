# 中英对照术语查询器 · 上线说明

一个单文件网页工具：导入中英对照语料后，输入中文词即可查到对应的英文译法，并标出出处段落。
数据以 JSON 形式存在本仓库里，**访客只读**；持有仓库写权限的人用各自的 GitHub 令牌提交修改，
提交后约 1 分钟，所有访客刷新即可看到新内容。

## 一、上线（约 5 分钟）

1. 在 GitHub 新建一个仓库（公开或私有均可，公开更省事）。
2. 把本目录（`github-deploy/`）里的**全部内容**上传到仓库根目录，保持结构：

```
index.html
.nojekyll
data/corpus.json      语料（中英对照）
data/glossary.json    术语库
data/query-log.json   查询记录统计
```

3. 仓库 **Settings → Pages** → Source 选 `Deploy from a branch` → 分支 `main`、目录 `/ (root)` → Save。
4. 等 1–2 分钟，访问 `https://你的用户名.github.io/仓库名/`。

> 如果 `data/` 目录没上传成功，页面会显示"共享数据为空"，此时先用主页面导入语料，
> 再到设置里「推送语料」把它写进仓库。

## 二、谁可以编辑（权限说明）

- **访客**：只能查询、查看。没有令牌，无法写入。
- **你和同事**：各自生成一个 **细粒度 GitHub 令牌**，只授权这一个仓库，权限只需 `Contents: Read and write`。
  令牌存在各自浏览器的设置里，不写进网页代码，别人按 F12 也拿不到。

生成令牌的路径：GitHub → 右上头像 → Settings → Developer settings →
Personal access tokens → **Fine-grained tokens** → Generate new token
→ Repository access 选 *Only select repositories* 勾选本仓库
→ Permissions → Repository permissions → **Contents: Read and write**
→ 生成后复制（只显示一次）。

## 三、日常使用

**查询**：打开网页 → 输入中文词 → 看结论卡与命中段落。每次查询的词、时间、命中数会记录在
浏览器本地。

**改术语库**：主页面「术语库」→「打开术语库」→ 在新标签页增删改 → 「推送到 GitHub」
→ 约 1 分钟后所有访客可见。令牌已在设置里配好，新标签页会自动带入，不用每次输入。

**改语料**：设置 → 共享数据 → 「编辑语料库」→ 新标签页里展开文件、逐段修改中英文或删除 →
「推送到 GitHub」。同样不需要重新输入令牌。

> 语料与术语库里已取消「仅限本地」标记：所有语料都可用于 AI 核对，也不再区分联网/不联网。

**查询记录**：每次查询都会自动提交到 `data/query-log.json`（同词次数累加、时间取较新）。
连续快速输入时做了 1.2 秒防抖，多词只算一次提交；提交失败会在 8 秒后自动重试一次。
设置里仍保留「同步查询记录」按钮，用于手动补交。

> 注意：这意味着每查一次就产生一次 Git 提交，也会触发 GitHub Pages 重新构建。
> 如果查询很频繁，建议把日志拆到单独分支（把 Pages 的构建源设为 `main`，
> 日志写 `logs` 分支），这样日志提交不会触发重建。需要的话可以再让我改。

## 四、数据结构

`data/corpus.json`
```json
{ "updatedAt": "...", "folders": ["通稿"], "files": [
  { "name": "文件名", "folder": "通稿", "units": [
    { "idx": 1, "zh": "中文段", "en": "English paragraph", "zp": [1,1], "ep": [2,2], "kind": "para" }
  ]}
]}
```

`data/glossary.json`
```json
{ "updatedAt": "...", "items": [ { "zh": "定心丸", "en": "inject confidence into", "src": "出处", "ts": 0 } ] }
```

`data/query-log.json`
```json
{ "updatedAt": "...", "words": { "定心丸": { "count": 3, "hits": 12, "lastAt": 0 } } }
```

## 五、注意事项

- **提交后不会立刻生效**：GitHub Pages 需要重新构建，通常 30–90 秒。着急看效果可强刷（Ctrl+F5）。
- **两个人同时改会冲突**：后提交的一方会因文件版本（sha）变化而失败，重新点一次推送即可（页面会先取最新版本再写）。
- **GitHub API 限额**：认证请求每小时 5000 次，日常编辑远够用。
- **不要在网页代码里写死令牌**：本工具从不把令牌写进 HTML，令牌只存在访问者自己的浏览器。
- **本地直接双击打开 index.html 也能用**，但此时读不到 `data/`（浏览器的本地文件限制），
  数据会退回存在本机浏览器里，只有你自己能看到。

---
内容由 AI 生成
