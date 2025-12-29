# 发布 markdown-nice 到 NPM 指南

## 前置准备

### 1. 注册 NPM 账号

如果还没有 NPM 账号：
1. 访问 https://www.npmjs.com/signup
2. 注册账号（用户名建议用 `xyzbit` 或其他你想要的名字）
3. 验证邮箱

### 2. 登录 NPM

```bash
cd /Users/staff/code/ai/markdown-nice
npm login
```

按提示输入：
- Username: 你的 NPM 用户名
- Password: 你的 NPM 密码
- Email: 你的邮箱
- OTP (如果启用了两步验证)

验证登录成功：
```bash
npm whoami
```

## 发布步骤

### 1. 确认 package.json 配置

当前配置：
- **包名**: `@xyzbit/markdown-nice`
- **版本**: `1.0.0`
- **License**: Apache-2.0

确认无误后继续。

### 2. 构建项目

```bash
cd /Users/staff/code/ai/markdown-nice
npm run build:all
```

预期输出：
```
✓ dist/wrapper.js
✓ dist/core.js
✓ dist/publish.js
✓ dist/styles/wenyan-styles.js
✓ dist/math/wenyan-math.js
✓ dist/browser/wenyan-core.js
```

### 3. 测试构建产物

```bash
# 检查 dist 目录
ls -la dist/

# 预期看到以下文件：
# - wrapper.js
# - core.js
# - publish.js
# - types/ (TypeScript 类型定义)
# - styles/wenyan-styles.js
# - math/wenyan-math.js
# - browser/wenyan-core.js
```

### 4. 发布到 NPM

```bash
npm publish --access public
```

> **注意**：因为包名以 `@xyzbit/` 开头（scoped package），需要添加 `--access public` 参数。

预期输出：
```
npm notice Publishing to https://registry.npmjs.org/
+ @xyzbit/markdown-nice@1.0.0
```

### 5. 验证发布

1. 访问 NPM 包页面：
   ```
   https://www.npmjs.com/package/@xyzbit/markdown-nice
   ```

2. 或者测试安装：
   ```bash
   # 在临时目录测试
   mkdir /tmp/test-markdown-nice
   cd /tmp/test-markdown-nice
   npm init -y
   npm install @xyzbit/markdown-nice
   ```

## 更新 FlowDraft-Frontend 依赖

发布成功后，更新前端项目：

```bash
cd /Users/staff/code/ai/FlowDraft-Frontend

# 修改 package.json 中的依赖
# 从: "markdown-nice": "git+https://github.com/xyzbit/markdown-nice.git"
# 改为: "@xyzbit/markdown-nice": "^1.0.0"

# 重新安装
rm -rf node_modules/markdown-nice
npm install

# 启动开发服务器
npm run dev
```

## 后续版本发布

### 更新版本号

```bash
# 补丁版本（bug 修复）：1.0.0 -> 1.0.1
npm version patch

# 次要版本（新功能）：1.0.0 -> 1.1.0
npm version minor

# 主要版本（破坏性变更）：1.0.0 -> 2.0.0
npm version major
```

### 重新发布

```bash
npm run build:all
npm publish --access public
```

## 常见问题

### Q1: 发布失败 - 包名已存在

**错误**：
```
npm error 403 Forbidden - PUT https://registry.npmjs.org/@xyzbit/markdown-nice
npm error 403 You do not have permission to publish "@xyzbit/markdown-nice".
```

**解决**：
1. 包名可能被占用，尝试改名：
   ```json
   "name": "@xyzbit/md-nice"
   ```

2. 或者使用不带 scope 的名字（需要确保全局唯一）：
   ```json
   "name": "flowdraft-markdown-nice"
   ```

### Q2: 登录失败 - 网络问题

**错误**：
```
npm error network request to https://registry.npmjs.org/ failed
```

**解决**：
1. 检查网络连接
2. 如果在国内，可能需要配置代理或使用 VPN
3. 或者使用淘宝镜像（但发布时需要切回官方源）：
   ```bash
   # 查看当前源
   npm config get registry
   
   # 切回官方源
   npm config set registry https://registry.npmjs.org/
   ```

### Q3: 两步验证 OTP

如果启用了两步验证，发布时需要提供 OTP：

```bash
npm publish --access public --otp=123456
```

### Q4: 包大小过大

**错误**：
```
npm error tarball exceeds maximum allowed size
```

**解决**：
检查 `.npmignore` 文件，排除不必要的文件：
```
# .npmignore
src/
test/
node_modules/
*.log
.git/
.vscode/
```

## 撤销发布

如果发布错误，可以在 72 小时内撤销：

```bash
# 撤销特定版本
npm unpublish @xyzbit/markdown-nice@1.0.0

# 撤销整个包（慎用！）
npm unpublish @xyzbit/markdown-nice --force
```

> **警告**：撤销发布后，24小时内不能使用相同的包名和版本号重新发布。

## 发布检查清单

发布前确认：

- [ ] package.json 中的包名、版本、描述正确
- [ ] README.md 文档完整
- [ ] LICENSE 文件存在
- [ ] 已运行 `npm run build:all` 且无错误
- [ ] dist 目录包含所有必需文件
- [ ] 已登录 NPM (`npm whoami` 显示用户名)
- [ ] 网络连接正常
- [ ] 如果是更新版本，version 已递增

## 自动化发布（可选）

可以配置 GitHub Actions 自动发布：

```yaml
# .github/workflows/publish.yml
name: Publish to NPM

on:
  release:
    types: [created]

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
          registry-url: 'https://registry.npmjs.org'
      - run: npm ci
      - run: npm run build:all
      - run: npm publish --access public
        env:
          NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

需要在 GitHub 仓库设置中添加 `NPM_TOKEN` secret。

---

**准备好了吗？** 按照上述步骤执行发布！

有问题随时咨询。🚀

