# 更新日志格式配置指南

本文档详细介绍小程序上传时的更新日志格式配置，让您的版本描述更加规范和易读。

## 📋 支持的格式类型

### 1. 简单格式 (simple)
**适用场景**：快速迭代的开发阶段
**特点**：只显示最新的一条提交信息，输出简洁

**配置方式：**
```bash
# 命令行
node cli/index.js upload --desc-format simple

# 配置文件
upload: {
    descFormat: 'simple'
}
```

**输出示例：**
```
feat(pages): 添加用户个人中心页面 (a1b2c3d)
```

### 2. 详细格式 (detailed) - 默认
**适用场景**：常规发布和测试阶段
**特点**：显示最近几条提交的详细列表，提供完整的变更历史

**配置方式：**
```bash
# 命令行
node cli/index.js upload --desc-format detailed --commit-count 5

# 配置文件
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
**适用场景**：正式发布，提供专业的版本说明
**特点**：按提交类型自动分组显示，符合标准变更日志格式

**配置方式：**
```bash
# 命令行
node cli/index.js upload --desc-format changelog

# 配置文件
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

## ⚙️ 配置选项详解

### 基础配置

在 `ci.config.js` 中的 `upload` 部分添加配置：

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

### 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `descFormat` | string | `'detailed'` | 描述格式类型 |
| `commitCount` | number | `5` | 获取的提交记录数量 |
| `descMaxLength` | number | `500` | 描述最大长度 |
| `includeHash` | boolean | `true` | 是否包含提交哈希值 |

## 📝 提交信息规范

为了获得最佳的变更日志效果，建议遵循项目的提交规范。详细说明请参考 [COMMIT_GUIDE.md](COMMIT_GUIDE.md)。

### 提交类型与图标映射

变更日志格式会根据提交类型自动添加相应图标：

| 类型 | 图标 | 分组名称 |
|------|------|----------|
| `feat` | ✨ | 新功能 |
| `fix` | 🐛 | 问题修复 |
| `docs` | 📝 | 文档更新 |
| `style` | 💄 | 样式调整 |
| `refactor` | ♻️ | 代码重构 |
| `perf` | ⚡ | 性能优化 |
| `test` | ✅ | 测试相关 |
| `build` | 📦 | 构建相关 |
| `ci` | 👷 | CI/CD |
| `chore` | 🔧 | 其他变更 |

### 快速提交
```bash
npm run commit  # 使用交互式提交工具
```

## 🎯 最佳实践

### 1. 不同阶段使用不同格式

| 阶段 | 推荐格式 | 原因 |
|------|----------|------|
| **开发阶段** | `simple` | 快速迭代，简洁明了 |
| **测试阶段** | `detailed` | 便于测试人员了解变更 |
| **生产发布** | `changelog` | 提供专业的版本说明 |

### 2. 环境配置示例

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

### 3. CI/CD 集成

```yaml
# GitHub Actions 示例
- name: Upload to WeChat
  run: |
    node cli/index.js upload \
      --desc-format changelog \
      --commit-count 5

# GitLab CI 示例
deploy:
  script:
    - npm run upload -- --desc-format changelog
```

## 🔧 故障排除

### 常见问题

**Q: 描述过长被截断怎么办？**
A: 调整 `descMaxLength` 参数或减少 `commitCount` 数量

**Q: 变更日志格式显示异常？**
A: 检查提交信息是否符合规范，不符合规范的提交会归类到"其他变更"

**Q: 如何查看详细的处理过程？**
A: 启用调试模式：
```bash
DEBUG=1 node cli/index.js upload --desc-format changelog
```

### 演示工具

查看不同格式的效果：
```bash
npm run demo:changelog  # 如果项目中有此脚本
```

## 📚 相关文档

- **[COMMIT_GUIDE.md](COMMIT_GUIDE.md)** - Git 提交规范详细指南
- **[README.md](README.md)** - 项目文档导航

## 💡 总结

通过合理配置更新日志格式，让小程序版本管理更加专业：

1. **开发阶段** - 使用简单格式提高效率
2. **正式发布** - 使用变更日志格式提供清晰说明
3. **结合规范** - 配合 Conventional Commits 获得最佳效果
4. **灵活配置** - 根据团队需求调整参数
