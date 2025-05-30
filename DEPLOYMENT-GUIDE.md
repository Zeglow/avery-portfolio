# Vercel 部署指南 - 解决 Footprint 计数归零问题

## 🚨 问题分析

每次更新代码后，Vercel自动部署会导致footprint计数归零的原因：

1. **配置不一致**：本地的`config.js`与部署时生成的配置不匹配
2. **JSONBin BIN ID不同**：访问了不同的数据存储
3. **环境变量缺失**：Vercel部署时没有正确的API配置

## 🔧 解决方案

### 步骤1: 在Vercel中设置环境变量

1. 登录 [Vercel Dashboard](https://vercel.com/dashboard)
2. 选择你的项目
3. 进入 `Settings` > `Environment Variables`
4. 添加以下环境变量：

```
FOOTPRINT_BIN_ID = YOUR_BIN_ID_FROM_JSONBIN
FOOTPRINT_API_KEY = YOUR_API_KEY_FROM_JSONBIN
```

⚠️ **重要**：使用你GitHub Secrets中的相同值！**永远不要在文档中暴露真实的API密钥！**

### 步骤2: 配置环境变量作用域

为每个环境变量设置：
- ✅ **Production** (必须)
- ✅ **Preview** (推荐)
- ✅ **Development** (可选)

### 步骤3: 触发重新部署

1. 保存环境变量后
2. 进入 `Deployments` 页面
3. 点击最新部署右侧的 `...` 菜单
4. 选择 `Redeploy`
5. 选择 `Use existing Build Cache` 或 `Redeploy without cache`

## 🔄 自动化构建流程

项目现在包含：

### 1. `vercel.json` 配置
```json
{
  "buildCommand": "chmod +x build.sh && ./build.sh",
  "env": {
    "FOOTPRINT_BIN_ID": "@footprint_bin_id",
    "FOOTPRINT_API_KEY": "@footprint_api_key"
  }
}
```

### 2. `build.sh` 构建脚本
- 自动检测环境变量
- 生成正确的配置文件
- 确保与本地配置一致

## 📋 验证部署

### 1. 检查控制台日志
部署完成后，在浏览器开发者工具中查看：
```
✅ Footprint config loaded from environment variables
```

### 2. 验证计数持久性
- 在网站上留下一个footprint
- 触发一次新的部署
- 验证计数是否保持

### 3. 测试API连接
在浏览器控制台运行：
```javascript
console.log(window.FOOTPRINT_CONFIG);
```

应该显示正确的binId和apiKey。

## 🚨 故障排除

### 问题1: 仍然归零
**原因**：环境变量中的BIN ID与GitHub Secrets不同
**解决**：
```bash
# 确保GitHub Secrets和Vercel环境变量完全一致
# 检查JSONBin Dashboard获取正确的配置
```

### 问题2: 配置文件未生成
**原因**：构建脚本权限或环境变量问题
**解决**：
1. 检查部署日志
2. 确认环境变量已正确设置
3. 手动触发重新部署

### 问题3: API调用失败
**原因**：API Key无效或权限不足
**解决**：
1. 验证JSONBin API Key权限
2. 检查API调用限额
3. 更新API Key并重新部署

## 🔒 安全提醒

1. ✅ 环境变量在GitHub Secrets和Vercel中是安全的
2. ✅ 不会暴露在客户端代码中
3. ✅ 只在构建时使用
4. ❌ **永远不要将真实API密钥提交到Git或写在文档中**
5. ❌ **永远不要在公开仓库中暴露敏感配置信息**

## 📞 需要帮助？

如果问题仍然存在：
1. 检查部署日志
2. 验证环境变量设置
3. 确认JSONBin服务状态
4. 联系开发者获取支持

---

**最后更新**: 2024年1月
**适用版本**: GitHub Actions + Vercel部署 