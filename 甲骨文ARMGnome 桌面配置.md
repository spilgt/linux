安装 Ubuntu 官方 Gnome 桌面：接下来做一次更新和升级，执行命令：
```
apt update -y ; apt upgrade -y
```

升级后设置一下语言：(为了让大家进入图形系统后能够设置中文界面！）

```
dpkg-reconfigure locales
```

在出现的第一画面里做如下选择，空格键是选择，Tab 键可以跳到 “OK” 上，Enter 键确认：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324777537656.png)

再进入到第二画面后，同样通过空格键选择中文为默认的语言编码，用 Tab 键跳到 “OK” 上回车确认：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324790421502.jpg)

设置完毕后，重新连接一次服务器，会发现系统支持中文展示了  
下面开始桌面环境的安装，这里安装 ubuntu desktop 而不是 xfce，执行命令
```
sudo apt -get install gnome
```

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324801675168.jpg)

安装过程耗时较长，耐心等待，直至完成。

因为当前桌面会与远程应用冲突所以需要切换桌面
```
sudo apt -get install lightdm
```
//其他涉及桌面管理环境代码
```
sudo apt-get remove lightdm  
```
// 删除指定桌面管理环境
```
sudo dpkg-reconfigure gdm3
```
// 重置默认桌面管理环境，重启生效
```
cat /etc/X11/default-display-manager
```
// 查看当前桌面管理环境
完毕后进行 Xrdp 的安装，提供远程桌面访问的能力，执行以下命令

```
apt install xrdp -y
```

Xrdp 会安装成服务，可以验证一下
```
systemctl status xrdp
```
![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324816813414.jpg)

可以看到红色 ERROR 这一行信息，如何解决呢？执行以下命令，可以看到红色的信息不见了
```
adduser xrdp ssl-cert  
systemctl restart xrdp  
systemctl status xrdp
```
![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324828767818.jpg)
这样桌面环境和远程服务安装好后，就可以连接到远程桌面。经过反复的测试和实验，使用 Windows 10 默认的远程桌面工具 mstsc，体验非常卡顿, 具体原因具体可以总结一下，就是微软的 rdp 与 Linux 的 Xrdp 进行连接时存在兼容性问题，不要以为纯粹是网络问题，其实甲骨文 ARM 的性能非常夸张，可以很负责任的告诉大家，甲骨文的这个 ARM 芯片，相比于 X86 架构，你很难在消费市场上找到能比他强的，总之性能就是给的实在，具体大家深入体会后就明白了。

安装 Linux FRP 服务  
接下来 FRP 将会彻底解决远程桌面卡顿的问题，在这里给大家普及一下 FRP 的运作机制，首先 FRP 分为客户端 frpc 和服务端 frps，大家只要记住，哪个要做中转机哪个就放服务端，客户端放在 ARM 上，如果像我这种用甲骨文春川的本来国内直连速度就比较好，那就可以服务端和客户端都放在 ARM 机器上，这样就相当于 ARM 本地做了一次端口转发，理解这个逻辑，那么就开始干！

实测 甲骨文首尔 ARM FRP 本地中转 油管 4K 丝滑

![](https://www.you2php.com/zb_users/upload/2021/12/202112081638969066715418.gif)

![](https://www.you2php.com/zb_users/upload/2021/12/202112101639103625357463.png)大家看对版本，每个版本里都包含 frps 和 frpc，如果大家用的是甲骨文春川，选 linux_arm64 就行。如果是比如用腾讯云做服务端的话，那么腾讯云是 amd64 的，甲骨文 ARM 是 arm64 的，在丢到各自的机器上时注意版本！！！重要的事情说三遍！别把版本放错了！别把版本放错了！别把版本放错了！再次重申 frps 是服务端, frpc 是客户端。

客户端方面

1. 解压包，把如下文件放到 / etc/frp 目录下

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324844671801.jpg)

2. 修改 frpc.ini 文件
```
[common]  
server_addr = 127.0.0.1（机器内部做转发，就用 127.0.0.1，如果用腾讯云香港做服转发，就填腾讯云的 IP）  
server_port = 7000  
token = 12345678

[13389]  
type = tcp  
remote_port = 13389  
local_ip = 127.0.0.1  
local_port = 3389
```

3. 把 systemd 文件夹下的 frpc.service，放到 / etc/systemd/system 下。

设置权限
```
chmod 754 frpc.service
```

设置开机启动即可
```
systemctl enable frpc.service
```

4. 在 / etc/frp 目录下
```
cp frpc /usr/bin  
chmod +x /usr/bin/frpc  
systemctl start frpc  
ps -ef|grep frpc
```

客户端结束

服务端方面

1. 解压包，把如下文件放到 / etc/frp 目录下

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324869479706.jpg)

2. 修改 frps.ini 文件（这里为了方便大家不掉坑，我这里直接给出自己已经搭建成功的配置文件给大家，完整覆盖过去）
```
[common]  
bind_port = 7000  
token = 12345678
```

3. 把 systemd 文件夹下的 frps.service，放到 / etc/systemd/system 下

设置权限
```
chmod 754 frps.service
```

设置开机启动即可
```
systemctl enable frps.service
```
4. 在 / etc/frp 目录下
```
cp frps /usr/bin  
chmod +x /usr/bin/frps  
systemctl start frps  
ps -ef|grep frps
```
完成，结束


在 RDP 里设置成这样：

![](https://www.you2php.com/zb_users/upload/2021/12/202112101639103677815345.png)  
然后大家可以开始登陆 RDP 了

1. 在 ARM 机器上做内部转发的在 RDP 输入甲骨文 IP:13389 进入后会显示 xrdp 界面，账号是 root 密码自己输入自己的。  
2. 在比如腾讯云上做服务端的，在 rdp 上输入腾讯云的 IP 地址：13389 即可进入 xrdp。  
连接到桌面后，一路点击 Next，最后点击 Start Using Ubuntu

接下来设置中文及中文输入法支持，依次点击左上角的 Activities-Show Applications-Language Support:

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324919826109.jpg)  
之后在 Language Support 中增加语言支持：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324930337030.jpg)

如果是上图这样的，先点击安装，再点击 “添加或删除语言” 按钮，找到 “中文（简体）” 打勾，Apply。

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324940837212.jpg)

完成后，顺便将键盘输入法系统选择成为 “iBus”：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324951949136.jpg)

重启系统，重连后会看到界面菜单均已中文显示。点击右上角电源标识旁边的小尖号，菜单里点击设置：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324961880350.jpg)

在设置的界面中找到 “区域与语言”，能够看到语言已经设置成为 “汉语”：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324982782225.jpg)

我们接下来要设置中文输入法，点击下面的 “+”，出现输入源小窗口：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633324992798455.jpg)

点击 “汉语”：

![](https://www.you2php.com/zb_users/upload/2021/10/202110041633325002748973.jpg)

点击 “中文（智能拼音）” 进行 “添加” 就完成了输入法的设置。

至此已经完成所有的配置工作，接下来使用 Ubuntu20.04 系统自带的 Firefox 浏览器畅快的上一下英特耐特吧

**2021/12/8 补充一下，增加声音转发支持**

系统必须是 Ubuntu20.04，否则不用往下看了  
先配置源

![](https://www.you2php.com/zb_users/upload/2022/02/202202161645023658539953.jpg)
