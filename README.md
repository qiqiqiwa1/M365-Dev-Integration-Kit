# Microsoft 365 开发工具包 README

### 概述  
本项目为Microsoft 365开发示例集合，涵盖Microsoft Graph API集成、Power Platform低代码开发及Teams扩展开发能力。基于《Office 365自主开发教程》的核心技术框架，提供快速接入Office 365数据的解决方案。

---

### 开发环境配置  
1. **Office 365租户**  
   - 需拥有Microsoft 365商业订阅账户（E3/E5开发者订阅最佳）
2. **Azure AD应用注册**  
   - 在Azure门户创建应用，配置`重定向URI`（Web/SPA类型）  
   - 添加API权限：`Mail.Read`、`Files.ReadWrite.All`等（按需选择）
3. **SDK安装**  
   ```bash
   # .NET开发者
   dotnet add package Microsoft.Graph
   # Node.js开发者  
   npm install @microsoft/microsoft-graph-client
   ```

---

### 认证配置示例  
```csharp
// 使用客户端凭证流（后台服务场景）
var credential = new ClientSecretCredential(
    "tenant_id",
    "client_id",
    "client_secret",
    new TokenCredentialOptions { AuthorityHost = AzureAuthorityHosts.AzurePublicCloud });
var graphClient = new GraphServiceClient(credential);
```
*注：敏感信息应存储在Azure Key Vault或环境变量中*

---

### 核心功能模块  
#### 1. Microsoft Graph数据操作  
- **邮件处理**  
  ```csharp
  var messages = await graphClient.Users["user@domain.com"].Messages
      .Request()
      .Filter("isRead eq false")
      .Top(10)
      .GetAsync();
  ```
  *实现原理：通过Microsoft Graph的`/users/{id}/messages`端点交互*  

#### 2. Power Platform集成  
- **自动化审批流**  
  使用Power Automate连接器触发邮件通知：  
  ```powerapps-formula
  Set(varApprovalRequest, 
      Patch(SharePointList, 
          Defaults(SharePointList), 
          {Title: "预算审批", Amount: 5000}))
  ```
  *支持与SharePoint Online列表数据双向同步*

---

### 部署指南  
1. **应用发布**  
   - Azure Web应用：配置身份验证中间件  
   - Teams应用：通过App Studio打包manifest.json  
2. **权限审核**  
   - 管理员需在https://admin.microsoft.com 同意API权限请求  
3. **监控配置**  
   - 启用Azure Application Insights日志追踪  

---

### 注意事项  
⚠️ **安全规范**  
- 生产环境禁用客户端密钥，改用证书认证或托管身份  
- 遵循最小权限原则（如仅申请`Mail.ReadBasic`而非完整权限）  

⚠️ **API限制**  
- Microsoft Graph单请求最多返回999条记录  
- 批量操作需使用JSON Batching技术  

---

### 资源索引  
- [Microsoft Graph官方文档](https://learn.microsoft.com/graph)  
- 参考书籍：《Programming Microsoft Office 365》（含Microsoft Graph API深度解析）  
- 开发者社区：Microsoft Tech Community > Office 365开发板块  

: 参考《Office365自主开发教程》中关于Microsoft Graph API集成与认证配置的技术规范
```  
