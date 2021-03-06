le-dns-shell
----------------
本脚本通过调用DNS API修改TXT记录, 使用DNS验证方式快速签发[Let's Encrypt](https://letsencrypt.org/)证书, DNS API支持 CloudXns 和 Dnspod。

脚本基于[dehydrated](https://github.com/lukas2511/dehydrated) (letsencrypt.sh), DNS hooks来自[xdtianyu/scripts](https://github.com/xdtianyu/scripts/tree/master/le-dns)。 脚本同样借鉴了[xdtianyu](https://github.com/xdtianyu/scripts/tree/master/le-dns)的原版脚本。

## 获取

**1.获取DNS API**

CloudXns: https://www.cloudxns.net/AccountManage/apimanage.html

Dnspod: https://www.dnspod.cn/console/user/security

**2.获取脚本与配置文件**

进入工作目录,例如 `cd /www/wwwroot/ssl`
```
wget https://raw.githubusercontent.com/Mr-Fook/le-dns-shell/master/le-dns.conf
wget https://raw.githubusercontent.com/Mr-Fook/le-dns-shell/master/le-dns.sh
chmod +x le-dns.sh
```

**3.按照说明修改配置文件** 

```vi le-dns.conf```
```
#DNS API 可选 cloudxns 和 dnspod, 并在对应区域填写API.
API="cloudxns"

#CloudXns
API_KEY="API_KEY"
SECRET_KEY="SECRET_KEY"

#Dnspod
TOKEN="TOKEN_ID,API_TOKEN"  #注意，中间有“,”才是完整的TOKEN,例如"198964,-11111111s"
RECORD_LINE="默认"

#域名列表
DOMAIN="xxx.xx"    #主域名，如google.com
CERT_DOMAINS="xxx.xx www.xxx.xx"  #待签域名列表，格式"xx.xx www.xx.xx xxx.xx.xx",添加时以空格间隔，上限100个.

#证书类型
CERTTYPE="ECC"     #可选 ECC 和 RSA, 需签发双证书时使用"BOTH",ECC证书更高效,但少数旧平台不支持.
KEY="prime256v1"   #ECC证书私钥类型，可选 secp384r1 和 prime256v1，签发ECC证书时生效.

#证书生成目录
CERTPATH="./certs" #此默认配置为脚本同目录下certs文件夹。
```

## 使用
**运行方式 1 :** 

`默认使用脚本同目录下配置文件 le-dns.conf`
```
./le-dns.sh
```
**运行方式 2 :** 

`使用指定配置文件,以 /root/xxx.conf 为例`
```
./le-dns.sh /root/xxx.conf
```
**运行方式 3 :**

`使用脚本同目录下指定配置文件,以 xxx.conf 为例`
```
./le-dns.sh xxx.conf
```
运行成功后证书会生成在配置文件指定的目录,默认为脚本同目录下 certs 文件夹。

使用其中的 `fullchain.pem` 和 `privikey.pem`。


## 其他说明
**1.关于Let's Encrypt 账户密钥** 

首次运行脚本后会创建 Let's Encrypt 账户密钥，用于 Let's Encrypt 识别你的身份(`签发过的子域名重签时不用更新DNS记录`), 密钥存放于脚本同目录下 accounts 文件夹。其后运行不再重新创建账户秘钥。如需重新创建秘钥, 需删除 accounts 文件夹, 或给 accounts文件夹 重命名。


**2. 配置cron计划任务自动更新证书**

以centos为例，`crontab -e`  开始编辑crontab。

设定每月1号01:00自动更新证书。
```
00 1 * * 1 /www/wwwroot/ssl/le-dns.sh  >> /www/wwwroot/ssl/log/le-dns.log 2>&1
```
设定每周一01:00自动更新证书。
```
00 1 1 * * /www/wwwroot/ssl/le-dns.sh  >> /www/wwwroot/ssl/log/le-dns.log 2>&1
```
自定义请参考crontab时间参数生成工具： http://www.cronmaker.com/

**3. 证书更新后自动重载相关服务**

在`le-dns.sh`最后加入相关命令，例如`service nginx reload`
## 其他

反馈bug发issue或者Telegram: [Mr_Fook](https://t.me/Mr_Fook)
