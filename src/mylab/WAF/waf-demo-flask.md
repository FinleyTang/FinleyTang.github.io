# 使用 Flask 框架实现的 Python 版本 WAF

这个 WAF 会在每个请求处理之前检查所有传递的参数，并根据预定义的正则表达式模式来检测和阻止恶意输入。

## 1. 安装 Flask
如果还没有安装 Flask，可以通过以下命令安装：

```python
pip install Flask
```
## 2. 创建 waf.py 文件
在项目目录下创建 waf.py 文件，并添加以下代码：


```python
from flask import Flask, request, abort
import re

app = Flask(__name__)


# 定义检测恶意输入的正则表达式模式
```python
patterns = [
    re.compile(r'<script\b[^>]*>(.*?)<\/script>', re.IGNORECASE),  # XSS
    re.compile(r'\b(select|union|insert|update|delete|drop|truncate|alter|create|replace|rename|load|declare|handler|lock|unlock|release|call|do|handler|purge|repeat|replace|savepoint|sleep|benchmark)\b', re.IGNORECASE),  # SQLi
    re.compile(r'\b(?:AND|OR|NOT|BETWEEN|LIKE|IN|IS)\b\s+(?:[\'"\d])', re.IGNORECASE),  # SQLi
    re.compile(r'[\'"`;()\[\]{}]', re.IGNORECASE),  # SQLi and XSS
    re.compile(r'/\*.*?\*/', re.IGNORECASE),  # SQLi comments
]


# 定义WAF函数，用于检测并阻止恶意输入

def waf(input_data):
    for pattern in patterns:
        if pattern.search(input_data):
            return True
    return False


# 使用before_request钩子在每个请求处理之前调用WAF函数

@app.before_request
def before_request():
    for key, value in request.args.items():
        if waf(value):
            abort(400, description="Malicious input detected!")

@app.route('/')
def home():
    return "Web Application Home!"

if __name__ == '__main__':
    app.run(host='0.0.0.0')
```
## 3. 运行 Flask 应用
在命令行中运行 Flask 应用：

```python
python waf.py
```
## 4. 验证 WAF 功能
创建一个测试脚本 test.py（或直接在浏览器中访问 URL）来验证 WAF 是否工作：


```python
import requests

# 正常请求
response = requests.get('http://127.0.0.1:5000/?input=normal')
print(response.text)

# 恶意请求
response = requests.get('http://127.0.0.1:5000/?input=<script>alert(\'XSS\')</script>')
print(response.status_code, response.text)

response = requests.get('http://127.0.0.1:5000/?input=SELECT * FROM users')
print(response.status_code, response.text)
```
你应该会看到以下输出：


```console
Web Application Home!
400 <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<title>400 Bad Request</title>
<h1>Bad Request</h1>
<p>Malicious input detected!</p>

400 <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<title>400 Bad Request</title>
<h1>Bad Request</h1>
<p>Malicious input detected!</p>

```
## 总结

以上示例展示了如何在 Python 中使用 Flask 框架实现一个简单的 Web 应用防火墙（WAF）。通过在每个请求处理之前检查传递的参数，WAF 可以有效地检测并阻止潜在的恶意输入。这种方法同样适用于其他框架和编程语言，只需根据具体环境调整实现细节即可。