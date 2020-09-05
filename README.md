
#                                                                      VPN
 Tutorial on setting up a L2TP VPN Server (refer to a description of a logger,I put the link in the end)


## 1.Install openswan
#### 1.1 run the fllow code
 ```$ sudo apt-get install openswan```
 
 ***
#### 1.2 if the are any error like `Package ‘openswan‘ has no installation candidate,run the follow code:
  
  ```$ sudo vi /etc/apt/sources.list.d/lzu.list```
  
  #####  1.2.1 copy the follow code to lzu.list
  

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
 
   
  #####  1.2.2 updata the sourse
  
```
  $ sudo apt-get updata
  ```
  #####  1.2.3 install openswan 
   ```
  $ sudo apt-get install openswan 
   #安装出现提示框，选择NO回车
   ```

# 2. Install xl2tpd and  configure IPSec service 
  #### 2.1 run the fllow code
   ```
  $ sudo apt-get install xl2tpd
   ```
   
  #### 2.2 edit /etc/ipsec.conf
  ```
  $ sudo vi /etc/ipsec.conf
  ```
  
  #### 2.2 copy the follow code to ipsec.conf 
  
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
                                                   #replace the IP address with your public network IP
        left=x.x.x.x
        leftprotoport=17/1701
        right=%any
        rightprotoport=17/%any
        forceencaps=yes
  ```
  * Pay attention to the prompt #behind, modify the IP 
   
  #### 2.3 modify /etc/ipsec.secrets
  ```
  $ sudo vi /etc/ipsec.secrets
  ```
  *Here x.x.x.x is replaced with the public IP address of your server, and the password in " " is the password you set yourself, which will be used when the client connects*
  
  ```
  x.x.x.x  %any: PSK "mima1234567890"
  ```
  #### 2.4 save the above code and run the follow code in terminal to let IPSEC work
  ```
  
$ for each in /proc/sys/net/ipv4/conf/*
  do
    echo 0 > $each/accept_redirects
    echo 0 > $each/send_redirects
  done
  ```
  #### 2.5 Start IPSEC service and check if IPSEC is working properly
  
  ```
  $ sudo /etc/init.d/ipsec start
  # Use the following command to confirm whether ipsec is working properly
  $ sudo ipsec verify
  # Note: only if there is no Faild
  ```
  **if the are any error,you could refer to the follow code**
  
  ```
#error 1.Checking /bin/sh is not /bin/dash   [WARNING] run the follow code  
  $ sudo dpkg-reconfigure dash
# select no as the English tips


#error 2.pluto is running [FAILED]
$ sudo /etc/init.d/ipsec start


#error 3：NETKEY: Testing XFRM related proc values [FAILED]
$ for each in /proc/sys/net/ipv4/conf/*
do
    echo 0 > $each/accept_redirects
    echo 0 > $each/send_redirects
done


#error 4：Pluto listening for IKE on udp 500 [FAILED]
$ apt-get install lsof


#error 5：Hardware RNG detected, testing if used properly            [FAILED]
$ sudo apt-get install rng-tools
  ```
  ***
  
 #### 2.6 modify  /etc/xl2tpd/xl2tpd.conf
 ```
 $ sudo vi /etc/xl2tpd/xl2tpd.conf
 ```
 #### 2.6 copy the follow contents to xl2tpd.conf
 ```
 [global]
ipsec saref = yes

[lns default]
ip range = 10.10.20.100-10.10.20.254
local ip = 10.10.20.1
require chap = yes
refuse pap = yes
require authentication = yes
ppp debug = yes
pppoptfile = /etc/ppp/options.xl2tpd
length bit = yes
```
#### 2.7 Modify PPP configuration

```
$ sudo vi /etc/ppp/options.xl2tpd
```
#### 2.7 add the follow contents to options.xl2tpd
```
refuse-mschap-v2
refuse-mschap
ms-dns 8.8.8.8
ms-dns 8.8.4.4
asyncmap 0
auth
lock
hide-password
local
#debug
name l2tpd
proxyarp
lcp-echo-interval 30
lcp-echo-failure 4
mtu 1404
mru 1404
```
#### 2.8 add a client
```
$ sudo vi /etc/ppp/chap-secrets
```
*Fill in the username and password (whatever you like)

```
username * password12345 *
```
# 3. Set forwarding
 #### 3.1 find the file which in /etc/sysctl.conf.
 * in /etc/sysctl.conf,you should find #net.ipv4.ip_forward=1  and remove #like follow
 ```
 net.ipv4.ip_forward=1
 ```
 #### 3.2 run the code to make Configuration takes effect
 $ sysctl -p
 
#### 3.3  Allow gre protocol , port 1723 , port 47. Final open forward
 ```diff
 $ sudo iptables -A INPUT -p gre -j ACCEPT 
 $ sudo iptables -A INPUT -p tcp --dport 1723 -j ACCEPT 
 $ sudo iptables -A INPUT -p tcp --dport 47 -j ACCEPT 
 $ sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
 
+ tips: Pay attention to fill in eth0, different machines are different,    
+ you can enter `ifconfig` in the terminal to view the network card     
+ and the name of the network card
```
# 4. Start VPN
 ```
 $ sudo /etc/init.d/xl2tpd restart 
 ```
# 5. some error
 when you connect vpn use win10 and meet some error. Open Control Panel\Network and Internet\Network Connections. find the vpn connect which you set.Open its properties, check the protocol in the security column.
 

`Allow use these protocols`
- [x] Unencrypted Password (PAP) (U)   
- [x] Challenge Handshake Authentication Protocol (CHAP) (H)   
- [x] duiduikuang'duiMicrosoft CHAP Version 2 (MS CHAP v2)   


# 6.reference
 [reference link which is Chinese]( https://my.oschina.net/zhoudage/blog/1630728 )

# 7.summary
If you restart your server,you could run the fllow conmand
```
#start ipsec etc, turn on dorward
$ sudo /etc/init.d/ipsec start
$ sudo /etc/init.d/xl2tpd restart 
$ sudo iptables -A INPUT -p gre -j ACCEPT 
$ sudo iptables -A INPUT -p tcp --dport 1723 -j ACCEPT 
$ sudo iptables -A INPUT -p tcp --dport 47 -j ACCEPT 
$ sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
```
 
 
