无需代理服务器，通过域前置(domain fronting)访问某些网站，比如维基百科或 Google 翻译。有关域前置的相关说明，请到[维基百科页面][wiki-df]查看。
若一个网站被封，一般并不是它的所有域名都被封，就因为这样我们得以使用域前置技术绕过封锁。

本 repo 提供被封网站的某个没有被封的域名和 IP。

## 支持域前置的网站列表
---
请看 [host_rules.md][rules]。要支持其他网站请提交 issue。


## 怎么使用
---
有两种方法可以使用域前置：

1. 使用 chromium 内核的浏览器，比如 Google 浏览器，微软的 edge，brave等，缺点是只能在浏览器上使用
2. 借助 [mitmproxy][mitm] 和[域前置脚本][df-py]


### 第一种方法
---
这方法比较简单，只需要在运行浏览器时加入两个参数。如 Windows 上 Google 浏览器：
```
"path/to/chrome.exe" --host-rules="<rules>" --host-resolver-rules="<rules>"
```
在 `powershell` 上运行要在命令前加上 `& `。

浏览器的执行文件路径可在浏览器打开 `chrome://version` 找到。

微软的 edge 还需到浏览器的`设置->系统与性能`把启动增强关闭，否则参数不生效。


[host_rules.md][rules] 有 `<rules>` 的内容。

### 第二种方法
---
按照以下步骤操作完成后，http 代理监听在 127.0.0.1:8080。

1. 下载安装 [mitmproxy][mitm-dl]
2. 下载这三个文件 `domain_fronting.json` `domain_fronting.py` `hosts.txt` 到同一个目录
3. 复制 `hosts.txt` 内容添加到系统 `hosts` 文件
4. 打开命令行并切换到 `domain_fronting.json` 所在目录，然后运行
```
mitmdump.exe -s ./domain_fronting.py --set domainfrontingfile=./domain_fronting.json --set connection_strategy=lazy --set stream_large_bodies=1 --no-http2 -p 8080
```
5. 配置浏览器或系统使用 http 代理
6. 在浏览器打开 http://mitm.it 按照提示安装 CA 证书，否则会报证书错误。



## Contribute
---
欢迎加入其他支持域前置的网站。只要修改 [hosts.source.txt][source] 即可。
此文件的格式：
```
# 以#开头的是注释，以及空行会被忽略
# 以=== 开头为网站名称
# 每行包含有四个字段，sni域名是没被封的可用于sni连接的域名
sni域名 IP 端口 被封的域名...
```
比如
```
=== google
www.gstatic.cn 106.75.251.36 443 *.google.com *.gstatic.com
```
每行的后面可以有多个域名。


## Credit
---
`domain_fronting.py` 由 mitmproxy 的 [domain_fronting.py][mitm-df] 修改而来。



[wiki-df]: https://zh.wikipedia.org/wiki/%E5%9F%9F%E5%89%8D%E7%BD%AE
[mitm]: https://github.com/mitmproxy/mitmproxy
[mitm-dl]: https://mitmproxy.org/
[df-py]: https://github.com/rabbit2123/domain-fronting/blob/main/domain_fronting.py
[rules]: https://github.com/rabbit2123/domain-fronting/blob/main/host_rules.md
[source]: https://github.com/rabbit2123/domain-fronting/blob/main/hosts.source.txt
[mitm-df]: https://github.com/mitmproxy/mitmproxy/blob/main/examples/contrib/domain_fronting.py
