> [!NOTE]
> ## 一     标准降级版.


>### **1.  安装官方最新版**
> `yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh ed8484bec`


>### 2. 下载降级版本 链接参考https://github.com/wei/baota. 
>  `wget https://github.com/wei/baota/releases/download/7.4.5/LinuxPanel-7.4.5.zip`

> 2.1 解压  
>  `unzip LinuxPanel-7.4.5.zip`
解压失败，需安装zip，执行[root@denglj~]# `yum install unzip zip`
2.2 打开目录 可以用`ls panel/`查看目录
`cd panel/`
2.3 更新
`bash update.sh`
2.4 删除压缩包
`cd .. && rm -f LinuxPanel-7.4.5.zip && rm -rf panel`

> **若权限问题导致更新失败，使用以下命令检查文件是否设置了不可更改的属性**
`lsattr /www/server/panel/script/site_task.py`
看到类似 i 的标志说明文件是不可修改的。你可以用以下命令移除该属性
`chattr -i /www/server/panel/script/site_task.py`
恢复 -i属性
`sudo chattr +i /www/server/panel/script/site_task.py`

>### 3. 一键去除官方联网脚本
> ```wget -O optimize.sh http://f.cccyun.cc/bt/optimize.sh --no-check-certificate && bash optimize.sh```

> 3.1 删除宝塔强制绑定手机js文件
`rm -f /www/server/panel/data/bind.pl`
3.2 bt网站搭建测试完成后 再运行禁止官方强制更新
`echo '127.0.0.1 bt.cn' >>/etc/hosts`
3.3其他 卸载bt
`wget http://download.bt.cn/install/bt-uninstall.sh`
`sh bt-uninstall.sh`

> [!NOTE]
> ## 二     安装类

>### 1. 选择版本 ng=1.16 php=7.2 sql=5.6.50 mysql=4.9

> ###  2. php设置=搜索`disable_functions`前面加注释=重启php

>#### 2.1 设置网站运行目录`public` 去除防跨站

> #### 2.2 设置伪静态，选择thinkphp
> #### 2.3 放行相应端口 开放端口9090、2080、456、
> #### 2.4 修改/pubilc/index.php内，`define('whost','wss://123.cn');define('ahost','http://123.cn');`替换相应url若ssl需改成wss://
> #### 2.5 修改/ymwl_pusher/config.php内 `domain = 'http://123.cn';`替换相应url，若ssl原则保持http不变
> #### 2.6 **若需配置ssl 则注意：不能开启强制HTTPS . 首域名不能开启dns 否则推送失败**

> ###  3 进入ssh打开推送
> `cd /www/wwwroot/xxx.com/ymwl_pusher`

> 3.1 查看指定端口号的进程情况
 `netstat -tunlp | grep 9090`
3.2（占用进程数）关闭进程     
`kill -9 953`
启动  
`php start.php start -d`
重启命令 
`php start.php restart -d`
停止 
`php start.php stop`

> [!TIP]
> phpmyadmin 关闭默认http的888端口  改为ssl 其他端口 加二级密码 。 不用时关闭

> ###  4 ssl设置及指定上传目录禁止执行PHP文件

>  4.1  在宝塔面板后台-网站，点击你需要设置的网站设置

