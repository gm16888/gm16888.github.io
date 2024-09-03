> [!NOTE]
>只允许页面在 iframe 中加载，直接访问页面则跳转到 baidu.com。
只允许本站的 iframe 加载页面，如果是其他站点的 iframe 加载则跳转到 baidu.com。
 ```
<script>
    // 检查页面是否在 iframe 中加载
    if (window.top === window.self) {
        // 如果页面不是在 iframe 中加载
        window.location.href = 'https://www.baidu.com';
    } else {
        // 如果页面在 iframe 中加载，检查父页面的来源
        if (document.referrer && (new URL(document.referrer).origin !== window.location.origin)) {
            // 如果父页面的来源不是本站，则跳转到百度
            window.top.location.href = 'https://www.baidu.com';
        }
    }
</script>
```
> [!NOTE]
>禁止F12+ 解除右键 [参考github](https://github.com/theajack/disable-devtool/blob/master/README.cn.md)

```
<script disable-devtool-auto src="https://cdn.jsdelivr.net/npm/disable-devtool@latest"></script>
<script>
    document.addEventListener('contextmenu', function(event) {
        event.stopPropagation(); // 阻止disable-devtool的事件传播
        event.stopImmediatePropagation(); // 立即阻止事件传播
        return true; // 允许右键菜单
    }, true);
</script>

```
> [!NOTE]
>禁止F12+ 解除右键 [参考github](https://github.com/theajack/disable-devtool/blob/master/README.cn.md)

> [!NOTE]
>fofa 语法
title="- LuCI" && (icon_hash="-2132841455" || icon_hash="1026535468" || icon_hash="-1566344686" || icon_hash="1768918484" || icon_hash="692834389" || icon_hash="-17760346" || icon_hash="1206686446" || icon_hash="474428474" || icon_hash="-1148219260")
is_domain=true 搜索域名的资产,只接受true和false。
after=”2017” && before=”2017-10-01” 时间范围段搜索
body_hash="-1567669936" && (country="JP" || country="SG" || region="HK") && is_domain=false+is_honeypot=false

> [!NOTE]
>notepad 如何批量删除每一行 指定第一个符号, 后面的所有 
打开 Notepad++ 并加载你的文件。
按 Ctrl + H 打开“替换”对话框。
设置搜索模式为正则表达式：
在“查找内容”框中输入正则表达式`,.*`如果是其他符号，比如冒号，可以输入`:.*`
在“替换为”框中留空。点击“全部替换”。

> [!NOTE]
>如何在每一行添加http://  如果有https://则不添加
按 Ctrl + H 打开“替换”对话框。
在“查找内容”框中输入正则表达式：```^(?!https://)```
在“替换为”框中输入：`http://`
