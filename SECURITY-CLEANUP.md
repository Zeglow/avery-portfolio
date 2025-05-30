# 🚨 紧急安全清理指南

## 发现的问题
在Git提交 `f81bb70` 中发现了真实的API key暴露在 `DEPLOYMENT-GUIDE.md` 文件中。

## 立即行动清单

### 1. 🔥 **立即更换API Key（最高优先级）**
1. 登录 [JSONBin.io](https://jsonbin.io/)
2. 删除当前API key: `$2a$10$PbCmT7OtNSzIl3yWYp1uB.eOaW2eL0zlm9hZb5n20QpWI9j3DktRO`
3. 生成新的API key
4. 更新GitHub Secrets中的 `FOOTPRINT_API_KEY`

### 2. 🧹 **清理Git历史记录**

**方案A: 简单清理（推荐）**
```bash
# 1. 从有问题的提交开始重置
git reset --hard f48b233  # 回到问题提交之前

# 2. 重新提交干净的文件
git add .
git commit -m "Clean deployment configuration without sensitive data"

# 3. 强制推送（注意：这会重写历史）
git push --force-with-lease origin main
```

**方案B: 彻底清理（更安全但复杂）**
```bash
# 使用git filter-branch清理敏感数据
git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch DEPLOYMENT-GUIDE.md' \
--prune-empty --tag-name-filter cat -- --all

# 清理本地引用
git for-each-ref --format='delete %(refname)' refs/original | git update-ref --stdin
git reflog expire --expire=now --all
git gc --prune=now

# 强制推送
git push --force-with-lease --all
```

### 3. 📋 **验证清理结果**
```bash
# 确认Git历史中不再包含敏感信息
git log --all -S '6839f8878960c979a5a3301a' --oneline
git log --all -S '$2a$10$PbCmT7OtNSzIl3yWYp1uB' --oneline
```

### 4. 🔒 **预防措施**
1. 添加 `.gitignore` 规则：
   ```
   config.js
   *.key
   *.secret
   ```

2. 使用pre-commit hook检查敏感信息：
   ```bash
   #!/bin/sh
   if git diff --cached | grep -E '\$2a\$10\$|6839f88'; then
     echo "错误：检测到敏感信息，提交被阻止"
     exit 1
   fi
   ```

3. 定期审计：
   ```bash
   git log --all --source --grep="api"
   git log --all -S 'password\|secret\|key' --oneline
   ```

## ⚠️ 重要提醒

1. **立即更换API key** - 这是最重要的！
2. 清理Git历史会重写历史记录
3. 如果有协作者，需要通知他们重新克隆仓库
4. 备份当前仓库状态（如果需要）

## 完成后的检查清单

- [ ] 更换了JSONBin API key
- [ ] 更新了GitHub Secrets
- [ ] 清理了Git历史记录
- [ ] 强制推送了干净的历史
- [ ] 验证历史记录中不再包含敏感信息
- [ ] 测试新的部署流程

---
**紧急程度**: 🔴 HIGH - 立即处理
**创建时间**: $(date) 