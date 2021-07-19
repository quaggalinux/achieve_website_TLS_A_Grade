# achieve_website_TLS_A_Grade 
网站获得TLS测试A Grade的配置方法  
   
现在可能很多小伙伴都在小鸡上建了站，但是网站的第一道安全屏障TLS是否安全，配置是否正确，这个是可以测试一下的  
   
访问网站https://www.ssllabs.com/ssltest/analyze.html ，然后输入你的网站域名，如果是之前测试过的，
需要点网页上的“Clear cache”小字清除原来的报告，然后网页会重新测试，如果你的网站使用了CDN，那就不用测试了，
因为测试出来的结果只是CDN的结果，一定是A Grade，不是A Grade的CDN早死掉了，
但这个测试结果和你网站的TLS是否安全完全没有关系  
   
下面说的情况都是基于你的网站是直接访问，没有使用CDN的状态，网站https://www.ssllabs.com/ssltest/analyze.html 
的测试结果认定A Grade的主要条件就是TLS Protocols，就是1.0，1.1，1.2，1.3这些，另外一个是Forward Secrecy（前向加密）
这两个条件任何一个不达到要求都拿B Grade，当然其他一些条件不满足也可能导致拿B Grade，但一般而言现在的web前端软件起来了
那么其他这些条件都满足的，除非是非常老版本的软件，但前面两个条件缺省配置一定是不满足的，所以一定是拿B Grade    
   
下面是介绍如何配置web前端软件以获得TLS测试A Grade  
下面的配置环境以ubuntu 18或更高版本，nginx 1.14或更高版本为例，其他操作系统版本及其他web前端软件就依葫芦画瓢  
  
首先你的网站得签了正确的证书，就是在主流浏览器上浏览时出正常的小锁头   
  
然后登录到系统，转到root用户，打入下面的命令编辑nginx主配置文件，如果是虚拟主机配置文件就要小心了，
因为主配置文件已经有一些ssl的配置语句，当你在虚拟主机配置文件加新的ssl配置语句后，出来用
nginx -t 命令测试配置文件正确性时会报错的，这时就要决定在那一个配置文件写这些ssl配置语句了，
因为同样命令只能写在一个地方
   
#nano /etc/nginx/nginx.conf  
  
修改或追加下面3行配置

ssl_protocols TLSv1.2 TLSv1.3;
ssl_prefer_server_ciphers on;
ssl_ciphers "EECDH+ECDSA+AESGCM EECDH+aRSA+AESGCM EECDH+ECDSA+SHA384 EECDH+ECDSA+SHA256 EECDH+aRSA+SHA384 EECDH+aRSA+SHA256 EECDH+aRSA+RC4 EECDH EDH+aRSA RC4 !aNULL !eNULL !LOW !3DES !MD5 !EXP !PSK !SRP !DSS !RC4";
  
保存并退出  
首先要保证第3行配置是一行过，没有回车的，否则nginx -t会报告语法错误  
第1行的意思是只使用TLSv1.2 TLSv1.3两种协议，因为允许使用TLS 1.0及TLS 1.1协议会导致测试B Grade  
第2行的意思是打开前向加密功能，没有打开这个功能会导致测试B Grade  
第3行的意思是禁止使用RC4前向加密协议，因为这个协议已被证明不安全  
  
检查nginx配置语法  
  
#nginx -t  
  
检查命令如果报告ok的话，就可以用下面命令重启nginx的服务并检查nginx运行状态  
  
#systemctl restart nginx  
#systemctl status nginx  
  
nginx状态正常的话，先用浏览器访问一下自己网站，看看能否访问，如果正常，那么再用前面那个网站测试TLS  
  
没有意外的话，就可以恭喜小伙伴的网站TLS测试获得 A Grade  
  




