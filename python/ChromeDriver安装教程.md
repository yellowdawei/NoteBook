## 解决方案

您需要下载与您 Chrome 浏览器版本匹配的 ChromeDriver。以下是具体步骤：

### 1. 检查 Chrome 浏览器版本

1. 打开 Chrome 浏览器
2. 在地址栏输入 `chrome://settings/help` 或点击右上角三个点 > 帮助 > 关于 Google Chrome
3. 记下版本号（例如：137.0.7151.103）

### 2. 下载匹配的 ChromeDriver

1. 访问 ChromeDriver 下载页面：https://chromedriver.chromium.org/downloads
2. 找到与您的 Chrome 版本匹配的 ChromeDriver 版本（137.0.7151.103）
3. 下载对应操作系统的版本（Windows 64位）

### 3. 更新代码使用指定路径的 ChromeDriver

修改您的代码，指定 ChromeDriver 的路径：

```
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from datetime import datetime
import time

# 指定 ChromeDriver 路径
chrome_driver_path = r"D:\path\to\chromedriver.exe"  # 替换为您的实际路径

# 获取当前日期并格式化为YYYYMMDD
current_date = datetime.now().strftime("%Y%m%d")

# 配置登录信息
login_url = "https://fe-xianyun-web.itheima.net/"
username = "demo"
password = f"721itheima.CN032@.{current_date}"
search_keyword = "西安"

# 初始化WebDriver
service = Service(chrome_driver_path)
driver = webdriver.Chrome(service=service)
```