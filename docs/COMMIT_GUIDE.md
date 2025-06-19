# Git 提交规范指南

本指南详细说明了项目的 Git 提交规范，包括使用方法、配置说明和自动化功能。

## 🚀 快速开始

### 推荐方式：一键提交
```bash
npm run commit
```
- ✅ **自动暂存**所有修改的文件
- ✅ **交互式填写**提交信息
- ✅ **自动检查**格式规范
- ✅ **支持中文**描述

### 备用方式
```bash
npm run commit:cz  # 使用 Commitizen 界面（需手动 git add）
```

### 测试配置
```bash
npm run commit:test  # 验证 Husky 配置是否正常
```

## 📝 提交格式

基于 [Conventional Commits](https://www.conventionalcommits.org/) 标准：

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

### 提交类型

| 类型 | 图标 | 描述 | 示例 |
|------|------|------|------|
| `feat` | ✨ | 新功能 | `feat(pages): 添加用户设置页面` |
| `fix` | 🐛 | 问题修复 | `fix(api): 修复数据加载失败问题` |
| `docs` | 📝 | 文档更新 | `docs: 更新部署指南` |
| `style` | 💄 | 样式调整 | `style(ui): 优化主题色彩搭配` |
| `refactor` | ♻️ | 代码重构 | `refactor(utils): 重构工具函数` |
| `perf` | ⚡ | 性能优化 | `perf(api): 优化数据查询性能` |
| `test` | ✅ | 测试相关 | `test: 添加用户模块单元测试` |
| `build` | 📦 | 构建相关 | `build: 升级webpack配置` |
| `ci` | 👷 | CI/CD | `ci: 添加自动部署流程` |
| `chore` | 🔧 | 其他变更 | `chore: 更新依赖包版本` |

### 影响范围（可选）

常用范围：`pages`, `components`, `utils`, `api`, `config`, `cli`, `miniprogram`, `build`, `deps`

### 提交示例

```bash
# 简单提交
feat: 添加用户认证功能
fix(pages): 修复首页加载问题
docs: 更新 API 文档

# 详细提交
feat(pages): 添加用户个人中心页面

- 实现用户信息展示
- 添加头像上传功能
- 支持个人信息编辑

Closes #123
```

## ⚙️ 自动化配置

### Husky Git Hooks

项目使用 Husky 实现自动化的 Git hooks：

#### Pre-commit Hook
```bash
# .husky/pre-commit
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

echo "🔍 执行 pre-commit 检查..."

# 检查是否有文件需要暂存
if [ -n "$(git diff --name-only)" ]; then
  echo "📁 自动暂存修改的文件..."
  git add .
fi

echo "✅ Pre-commit 检查完成"
```

#### Commit-msg Hook
```bash
# .husky/commit-msg
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

npx --no-install commitlint --edit "$1"
```

### 配置文件

#### commitlint.config.js
统一的提交规范配置，整合了类型和范围定义：

```javascript
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'docs', 'style', 'refactor',
      'perf', 'test', 'build', 'ci', 'chore'
    ]],
    'scope-enum': [2, 'always', [
      'pages', 'components', 'utils', 'api', 'config',
      'cli', 'miniprogram', 'build', 'deps'
    ]],
    'subject-max-length': [2, 'always', 100],
    'subject-case': [0]  // 允许中文
  }
};
```

### 工作流程

```
开发者修改代码
    ↓
运行 npm run commit
    ↓
husky pre-commit hook 自动暂存文件
    ↓
Commitizen 提供交互界面
    ↓
选择提交类型、输入范围、描述等
    ↓
生成符合 Conventional Commits 规范的提交信息
    ↓
git commit 执行提交
    ↓
husky commit-msg hook 验证格式
    ↓
提交成功
```

## 🔧 初始化设置

### 新环境设置
```bash
# 安装依赖
npm install

# 设置 Husky（如果需要）
bash scripts/setup-husky.sh

# 测试配置
npm run commit:test
```

### setup-husky.sh 脚本功能
- 初始化 husky
- 创建 pre-commit 和 commit-msg hooks
- 设置文件权限
- 验证配置是否正确

## 🚀 与 CI/CD 集成

本项目的 CLI 工具会自动获取 Git 提交信息作为小程序上传描述，规范的提交信息有助于：

1. **更好的版本追踪** - 清晰的提交历史
2. **自动生成更新日志** - 支持多种格式
3. **提高团队协作效率** - 统一的提交规范
4. **便于问题定位和回滚** - 明确的变更记录

### 更新日志格式
项目支持三种更新日志格式，详见 [CHANGELOG_FORMATS.md](CHANGELOG_FORMATS.md)：
- **简单格式** - 适合快速迭代
- **详细格式** - 适合常规发布
- **变更日志格式** - 适合正式发布（推荐）

## ❓ 常见问题

### Q: 提交被拒绝怎么办？
A: 检查提交信息是否符合规范，修改后重新提交。常见问题：
- 提交类型不在允许列表中
- 主题超过100字符
- 格式不符合 `type(scope): subject` 规范

### Q: 如何修改最后一次提交信息？
A: 使用 `git commit --amend` 修改最后一次提交，或使用 `git rebase -i` 修改历史提交。

### Q: 可以自定义提交类型吗？
A: 可以在 `commitlint.config.js` 中修改 `type-enum` 和 `scope-enum` 规则。

### Q: 如何临时禁用检查？
A:
```bash
# 方法1：环境变量
HUSKY=0 git commit -m "message"

# 方法2：跳过验证（不推荐）
git commit -m "message" --no-verify
```

### Q: 自动暂存功能失效怎么办？
A: 检查 `.husky/pre-commit` 文件是否存在且有执行权限：
```bash
chmod +x .husky/pre-commit
```

### Q: Commitizen 界面显示异常？
A: 确保安装了所有依赖：
```bash
npm install inquirer chalk
```

## 🎯 最佳实践

1. **日常开发**: 使用 `npm run commit` 获得最佳体验
2. **团队协作**: 统一使用项目配置的提交规范
3. **CI/CD**: 利用规范化的提交信息自动生成更新日志
4. **版本管理**: 结合语义化版本控制 (SemVer) 使用
