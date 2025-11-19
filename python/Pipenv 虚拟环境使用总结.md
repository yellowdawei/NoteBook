# Pipenv 虚拟环境使用指南（完整版 - PowerShell + CMD）

## 🎯 快速开始

### 目标：在当前目录创建虚拟环境

text

```
项目文件夹/
├── .venv/          # ← 虚拟环境（在当前目录）
├── Pipfile         # 依赖配置
├── Pipfile.lock    # 版本锁定
└── 你的代码文件
```

```
# 下载pipenv
pip install pipenv
# pip install --user pipenv
```

------

## 🔧 环境创建流程

### PowerShell 版本

powershell

```
# 设置环境变量（当前会话有效）
$env:PIPENV_VENV_IN_PROJECT=1
# 如果有旧环境，先删除
pipenv --rm

# 创建虚拟环境
pipenv install
# 或者指定Python版本
pipenv --python 3.9 install

# 激活虚拟环境
pipenv shell

# 在当前会话中绕过执行策略
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope Process

# 然后激活虚拟环境
.\.venv\Scripts\activate.ps1
```



### Command Prompt (CMD) 版本

cmd

```
REM 设置环境变量（当前窗口有效）
set PIPENV_VENV_IN_PROJECT=1

REM 删除旧环境
pipenv --rm

REM 创建新环境
pipenv install

REM 或者指定Python版本
pipenv --python 3.9 install
```



------

## ✅ 验证创建位置

### 通用验证命令

powershell

```
# PowerShell 和 CMD 都适用
pipenv --venv

# ✅ 成功：显示 当前目录\.venv
# 例如：D:\Desktop\pychat\.venv

# ❌ 失败：显示用户目录路径
# 例如：C:\Users\用户名\.virtualenvs\...
```



### 详细验证

powershell

```
# 检查虚拟环境路径
pipenv --venv

# 检查Python解释器路径
pipenv --py

# 检查是否在虚拟环境中
python -c "import sys; print('Python路径:', sys.executable)"
```



------

## ⚙️ 永久配置方案

### 方法一：系统环境变量（推荐）

**适用于 PowerShell 和 CMD**

1. **Win + R** → 输入 `sysdm.cpl` → 确定
2. 点击"**高级**"选项卡 → "**环境变量**"
3. 在"**系统变量**"中点击"**新建**"：
   - 变量名：`PIPENV_VENV_IN_PROJECT`
   - 变量值：`1`
4. 点击"确定"保存
5. **重启所有命令行窗口**

### 方法二：项目级配置

在 `Pipfile` 中指定 Python 版本：

ini

```
[requires]
python_version = "3.9"

[scripts]
start = "python app.py"
```



------

## 🚀 使用虚拟环境

### 方式1：激活环境（推荐）

**PowerShell:**

powershell

```
# 进入虚拟环境
pipenv shell
# ✅ 提示符显示： (项目名-随机字符) 或 (.venv)

# 退出环境
exit

# 如果激活脚本报错，先设置执行策略：
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```



**Command Prompt:**

cmd

```
REM 进入虚拟环境
pipenv shell
REM ✅ 提示符显示： (项目名-随机字符) 或 (.venv)

REM 退出环境
exit
```



### 方式2：手动激活

**PowerShell:**

powershell

```
# 手动激活（需要先创建好环境）
.\.venv\Scripts\Activate.ps1

# 或者使用完整路径
& ".\\.venv\\Scripts\\Activate.ps1"
```



**Command Prompt:**

cmd

```
REM 手动激活
.venv\Scripts\activate.bat

REM 退出
deactivate
```



### 方式3：直接运行（无需激活）

**PowerShell & CMD 通用:**

powershell

```
# 在虚拟环境中运行Python脚本
pipenv run python your_script.py

# 运行其他命令
pipenv run pip list
pipenv run python -m flask run
```



------

## 📦 包管理命令

### 安装依赖

**PowerShell & CMD 通用:**

powershell

```
# 安装包到虚拟环境
pipenv install flask
pipenv install requests numpy pandas

# 安装开发依赖（只在开发环境使用）
pipenv install --dev pytest
pipenv install --dev black flake8

# 从现有requirements.txt安装
pipenv install -r requirements.txt

# 安装所有Pipfile中的依赖
pipenv install --dev
```



