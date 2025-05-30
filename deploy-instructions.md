# GitHub Actions部署包使用指南

## 📦 下载完成后的部署步骤

### 1. 解压部署包
```bash
# 创建临时部署目录
mkdir ~/temp-deploy
cd ~/temp-deploy

# 将下载的portfolio-ready-to-deploy.zip解压到当前目录
unzip ~/Downloads/portfolio-ready-to-deploy.zip
```

### 2. 验证配置文件
```bash
# 检查config.js文件是否正确生成
cat config.js

# 应该看到类似以下内容：
# window.FOOTPRINT_CONFIG = {
#   binId: 'YOUR_BIN_ID',
#   apiKey: 'YOUR_API_KEY_FROM_GITHUB_SECRETS',
#   enabled: true
# };
```

### 3. 部署到Vercel
```bash
# 在解压的目录中运行部署命令
vercel --prod

# 如果需要重新链接项目
vercel link --yes

# 然后部署
vercel --prod
```

### 4. 验证部署
部署完成后：
1. 访问新的部署URL
2. 打开浏览器开发者工具
3. 在控制台应该看到：`✅ Footprint config loaded from environment variables`
4. 测试footprint功能是否正常工作

## ⚠️ 重要提醒

- 每次代码更新后，都需要重新下载新的GitHub Actions artifact
- artifact中的config.js包含了正确的GitHub Secrets配置
- 不要直接部署本地代码，因为本地的config.js可能不是最新的
- **永远不要在代码或文档中暴露真实的API密钥** 