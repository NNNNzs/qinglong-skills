# 🐉 青龙面板 OpenClaw 技能

通过 OpenClaw 控制 [青龙面板](https://github.com/whyour/qinglong) 的定时任务、环境变量、脚本、依赖、日志等。

## 功能一览

| 模块 | 支持操作 |
|------|----------|
| **定时任务** | 列表、创建、编辑、删除、运行、停止、启用/禁用、置顶/取消置顶、查看日志 |
| **环境变量** | 列表、创建、编辑、删除、启用/禁用、排序、置顶 |
| **脚本管理** | 列表、查看、保存、运行、停止、删除 |
| **依赖管理** | 列表、安装、重装、取消、删除（Node/Linux/Python3） |
| **订阅管理** | 列表、运行、停止、启用/禁用、删除、查看日志 |
| **日志查看** | 列表、查看、删除 |
| **系统管理** | 系统信息、配置、检查更新、更新系统、重启、执行命令、重置密码 |
| **配置文件** | 列表、查看、保存 |

## 安装配置

### 第一步：获取青龙 API 凭证

1. 打开青龙面板 Web 界面
2. 进入 **配置** → **应用**
3. 点击 **创建应用**
4. 勾选需要的权限范围（如：crons、envs、scripts、logs、system）
5. 复制生成的 **Client ID** 和 **Client Secret**

### 第二步：在 OpenClaw Gateway UI 配置环境变量

本技能定义了 3 个环境变量，可在 Gateway UI 中直接配置：

| 变量名 | 说明 | 是否必填 |
|--------|------|----------|
| `QINGLONG_URL` | 面板地址，如 `http://192.168.1.100:5700` | ✅ |
| `QINGLONG_CLIENT_ID` | 应用 Client ID | ✅ |
| `QINGLONG_CLIENT_SECRET` | 应用 Client Secret（加密存储） | ✅ |

### 第三步：安装技能

将 `qinglong-skills` 文件夹复制到 OpenClaw 技能目录：

```bash
cp -r qinglong-skills ~/.openclaw/workspace/skills/qinglong
```

## 使用方法

### 通过 OpenClaw 智能体

智能体可直接调用 `scripts/ql.sh` 脚本完成所有操作。

### 定时任务管理

```bash
# 查看所有定时任务
scripts/ql.sh cron list

# 查看指定任务详情
scripts/ql.sh cron get <任务ID>

# 创建定时任务
scripts/ql.sh cron create --command "task test.js" --schedule "0 0 * * *" --name "每日任务"

# 编辑定时任务
scripts/ql.sh cron update <任务ID> --command "task new.js" --schedule "0 12 * * *"

# 删除定时任务（支持批量）
scripts/ql.sh cron delete <任务ID>
scripts/ql.sh cron delete 1 2 3

# 立即运行任务
scripts/ql.sh cron run <任务ID>

# 停止正在运行的任务
scripts/ql.sh cron stop <任务ID>

# 启用 / 禁用任务
scripts/ql.sh cron enable <任务ID>
scripts/ql.sh cron disable <任务ID>

# 置顶 / 取消置顶
scripts/ql.sh cron pin <任务ID>
scripts/ql.sh cron unpin <任务ID>

# 查看任务日志
scripts/ql.sh cron log <任务ID>
```

### 环境变量管理

```bash
# 查看所有环境变量
scripts/ql.sh env list

# 搜索环境变量
scripts/ql.sh env list "JD"

# 创建环境变量
scripts/ql.sh env create --name "JD_COOKIE" --value "pt_key=xxx;pt_pin=xxx" --remarks "备注"

# 编辑环境变量
scripts/ql.sh env update --id <ID> --name "JD_COOKIE" --value "新值"

# 删除环境变量
scripts/ql.sh env delete <ID>

# 启用 / 禁用
scripts/ql.sh env enable <ID>
scripts/ql.sh env disable <ID>
```

### 脚本管理

```bash
# 查看脚本列表
scripts/ql.sh script list

# 查看脚本内容
scripts/ql.sh script get --file "test.js"

# 保存脚本
scripts/ql.sh script save --file "test.js" --content "console.log('hello')"

# 运行脚本
scripts/ql.sh script run --file "test.js"

# 停止脚本
scripts/ql.sh script stop --file "test.js"

# 删除脚本
scripts/ql.sh script delete --file "test.js"
```

### 依赖管理

```bash
# 查看依赖列表
scripts/ql.sh dep list

# 安装 Node.js 依赖（type=0）
scripts/ql.sh dep install --name "axios" --type 0

# 安装 Linux 依赖（type=1）
scripts/ql.sh dep install --name "curl" --type 1

# 安装 Python3 依赖（type=2）
scripts/ql.sh dep install --name "requests" --type 2

# 重装依赖
scripts/ql.sh dep reinstall <ID>

# 删除依赖
scripts/ql.sh dep delete <ID>
```

### 订阅管理

```bash
# 查看订阅列表
scripts/ql.sh sub list

# 运行 / 停止订阅
scripts/ql.sh sub run <ID>
scripts/ql.sh sub stop <ID>

# 启用 / 禁用订阅
scripts/ql.sh sub enable <ID>
scripts/ql.sh sub disable <ID>
```

### 系统管理

```bash
# 查看系统信息（版本、状态等）
scripts/ql.sh system info

# 查看系统配置
scripts/ql.sh system config

# 检查更新
scripts/ql.sh system check-update

# 更新系统
scripts/ql.sh system update

# 重启系统
scripts/ql.sh system reload

# 执行命令
scripts/ql.sh system command-run --command "task test.js"

# 重置登录密码
scripts/ql.sh system auth-reset --username admin --password newpass
```

### Token 管理

```bash
# 刷新 Token
scripts/ql.sh token refresh

# 查看缓存的 Token
scripts/ql.sh token show

# 清除 Token 缓存
scripts/ql.sh token clear
```

## 实用技巧

### 批量操作

```bash
# 批量运行多个任务
scripts/ql.sh cron run 1 2 3 4 5

# 批量启用任务
scripts/ql.sh cron enable 1 2 3

# 批量删除环境变量
scripts/ql.sh env delete 10 11 12
```

### 配合 jq 过滤

```bash
# 查看所有禁用的任务
scripts/ql.sh cron list | jq '.data[] | select(.isDisabled == 1) | {id, name}'

# 查看所有环境变量名和值
scripts/ql.sh env list | jq '.data[] | {name, value}'

# 查看任务执行状态
scripts/ql.sh cron list | jq '.data[] | {id, name, status, last_execution_time}'
```

## 常见问题

### 401 未授权

检查 `QINGLONG_CLIENT_ID` 和 `QINGLONG_CLIENT_SECRET` 是否正确。

### 连接被拒绝

确认 `QINGLONG_URL` 地址可从当前机器访问。如果是 Docker 部署，检查端口映射。

### 权限不足

确保在青龙面板创建应用时勾选了所需的权限范围（Scopes）。

### Token 过期

脚本会自动刷新 Token，无需手动处理。如遇问题可执行：
```bash
scripts/ql.sh token clear
scripts/ql.sh token refresh
```

## 项目结构

```
qinglong-skills/
├── SKILL.md              # OpenClaw 技能定义（含 Gateway UI 环境变量配置）
├── README.md             # 英文说明文档
├── README_zh-CN.md       # 中文说明文档（本文件）
├── scripts/
│   └── ql.sh             # 核心 CLI 脚本，封装全部 REST API
└── references/
    └── api.md            # 完整 API 参考文档
```

## 相关链接

- [青龙面板 GitHub](https://github.com/whyour/qinglong)
- [OpenClaw 文档](https://docs.openclaw.ai)
- [ClawHub 技能市场](https://clawhub.com)
