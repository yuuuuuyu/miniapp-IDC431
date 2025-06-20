# miniprogram-ci 增强版

基于微信官方 miniprogram-ci 的增强版本，提供更便捷的小程序开发和部署工具。

## 功能特性

- ✅ **预览功能**: 支持交互式和非交互式预览
- ✅ **上传功能**: 自动版本管理，Git提交信息作为描述
- ✅ **构建npm**: 对应开发者工具的构建npm功能
- ✅ **配置管理**: 灵活的配置文件支持
- ✅ **环境支持**: 支持开发、测试、生产环境配置
- ✨ **规范化日志**: 支持多种更新日志格式（简单/详细/变更日志）
- ✨ **智能格式化**: 自动按提交类型分组，生成专业的版本说明

## 安装

```bash
npm install
```

## 配置

1. 复制 `ci.config.js` 并根据项目实际情况修改配置
2. 从微信公众平台下载私钥文件，放在项目根目录下
3. 配置IP白名单（推荐）

## 使用方法

### 构建npm

构建npm包，对应微信开发者工具的"工具 -> 构建npm"功能。

```bash
# 使用npm脚本
npm run build-npm

# 直接使用CLI
node cli/index.js build-npm

# 显示详细构建信息
node cli/index.js build-npm --verbose

# 排除特定文件
node cli/index.js build-npm --ignores "test/**/*,docs/**/*"
```

#### 构建npm参数说明

miniprogram-ci的构建npm功能支持三个重要参数：

1. **project** (必填): 项目对象，包含appid、项目路径、私钥等信息
2. **options.ignores** (可选): 指定构建npm时需要排除的规则，支持glob模式
3. **options.reporter** (可选): 构建过程的回调函数，用于接收构建信息

#### 智能构建模式

本工具会自动检测项目配置：

- **手动构建模式**: 如果 `project.config.json` 中设置了 `packNpmManually: true` 和 `packNpmRelationList`，将使用 `ci.packNpmManually` 方法
- **标准构建模式**: 否则使用标准的 `ci.packNpm` 方法

这样可以适应不同的项目结构，特别是当 `node_modules` 不在小程序目录下时。

#### 配置文件中的构建npm选项

在 `ci.config.js` 中可以配置默认的构建npm选项：

```javascript
buildNpm: {
    ignores: [], // 构建npm时需要排除的规则
    verbose: false // 是否显示详细构建信息
}
```

### 预览功能

```bash
# 交互式预览（默认）
npm run preview

# 非交互式预览
node cli/index.js preview --no-interactive --qrcode-format terminal
```

### 上传功能

```bash
# 自动递增版本号上传
npm run upload

# 指定版本号上传
node cli/index.js upload --version 1.0.1

# 自定义描述和机器人
node cli/index.js upload --desc "手动指定的描述" --robot 2
```

#### 更新日志格式 🆕

支持三种更新日志格式，让版本描述更加规范：

```bash
# 简单格式：只显示最新提交
node cli/index.js upload --desc-format simple

# 详细格式：显示最近几条提交（默认）
node cli/index.js upload --desc-format detailed --commit-count 5

# 变更日志格式：按类型分组显示（推荐）
node cli/index.js upload --desc-format changelog

# 自定义参数
node cli/index.js upload \
  --desc-format changelog \
  --commit-count 8 \
  --desc-max-length 400 \
  --no-include-hash
```

**格式效果预览：**

```bash
# 查看不同格式的效果演示
npm run demo:changelog
```

详细配置说明请参考：[更新日志格式配置指南](docs/CHANGELOG_FORMATS.md)

## 命令行选项

### 通用选项
- `--interactive` / `--no-interactive`: 启用/禁用交互模式
- `--help`: 显示帮助信息

### 构建npm选项
- `--ignores <patterns>`: 指定需要排除的规则（逗号分隔）
- `--verbose`: 显示详细构建信息

### 预览选项
- `--desc <string>`: 预览描述
- `--qrcode-format <type>`: 二维码格式 (image|terminal)
- `--qrcode-output <path>`: 二维码输出路径
- `--page-path <path>`: 预览页面路径
- `--search-query <query>`: 预览参数
- `--scene <number>`: 场景值

### 上传选项
- `--version <string>`: 版本号
- `--desc <string>`: 上传描述
- `--robot <number>`: CI机器人编号 (1-30)
- `--increment-type <type>`: 版本递增类型 (major|minor|patch)
- `--no-auto-increment`: 禁用自动版本递增
- `--desc-format <type>`: 描述格式 (simple|detailed|changelog)
- `--commit-count <number>`: 获取提交记录数量
- `--desc-max-length <number>`: 描述最大长度
- `--no-include-hash`: 不包含提交哈希值

## 环境变量

- `NODE_ENV`: 环境 (development|staging|production)
- `VERSION`: 版本号
- `ROBOT`: 机器人编号
- `APPID`: 小程序 AppID
- `DEBUG`: 调试模式

## 注意事项

1. 构建npm前请确保项目中存在 `package.json` 和 `node_modules` 目录
2. 构建npm会生成 `miniprogram_npm` 目录，位置取决于配置
3. 如果构建过程中出现警告，请检查相关的npm包是否正确安装
4. 建议在上传前先执行构建npm，确保第三方包能正常使用

## 构建位置配置

为了确保开发者工具和CI工具的构建行为一致，建议在 `project.config.json` 中配置：

```json
{
  "setting": {
    "packNpmManually": false,
    "packNpmRelationList": [
      {
        "packageJsonPath": "./package.json",
        "miniprogramNpmDistDir": "./"
      }
    ]
  }
}
```

这样配置后：
- **开发者工具**: 会在项目根目录生成 `miniprogram_npm`
- **CI工具**: 也会在项目根目录生成 `miniprogram_npm`
- 两者行为保持一致

### 构建位置说明

- `miniprogramNpmDistDir: "./"` - 构建到项目根目录
- `miniprogramNpmDistDir: "./miniprogram"` - 构建到miniprogram目录下

根据你的项目结构选择合适的配置。

## 许可证

MIT
