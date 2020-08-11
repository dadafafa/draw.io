# vpn
Tutorial on setting up a VPN (copy from https://my.oschina.net/zhoudage/blog/1630728 )


# 1.Install openswan
 `$ sudo apt-get install openswan`
 
  if the are any error like Package ‘openswan‘ has no installation candidate,run the follow code:
  
  `sudo vi /etc/apt/sources.list.d/lzu.list`
  
 copy the follow code to lzu.list
  
  ****

|作者|dadafafa|
|---|---
  |deb http://mirror.lzu.edu.cn/ubuntu/ precise main restricted universe multiverse |
  |deb http://mirror.lzu.edu.cn/ubuntu/ precise-security main restricted universe multiverse|
  |deb http://mirror.lzu.edu.cn/ubuntu/ precise-updates main restricted universe multiverse|
  |deb http://mirror.lzu.edu.cn/ubuntu/ precise-proposed main restricted universe multiverse|
  |deb http://mirror.lzu.edu.cn/ubuntu/ precise-backports main restricted universe multiverse|
  |deb-src http://mirror.lzu.edu.cn/ubuntu/ precise main restricted universe multiverse|
  |deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-security main restricted universe multiverse|
  |deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-updates main restricted universe multiverse|
  |deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-proposed main restricted universe multiverse|
  |deb-src http://mirror.lzu.edu.cn/ubuntu/ precise-backports main restricted universe multiverse|
  
  ****
   
   
   
   
   
   
  
  
   

