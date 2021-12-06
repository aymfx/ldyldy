## 91 版本 之后的谷歌浏览器不能带 cookie 了

#! > 原因是 Chrome 80 版本后当服务端未指定 HTTP 响应头的 SameSite 属性时，Chrome 默认的 SameSite 策略为 Lax，该策略中当向跨域域名发送 POST 请求时无法携带 Cookie。

`第一种91之前的方式`
Chrome 中访问地址 chrome://flags/#same-site-by-default-cookies，将 SameSite by default cookies 设置为 Disabled 后重启浏览器再运行项目即可解决。该设置默认情况下会将未指定 SameSite 属性的请求看做 SameSite=Lax 来处理。

`91包括91之后，可能没了SameSite by default cookies，我们搜索这个SameSite`
我们可以将搜到的两个置为 disabled 亲测可行

![20210719144619](http://www.maymfx.cn/pic/20210719144619.png)

## electron能够启动网页但是不能启动桌面端的原因

- vue-cli 会检测 yarn.lock 当yarn没有被安装的话 则不会继续往下走
- npm i -g yarn  安装yarn可以解决这个问题

## 出现不能创建目录的问题

```
sudo chown -R $USER /Library/Caches/Homebrew/
```

