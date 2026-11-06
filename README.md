# Jack_Steve 的个人网站（Quartz）

基于 [Quartz 4](https://quartz.jzhao.xyz/) 构建的个人知识库站点，内容为 Obsidian 笔记。

- 线上地址：<https://bilibilijacksteve.github.io/site/>
- 旧版 mkdocs 站点已归档至 [`archive/mkdocs`](https://github.com/BiliBiliJackSteve/site/tree/archive/mkdocs) 分支

## 目录结构

```
content/        # Obsidian 库（.obsidian 不入库，需本地配置）
├── index.md    # 站点首页
├── 10-数学与学业/
├── 20-语言学习/
├── 30-编程与技术/
├── 40-生活管理/  # 私人内容，构建时被 ignorePatterns 排除，不会发布
└── 附件/        # 图片等附件
```

## 日常使用

1. 用 Obsidian 打开 `content/` 文件夹作为库进行编辑
2. 提交并推送到 `main` 分支：
   ```bash
   git add -A && git commit -m "更新笔记" && git push
   ```
3. GitHub Actions 自动构建并发布到 GitHub Pages

## 本地预览

要求 Node.js ≥ 20。

```bash
npm ci                # 首次安装依赖
npx quartz build --serve   # 本地构建并预览（默认 http://localhost:8080）
```

## 发布配置说明

- 私有目录排除：`quartz.config.ts` 中 `ignorePatterns` 包含 `40-生活管理`，如需排除其他目录在此追加
- 部署方式：`.github/workflows/deploy.yml`（GitHub Actions → GitHub Pages）
