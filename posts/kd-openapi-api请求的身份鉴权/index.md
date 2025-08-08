# kd-OpenAPI-API请求的身份鉴权


OpenAPI共支持五种认证方式，分别是：

- AccessToken 认证
- JWT认证
- 摘要认证
- 基本认证
- 签名认证

其中，AccessToken认证应用最广泛，基本认证则最方便。从安全和易用性的角度进行分析，不同的认证方式各有特点，用户可以根据具体需求，选择合适的认证方式。

| 特点   | 强弱分析                                               |
| ------ | ------------------------------------------------------ |
| 安全性 | 签名认证 > 摘要认证 > JWT > AccessToken认证 > 基本认证 |
| 便捷性 | 基本认证 >JWT > AccessToken认证 > 摘要认证 > 签名认证  |

## AccessToken 认证/JWT认证

Access Token 是在身份验证和授权过程中用于确保安全性的一种机制。它使得应用能够安全地代表用户访问资源，同时避免泄露用户的敏感信息。它通常有一个有效期，过期后需要通过 Refresh Token 或重新授权来更新。 

**Access Token（访问令牌）** 是一种用来验证用户身份和授权应用访问特定资源的凭证。在现代的身份验证和授权体系中，特别是基于 OAuth 2.0 和 OpenID Connect 协议的系统中，access token 扮演着非常重要的角色。它通常由身份验证服务（例如，OAuth 2.0 授权服务器）发放，并被应用程序用来访问受保护的 API 或资源。

![img](./images/v2-e3dabd7f8dec94de9f96451d424e9184_1440w.jpg)

### 认证流程

1. 客户端通过认证服务器请求认证；
2. 认证服务器检验客户端认证是否有效，如果有效，返回一个 Access Token 和一个 Refresh Token；
3. 客户端通过 Access Token 去请求服务器的资源；
4. 如果 Access Token 有效，服务器返回给客户端资源，如果 Access Token 失效，服务器返回给客户端 Token 失效的信息，然后客户端会通过 Refresh Token 再次请求获取新的 Access Token；

### **Access Token 是什么？**

Access Token 通常包含以下信息：

- **用户信息**：如用户的 ID、权限、角色等。
- **有效期**：access token 通常有一个有效期，过期后需要重新获取。
- **授权范围**：即该令牌可以访问的资源范围，例如特定的 API 路径或服务。
- **签名**：为了防止篡改，access token 会被加密或使用签名确保其完整性和真实性。

### 为什么需要 Access Token？

Access Token 的核心作用是验证用户身份并授权访问资源。它解决了以下几个问题：

- **安全性**：access token 可以防止用户名和密码泄露。使用 token 替代密码传输，有效防止暴露用户的敏感信息。
- **跨系统授权**：在分布式系统或第三方应用中，token 可以用于跨服务、跨平台的身份验证与授权，避免了每次都需要重新登录。
- **短期有效**：与长期的凭证（如用户名、密码）不同，access token 是短期有效的，这降低了被滥用的风险。

### **Access Token 的格式**

Access Token 的格式可以有多种，常见的格式包括：

- **JWT（JSON Web Token）**：JWT 是一种基于 JSON 的轻量级的身份验证令牌，通常由三部分组成：头部（header）、负载（payload）、签名（signature）。JWT 是目前最常用的 Access Token 格式。
- **Opaque Token（不透明令牌）**：不像 JWT 可以直接查看和解析，Opaque Token 是一串随机的字符串，不能直接查看其中的内容，需要通过授权服务器来验证。

### **Access Token 的安全性**

Access Token 需要妥善保护，避免泄露。泄露的 Access Token 可以被恶意用户用来冒充合法用户进行非法操作。为了增强安全性，常见的做法包括：

- **使用 HTTPS**：确保所有的 Access Token 都通过 HTTPS 传输，防止中间人攻击。
- **使用短生命周期 Token**：Access Token 设置较短的有效期，减小令牌泄露后的风险。
- **使用 Refresh Token 刷新**：使用 Refresh Token 更新 Access Token，避免长期暴露 Access Token。

## 摘要认证

