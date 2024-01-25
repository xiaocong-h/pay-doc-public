---
title: 签名秘钥生成方式
---

## OpenSSL安装
### Linux（Ubuntu）


`sudo apt-get install openssl`  


### Windows

可以在官网下载 [Downloads](https://www.openssl.org/source/) .

### macOS

自带，或者输入一下命令重新安装



```brew reinstall openssl```


## RSA密钥对的产生
### Linux \ macOS


```
openssl genrsa -out pay_rsa_private_key.pem 1024
openssl pkcs8 -topk8 -inform PEM -in pay_rsa_private_key.pem  -outform PEM -nocrypt
openssl rsa -in pay_rsa_private_key.pem -pubout -out  pay_rsa_public_key.pem
```

### Windows 在命令行窗口操作：


```
C:\OpenSSL-Win32\bin>openssl.exe
OpenSSL> genrsa -out pay_rsa_private_key.pem 1024
OpenSSL> pkcs8 -topk8 -inform PEM -in pay_rsa_private_key.pem  -outform PEM -nocrypt
OpenSSL> rsa -in pay_rsa_private_key.pem -pubout -out  pay_rsa_public_key.pem
OpenSSL> exit
```


### 使用方式：

在当前目录下会产生两个文件pay_rsa_private_key.pem和 pay_rsa_public_key.pem. 前面一个是私钥文件，后面一个是公钥文件。商家自行保管私钥文件，用来对关键接口签名，并将公钥文件除注释部分内容通过接口上传，EchoooPay 后台会对接口验签。

#### PKCS8格式的标注私钥文件


```
-----BEGIN PRIVATE KEY-----
MIICdgIBADANBgkqhkiG9w0BAQEFAASCAmAwggJcAgEAAoGBANabv9RXmXbcD1qT
I0zTqhRfsee6Yo7OnfXCI0NNeeUJMAqz0twu60uTI/PDJ70FA7+tZ+9AJZhv69dO
gdO3aTwYJVoWYcmcvj3Lq4w3KZL4da9W8zyazYHJZn0hIs/pbENzBT8JgrnVgywM
uEw1MMBiSSq/HpSw7flWlr9Hrm9LAgMBAAECgYAe4S5LCYfFeIilCcLsjRBN+i8J
HuKLleNYt2SHjKBbemT1RUaz8/RbXYKw0oXnRs9xRyxLWrmOI5yV0HAR3LRBc+va
DzaeE03fMavlHhnUV50M+FWLDplia7R/CvIVLLSO8nnuSCe8M2RDaNPtZNGkSfnf
I87xEFGL1NIf9oQoQQJBAOqsXA/bBz5623aQOZmS9Dv3PBgSQafO8T2AqqOoS51N
9M9ODPQntE5nhmA3+sCpnYi41XjJUhdjbkfU9VhfWPsCQQDqHJVDC/a15X6vzcSw
ZyeFITXM9FNkPcpXQtz79HWrrzq9UaTOVmWZVqTUbQmaV96clhzDNXlZWSF/oxdJ
mRHxAkEA6TrwLFn08yXLZCSm+njQ/6ASO6I5WnwTyppL/WdP70EBI99ghG/JhXri
VFKOhliM1stMbkU3r0ME4aNHS9NHbQJAavkokvxSfQcifj5t05UvD7v/E2nI+RLq
9DiPNWmcoxhspLk7rzT3M7vNkWtJagcgpzhIaEJ08oixr9rb9ztEYQJAY8t4Z/3A
DQLETh3GZ5XPPCpEFElAXJg1ksNU/HAlF6+DCwv4E5ainVAlR3+ZeXcvw0a/KTpg
vOntaIf9dsS4Fw==
-----END PRIVATE KEY-----
```

#### 公钥文件


```
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDWm7/UV5l23A9akyNM06oUX7Hn
umKOzp31wiNDTXnlCTAKs9LcLutLkyPzwye9BQO/rWfvQCWYb+vXToHTt2k8GCVa
FmHJnL49y6uMNymS+HWvVvM8ms2ByWZ9ISLP6WxDcwU/CYK51YMsDLhMNTDAYkkq
vx6UsO35Vpa/R65vSwIDAQAB
-----END PUBLIC KEY-----
```


#### 需要上传的公钥文件信息：


```
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDWm7/UV5l23A9akyNM06oUX7Hn
umKOzp31wiNDTXnlCTAKs9LcLutLkyPzwye9BQO/rWfvQCWYb+vXToHTt2k8GCVa
FmHJnL49y6uMNymS+HWvVvM8ms2ByWZ9ISLP6WxDcwU/CYK51YMsDLhMNTDAYkkq
vx6UsO35Vpa/R65vSwIDAQAB
```