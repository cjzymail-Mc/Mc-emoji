# Claude 账户会话迁移脚本 — 实施计划

## 背景
用户拥有 3 个 Claude Code Pro 账户（mc、yk、xh），各自使用独立的配置目录。当某个账户的 token 消耗达到上限时，需要将会话数据迁移到另一个账户，以便通过 `claude --resume` 继续工作。目前这个过程是手工操作，步骤繁琐且容易出错。本脚本旨在通过一个简单的图形界面自动完成整个迁移流程。

## 账户目录映射

| 账户 | 目录路径 |
|------|----------|
| mc | `%USERPROFILE%\.claude-mc\` |
| yk | `%USERPROFILE%\.claude\` |
| xh | `%USERPROFILE%\.claude-xh\` |

## 需要迁移的文件

针对某个项目（例如 `D--Technique-Support-Claude-Code-Learning-Mc-emoji`）：
1. **项目文件夹**：`<账户目录>\projects\<项目名>\`（整个文件夹）
2. **history.jsonl**：`<账户目录>\history.jsonl`
3. **settings.json**：`<账户目录>\settings.json`

## 实施计划

### 创建文件：`D:\Technique Support\Claude Code Learning\Mc-emoji\claude_migrate.py`

使用 Python 内置的 **tkinter** 库开发（无需安装额外依赖）。

### 界面流程

**第一个窗口 — 迁移方向选择：**
- 自动检测当前项目：根据脚本运行时的当前工作目录（CWD）转换为 Claude 的文件夹命名格式（将 `:\` 和 `\` 及空格替换为 `-`）
- 布局从上到下：
  1. 标题：「Claude 账户会话迁移工具」
  2. 当前项目名称（加粗/醒目显示）
  3. 项目存在状态（一行）：`mc: ✓ 存在  |  yk: ✓ 存在  |  xh: ✗ 不存在`
  4. 各账户对应路径（三行）：
     - `账户1：mc  ——  %USERPROFILE%\.claude-mc\`
     - `账户2：yk  ——  %USERPROFILE%\.claude\`
     - `账户3：xh  ——  %USERPROFILE%\.claude-xh\`
  5. 分隔线
  6. 6 个按钮，分 3 组排列（组间有分隔线）：
     - `【账户1：mc】→【账户2：yk】` | `【账户1：mc】→【账户3：xh】`
     - `【账户2：yk】→【账户1：mc】` | `【账户2：yk】→【账户3：xh】`
     - `【账户3：xh】→【账户1：mc】` | `【账户3：xh】→【账户2：yk】`

**第二个窗口 — 确认对话框：**
- 显示信息：
  - 源账户名称 + 完整路径
  - 目标账户名称 + 完整路径
  - 项目文件夹名称
  - 备份存放位置
  - 将要迁移的文件/文件夹列表
- 两个按钮：
  - `确认执行`（立即执行迁移）
  - `返回`（返回上级菜单）

### 迁移逻辑（点击"确认执行"后）

**第1步 — 确定备份路径：**
- 备份目录：`<源账户目录>\back-up\backup#NNN`
- NNN 自动递增，基于已有备份编号，三位数补零（001、002、003...）

**第2步 — 备份源账户数据：**
- 复制 `<源账户目录>\projects\<项目>` → `<备份目录>\<项目>\`（项目文件夹直接放在 backup#NNN 下，不带 projects 中间层）
- 复制 `<源账户目录>\history.jsonl` → `<备份目录>\history.jsonl`
- 复制 `<源账户目录>\settings.json` → `<备份目录>\settings.json`

**第3步 — 清理目标账户旧数据：**
- 删除 `<目标账户目录>\projects\<项目>\`（如果存在）
- 删除 `<目标账户目录>\history.jsonl`（如果存在）
- 删除 `<目标账户目录>\settings.json`（如果存在）

**第4步 — 复制到目标账户：**
- 复制 `<源账户目录>\projects\<项目>` → `<目标账户目录>\projects\<项目>\`
- 复制 `<源账户目录>\history.jsonl` → `<目标账户目录>\history.jsonl`
- 复制 `<源账户目录>\settings.json` → `<目标账户目录>\settings.json`

**第5步 — 显示结果：**
- 弹出成功提示框，提醒用户执行 `claude --resume` 继续工作

### 关键技术细节
- 使用 `shutil.copytree` 复制文件夹，使用 `shutil.copy2` 复制单个文件
- 使用 `shutil.rmtree` 删除文件夹，使用 `os.remove` 删除单个文件
- 使用 `os.path.expandvars` 解析 `%USERPROFILE%` 环境变量
- 项目名称自动检测：将 CWD 路径（如 `D:\Technique Support\Claude Code Learning\Mc-emoji`）转换为 `D--Technique-Support-Claude-Code-Learning-Mc-emoji`
- 错误处理：对所有文件操作使用 try/except，失败时在 messagebox 中显示错误信息
- 界面文字全部使用中文

## 验证步骤
1. 在项目目录下运行 `python claude_migrate.py`
2. 验证主窗口显示 6 个迁移按钮和自动检测到的项目名称
3. 点击某个按钮，验证确认对话框显示正确的路径信息
4. 点击"返回"，验证能回到主菜单
5. 执行一次实际迁移（如 yk → mc），验证：
   - 源账户的 `back-up\backup#001\` 中已创建备份
   - 目标账户的旧项目文件夹已删除
   - 源账户的文件已正确复制到目标账户
6. 在目标账户中执行 `claude --resume`，验证会话恢复正常
