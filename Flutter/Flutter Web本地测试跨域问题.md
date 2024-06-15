# Flutter Web本地测试跨域问题

Flutter的web在本地测试的时候，涉及到http跨域请求时，会报错：

`
Access to XMLHttpRequest at '\*\*\*' from origin 'http://localhost:6715' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.
`

可以把Flutter升级到3.3.0版本或以上版本，就可以使用下面的命令关闭CORS：

`flutter run -d chrome --web-browser-flag "--disable-web-security"`

那如何用局域网其他设备访问这个网站呢？例如PC（IP: 192.168.0.100）运行下面命令

`flutter run -d chrome --web-browser-flag "--disable-web-security" --web-hostname 0.0.0.0 --web-port 13001`

运行起来之后，可以在其他设备的浏览器中直接输入`http://192.168.0.100:13001 `, 其中 192.168.0.100 是你的电脑ip地址，就可以访问网站

* * *

[原文链接](https://wangqianhong.com/2021/12/flutter%ef%bc%88%e5%8d%81%e4%ba%94%ef%bc%89-web%e6%9c%ac%e5%9c%b0%e6%b5%8b%e8%af%95%e8%b7%a8%e5%9f%9f%e9%97%ae%e9%a2%98/)