>  4.2  配置文件里面。找到 `#SSL-END`和`#PHP-INFO-START  PHP引用配置，可以注释或修改`
>  在`#SSL-END`下一行  `#PHP-INFO-START  PHP引用配置，可以注释或修改`之前插入
```
#目录禁止执行PHP文件
location ~* /(upload|assets)/(.*).(php)$ {
	return 403;
}
```
>  或者 改为完整版
```
#目录禁止执行PHP文件
location / {
    location ~ ^/(upload|assets)/.*\.(php|php5|jsp)$ {deny all;return 404;
    }
    if (!-e $request_filename){
        rewrite  ^(.*)$  /index.php?s=$1  last;   break;
    }
}
```
![作废](https://github.com/user-attachments/assets/a870da8f-2ba4-47b5-ad69-6e432699e9aa)
>  如图所示，其中红色划线是你需要禁止运行PHP的文件夹名称，你可自行添加或者删除。

>  4.3  ====ssl https需要 网站配置文件 单独配置  
```
    location /app{
      proxy_pass http://127.0.0.1:1457;     
      proxy_http_version 1.1;
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "Upgrade";
      proxy_set_header X-Real-IP $remote_addr;
}
```
>  如图所示位置在最下方access_log 上方 其中http://127.0.0.1:1457的端口号根据自己设定的自定义
![1](https://github.com/user-attachments/assets/b3e13cc1-0551-418f-8bd7-e83f81a04233)

>  4.4  ==== BT ssl证书添加失败 您好，这个错误通常是由于使用了不兼容的 OpenSSL 版本导致的，X509Req 版本只支持 0。您可到服务器终端执行以下命令：
```
btpip install pyOpenSSL==22.1.0
btpip install cffi==1.14
bt 1
```





> [!NOTE]
>  ## ================= bt后门去除=================

> ### 1. 上传后门 删除 [参考链接](https://www.imdupeng.cn/server/bt-cn_backdoor.html)
> `echo "" > /www/server/panel/script/site_task.py`
`chattr +i /www/server/panel/script/site_task.py`
`rm -rf /www/server/panel/logs/request/*`
`chattr +i -R /www/server/panel/logs/request`
四行的含义分别是:将脚本文件清空
脚本文件添加写保护，防止内容被写回（即使是root，不取消保护也无法写入）
清空所有统计日志
为request文件夹添加写保护，防止内容写入

> ### 2.  自动更新 删除  .py 用`"""`开始+结尾来注释多行代码下面需要修改的文件基本在/www/server/panel/下。所以前缀省略
> 2.1  `/class/ajax.py`
> 删除`def UpdatePanel(self,get):`下整段关于更新的代码。也就是到`#检查是否安装任何 def CheckInstalled(self,get):`前所有代码。
> 2.2  `/task.py`
> 注释（禁止运行）`def update_panel():`
> `# os.system(“curl http://download.bt.cn/install/update6.sh|bash &”) `，加入“#”即可。
> 2.3 `/tools.py`
> 注释`elif u_input == 16:`
> `# os.system(“curl http://download.bt.cn/install/update6.sh|bash”)` ，加入“#”即可。

> ### ps.  安全--禁止pingip  禁止不必要的端口




> ### 3. Web RTC泄露 [参考](https://www.52pojie.cn/thread-1704087-1-1.html)
> `https://ip.voidsec.com`如果Web RTC:是空 就不用管，如果Web RTC:后边显示的是你本地的IP，那就按以下教程设置
>  Firefox浏览器 禁用WebRTC的方法是：在浏览器上输入：about:config。之后搜索：media.peerconnection.enabled。找到它后双击，将其改成 false 即可。
>  Chrome浏览器 禁用WebRTC的方法是：在Chrome应用商店里，安装一个名为WebRTC Leak Prevent的扩展, 然后设置成Disable就行了

> ## ======宝塔BT7.9.2(含)以下版本RCE安全漏洞及其修复======
> #### 影响范围 1: 网站日志 （影响最大）2: Nginx错误日志 3: 面板运行日志 
> #### 解决方法2 不点击上述功能 修改文件，增加过滤即可
>####   1、网站日志
 > `/www/server/panel/class/panelSite.py`
> 大概在4922多行吧，搜索`GetSiteLogs`，把`GetSiteLogs`和`get_site_errlog`用以下替换掉
```
    # xss 防御
    def xsssec(self,text):
        replace_list = {
            "<":"＜",
            ">":"＞",
            "'":"＇",
            '"':"＂",
        }
        for k,v in replace_list.items():
            text = text.replace(k,v)
        return public.xssencode2(text)    
    
    #取网站日志
    def GetSiteLogs(self,get):
        serverType = public.get_webserver()
        if serverType == "nginx":
            logPath = '/www/wwwlogs/' + get.siteName + '.log'
        elif serverType == 'apache':
            logPath = '/www/wwwlogs/' + get.siteName + '-access_log'
        else:
            logPath = '/www/wwwlogs/' + get.siteName + '_ols.access_log'
        if not os.path.exists(logPath): return public.returnMsg(False,'日志为空')
        return public.returnMsg(True,self.xsssec(public.GetNumLines(logPath,1000)))

    #取网站错误日志
    def get_site_errlog(self,get):
        serverType = public.get_webserver()
        if serverType == "nginx":
            logPath = '/www/wwwlogs/' + get.siteName + '.error.log'
        elif serverType == 'apache':
            logPath = '/www/wwwlogs/' + get.siteName + '-error_log'
        else:
            logPath = '/www/wwwlogs/' + get.siteName + '_ols.error_log'
        if not os.path.exists(logPath): return public.returnMsg(False,'日志为空')
        return public.returnMsg(True,self.xsssec(public.GetNumLines(logPath,1000)))
```

>####  2. Nginx错误日志
> `/www/server/panel/class/ajax.py`
> 大概在1135多行吧，搜索`GetOpeLogs`  把`GetOpeLogs`用以下替换掉
```
#取指定日志
    def GetOpeLogs(self,get):
        if not os.path.exists(get.path): return public.returnMsg(False,'AJAX_LOG_FILR_NOT_EXISTS')
        return public.returnMsg(True,public.xsssec(public.GetNumLines(get.path,1000)))
```

>####  3. 面板运行日志
> `/www/server/panel/class/config.py`
> 大概在1480多行吧，搜索`get_panel_error_logs`   把`get_panel_error_logs`用以下替换掉
```# xss 防御
    def xsssec(self,text):
        return text.replace('<', '&lt;').replace('>', '&gt;')
    #取面板运行日志
    def get_panel_error_logs(self,get):
        filename = 'logs/error.log'
        if not os.path.exists(filename): return public.returnMsg(False,'没有找到运行日志')
        result = public.GetNumLines(filename,2000)
        return public.returnMsg(True,self.xsssec(result))
```



> [!TIP]
> ##  ================ tonyenc加密使用方法 ================

>####  第一步： 下载扩展文件
> `git clone https://github.com/lihancong/tonyenc.git`

>####  第二步：  修改密文
在tonyenc文件夹下core.h文件 
![1](https://github.com/user-attachments/assets/fbf29088-df39-4251-8c5c-547c9e910070)

>####  第三步：  生成扩展文件
> 3.1 cd 到tonyenc文件夹下
`cd /root/tonyenc`
3.2  输入命令    `phpize`
如果环境找不到phpize，请使用绝对路径/www/server/php/72/bin/phpize
![1](https://github.com/user-attachments/assets/95861ee2-9d3b-471e-90c6-70b289d87d65)如图则正常
3.3  输入命令
`./configure --with-php-config=/www/server/php/72/bin/php-config`
3.4  输入命令`make && make install` 命令完会在目录生成一个modules/tonyenc.so 文件的文件
![1](https://github.com/user-attachments/assets/6fb16a03-9167-4182-b11a-70ebb50329f5)
3.5  输入命令`make test`

>####  第四步：  将tonyenc.so配置到php.ini和php-cli.ini里面
> 路径在`/www/server/php/72/etc/php.ini` 和 `/www/server/php/72/etc/php-cli.ini`
修改php.ini文件和php-cli.ini末尾最加刚才的扩展
`Extension = tonyenc.so`
重启php 输入
`php -m |grep tonyenc`
如果有输出`tonyenc `
![1](https://github.com/user-attachments/assets/0932a8cd-ba09-4882-97ea-92934e8cbbd3)
表示安装扩展成功
如没有输出 ，重启服务并重新检查
`systemctl restart php-fpm`
`systemctl restart nginx`

> ### ===========  如何加密  =========== 
> 随便在网站目录下新建一个demo文件夹
`mkdir /www/wwwroot/demo/`
> 新建文件 demo.php     内容为
```php
<?php
echo "kongiq你好";
?>
```

>  新建加密文件,这个文件它提供了，直接复制下来,比如我这个文件名叫：jiami.php
```php
<?php
/**
 * tonyenc.php: Encrypt or decrypt the script with tonyenc.
 *
 * A high performance and cross-platform encrypt extension for PHP source code.
 *
 * @author:  Tony
 * @site:    lihancong.cn
 * @github:  github.com/lihancong/tonyenc
 */
if (version_compare(PHP_VERSION, 7, '<'))
    die("PHP must later than version 7.0\n");
if (php_sapi_name() !== 'cli')
    die("Must run in cli mode\n");
if (!extension_loaded('tonyenc'))
    die("The extension: 'tonyenc' not loaded\n");
if ($argc <= 1)
    die("\nusage: php tonyenc.php file.php ...     encrypt the php file(s) or directory(s)\n\n");
array_shift($argv);
foreach ($argv as $fileName) {
    if (is_file($fileName)) {
        handle($fileName);
    } elseif (is_dir($fileName)) {
        $DirectoriesIt = new RecursiveDirectoryIterator($fileName, FilesystemIterator::SKIP_DOTS);
        $AllIt         = new RecursiveIteratorIterator($DirectoriesIt);
        $it            = new RegexIterator($AllIt, '/^.+\.php$/i', RecursiveRegexIterator::GET_MATCH);
        foreach ($it as $v)
            handle($v[0]);
    } else {
        echo "Unknowing file: '$fileName'\n";
    }
}
function handle($file)
{
    if ($fp = fopen($file, 'rb+') and $fileSize = filesize($file)) {
        $data = tonyenc_encode(fread($fp, $fileSize));
        if ($data !== false) {
            if (file_put_contents($file, '') !== false) {
                rewind($fp);
                fwrite($fp, $data);
            }
        }
        fclose($fp);
    }
}
```
>  现在加密demo.php那个文件

>  在命令符输入
>  `php jiami.php demo.php`
>  就可以完成demo.php文件加密了

>  对文件夹下的所有文件加密，例如
```
config/a.php
config/b.php
```

>  命令符直接
php jiami.php config/
>  就可以完成config的a.php,b.php加密




## 其他待修改
Nginx 设置非微信 支付宝ua跳转   ====网站设置-内
```
if ($http_user_agent !~* "MicroMessenger|alipayclient") { 
            return 301 https://netdisk.moyublog.com/code_demo/5f6cfb3e70371/;
        }
    }
	
)    
```
如图所示  放在最下面
![1](https://github.com/user-attachments/assets/6be956e7-9976-417c-81de-b4a8ff04d387)



> [!TIP]
>  #### ==============开启php加速==============

在PHP管理页面，选择“安装扩展”，找到“opcache”，点“安装”
![1](https://github.com/user-attachments/assets/4114af32-43a5-4ea2-a2d7-7793d6ed34e9)
在php管理页面，选择“配置修改”，把max_execution_tim（最大脚本运行时间）修改为60
选择“性能调整”，把并发方案改成30；max_spare_servers（最大空闲进程数）改成20

> [!WARNING]
>  #### ==============修改22端口==============

> [!WARNING]
>  #### ==============定时删除==============
> [!WARNING]
>  #### ==============宝塔计划任务Shell定时删除某各目录下所有文件==============


> [!WARNING]
>>  #### ==========================================ssl证书说明
没有dns的 用源服务器 将证书和密钥手动填写到宝塔其他证书栏（只填写有没dns的域名，其他dns的域名不填写）  并关闭宝塔强制htpps 否则会有重定向
cloudflare  ssl设置==边缘证书==始终使用 HTTPS 


>  #### ===============================================aapanl===
安装以后
进入面板 添加ssh端口  连接  尝试重新运行 yum 命令进行软件包的安装或更新： sudo yum install vim 装结束后再安装各软件


































<pre><code>
# 你的代码在这里
print("Hello, world!")
</code></pre>



<div style="text-align: center;">
<pre><code>
# 你的代码在这里
print("Hello, world!")
</code></pre>
</div>

##  宝塔  Debian 11.6 64位 硅云
Python 版本：
```python3 --version```
如果没有安装 Python 3，请使用以下命令安装（在有网络的情况下）：
```yum install python3
```

安装 pip
```curl -O https://bootstrap.pypa.io/get-pip.py
python3 get-pip.py
```
3. 验证 pip 是否成功安装
`pip3 --version`
4. 使用 pip 安装 Pillow
`pip3 install Pillow`


