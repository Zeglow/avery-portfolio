# 📦 手动部署指南 - GitHub Actions + Vercel

## 🎯 工作流程概述

这个工作流程确保你的footprint计数器在每次部署后保持一致：

```
代码修改 → GitHub → GitHub Actions生成package → 手动下载 → 手动部署 → ✅ 计数保持
```

## 🚨 **重要：首先禁用Vercel自动部署**

**⚠️ 必须执行这一步，否则Vercel会自动部署没有config.js的版本！**

1. 登录 [Vercel Dashboard](https://vercel.com/dashboard)
2. 选择你的portfolio项目
3. 进入 `Settings` → `Git`  
4. 找到 **"Auto Deploy"** 或 **"Automatic Deployments"**
5. **禁用/关闭** 这个选项
6. 保存设置

## 📋 完整部署步骤

### 步骤 1: 修改和提交代码
```bash
# 在本地修改代码
git add .
git commit -m "Update portfolio content"
git push origin main
```

### 步骤 2: 等待GitHub Actions完成
1. 访问你的GitHub仓库
2. 点击 `Actions` 标签页
3. 等待 "Generate Deployment Package with Footprint Config" 完成
4. 看到绿色✅表示成功

### 步骤 3: 下载部署包
1. 在Actions页面点击最新的成功运行
2. 滚动到页面底部的 `Artifacts` 部分
3. 点击下载 `portfolio-ready-to-deploy`
4. 文件会下载到你的Downloads文件夹

### 步骤 4: 解压和部署
```bash
# 创建临时部署目录
mkdir ~/temp-deploy
cd ~/temp-deploy

# 解压下载的文件
unzip ~/Downloads/portfolio-ready-to-deploy.zip

# 验证config.js存在且正确
ls -la config.js
head -3 config.js  # 应该看到FOOTPRINT_CONFIG

# 部署到Vercel
vercel --prod

# 如果需要重新链接项目
# vercel link --yes
# vercel --prod
```

### 步骤 5: 验证部署
1. 访问新的部署URL
2. 打开浏览器开发者工具 (F12)
3. 在控制台应该看到：`✅ Footprint config loaded from environment variables`
4. 测试footprint功能确保计数正确

## 🔍 故障排除

### 问题1: GitHub Actions失败
**检查项目：**
- GitHub Secrets是否正确设置 (`FOOTPRINT_BIN_ID`, `FOOTPRINT_API_KEY`)
- 是否有语法错误在提交的代码中

### 问题2: 下载的包中没有config.js
**解决方案：**
- 检查GitHub Actions日志
- 确认Secrets已正确配置
- 重新运行GitHub Actions

### 问题3: 部署后footprint仍然清零
**可能原因：**
- ❌ Vercel自动部署仍然启用（部署了没有config.js的版本）
- ❌ 使用了错误的artifact
- ❌ config.js文件损坏

**解决方案：**
1. **立即禁用Vercel自动部署**
2. 检查浏览器控制台是否有config.js加载成功的消息
3. 重新下载最新的artifact并部署

### 问题4: Vercel部署失败
**常见解决方案：**
```bash
# 重新链接项目
vercel link

# 或者删除.vercel文件夹重新开始
rm -rf .vercel
vercel

# 然后部署
vercel --prod
```

## ⚡ 快速检查清单

每次部署前确认：
- [ ] 代码已推送到GitHub
- [ ] GitHub Actions运行成功（绿色✅）
- [ ] 下载了最新的artifact
- [ ] Vercel自动部署已禁用
- [ ] 部署后检查浏览器控制台

## 🎯 成功标志

部署成功后，你应该看到：
1. 浏览器控制台显示：`✅ Footprint config loaded from environment variables`
2. Footprint计数器显示正确的数字
3. 可以正常留下footprint

---

**📝 记住**：每次修改代码都需要重复这个流程，但这确保了你的API keys安全和footprint数据一致性！ 