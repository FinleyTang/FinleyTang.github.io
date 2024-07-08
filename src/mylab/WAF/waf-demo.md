# 集成 WAF 到 PHP 应用的完整步骤
以下是将 Web 应用防火墙（WAF）集成到 PHP 应用的详细步骤：

## 1. 确定 php.ini 文件路径
通过命令行确认当前 PHP 使用的配置文件位置：

php --ini
输出示例：
```
Configuration File (php.ini) Path: /usr/local/etc/php
Loaded Configuration File:         /usr/local/etc/php/php.ini
Scan for additional .ini files in: /usr/local/etc/php/conf.d
Additional .ini files parsed:      /usr/local/etc/php/conf.d/docker-php-ext-imap.ini
```

## 2. 复制模板文件并创建 php.ini
如果 Loaded Configuration File 为 (none)，需要从模板文件复制并重命名 php.ini 文件：
```
cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini
```
或

```
cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini
```

## 3. 编辑 php.ini 文件
使用文本编辑器打开 php.ini 文件，并添加 auto_prepend_file 指令：

```
nano /usr/local/etc/php/php.ini
```

在文件中添加或确认以下行：
```
auto_prepend_file=/var/www/html/waf.php
```
保存并关闭文件。

## 4. 创建并配置 waf.php 文件
确保 waf.php 文件存在且具有读取权限：
```
sudo touch /var/www/html/waf.php
sudo chown www-data:www-data /var/www/html/waf.php
sudo chmod 644 /var/www/html/waf.php
```
编辑 waf.php 文件：

```
nano /var/www/html/waf.php
```

添加以下内容：

```
<?php
// 防止函数重复声明
if (!function_exists('waf')) {
    function waf($input) {
        $patterns = [
            '/<script\b[^>]*>(.*?)<\/script>/is', // XSS
            '/(select|union|insert|update|delete|drop|truncate|alter|create|replace|rename|load|declare|handler|lock|unlock|release|call|do|handler|purge|repeat|replace|savepoint|sleep|benchmark)\b/i', // SQLi
            '/(\b(?:AND|OR|NOT|BETWEEN|LIKE|IN|IS)\b)\s+(?:[\'"\d])/i', // SQLi
            '/[\'"`;()\[\]{}]/', // SQLi and XSS
            '/\/\*.*?\*\//s', // SQLi comments
        ];

        foreach ($patterns as $pattern) {
            if (preg_match($pattern, $input)) {
                echo "Malicious input detected!";
                exit;
            }
        }
    }

    // 检查所有通过 $_REQUEST 传递的参数
    foreach ($_REQUEST as $key => $value) {
        waf($value);
    }
}
```

保存并关闭文件。

## 5. 重启 PHP-FPM 和 Web 服务器
确保配置更改后，重启 PHP-FPM 和 Web 服务器。

对于 Apache：

```
sudo service apache2 restart
```

对于 Nginx（假设使用 PHP-FPM）：

```
sudo service php-fpm restart
sudo service nginx reload
```
## 6. 创建 test.php 文件进行验证
创建一个简单的 test.php 文件：

```
nano /var/www/html/test.php
```
添加以下内容：

```
<?php
echo "WAF is working!";
```
保存并关闭文件。

访问 test.php 并尝试通过 URL 传递恶意代码来测试。例如：

```
http://yourdomain.com/test.php?input=<script>alert('XSS')</script>
```
如果配置正确，应显示 "Malicious input detected!"。如果显示 "WAF is working!"，说明 WAF 已正确加载并运行。

通过上述步骤，你可以成功将 WAF 集成到 PHP 应用中，以提高应用的安全性。