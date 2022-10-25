------

查询IP地址

```sh
curl cip.cc
```

------

向日葵linux Ubuntu图形界面卸载指令

```
sudo apt-get --purge remove sunloginclient
```

向日葵linux Ubuntu图形界面安装指令
```
sudo dpkg -i 文件名.deb
```
出现未安装依赖包使用此命令
```
sudo apt-get install -f
```

------

甲骨文Ubuntu开启端口防火墙

```
iptables -I INPUT -p tcp --dport 填入你需要的端口 -j ACCEPT
```
------

关闭防火墙清楚配置规则

```
1、Ubuntu系统下：
开放所有端口
iptables -P INPUT ACCEPT
iptables -P FORWARD ACCEPT
iptables -P OUTPUT ACCEPT
iptables -F
```

------

开启bbr加速和开启ROOT登录

```shell
wget --no-check-certificate https://raw.githubusercontent.com/jinwyp/one_click_script/master/trojan_v2ray_install.sh && chmod +x ./trojan_v2ray_install.sh && ./trojan_v2ray_install.sh
```

------

