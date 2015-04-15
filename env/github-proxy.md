## Github Proxy设置

转载自 <http://www.chenyudong.com/archives/use-git-or-github-in-company-local-net.html>

## 在公司的局域网使用git或github 设置代理

在公司这样的局域网环境中，向要走网络必须走HTTP代理出去。不能直接访问外面的服务，所以这样安全了些，但是也提供了不便的地方。因此需要设置一些代理才能使用。

常用的代理有：

* HTTP、HTTPS代理 许多程序支持http代理
* SOCKS代理 不是所有的程序都支持socks代理，但是常用的软件都支持

github上的仓库支持ssh、https、git三种协议的chekout（clone）操作。

### 生成SSH Key
参考<http://www.chenyudong.com/archives/ssh-using-private-public-key-no-password.html>进行SSH密钥的生产

### git使用http访问
github上可以使用https进行访问。

    $ git config --global http.proxy http://web-proxy.oa.com:8080

但是这样可以clone了。但是如果要push代码，那就麻烦了。每次都需要输入密码。

### git使用ssh进行访问
使用ssh协议不仅可以访问github，还可以访问我们自己的git私有仓库，可以参考文章通过SSH创建私有git仓库。

首先，Windows用户先下载一个mysgit客户端，下个portable版的就好了，<https://github.com/msysgit/msysgit/releases>里面有git程序。Linux用户跳过。

第二步，配置ssh。Windows用户运行mysgit中的git-bash.bat来启动终端。编辑vim ~/.ssh/config ，将下面的内容写入到文件中

```
Host github.com *.github.com
    ProxyCommand connect -H web-proxy.oa.com:8080 %h %p   #设置代理
    # IdentityFile ~/.ssh/privatekey/id_rsa.github # 可有可无
    User git
```

`ProxyCommand`说明了设置代理，其中`connect`是个程序，Windows用户下载了mysgit，里面有这个程序，Linux用户可能没有，需要安装`sudo apt-get install connect-proxy`。

如果你使用corkscrew，那么解压缩附件，把corkscrew.exe和cygwin1.dll拷贝到mysgit的bin目录中。附：[corkscrew.zip](http://www.chenyudong.com/uploads/2014/01/corkscrew.zip)

第三步，测试

```
ssh -T git@github.com
Hi username! You've successfully authenticated, but GitHub does not provide shell access.
```