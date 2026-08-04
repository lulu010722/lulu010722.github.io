# AGENTS.md

本文件为 AI 代理在此仓库中工作时提供指导。

## 命令

- 包管理器**只能用 pnpm**（`preinstall` 通过 only-allow 强制；不要用 npm/yarn）。
- `pnpm dev` — 开发服务器，端口 4321。
- `pnpm build` — `astro build` **之后还要执行** `pagefind --site dist`（生成搜索索引；不要省略后半部分）。
- `pnpm check` — Astro 类型检查；`pnpm type-check` — `tsc --noEmit --isolatedDeclarations`。
- `pnpm lint` / `pnpm format` — Biome（`biome check`/`biome format` 加 `--write ./src`）。Biome 使用**默认配置——没有 biome.json**，不要引用不存在的配置文件。
- 完成工作前对修改过的文件运行 `pnpm lint` 和 `pnpm format`。
- `pnpm new-post <filename>` — 通过 `scripts/new-post.js` 创建新文章。
- 仓库没有测试套件；不要编造测试命令。

## 内容

- 文章位于 `src/content/posts/<topic>/index.md`（中文主题目录，图片与文章放在一起）。草稿放在 `src/content/drafts/`。
- frontmatter 的 schema 定义在 `src/content/config.ts`（zod）。注意：`category` 是**字符串数组**，`sticky` 是用于置顶文章的数字，`prevTitle`/`prevSlug`/`nextTitle`/`nextSlug` 仅供内部使用——不要手动设置。
- Markdown 会经过 `astro.config.js` 插件的重度后处理：KaTeX 数学公式、`:::note`/`:::tip` 提示块、GitHub 仓库卡片、Expressive Code（可折叠代码块、行号）、sectionize、摘要、阅读时间。

## 约定

- 提交信息简短且用小写，例如 "pin post using sticky"。
- 站点标题/URL（`https://lulu010722.com/`）硬编码在 `src/config.ts` 中；CI 在推送到 main 时会把 `dist/` 部署到 gh-pages 分支。
