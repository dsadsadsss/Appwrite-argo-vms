# Appwrite-argo-vms
# Appwrite 部署教程

本教程将指导您如何在 Appwrite 上部署这个代理和订阅管理功能。

## 📋 目录

- [准备工作](#准备工作)
- [注册 Appwrite 账号](#注册-appwrite-账号)
- [创建项目](#创建项目)
- [部署函数](#部署函数)
- [配置环境变量](#配置环境变量)
- [设置定时任务](#设置定时任务)
- [测试和验证](#测试和验证)
- [常见问题](#常见问题)

## 准备工作

在开始之前,请确保您已经:

- 拥有 GitHub 账号
- 将项目代码推送到 GitHub 仓库
- 准备好所需的配置信息(UUID、域名、Token 等)

## 注册 Appwrite 账号

### 1. 访问 Appwrite Cloud

打开浏览器访问 [https://cloud.appwrite.io](https://cloud.appwrite.io)

### 2. 创建账号

- 点击 **Sign Up** 按钮
- 选择注册方式:
  - 使用 GitHub 账号快速注册(推荐)
  - 使用邮箱注册
- 完成邮箱验证(如果使用邮箱注册)

### 3. 登录控制台

注册成功后,您将自动进入 Appwrite 控制台

## 创建项目

### 1. 创建新项目

- 在控制台首页点击 **Create Project**
- 输入项目名称,例如: `Proxy Service`
- 选择项目所在区域(建议选择离您最近的区域)
- 点击 **Create** 完成创建

### 2. 进入项目

创建完成后,点击项目卡片进入项目管理页面

## 部署函数

### 1. 进入 Functions 页面

- 在左侧菜单栏找到 **Functions**
- 点击 **Create Function** 按钮

### 2. 配置函数基本信息

- **Function Name**: 输入函数名称,例如 `proxy-service`
- **Runtime**: 选择 `Node.js 18.0` 或更高版本
- 点击 **Next** 继续

### 3. 选择部署方式 - GitHub 导入

#### 方式一: 连接 GitHub 仓库(推荐)

1. 选择 **Connect Git repository**
2. 点击 **Connect GitHub Account**
3. 授权 Appwrite 访问您的 GitHub
4. 选择包含代码的仓库
5. 配置构建设置:
   - **Branch**: 选择分支(通常是 `main` 或 `master`)
   - **Root Directory**: 留空(如果代码在根目录)
   - **Build Command**: 留空(不需要构建)
   - **Entrypoint**: `index.js`
6. 点击 **Create** 开始部署

#### 方式二: 手动上传

1. 选择 **Manual deployment**
2. 将 `index.js` 和 `package.json` 打包成 `.tar.gz` 文件
3. 上传压缩包
4. 设置 **Entrypoint**: `index.js`
5. 点击 **Deploy**

### 4. 等待部署完成

- 部署过程大约需要 1-3 分钟
- 可以在 **Deployments** 标签页查看部署状态
- 状态变为 **Ready** 表示部署成功

## 配置环境变量

### 1. 进入 Settings 页面

在函数详情页面,点击 **Settings** 标签

### 2. 添加环境变量

找到 **Environment Variables** 部分,点击 **Add Variable** 添加以下变量:

#### 基础配置

| 变量名 | 说明 | 示例值 | 必填 |
|--------|------|--------|------|
| `UUID` | 用户标识符 | `fd80f56e-93f3-4c85-b2a8-c77216c509a7` | 是 |
| `MPATH` | VMess 路径 | `vms` | 否 |
| `VM_PORT` | VMess 端口 | `8001` | 否 |
| `VPATH` | VLess 路径 | `vls` | 否 |
| `VL_PORT` | VLess 端口 | `8002` | 否 |
| `TMP_ARGO` | 协议类型，可选vms,vls,xhttp | `vms` | 否 |

#### 订阅配置

| 变量名 | 说明 | 示例值 | 必填 |
|--------|------|--------|------|
| `SUB_NAME` | 节点名称 | `appwrite` | 否 |
| `SUB_URL` | 订阅上传地址 | `https://example.com/sub` | 否 |

#### 哪吒监控配置(可选)

| 变量名 | 说明 | 示例值 | 必填 |
|--------|------|--------|------|
| `NSERVER` | 哪吒服务器地址 | `monitor.example.com:443` | 否 |
| `NKEY` | 哪吒密钥 | `your-nezha-key` | 否 |
| `NPORT` | 哪吒端口，v1不填 | `443` | 否 |
| `NTLS` | 是否启用 TLS，0关闭 | `1` | 否 |

#### Argo 隧道配置(可选)

| 变量名 | 说明 | 示例值 | 必填 |
|--------|------|--------|------|
| `TOK` | Argo Token | `your-argo-token` | 否 |
| `DOM` | Argo 域名 | `tunnel.example.com` | 否 |

### 3. 保存配置

- 添加完所有变量后,点击 **Update** 保存
- 重新部署函数以使变量生效(点击 **Redeploy** 按钮)

## 设置定时任务

为了保持函数活跃并自动检查进程状态,建议设置定时任务。

### 1. 配置 Execution

在函数的 **Settings** 页面:

- 找到 **Execution** 部分
- 启用 **Scheduled executions**
- 设置执行计划(Cron 表达式):
  - 每 5 分钟执行一次: `*/5 * * * *`
  - 每 10 分钟执行一次: `*/10 * * * *`
  - 每小时执行一次: `0 * * * *`

### 2. 设置超时时间

- **Timeout**: 建议设置为 `300` 秒(5 分钟)
- **Execute Access**: 选择 `Any`

### 3. 保存设置

点击 **Update** 保存配置

## 测试和验证

### 1. 获取函数 URL

- 在函数详情页面,找到 **Domains** 部分
- 复制函数的公开 URL,格式类似:
  ```
  https://[PROJECT-ID].appwrite.global/functions/[FUNCTION-ID]/
  ```

### 2. 测试端点

使用浏览器或 curl 测试以下端点:

#### 测试欢迎页面
```bash
curl https://your-function-url.appwrite.global/
```
应该返回: `welcome to my website!`

#### 查看系统信息
```bash
curl https://your-function-url.appwrite.global/info
```
返回系统信息 JSON

#### 查看订阅信息
```bash
curl https://your-function-url.appwrite.global/[您的UUID]
```
返回订阅数据

#### 健康检查
```bash
curl https://your-function-url.appwrite.global/health
```
返回健康状态


## 常见问题

### Q1: 函数部署失败怎么办?

**解决方案**:
- 检查 `package.json` 中的依赖是否正确
- 确保 Node.js 版本选择正确(≥18.0.0)
- 查看部署日志中的错误信息
- 尝试手动部署而不是 Git 集成

### Q2: 进程无法启动?

**解决方案**:
- 检查环境变量是否正确配置
- 确认 `URL_BOT` 或 `URL_BOT2` 地址可访问
- 查看函数日志中的详细错误信息
- 手动调用 `/start` 端点尝试启动

### Q3: 定时任务不执行?

**解决方案**:
- 确认已启用 **Scheduled executions**
- 检查 Cron 表达式是否正确
- 查看 **Executions** 标签页确认任务是否运行
- 确保超时时间设置合理

### Q4: 无法访问订阅数据?

**解决方案**:
- 确认 UUID 配置正确
- 等待几分钟让进程完全启动
- 调用 `/start` 手动启动进程

### Q5: 函数超时怎么办?

**解决方案**:
- 增加 **Timeout** 设置(最大 900 秒)
- 优化代码执行效率
- 检查是否有阻塞操作

### Q6: 如何更新代码?

**GitHub 自动部署**:
- 推送代码到 GitHub 仓库
- Appwrite 会自动检测并重新部署

**手动更新**:
- 进入函数页面
- 点击 **Create Deployment**
- 上传新的代码包


## 🎉 部署完成

恭喜!您已经成功在 Appwrite 上部署了代理服务。现在可以通过函数 URL 访问服务,并使用订阅功能了。

如有其他问题,请查看 [Appwrite 官方文档](https://appwrite.io/docs) 或在 GitHub Issues 中提问。
