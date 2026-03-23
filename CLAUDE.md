# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 仓库定位

- 该仓库是 MXW 品牌资产的单一事实源（SSOT），核心内容是可直接发布的 SVG 资产。
- 当前没有应用代码、构建系统或测试框架；主要工程逻辑在发布工作流。

## 高层结构

- `public/`：品牌资产源文件目录（发布时整体打包）。
  - 根目录是当前生效资产（如主标、圆标、favicon）。
  - `public/logo-options/` 存放导出变体与品牌规范文档 `MXW-BRAND-GUIDELINES.md`。
- `.github/workflows/release-brand-assets.yml`：发布流水线，负责校验资产完整性、打包并创建 GitHub Release。

## 发布流程（核心架构）

`release-brand-assets.yml` 的逻辑是本仓库最关键的“代码路径”：

1. 触发方式：
   - 推送标签 `v*.*.*`
   - 手动触发 `workflow_dispatch`（需提供 `tag`）
2. 版本解析与校验：
   - 标签必须匹配 `v<major>.<minor>.<patch>`
   - 手动触发时会 checkout 到对应 tag 再打包
3. 资产校验：
   - 对工作流内 `required_files` 列表逐一检查“文件存在且非空”
4. 产物构建：
   - 生成 `brand-assets-<tag>.tar.gz`，内容为整个 `public/`
5. 发布：
   - 使用 `softprops/action-gh-release@v2` 上传归档到对应 GitHub Release

## 常用命令

> 本仓库暂无 lint/test 命令。开发与发布前主要做“资产存在性校验 + 打包验证”。

- 查看改动：
  - `git status`
  - `git diff -- public .github/workflows/release-brand-assets.yml`

- 本地执行与 CI 一致的关键文件校验（可直接复制）：
  - `required_files=("public/favicon.svg" "public/logo.svg" "public/logo-circle.svg" "public/logo-options/logo-favicon-16.svg" "public/logo-options/logo-primary-dark.svg" "public/logo-options/logo-primary-light.svg" "public/logo-options/logo-secondary-square.svg" "public/logo-options/logo-mono-black.svg" "public/logo-options/logo-mono-white.svg" "public/logo-options/MXW-BRAND-GUIDELINES.md"); for file in "${required_files[@]}"; do [[ -s "$file" ]] || { echo "missing or empty: $file"; exit 1; }; done`

- 单项检查（替代“单测”）：
  - `test -s public/logo.svg`

- 本地打包发布产物（与工作流构建步骤一致）：
  - `tag=v0.1.0; tar -czf "brand-assets-${tag}.tar.gz" public && ls -lh "brand-assets-${tag}.tar.gz"`

- 触发正式发布（标签触发）：
  - `git tag vX.Y.Z`
  - `git push origin vX.Y.Z`

## 修改资产时的注意点

- 若重命名/新增/删除发布必需资产，必须同步更新：
  - `public/` 对应文件
  - `.github/workflows/release-brand-assets.yml` 中的 `required_files`
- 品牌语义与视觉约束以 `public/logo-options/MXW-BRAND-GUIDELINES.md` 为准。

## 最小维护清单

1. 修改 `public/` 资产后，先运行关键文件校验命令，确保必需文件存在且非空。
2. 若资产文件名发生变化，同步更新 workflow 的 `required_files`。
3. 本地执行一次打包命令，确认 `brand-assets-<tag>.tar.gz` 可正常生成。
4. 提交后使用语义化版本标签发布（`vX.Y.Z`）。
5. 在 GitHub Actions 中确认 `release-brand-assets` 成功，并检查 Release 附件可下载。
