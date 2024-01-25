# 回调接口

## 商户订单回调

### 回调信息：POST

商户订单号
EchoooPay订单号
商户入网钱包地址
订单法币币种
订单法币金额
订单状态
付款网络ID（链ID）：例：Ethereum
付款币种
付款数量
订单收款地址
完成时间
signature

### 流程:

平台维护一对RSA秘钥对，私钥由平台管理不公开，公钥公开。

公钥:

```
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAhLrV9mzKGU2ntzXAt/AUn+JaA8T6WAUtBiT+EQjRjEi6gYXlxOEsmkh2a0lmlaYdIewUmmsyHYvpD5pB1r6GmWUomIzOqB15sdVCmvydMwF3cKqYmrUH45R3ap/mqqP+3C+2Ed/FiMRMkfxvAMMCy3ow4xD/P72LLoWtQwq/ULx41Y3Ps3Ckf+8kFRsNigCm5nkgs6S+hOTc40j+GaoiLc4ORb9CivV3BcnQ2CVsp48VIH3DBRa1gGPAQ0dbB08IlGf6zzKNgzHiagx8u0G78x9DkG8kujCy5L+eWV2QcrRSEQM8MSDDnlqmjdRZw3vJ07RH+8rxwignccq68w2E0QIDAQAB
```
平台使用私钥签名回调请求, 写入回调信息的 signature 字段, 商户在自己实现的回调接口使用公钥验证签名，签名通过后才处理回调信息逻辑, 否则丢弃。

### 签名和验签规则:


在回调返回参数列表中，除去signature参数外，回调返回的参数都是要签名的参数。

对数组里的每一个值从a到z的顺序排序，若遇到相同首字母，则看第二个字母，以此类推。

排序完成之后，再把所有数组值以key="value"和“&"字符连接起来，这串字符串便是待验签名字符串。签名算法同开放接口鉴权部分。

Note:

没有值的参数，无需包含到待签名数据中；

签名时将字符转化成字节流时指定的字符集为 UTF-8；

商户验签代码示例:

```
// 商户使用公钥验证签名,其中data为 '待验签名字符串', signature为回调返回签名
public static boolean publicKeyVerify(String data, String publicKey, String signData, Charset charset) throws Exception {
    byte[] publicBytes = Base64.decodeBase64(publicKey);
    X509EncodedKeySpec keySpec = new X509EncodedKeySpec(publicBytes);
    KeyFactory keyFactory = KeyFactory.getInstance("RSA");
    PublicKey pubKey = keyFactory.generatePublic(keySpec);
    Signature signature = Signature.getInstance("SHA256withRSA");
    signature.initVerify(pubKey);
    byte[] dataBytes = data.getBytes(charset);
    signature.update(dataBytes);
    return signature.verify(Base64.decodeBase64(signData));
}
```