摘要认证使用摘要代替token的传输，并采用MD5、SHA等不可逆哈希算法对参数摘要。首先，客户端通过指定的加密算法将随机数、时间戳等信息生成摘要。然后，调用请求时，将摘要放在请求头中发送给服务器；最后，服务器拿到摘要后，使用同样的算法和数据库密钥进行摘要并对比，匹配则身份验证通过。

### 认证流程

![img](./images/1615446-20200331162837843-77969056.png)

（1）客户端发送一个未经认证的请求到服务器。
（2）服务器返回一个HTTP 401 Unauthorized响应，其中包含一个WWW-Authenticate头部字段。
（3）客户端收到401响应后，会根据服务器提供的信息，计算出一个摘要（digest）。客户端将摘要信息添加到请求头中，并重新发送认证请求。
（4）服务器收到带有摘要的请求后，会使用相同的算法计算出摘要，并与客户端发送的摘要进行比较，如果一致，则服务器会接受该请求并返回请求的资源，否则，服务器拒绝请求并返回401。

### 简单示例

常用参数

```
username，用户。
password，密码。
realm，域，用来指示需要哪个域的用户名和密码。
nonce，服务端产生的随机数，唯一的、不重复的。
nc，认证的次数。
cnonce，客户端产生的随机数，唯一的、不重复的。
qop，包含auth和auth-int两种策略。
opaque，服务器端产生的字符串，透传而已。
response，服务器根据相关信息计算摘要，并与客户端传过来的response值对比，如果一样，则认证通过，否则通不过。
```

**HttpClient 代码示例**  模拟客户端两次请求

```java
/**
 * 摘要认证 两次请求
 *
 * @param url
 * @return 返回结果
 */
public static Boolean doPostDigest(String url, String username, String password) {
    log.info("Post请求url:[{}]", url);
    CloseableHttpClient httpClient = null;
    CloseableHttpResponse response = null;
    HttpPost httpPost = null;
    String strResponse = null;
    Boolean flag = false;
    try {
        httpClient = HttpClients.createDefault();
        httpPost = new HttpPost(url);
        // 构造请求头
        httpPost.setHeader("Content-type", "application/json; charset=utf-8");
        httpPost.addHeader("Cache-Control", "no-cache"); //设置缓存
        httpPost.setHeader("Connection", "Close");

        RequestConfig.Builder builder = RequestConfig.custom();
        builder.setSocketTimeout(3000); //设置请求时间
        builder.setConnectTimeout(5000); //设置超时时间
        builder.setRedirectsEnabled(false);//设置是否跳转链接(反向代理)
        // 设置 连接 属性
        httpPost.setConfig(builder.build());
        // 执行请求
        response = httpClient.execute(httpPost);
        HttpEntity responseEntity = response.getEntity();
        // 检验返回码
        int statusCode = response.getStatusLine().getStatusCode();
        log.info("第一次发送摘要认证 Post请求 返回码:{}", statusCode);
        if (401 == statusCode) {
            strResponse = EntityUtils.toString(responseEntity, "utf-8");
            log.info("Post请求401返回结果:{}", strResponse);

            // 组织参数，发起第二次请求
            Header[] headers = response.getHeaders("WWW-Authenticate");
            HeaderElement[] elements = headers[0].getElements();
            String realm = null;
            String qop = null;
            String nonce = null;
            String opaque = null;
            String method = "POST";
            String uri = "/VIID/System/Register";
            for (HeaderElement element : elements) {
                if (element.getName().equals("Digest realm")) {
                    realm = element.getValue();
                } else if (element.getName().equals("qop")) {
                    qop = element.getValue();
                } else if (element.getName().equals("nonce")) {
                    nonce = element.getValue();
                } else if (element.getName().equals("opaque")) {
                    opaque = element.getValue();
                }
            }
            // 以上为 获取第一次请求后返回的 数据
            String nc = "00000001";
            String cnonce = "uniview";
            // 后期变成可配置
            String a1 = username + ":" + realm + ":" + password;
            String a2 = method + ":" + uri;
            String response1 = null;
            // 获取 Digest 这个字符串
            String backString = response.getFirstHeader("WWW-Authenticate").getValue();
            try {
                response1 = md5DigestAsHex((md5DigestAsHex(a1.getBytes("UTF-8")) + ":" + nonce + ":" + nc
                        + ":" + "uniview" + ":" + qop + ":" + md5DigestAsHex(a2.getBytes("UTF-8"))).getBytes("UTF-8"));
            } catch (UnsupportedEncodingException e) {
                log.error("MD5异常:{}", e.getLocalizedMessage());
            }
            httpPost.addHeader("Authorization", backString + ",username=\"" + username + "\"" + ",realm=\"" + realm + "\""
                    + ",nonce=\"" + nonce + "\"" + ",uri=\"" + uri + "\"" + ",qop=\"" + qop + "\"" + ",nc=\"" + nc + "\""
                    + ",cnonce=\"" + cnonce + "\"" + ",response=\"" + response1 + "\"" + ",opaque=\"" + opaque);

            // 发送第二次请求
            response = httpClient.execute(httpPost);
            HttpEntity entity = response.getEntity();
            int statusCode1 = response.getStatusLine().getStatusCode();
            log.info("第二次发送摘要认证 Post请求 返回码:{}");
            if (HttpStatus.SC_OK == statusCode1) {
                strResponse = EntityUtils.toString(entity, StandardCharsets.UTF_8);
                flag = true;
                return flag;
            } else {
                strResponse = EntityUtils.toString(entity, StandardCharsets.UTF_8);
                log.error("第二次鉴权认证请求非 200 返回结果:{}", strResponse);
                return flag;
            }
        } else {
            strResponse = EntityUtils.toString(responseEntity, StandardCharsets.UTF_8);
            log.error("第一次鉴权认证请求非401 返回结果:{}", strResponse);
        }
    } catch (Exception e) {
        log.error("摘要认证 发送请求失败", e.getLocalizedMessage());
    } finally {
        if (null != httpPost) {
            httpPost.releaseConnection();
        }
        if (null != response) {
            try {
                response.close();
            } catch (IOException e) {
                log.error("httpResponse流关闭异常：", e);
            }
        }
        if (null != httpClient) {
            try {
                httpClient.close();
            } catch (IOException e) {
                log.error("httpClient 流关闭异常：", e);
            }
        }
    }
    return flag;
}
```

