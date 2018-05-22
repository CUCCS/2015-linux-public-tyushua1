### Linux系统与网络管理 实验报告5
### 实验名称  
- web服务器配置

### 实验环境  
- server1
  - ubuntu 16.04 (host only网卡：192.168.56.102)
  - verynginx(port:80)
  - nginx(port:8080)
- server2
  - ubuntu 16.04(host only网卡：192.168.56.103)
  - wordpress
- 安装配置nginx和verynginx
  - 安装nginx  
```
sudo apt install nginx
```  
  - 配置nginx为8080端口  
   参考：[https://blog.csdn.net/w20101310/article/details/78835668](https://blog.csdn.net/w20101310/article/details/78835668)  
  - 安装verynginx
  ```shell
  git clone https://github.com/alexazhou/VeryNginx.git
sudo apt install libpcre3 libpcre3-dev libssl-dev build-essential
sudo python install.py install
#将/opt/verynginx/openresty/nginx/conf/nginx.conf文件第二行改为user www-data
启动服务 sudo /opt/verynginx/openresty/nginx/sbin/nginx
  ```
  - 在一台主机上同时配置nginx和verynginx完成  
  ![](images/port80.png)  
  ![](images/verynginx1.png)  
  ![](images/nginx.png)

- 安装配置WordPress
  - 参考链接  
  [https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-lamp-on-ubuntu-16-04)  
  - 使用Wordpress搭建的站点对外提供访问的地址为： https://wp.sec.cuc.edu.cn 和 http://wp.sec.cuc.edu.cn
    - client 修改hosts文件  
    ![](images/hosts.png)
    - 通过域名访问
    ![](images/domain-wordpress.png)
    - http  
    ![](images/wordpress1.png)  
    - https
      - [How To Create a Self-Signed SSL Certificate for Apache in Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-create-a-self-signed-ssl-certificate-for-apache-in-ubuntu-16-04)  
    ![](images/https.png)

  - DVWA未安装成功


### 实验要求
#### 设置反向代理  
- verynginx配置  
  - matcher  
  ![](images/fd-match.png)
  - proxy pass  
  ![](images/fd.png)
- client 修改hosts文件
![]()  
- 配置未生效  

#### 安全加固要求
- verynginx设置Matcher匹配，Response响应和Filter过滤
- 使用IP地址方式均无法访问上述任意站点，并向访客展示自定义的友好错误提示信息页面-1  
  - matcher
  ![](images/ip-match.png)  
  - filter  
  ![](images/ip-filter.png)
  - response
  ![](images/ip-response.png)
- 在不升级Wordpress版本的情况下，通过定制VeryNginx的访问控制策略规则，热修复WordPress < 4.7.1 - Username Enumeration
  - 修复方案：禁止对/wp-json/wp/v2/users/的访问  
  - matcher
  ![](images/bug-match.png)
  - filter
  ![](images/bug-filter.png)
  - response
  ![](images/bug-response.png)

#### verynginx配置要求
- VeryNginx的Web管理页面仅允许白名单上的访客来源IP，其他来源的IP访问均向访客展示自定义的友好错误提示信息页面-3
  - matcher
  ![](images/white-match.png)
  - filter
  ![](images/white-filter.png)
  - response
  ![](images/white-response.png)

- 通过定制VeryNginx的访问控制策略规则实现：
  - 限制DVWA站点的单IP访问速率为每秒请求数 < 50  
  - 限制Wordpress站点的单IP访问速率为每秒请求数 < 20  
    - freqency limit
    ![](images/freqency_limit.png)
  - 超过访问频率限制的请求直接返回自定义错误提示信息页面-4
  ![](images/frequency-response.png)

  - 禁止curl访问
    - matcher
    ![](images/no-curl-match.png)
    - filter
    ![](images/no-curl-filter.png)