### 查看和管理依赖

powershell

```
# 查看已安装的包
pipenv graph

# 查看依赖关系树
pipenv graph --reverse

# 更新所有包
pipenv update

# 更新特定包
pipenv update package_name

# 卸载包
pipenv uninstall package_name
```



------

## 📋 完整命令速查表

| 命令                                | 功能              |
| :---------------------------------- | :---------------- |
| `pipenv install`                    | 创建环境+安装依赖 |
| `pipenv （run pip）install package` | 安装包            |
| `pipenv install --dev package`      | 安装开发依赖      |
| `pipenv shell`                      | 进入虚拟环境      |
| `pipenv run command`                | 直接运行命令      |
| `pipenv --venv`                     | 查看环境路径      |
| `pipenv --py`                       | 查看Python路径    |
| `pipenv --rm`                       | 删除虚拟环境      |
| `pipenv graph`                      | 查看依赖树        |
| `pipenv update`                     | 更新所有包        |
| `pipenv lock`                       | 生成lock文件      |
| `pipenv check`                      | 安全检查          |
| `pipenv clean`                      | 清理缓存          |
| pipenv run pip list`                | 查看当前目录的包  |

------

## 🚨 故障排除指南

### 问题1：虚拟环境不在当前目录

**症状：** `pipenv --venv` 显示用户目录路径

**解决方案：**

powershell

```
# PowerShell 解决方案
pipenv --rm
$env:PIPENV_VENV_IN_PROJECT=1
pipenv install
```



cmd

```
REM CMD 解决方案
pipenv --rm
set PIPENV_VENV_IN_PROJECT=1
pipenv install
```



### 问题2：PowerShell 执行策略限制

**错误信息：** "无法加载脚本，因为在此系统上禁止运行脚本"

**解决方案：**

powershell

```
# 以管理员身份运行PowerShell，然后执行：
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

# 或者只为当前用户设置：
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
```



### 问题3：Python版本不对

**解决方案：**

powershell

```
# 删除重装指定版本
pipenv --rm
pipenv --python 3.9 install
```



### 问题4：权限问题

**解决方案：**

powershell

```
# 以管理员身份运行命令行
# 或者使用 --user 参数
pipenv install --user
```



------

## 💡 最佳实践

### 1. **项目结构规范**

text

```
my-project/
├── .venv/              # 虚拟环境（在.gitignore中）
├── src/                # 源代码
├── tests/              # 测试代码
├── Pipfile             # ✅ 提交到Git
├── Pipfile.lock        # ✅ 提交到Git
└── .gitignore          # 包含 `.venv/`
```



### 2. **.gitignore 配置**

text

```
# 虚拟环境
.venv/
venv/
env/

# Pipenv相关
.Pipfile.local
.python-version
```



### 3. **依赖管理**

powershell

```
# 明确区分生产依赖和开发依赖
pipenv install flask          # 生产依赖
pipenv install --dev pytest   # 开发依赖

# 定期更新依赖
pipenv update --outdated      # 检查可更新包
pipenv update                 # 更新所有包
```



### 4. **团队协作**

powershell

```
# 新成员加入项目时
git clone project-url
cd project
pipenv install           # 安装生产依赖
pipenv install --dev     # 安装开发依赖
pipenv shell             # 进入环境
```



------

## ✅ 成功检查清单

- `pipenv --venv` 显示当前目录下的 `.venv` 路径
- 项目根目录存在 `.venv` 文件夹
- `pipenv run python --version` 显示正确的Python版本
- `pipenv shell` 能正常进入虚拟环境
- `pipenv graph` 能显示安装的包
- `.gitignore` 中包含 `.venv/`

------

## 🎯 快速参考卡片

### PowerShell 用户

powershell

```
# 创建环境
$env:PIPENV_VENV_IN_PROJECT=1; pipenv install

# 日常使用
pipenv shell          # 进入
pipenv install flask  # 装包
exit                  # 退出
```



### CMD 用户

cmd

```
REM 创建环境
set PIPENV_VENV_IN_PROJECT=1 && pipenv install

REM 日常使用
pipenv shell          REM 进入
pipenv install flask  REM 装包
exit                  REM 退出
```

