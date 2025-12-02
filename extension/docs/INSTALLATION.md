# TRQuant Cursor Extension 安装指南

## 目录

1. [系统要求](#系统要求)
2. [Linux 安装](#linux-安装)
3. [Windows 安装](#windows-安装)
4. [macOS 安装](#macos-安装)
5. [开发者模式](#开发者模式)
6. [配置说明](#配置说明)
7. [MCP 设置](#mcp-设置)
8. [常见问题](#常见问题)

---

## 系统要求

### 必需组件

| 组件 | 版本要求 |
|------|----------|
| Cursor IDE | 最新版本 |
| Node.js | >= 18.0.0 |
| Python | >= 3.10 |
| npm / yarn | 最新版本 |

### Python 依赖

```
jqdatasdk>=1.8.0
pandas>=1.5.0
numpy>=1.24.0
pymongo>=4.0.0
akshare>=1.10.0
```

---

## Linux 安装

### 自动安装（推荐）

```bash
# 进入项目目录
cd /path/to/TRQuant/extension

# 运行安装脚本
chmod +x scripts/setup.sh
./scripts/setup.sh
```

### 手动安装

#### 1. 安装 Node.js 依赖

```bash
cd /path/to/TRQuant/extension
npm install
```

#### 2. 创建 Python 虚拟环境

```bash
# 在 TRQuant 根目录
cd /path/to/TRQuant
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

#### 3. 编译 TypeScript

```bash
cd extension
npm run compile
```

#### 4. 安装扩展

方法A - 开发者模式：
```bash
# 在 Cursor 中按 F5 启动调试
```

方法B - 打包安装：
```bash
# 安装 vsce
npm install -g @vscode/vsce

# 打包
vsce package

# 安装生成的 .vsix 文件
cursor --install-extension trquant-*.vsix
```

---

## Windows 安装

### 自动安装（推荐）

以管理员身份运行 PowerShell：

```powershell
# 进入项目目录
cd C:\path\to\TRQuant\extension

# 运行安装脚本
.\scripts\setup.bat
```

### 手动安装

#### 1. 安装 Node.js 依赖

```powershell
cd C:\path\to\TRQuant\extension
npm install
```

#### 2. 创建 Python 虚拟环境

```powershell
# 在 TRQuant 根目录
cd C:\path\to\TRQuant
python -m venv venv
.\venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

#### 3. 编译 TypeScript

```powershell
cd extension
npm run compile
```

#### 4. 配置 Python 路径

编辑 Cursor 设置 (`settings.json`)：

```json
{
    "trquant.pythonPath": "C:\\path\\to\\TRQuant\\venv\\Scripts\\python.exe"
}
```

---

## macOS 安装

### 自动安装（推荐）

```bash
# 进入项目目录
cd /path/to/TRQuant/extension

# 运行安装脚本
chmod +x scripts/setup.sh
./scripts/setup.sh
```

### 手动安装

与 Linux 步骤相同。

---

## 开发者模式

### 调试扩展

1. 在 Cursor 中打开 `extension` 目录
2. 按 `F5` 启动调试
3. 新窗口中测试扩展功能

### 实时编译

```bash
npm run watch
```

### 运行测试

```bash
# Python 测试
cd extension/python
python test_bridge.py
python test_mcp.py
```

---

## 配置说明

### 扩展配置项

在 Cursor 设置中配置（JSON）：

```json
{
    // Python 解释器路径
    "trquant.pythonPath": "python3",
    
    // 服务器配置
    "trquant.serverHost": "127.0.0.1",
    "trquant.serverPort": 5000,
    "trquant.timeout": 60000,
    
    // MCP 配置
    "trquant.mcpEnabled": true,
    "trquant.mcpPort": 5001,
    
    // 策略默认配置
    "trquant.defaultPlatform": "ptrade",
    "trquant.defaultStyle": "multi_factor",
    
    // 风控默认参数
    "trquant.defaultMaxPosition": 0.1,
    "trquant.defaultStopLoss": 0.08,
    "trquant.defaultTakeProfit": 0.2
}
```

### JQData 配置

确保 TRQuant 根目录有 `jqdata_config.json`：

```json
{
    "username": "your_username",
    "password": "your_password"
}
```

---

## MCP 设置

### 自动注册

1. 在 Cursor 中执行命令 `TRQuant: 启用 MCP Server`
2. 重启 Cursor

### 手动注册

编辑 MCP 配置文件：

**Linux**: `~/.cursor/mcp.json`
**macOS**: `~/Library/Application Support/Cursor/mcp.json`
**Windows**: `%APPDATA%\Cursor\mcp.json`

添加以下内容：

```json
{
    "mcpServers": {
        "trquant": {
            "command": "/path/to/TRQuant/venv/bin/python",
            "args": ["/path/to/TRQuant/extension/python/mcp_server.py"],
            "env": {
                "PYTHONIOENCODING": "utf-8",
                "TRQUANT_ROOT": "/path/to/TRQuant"
            }
        }
    }
}
```

### 验证 MCP

重启 Cursor 后，在 AI 对话中输入：

```
请调用 trquant_market_status 工具获取市场状态
```

如果配置正确，AI 将返回市场状态信息。

---

## 常见问题

### Q: 命令执行超时

**A**: 增加超时时间：
```json
{
    "trquant.timeout": 120000
}
```

### Q: Python 找不到

**A**: 配置完整路径：
```json
{
    "trquant.pythonPath": "/usr/bin/python3"
}
```

### Q: JQData 认证失败

**A**: 检查 `jqdata_config.json` 配置是否正确，账号是否有效。

### Q: MCP 工具不可用

**A**: 
1. 确认 `mcp.json` 配置正确
2. 重启 Cursor
3. 检查 Python 路径和依赖

### Q: WebView 显示空白

**A**: 
1. 检查开发者工具 (Ctrl+Shift+I) 的控制台错误
2. 确保 WebView 允许脚本执行

### Q: 策略代码生成失败

**A**: 
1. 检查因子是否有效
2. 查看 TRQuant Output 通道的日志

---

## 获取帮助

- 查看日志：`TRQuant: 查看日志`
- 项目文档：`/docs` 目录
- 提交问题：GitHub Issues
