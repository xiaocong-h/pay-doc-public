# 开放API鉴权方式

````markdown


## 开放api接口鉴权:
### 流程:
1. 秘钥管理处生成app_key, app_secret, 保存在服务端pay_merchant_secret表中，并透出给商户。

2. 商户使用在请求Header中传入appKey:secret_key123进行查询接口调用。

3. 增删改接口在传入 2 步骤中的appKey的同时还需要用商户私钥签名和时间戳(都是在 Header中传入，如signToken:sign123, Timestamp=1704643200000(ms))。



### 签名signToken生成规则：
1. 商户端拼接待签名字符串，拼接内容： 时间戳(单位ms)_URI_param

2. 对以上待签名字符串进行使用私钥签名（私钥生成方式），作为signToken。

#### 拼接内容说明：

1. URI为Path部分,比如 /service-pay/sellerApi/getMerchantByUsername

2. 请求将参数及其值拼接成一个字串参数名和参数值用=连接(按字母ASCII 码升序排序，首字母相同则对比下个字母，如前面全部一致短的排在先)
 
   - 如 get 请求参数: ?aparam=2&aaparam=3&username=4802097272&abparam=1 处理完后为：aaparam=3&abparam=1&aparam=2&username=4802097272 
   - 如 post 请求参数如下
   `{"username":"4802097272","aparam":"2","abparam":"1","aaparam":"3"}`处理完后为aaparam=3&abparam=1&aparam=2&username=4802097272

    _注意: 参数拼接过程在调用者应当发生在参数被URL Encoded之前；拼接的参数不需要经过URL Encode，例如符号“&”，“：”或者中文等都应该保持原样。拼接过程与请求被传输过程中的编码过程是两件互不干扰的过程。_

3. 各部分内容用下划线"_" 连接一起作为完整拼接字符串 
### 完整示例 
待签名参数示例: 以接口/service-pay/sellerApi/getMerchantByUsername接口为例



#### 商户公钥： 
```
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDWm7/UV5l23A9akyNM06oUX7Hn
umKOzp31wiNDTXnlCTAKs9LcLutLkyPzwye9BQO/rWfvQCWYb+vXToHTt2k8GCVa
FmHJnL49y6uMNymS+HWvVvM8ms2ByWZ9ISLP6WxDcwU/CYK51YMsDLhMNTDAYkkq
vx6UsO35Vpa/R65vSwIDAQAB
```


#### 商户私钥：
```
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
```


#### 时间戳：

`124124`


#### URL:

`/service-pay/sellerApi/getMerchantByUsername`


#### HTTP Body:

`null`


#### 排序拼接后待签名字符串：

`124124_/service-pay/sellerApi/getMerchantByUsername_aaparam=3&abparam=1&aparam=2&username=4802097272`

#### 生成签名
将'排序拼接后待签名字符串'的结果使用'商户私钥'产生一个SHA256withRSA摘要，并且以Base64编码，产生最终签名. 

_注意：将步骤3的结果传入SHA256withRSA进行计算时，要对字符串进行UTF8编码。以下为java代码示例_


```java
    // 使用私钥进行签名
    private static String sign(String privateKeyStr, String data) throws Exception {
        byte[] keyBytes = Base64.getDecoder().decode(privateKeyStr);
        PKCS8EncodedKeySpec keySpec = new PKCS8EncodedKeySpec(keyBytes);
        KeyFactory keyFactory = KeyFactory.getInstance("RSA");
        PrivateKey privateKey = keyFactory.generatePrivate(keySpec);

        Signature signature = Signature.getInstance("SHA256withRSA");
        signature.initSign(privateKey);
        signature.update(data.getBytes("UTF-8"));

        byte[] signBytes = signature.sign();
        return Base64.getEncoder().encodeToString(signBytes);
    }
```

使用上文中的商户私钥, 产生的签名为

```
V3pfPN1F3RX9Slak0EOhBmWI79iwmsQTECOLs5HOnLa3AOiYx7pZHMAroA3wJ6ksik1bORwhNVdhIf0jexzisD/SZHMRniZmSd7l6+PLT/iE/sguxyhqyz68tvXGSj5+Bv33cH5JMqIHH6ey4R+ojDgY4/zHKMnsdIkbdyQAk/o=
```

````
