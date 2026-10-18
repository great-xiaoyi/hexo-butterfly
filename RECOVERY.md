# 博客恢复指南（回家照此操作）

> 2026-01-18 在公司电脑完成丢失程度鉴定。本文件是完整恢复手册，按顺序执行即可，无需再查别的资料。

## 鉴定结论（30 秒版）

- 仓库：https://github.com/great-xiaoyi/hexo-butterfly ，**源码在 `master` 分支**（`main` 只有 README，别在 main 找）
- **个性化配置 100% 完好**——"魔改"主要在这一层，一行没丢：
  - `_config.yml`（200 行站点配置）
  - `_config.butterfly.yml`（1255 行主题配置：菜单/头像/背景/社交/说说/搜索/inject/CDN 全在这）
  - `source/css/`（juzhong.css、loading-bar.css、style.css 三个自定义样式）
  - `source/_data/`（友链 link.yml、说说 shuoshuo.yml）
  - `source/_posts/`（《从0开始的Python学习》《Hello World》）及标签/分类/友链/说说页面
- 缺的是两个"裸子模块"（当初没提交 `.gitmodules`，git 只记了指针没记文件）：
  `themes/butterfly`、`source/live2d-widget-v3` —— **都是公开代码，可按 commit 精确复原**
- 唯一真损失：图床 `117.72.81.48` 已失联，所有外链图片需另找来源（见步骤 6）
- 魔改三层结论：配置层 ✅ 完好、样式层 ✅ 完好、源码层（主题模板）⚠️ 仅此层可能丢且概率低
  ——子模块指针指向官方**原封**提交，说明当年大概率没改过主题源码

---

## 步骤 0：先翻家里的旧文件夹（最重要，先花 5 分钟）

家里电脑如果还有当年的博客项目文件夹：

1. **整个文件夹先打包备份**（微信/网盘/U盘都行）
2. 重点看 `themes/butterfly/` 里有没有源码级修改（改过 `.pug`/`.styl`/`.js` 的痕迹；或看文件修改时间是否晚于 2025-01-10）——**有修改先原样保存**，这是唯一 git 里没有的东西
3. 顺手收集一切图片原图（头像、背景、logo、封面、打赏码）

## 步骤 1：克隆仓库

```bash
git clone -b master https://github.com/great-xiaoyi/hexo-butterfly.git
cd hexo-butterfly
```

## 步骤 2：恢复 Butterfly 主题

```bash
git clone https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
cd themes/butterfly
git checkout 0dc6aede352d2ebb4e412954aabd557f85360139
cd ../..
```

> `0dc6aede` 是 2025-01-10 的提交，与你当年用的完全一致，配置兼容性有保证。

## 步骤 3：恢复看板娘组件

```bash
git clone https://github.com/letere-gzj/live2d-widget-v3.git source/live2d-widget-v3
cd source/live2d-widget-v3
git checkout 87c49e13268d64a4719d3d63ba07d9796eb54cb3
cd ../..
```

> 原仓库已找不到该提交，是别人 fork 里存着的。若届时 fork 也没了：任意同款 `live2d-widget-v3` 均可（只是装饰件，注入路径 `/live2d-widget-v3/load.js`）。

## 步骤 4：安装依赖

```bash
pnpm install    # 有 pnpm-lock.yaml；没装 pnpm 就 npm install
```

## 步骤 5：本地验证

```bash
hexo clean && hexo s
```

浏览器开 http://localhost:4000 对清单：

- [ ] 菜单齐全：首页 / 历史 / 标签 / 分类 / 链接 / 悄悄话 / 来找我聊天
- [ ] 导航 logo、背景图位置（图床挂了这里缺图是正常的，见步骤 6）
- [ ] 两篇文章都在
- [ ] 说说页、友链页、标签页、分类页正常
- [ ] 代码块：浅色主题 + Mac 红黄绿按钮
- [ ] 本地搜索能用
- [ ] 自定义样式生效（居中 / loading-bar / 全局样式）

## 步骤 6：抢救图片（唯一真损失）

以下全是 `http://117.72.81.48/i/...` 外链，服务器已失联：

| 用途 | 原图床路径 | 改哪里 |
|------|-----------|--------|
| 导航 logo | `/i/2025/01/12/6783e09e6bd8f.png` | `_config.butterfly.yml` → `nav.logo` |
| favicon | `/i/2025/01/12/6783d55a94349.png` | `_config.butterfly.yml` → `favicon` |
| 头像 | `/i/2025/01/13/678482fdb5a5d.jpg` | `avatar.img`（说说页也用它） |
| 网站背景 | `/i/2025/01/13/678488b9f0f07.png` | `background` |
| 文章封面 | `/i/2025/01/15/678794ca660b6.png` | 文章 front-matter 的 `cover` |
| 说说 GIF | `/i/2025/01/13/6784b3544bc72.gif` | `source/_data/shuoshuo.yml`（GitHub 主页 README 也引用了它） |

另有本地引用但**从未提交**的（`source/img/` 整个目录缺失），要补或换：

- `/img/error-page.png` —— 404 页背景
- `/img/friend_404.gif` —— 友链 404 图
- `/img/404.jpg` —— 文章页 404 图
- `/img/alipay.jpg`、`/img/wechat.jpg` —— 打赏码

**来源建议**：家里电脑、云盘、聊天记录、微信收藏、旧手机相册、web.archive.org 碰运气。

**找到后统一放 `source/img/`，配置改成 `/img/文件名`，从此本地化，不再依赖图床。**

## 步骤 7：部署

`_config.yml` 的 deploy 现指向 `zjh@103.112.96.73:/home/git/blog.git`，域名 `xiaoyihome.top` 已过期（DNS 都没了）。

- 旧服务器还在 → 不动，照旧 `hexo d`
- 不在了 → 换 GitHub Pages / Vercel / 新服务器，改两处：`url:` 和 `deploy:`

## 步骤 8：防丢（重建成功后立刻做）

当年丢文件的根源：主题和看板娘是嵌套 git 仓库，`git add .` 只记指针不记内容。重建后直接消灭这个坑：

```bash
rm -rf themes/butterfly/.git source/live2d-widget-v3/.git   # 变成普通目录
git add themes/butterfly source/live2d-widget-v3
git commit -m "把主题和看板娘纳入仓库管理，不再用嵌套 git 仓库"
git push
```

（300MB 级别的大文件除外；Butterfly 主题和 live2d 小件直接进仓库完全没问题。）

## 顺手小修

- `_config.butterfly.yml` 的 inject 里 `<script src="/live2d-widget-v3/load.js"">` 有个多余引号，可改成 `<script src="/live2d-widget-v3/load.js"></script>`
- 根目录 `untitled-1.yml`、`untitled-2.yml` 是旧配置草稿（untitled-2 是"阿易的小窝"时期的），想留纪念挪进 `backup/`，不想要可删

---

## 关键 SHA 备忘

| 东西 | 仓库 | commit |
|------|------|--------|
| 主题 | `jerryc127/hexo-theme-butterfly` | `0dc6aede352d2ebb4e412954aabd557f85360139` |
| 看板娘 | `letere-gzj/live2d-widget-v3`（fork） | `87c49e13268d64a4719d3d63ba07d9796eb54cb3` |

源码分支：**master**。
