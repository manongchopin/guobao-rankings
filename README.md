# guobao-rankings

`chopinfisher.com/world/sports-rankings.html` 的榜单数据源。

这个仓库**只负责抓数据**，不含网站代码。网站本身的源码在本地
`shanghai-guobao/web`，不备份到 GitHub。

## 它做什么

每天北京时间 08:00，GitHub Actions 会：

1. 抓取 11 张榜单（斯诺克 5 张、网球 6 张）
2. 解析 WTA 官方发布的两份奖金 PDF
3. 重写 `sports-rankings-data.js`
4. 有变化才提交

之所以放在 GitHub 跑，是因为搬数据的机器在上海，
`snooker.org` 被 Cloudflare 拦、维基百科 DNS 被污染，本地抓不到这两张表。

## 产出

- `sports-rankings-data.js` —— 唯一产物，格式是 `window.SPORTS_RANKINGS = {...}`，
  可以直接被网站当脚本引入。

抓取失败的表不会写假数据：它保留上一次内容并打 `stale` 标记，
页面上会显示「未更新」。

## 数据来源

| 榜单 | 来源 |
| --- | --- |
| 斯诺克世界排名 | snooker.org |
| 斯诺克过百 / 147 / 排名赛冠军 | snookerinfo.co.uk |
| 中国球员夺冠明细 | 维基百科各球员页 |
| ATP / WTA 排名、中国球员排名 | tennisexplorer.com、wtatennis.com |
| 生涯奖金、赛季奖金 | WTA 官方 PDF |

## 中文名

`scripts/sports-names.json` 保存全部译名映射。新球员没有译名时会回落成
外文原名，并在 Actions 日志末尾列出来，补进去即可。

## 手动运行

Actions 页面 → Update Rankings → Run workflow。

## 和网站的关系

`rankings-updater` 每天 08:30 检查本仓库的 `sports-rankings-data.js`；
只有 `fetchedAt` 变新才复制到服务器。

## 怎么到网站上

网站本地有一个同步脚本，把这个仓库的 `sports-rankings-data.js` 拉下来覆盖
到网站目录，然后由站点 agent 发布。这个仓库本身不碰服务器。
