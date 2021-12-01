>1
![скриншот](https://github.com/Vladislav-Pugachev/netology-DevOps-dz_-14/blob/main/zadanie_1.jpg)

>2
![скриншот](https://github.com/Vladislav-Pugachev/netology-DevOps-dz_-14/blob/main/zadanie_2.jpg)

>3

>vagrant
```buildoutcfg
Vagrant.configure("2") do |config|
  config.vm.define "master" do |subconfig|
    subconfig.vm.box = "bento/ubuntu-20.04"
	subconfig.vm.hostname = "master"
	subconfig.vm.network :private_network, ip: "10.0.0.1"
  end

  config.vm.define "web" do |subconfig|
    subconfig.vm.box = "bento/ubuntu-20.04"
	subconfig.vm.hostname = "web"
	subconfig.vm.network :private_network, ip: "10.0.0.2"
  end
end
```

>web
```buildoutcfg
root@web:~# apt install nginx
root@web:~# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/nginx-selfsigned.key -out /etc/ssl/certs/nginx-selfsigned.crt
root@web:~# openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
root@web:~# nano /etc/nginx/conf.d/default.conf

server {
    listen       80;
    listen       443 ssl http2;
    server_name  localhost;
    include snippets/self-signed.conf;
    include snippets/ssl-params.conf;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
    }
```
>master
```buildoutcfg
root@master:~# curl -k https://10.0.0.2
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

>4

```buildoutcfg
root@master:~/testssl.sh# ./testssl.sh -U --sneaky https://habr.com/

###########################################################
    testssl.sh       3.1dev from https://testssl.sh/dev/
    (f6571c7 2021-11-30 11:19:44 -- )

      This program is free software. Distribution and
             modification under GPLv2 permitted.
      USAGE w/o ANY WARRANTY. USE IT AT YOUR OWN RISK!

       Please file bugs @ https://testssl.sh/bugs/

###########################################################

 Using "OpenSSL 1.0.2-chacha (1.0.2k-dev)" [~183 ciphers]
 on master:./bin/openssl.Linux.x86_64
 (built: "Jan 18 17:12:17 2019", platform: "linux-x86_64")


 Start 2021-12-01 05:39:28        -->> 178.248.237.68:443 (habr.com) <<--

 rDNS (178.248.237.68):  --
 Service detected:       HTTP


 Testing vulnerabilities

 Heartbleed (CVE-2014-0160)                not vulnerable (OK), no heartbeat extension
 CCS (CVE-2014-0224)                       not vulnerable (OK)
 Ticketbleed (CVE-2016-9244), experiment.  not vulnerable (OK)
 ROBOT                                     not vulnerable (OK)
 Secure Renegotiation (RFC 5746)           supported (OK)
 Secure Client-Initiated Renegotiation     not vulnerable (OK)
 CRIME, TLS (CVE-2012-4929)                not vulnerable (OK)
 BREACH (CVE-2013-3587)                    no gzip/deflate/compress/br HTTP compression (OK)  - only supplied "/" tested
 POODLE, SSL (CVE-2014-3566)               not vulnerable (OK)
 TLS_FALLBACK_SCSV (RFC 7507)              No fallback possible (OK), no protocol below TLS 1.2 offered
 SWEET32 (CVE-2016-2183, CVE-2016-6329)    VULNERABLE, uses 64 bit block ciphers
 FREAK (CVE-2015-0204)                     not vulnerable (OK)
 DROWN (CVE-2016-0800, CVE-2016-0703)      not vulnerable on this host and port (OK)
                                           make sure you don't use this certificate elsewhere with SSLv2 enabled services
                                           https://censys.io/ipv4?q=23C599AB56B3C8DD6984AFE74F7BE26C88B8EDFD9C47F3B97808D9CFF159C8C4 could help you to find out
 LOGJAM (CVE-2015-4000), experimental      not vulnerable (OK): no DH EXPORT ciphers, no DH key detected with <= TLS 1.2
 BEAST (CVE-2011-3389)                     not vulnerable (OK), no SSL3 or TLS1
 LUCKY13 (CVE-2013-0169), experimental     potentially VULNERABLE, uses cipher block chaining (CBC) ciphers with TLS. Check patches
 Winshock (CVE-2014-6321), experimental    not vulnerable (OK)
 RC4 (CVE-2013-2566, CVE-2015-2808)        no RC4 ciphers detected (OK)


 Done 2021-12-01 05:40:30 [  73s] -->> 178.248.237.68:443 (habr.com) <<--


root@master:~/testssl.sh#
```

>SWEET32 — это атака поиска коллизий в режиме сцепления блоков с использованием обратной связи CBC

>5
```buildoutcfg
root@master:~# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /root/.ssh/id_rsa
Your public key has been saved in /root/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:3cvey8EPe7w+kDK33LDooh82oZmtJVVOLSWztgom5lQ root@master
The key's randomart image is:
+---[RSA 3072]----+
|           o .   |
|            *    |
|       E   * .   |
|      .  .=.o    |
|     + oSo.o. .  |
|    + o B o+ B   |
|     . = B  O X. |
|        =.oo * Bo|
|       oo.o.. *=+|
+----[SHA256]-----+
root@master:~# ssh-copy-id vagrant@10.0.0.2
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
The authenticity of host '10.0.0.2 (10.0.0.2)' can't be established.
ECDSA key fingerprint is SHA256:wSHl+h4vAtTT7mbkj2lbGyxWXWTUf6VUliwpncjwLPM.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
vagrant@10.0.0.2's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'vagrant@10.0.0.2'"
and check to make sure that only the key(s) you wanted were added.

root@master:~# ssh vagrant@10.0.0.2
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 01 Dec 2021 06:20:12 AM UTC

  System load:  0.12              Processes:             112
  Usage of /:   2.5% of 61.31GB   Users logged in:       1
  Memory usage: 17%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 10.0.0.2


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Tue Nov 30 09:21:27 2021 from 10.0.0.1
vagrant@web:~$ exit
```

>6.

```buildoutcfg
root@master:~# cat /etc/hosts
127.0.0.1       localhost
127.0.1.1       vagrant.vm      vagrant

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
127.0.2.1 master master

10.0.0.2 webnginx
```

```
root@master:~# ssh vagrant@webnginx
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed 01 Dec 2021 07:05:45 AM UTC

  System load:  0.08              Processes:             114
  Usage of /:   2.5% of 61.31GB   Users logged in:       1
  Memory usage: 18%               IPv4 address for eth0: 10.0.2.15
  Swap usage:   0%                IPv4 address for eth1: 10.0.0.2


This system is built by the Bento project by Chef Software
More information can be found at https://github.com/chef/bento
Last login: Wed Dec  1 07:04:45 2021 from 10.0.0.1
vagrant@web:~$
```


>7.
```buildoutcfg
root@master:~# tcpdump -i eth1 -c 100 -w tets.pcap
tcpdump: listening on eth1, link-type EN10MB (Ethernet), capture size 262144 bytes
100 packets captured
105 packets received by filter
0 packets dropped by kernel
```

>8

```buildoutcfg
root@master:~# nmap scanme.nmap.org
Starting Nmap 7.80 ( https://nmap.org ) at 2021-12-01 07:42 UTC
Nmap scan report for scanme.nmap.org (45.33.32.156)
Host is up (0.21s latency).
Other addresses for scanme.nmap.org (not scanned): 2600:3c01::f03c:91ff:fe18:bb2f
Not shown: 996 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
9929/tcp  open  nping-echo
31337/tcp open  Elite
```

9.
>До настройки
```buildoutcfg
root@master:~# nmap 10.0.0.2
Starting Nmap 7.80 ( https://nmap.org ) at 2021-12-01 07:42 UTC
Nmap scan report for webnginx (10.0.0.2)
Host is up (0.0099s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
111/tcp open  rpcbind
443/tcp open  https
MAC Address: 08:00:27:54:6B:5E (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 1.46 seconds
```
>Настройка
```buildoutcfg
root@web:~# apt install ufw
root@web:~# nano /etc/ufw/applications.d/nginx
[NGINX]
title=Web Srver
description=Most popular open source application for web.
ports=80,443/tcp
root@web:~# ufw allow NGINX 
root@web:~# ufw allow OpenSSH
root@web:~# ufw enable

root@web:~# ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), disabled (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
80,443/tcp (NGINX)         ALLOW IN    Anywhere
22/tcp (OpenSSH)           ALLOW IN    Anywhere
80,443/tcp (NGINX (v6))    ALLOW IN    Anywhere (v6)
22/tcp (OpenSSH (v6))      ALLOW IN    Anywhere (v6)
```
>После
```buildoutcfg
root@master:~# nmap 10.0.0.2
Starting Nmap 7.80 ( https://nmap.org ) at 2021-12-01 08:00 UTC
Nmap scan report for webnginx (10.0.0.2)
Host is up (0.0075s latency).
Not shown: 997 filtered ports
PORT    STATE SERVICE
22/tcp  open  ssh
80/tcp  open  http
443/tcp open  https
MAC Address: 08:00:27:54:6B:5E (Oracle VirtualBox virtual NIC)

Nmap done: 1 IP address (1 host up) scanned in 5.56 seconds
```