```java

public static String encode(String password) {

    try {
        //获取MD5对象
        MessageDigest instance = MessageDigest.getInstance("MD5");
        //对字符串进行加密，返回字节数组
        byte[] digest = instance.digest(password.getBytes());

        StringBuffer sb = new StringBuffer();
        for (byte b : digest) {
            //获取字节低八位有效值
            int i = b & 0xff;
            //将整数转换为16进制
            String hexString = Integer.toHexString(i);
            //将长度为1时，补零
            if (hexString.length() < 2) {
                hexString = "0" + hexString;
            }
            //MD5永远是32位
            sb.append(hexString);
        }

        return sb.toString();
    } catch (NoSuchAlgorithmException e) {
        //没有该算法时抛出此异常
        e.printStackTrace();

    }
    return "";
}
```

**某系统摘要认证**

1. 生成第三方appId和第三方app的摘要认证加密秘钥；

2. API调用过程中加入验证信息。

![image-20250606142918454](./images/image-20250606142918454.png)

URL请求参数说明

| **请求参数**   | **类型** | **必传** | **字段说明**                                                 |
| -------------- | -------- | -------- | ------------------------------------------------------------ |
| name           | string   | 是       | 请求参数，供应商名称                                         |
| pageSize       | int      | 是       | 请求参数，分页数量                                           |
| PageNo         | int      | 是       | 请求参数，查询页码                                           |
| appId          | string   | 是       | 第三方appId                                                  |
| timestamp      | string   | 是       | 当前时间，和服务器时间相差10分钟就为无效请求，目前格式为yyyy-MM-dd HH:mm:ss |
| signatureNonce | string   | 是       | 访问随机数，最好32位uuid，如果随机数已经访问过，则再次访问无效 |
| signature      | string   | 是       | 用SHA-256算法和摘要加密认证密钥key对query的拼接参数。公式：HMACSHA256（query的拼接参数+timestamp+signatureNonce，key） |
| parameters     | string   | 是       | 参与摘要的参数列表，多个参数用“,”隔开                        |
| user           | string   | 是       | 金蝶云或云之家的用户名                                       |
| accountId      | string   | 是       | 数据中心id                                                   |
| usertype       | string   | 否       | 用户类型：Mobile： 标识为手机，默认为手机Email：标识为emailUserName：标识为用户名 |

