# vpn
Tutorial on setting up a VPN (copy from https://my.oschina.net/zhoudage/blog/1630728 )


# 1.Install openswan
 `$ sudo apt-get install openswan`
 
  if the are any error like Package ‘openswan‘ has no installation candidate,run the follow code:
  
  `sudo vi /etc/apt/sources.list.d/lzu.list`
  
 copy the follow code to lzu.list
  
  ****
  ```
 deb http://mirror.lzu.edu.cn/ubuntu/ precise main restricted universe multiverse
 deb http://mirror.lzu.edu.cn/ubuntu/ precise-security main restricted universe multiverse
 deb http://mirror.lzu.edu.cn/ubuntu/ precise-updates main restricted universe multiverse
 deb http://mirror.lzu.edu.cn/ubuntu/ precise-proposed main restricted universe multiverse
 deb http://mirror.lzu.edu.cn/ubuntu/ precise-backports main restricted universe multiverse
 deb-src http://mirror.lzu.edu.cn/ubuntu/ precise main restricted universe multiverse
 deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-security main restricted universe multiverse
 deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-updates main restricted universe multiverse
 deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-proposed main restricted universe multiverse
 deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-backports main restricted universe multiverse
  ```
  ****
   
  updata the sourse
  $ sudo apt-get updata
   install openswan 
  $ sudo apt-get install openswan 
   #安装出现提示框，选择NO回车

# 2. Install xl2tpd and  configure IPSec service
  
  $ sudo apt-get install xl2tpd
   
   edit /etc/ipsec.conf
   
  $ sudo vi /etc/ipsec.conf
  
  copy the follow code to ipsec.conf 
  ```
  config setup
        nat_traversal=yes
        virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v4:!10.152.2.0/24
        oe=off
        protostack=netkey
 
conn L2TP-PSK-NAT
        rightsubnet=vhost:%priv
        also=L2TP-PSK-noNAT
 
conn L2TP-PSK-noNAT
        authby=secret
        pfs=no
        auto=add
        keyingtries=3
        rekey=no
        dpddelay=30
        dpdtimeout=120
        dpdaction=clear
        ikelifetime=8h
        keylife=1h
        type=transport
        # 替换 IP 地址为你的公网IP
        left=x.x.x.x
        leftprotoport=17/1701
        right=%any
        rightprotoport=17/%any
        forceencaps=yes
  ```
  Pay attention to the prompt #behind, modify the IP 
   
  modify /etc/ipsec.secrets
  ```
  $ sudo vi /etc/ipsec.secrets
  ```
  *Here x.x.x.x is replaced with the public IP address of your server, and the password in "" is the password you set yourself, which will be used when the client connects
  
  ```
  x.x.x.x  %any: PSK "mima1234567890"
  ```
  save the above code and run the follow code in terminal
  ```
  
$ for each in /proc/sys/net/ipv4/conf/*
  do
    echo 0 > $each/accept_redirects
    echo 0 > $each/send_redirects
  done
  ```
   

