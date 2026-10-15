---
description: 本页面为私人空间，仅提供私人资料查询及便利，与学习内容无关
---

# 私人空间

## TeamSpeak 3服务器在CentOS 7 上的安装及解决办法 <a href="#teamspeak3" id="teamspeak3"></a>

安装解压软件

```shell
yum -y install nano wget perl tar net-tools bzip2
```

下载TeamSpeak 3，在官网获取安装包：[https://www.teamspeak.com/en/downloads/#server](https://www.teamspeak.com/en/downloads/#server)\
目前最新版本如下(64 bit)：

```shell
wget https://files.teamspeak-services.com/releases/server/3.13.7/teamspeak3-server_linux_amd64-3.13.7.tar.bz2
```

完成后，解压文件，并进入目录接受软件EULA以Root身份启用软件（危险启用）：

```shell
tar xvf teamspeak3-server_linux_amd64*.tar.bz2
cd teamspeak3-server_linux_amd64
touch .ts3server_license_accepted
./ts3server_startscript.sh start inifile=ts3server.ini
```

完成页面：

```markup
------------------------------------------------------------------
                      I M P O R T A N T
------------------------------------------------------------------
               Server Query Admin Account created
         loginname= "服务器管理ID", password= "管理员密码"
         apikey= "第 三 方 可 接 入 管 理 员 权 限 KEY "
------------------------------------------------------------------


------------------------------------------------------------------
                      I M P O R T A N T
------------------------------------------------------------------
      ServerAdmin privilege key created, please use it to gain
      serveradmin rights for your virtualserver. please
      also check the doc/privilegekey_guide.txt for details.

       token=管理员代码
-------------------------------------------------------------------
```

停止服务

```shell
cd teamspeak3-server_linux_amd64
./ts3server_startscript.sh stop
```

## SpeedTest安装 <a href="#speedtest" id="speedtest"></a>

```shell
wget https://bintray.com/ookla/rhel/rpm -O bintray-ookla-rhel.repo
mv bintray-ookla-rhel.repo /etc/yum.repos.d/
yum install speedtest
```

## SS-AUTO

```shell
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

## V2RAY-AUTO

```bash
wget -N --no-check-certificate -q -O install.sh "https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh" && chmod +x install.sh && bash install.sh
```

## L2TP-AUTO

```shell
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/across/master/l2tp.sh
chmod +x l2tp.sh
```

{% file src="../.gitbook/assets/Debussy-Deux arabesques.mid" %}
德彪西-阿拉伯风格曲两首.midi音频文件
{% endfile %}