## 基本认证

Basic认证是最常见的HTTP认证方式之一。在Basic认证中，客户端发送请求时，会在请求头中包含一个"Authorization"字段，该字段包含了经过Base64编码的用户名和密码。服务器收到请求后，会解码该字段并验证用户名和密码是否正确。

例如，用户名是 `admin`，密码是 `password`，将这两者拼接为 `admin:password`，然后用 Base64 编码后变成 `YWRtaW46cGFzc3dvcmQ=`，该字符串会被添加到请求头中的 `Authorization` 字段，格式如下：

`Authorization: Basic YWRtaW46cGFzc3dvcmQ=`

**HttpURLConnection类进行HTTPS基本认证的示例代码**

```java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.Base64;

public class HttpsBasicAuthExample {

    public static void main(String[] args) throws Exception {
        // 设置请求的URL
        URL url = new URL("
        
        // 创建HttpURLConnection对象
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        
        // 设置请求方法为GET
        connection.setRequestMethod("GET");
        
        // 添加Authorization字段
        String username = "your_username";
        String password = "your_password";
        String credentials = username + ":" + password;
        String encodedCredentials = Base64.getEncoder().encodeToString(credentials.getBytes(StandardCharsets.UTF_8));
        connection.setRequestProperty("Authorization", "Basic " + encodedCredentials);
        
        // 发送请求并获取响应
        int responseCode = connection.getResponseCode();
        BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        String line;
        StringBuilder response = new StringBuilder();
        while ((line = reader.readLine()) != null) {
            response.append(line);
        }
        reader.close();
        
        // 打印响应内容
        System.out.println("Response Code: " + responseCode);
        System.out.println("Response Body: " + response.toString());
    }
}
```

## 签名认证

### 认证流程

1. 客户端准备请求：客户端构建API请求，并收集所有必要的参数，如请求方法、URL、时间戳、身份凭证等。
2. 参数排序：将请求参数按照一定的规则进行排序，通常是按参数名称的字母顺序进行排序。
3. 生成待签名字符串：将排序后的参数按照一定的格式拼接成一个待签名的字符串。
4. 添加密钥：将密钥（一般是客户端的私钥）加入待签名字符串中。
5. 计算签名：使用特定的加密算法（如HMAC-SHA1、HMAC-SHA256等）对待签名字符串进行加密，生成签名。
6. 发送请求：将生成的签名添加到API请求中的特定位置（如请求头或请求参数）。
7. 服务器端验证签名：服务器端收到请求后，会使用相同的算法和密钥对接收到的参数进行签名计算。然后将服务器端计算得到的签名与请求中的签名进行比对，如果一致，则验证通过；否则，验证失败。

**某系统实现方案**

1. 第三方系统产生随机会话密钥randomKey, 通过randomKey和加密策略将请求body参数加密，放在字段encryptData中，同时将会话密钥randomKey通过平台公钥RSA加密，放入字段dgtlEnvlp中；
2. 第三方系统通过SHA256将随机数，时间戳及请求body报文加签后发送给OpenAPI；
3. OpenAPI通过openApiSign(SecretKey)验证后，获取代理用户，第三方应用ID及数据中心，再验证签名，此时会检查随机数、时间戳以及请求参数的合法性和有效性；
4. 签名验证通过后，解密并还原数据，进行下一步业务处理，如查询或保存数据；
5. OpenAPI产生随机数，加密返回结果数据；
6. OpenAPI对返回结果数据进行SHA256签名；
7. 第三方系统接收到返回结果进行验证签名；
8. 第三方系统解密返回的结果，进行相关业务处理。

