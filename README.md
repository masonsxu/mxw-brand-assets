# MXW Brand Assets

MXW 品牌资产仓库（SSOT），用于集中维护可发布的 SVG 品牌文件与品牌规范。

## 仓库内容

- `public/`：当前生效的品牌资产。
- `public/logo-options/MXW-BRAND-GUIDELINES.md`：品牌规范（一页版）。
- `.github/workflows/release-brand-assets.yml`：自动发布工作流（校验 + 打包 + GitHub Release）。

## 常用维护命令

- 查看改动：
  - `git status`
  - `git diff -- public .github/workflows/release-brand-assets.yml`

- 关键文件完整性校验（与 CI 一致）：
  - `required_files=("public/favicon.svg" "public/logo.svg" "public/logo-circle.svg" "public/logo-options/logo-favicon-16.svg" "public/logo-options/logo-primary-dark.svg" "public/logo-options/logo-primary-light.svg" "public/logo-options/logo-secondary-square.svg" "public/logo-options/logo-mono-black.svg" "public/logo-options/logo-mono-white.svg" "public/logo-options/MXW-BRAND-GUIDELINES.md"); for file in "${required_files[@]}"; do [[ -s "$file" ]] || { echo "missing or empty: $file"; exit 1; }; done`

- 本地打包验证：
  - `tag=v0.1.0; tar -czf "brand-assets-${tag}.tar.gz" public && ls -lh "brand-assets-${tag}.tar.gz"`

## 自动发布（简版）

本仓库使用 GitHub Actions 全自动发布，无需手工上传资产。

1. 创建并推送语义化版本标签：
   - `git tag vX.Y.Z`
   - `git push origin vX.Y.Z`
2. 工作流会自动：
   - 校验 `required_files` 中所有关键文件是否存在且非空
   - 打包 `public/` 为 `brand-assets-vX.Y.Z.tar.gz`
   - 创建/更新对应 GitHub Release 并上传归档

## License

本仓库采用 **All Rights Reserved**（见 `LICENSE`）。未经授权，不得复制、修改、分发或商用仓库内品牌资产。