# 更新日志格式配置指南

本文档介绍如何配置小程序上传时的更新日志格式，让您的版本描述更加规范和易读。

## 支持的格式类型

### 1. 简单格式 (simple)
只显示最新的一条提交信息，适合快速迭代的场景。

**配置方式：**
```bash
# 命令行
node cli/index.js upload --desc-format simple

# 或在 ci.config.js 中配置
upload: {
    descFormat: 'simple'
}
```

**输出示例：**
```
feat(pages): 添加用户个人中心页面 (a1b2c3d)
```

### 2. 详细格式 (detailed) - 默认
显示最近几条提交的详细列表，适合常规发布。

**配置方式：**
```bash
# 命令行
node cli/index.js upload --desc-format detailed --commit-count 5

# 或在 ci.config.js 中配置
upload: {
    descFormat: 'detailed',
    commitCount: 5
}
```

**输出示例：**
```
最近更新:
1. feat(pages): 添加用户个人中心页面 (a1b2c3d)
2. fix(api): 修复登录接口超时问题 (b2c3d4e)
3. style(components): 优化按钮组件样式 (c3d4e5f)
4. docs: 更新API文档 (d4e5f6g)
5. chore: 更新依赖版本 (e5f6g7h)
```

### 3. 变更日志格式 (changelog) - 推荐
按提交类型分组显示，符合标准变更日志格式，适合正式发布。

**配置方式：**
```bash
# 命令行
node cli/index.js upload --desc-format changelog

# 或在 ci.config.js 中配置
upload: {
    descFormat: 'changelog'
}
```

**输出示例：**
```
✨ 新功能:
• (pages)添加用户个人中心页面 (a1b2c3d)
• (api)新增数据导出功能 (f6g7h8i)

🐛 问题修复:
• (api)修复登录接口超时问题 (b2c3d4e)
• (components)修复表单验证错误 (g7h8i9j)

💄 样式调整:
• (components)优化按钮组件样式 (c3d4e5f)

📝 文档更新:
• 更新API文档 (d4e5f6g)

🔧 其他变更:
• 更新依赖版本 (e5f6g7h)
```

## 配置选项详解

### 基础配置

在 `ci.config.js` 中的 `upload` 部分添加以下配置：

```javascript
upload: {
    // 描述格式：'simple' | 'detailed' | 'changelog'
    descFormat: 'changelog',
    
    // 描述最大长度（超出会自动截断）
    descMaxLength: 500,
    
    // 获取的提交记录数量
    commitCount: 5,
    
    // 是否包含提交哈希值
    includeHash: true
}
```

### 命令行选项

所有配置都可以通过命令行参数覆盖：

```bash
# 完整示例
node cli/index.js upload \
  --desc-format changelog \
  --commit-count 10 \
  --desc-max-length 300 \
  --no-include-hash
```

## 提交信息规范

为了获得最佳的变更日志效果，建议遵循 [Conventional Commits](https://www.conventionalcommits.org/) 规范：

### 提交类型说明

| 类型 | 图标 | 说明 | 示例 |
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

### 提交信息格式

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

**示例：**
```
feat(pages): 添加用户个人中心页面

- 实现用户信息展示
- 添加头像上传功能
- 支持个人信息编辑

Closes #123
```

## 最佳实践

### 1. 选择合适的格式

- **开发阶段**：使用 `simple` 格式，快速迭代
- **测试阶段**：使用 `detailed` 格式，便于测试人员了解变更
- **生产发布**：使用 `changelog` 格式，提供专业的版本说明

### 2. 合理设置参数

```javascript
// 开发环境配置
upload: {
    descFormat: 'simple',
    commitCount: 1,
    descMaxLength: 200,
    includeHash: false
}

// 生产环境配置
upload: {
    descFormat: 'changelog',
    commitCount: 10,
    descMaxLength: 500,
    includeHash: true
}
```

### 3. 环境变量支持

可以通过环境变量动态调整配置：

```bash
# 设置环境变量
export DESC_FORMAT=changelog
export COMMIT_COUNT=8
export DESC_MAX_LENGTH=400

# 运行上传
npm run upload
```

### 4. CI/CD 集成

在 `.gitlab-ci.yml` 或 GitHub Actions 中使用：

```yaml
# GitLab CI
deploy:
  script:
    - npm run upload -- --desc-format changelog --commit-count 5

# GitHub Actions
- name: Upload to WeChat
  run: npm run upload -- --desc-format changelog
```

## 故障排除

### 常见问题

1. **提交信息不符合规范**
   - 解决：使用 `npm run commit` 进行规范化提交
   - 或者手动修改提交信息格式

2. **描述过长被截断**
   - 解决：调整 `descMaxLength` 参数
   - 或者减少 `commitCount` 数量

3. **变更日志格式显示异常**
   - 解决：检查提交信息是否符合 Conventional Commits 规范
   - 不符合规范的提交会归类到"其他变更"

### 调试模式

启用调试模式查看详细信息：

```bash
DEBUG=1 node cli/index.js upload --desc-format changelog
```

## 总结

通过合理配置更新日志格式，可以让您的小程序版本管理更加专业和规范。建议：

1. 在开发阶段使用简单格式提高效率
2. 在正式发布时使用变更日志格式提供清晰的版本说明
3. 结合 Conventional Commits 规范获得最佳效果
4. 根据团队需求调整配置参数

这样既能满足快速开发的需求，又能为用户和团队成员提供清晰的版本变更信息。
