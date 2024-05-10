# ruoyi自定义实现前后端traceid加密校验


<!--more-->

### 业务背景

前后端接口调用适用Restful风格，有接口级别的用户权限加校验，同时也包含部分对外公用接口（不鉴权调用），此时为了防止第三方频繁恶意刷新接口，加traceid请求头校验。

### 业务设计

![image-20240429101925819](/java_images/ruoyi/image-20240429101925819.png)

### 业务代码

#### 请求头traceid过滤器

```java
import com.alibaba.fastjson2.JSON;
import com.ruoyi.common.core.domain.AjaxResult;
import com.ruoyi.common.utils.ServletUtils;
import com.ruoyi.common.utils.StringUtils;
import com.ruoyi.common.utils.request.RequestCryptoUtil;
import com.ruoyi.framework.aspectj.LogAspect;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.http.HttpHeaders;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import javax.servlet.FilterChain;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.List;

@Component
public class RequestHeaderTimeFilter extends OncePerRequestFilter {
    private static  final Boolean LOCAL_REQUEST_CHECK = false;

    private static final Long TIME_OUT = Long.valueOf(1*60*1000);
    private static final Logger log = LoggerFactory.getLogger(LogAspect.class);
    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        String requestId = "traceId";
        String traceId = request.getHeader(requestId);
        String host = request.getHeader("Host");
        //本地开发测试直接跳过
        //非本地请求校验请求id
        if(StringUtils.isEmpty(host)){
            timeOutResponse(request,response,"非法请求,host为空");
            return;
        }
        if(!host.startsWith("localhost") || LOCAL_REQUEST_CHECK){
            if(StringUtils.isNotEmpty(traceId)){
                try {
                    String timestamp = RequestCryptoUtil.decrypt(traceId);
                    if(System.currentTimeMillis() - Long.valueOf(timestamp) > TIME_OUT){
                        timeOutResponse(request,response,"检查异常：非法请求，禁止非法抓包哦");
                        return;
                    }
                } catch (Exception e) {
                    timeOutResponse(request,response,"请求异常，the secrect is wrong");
                    return;
                }
            }else{
                timeOutResponse(request,response,"非法请求,traceId为空");
                return;
            }
        }
        filterChain.doFilter(request,response);
    }

    private void timeOutResponse(HttpServletRequest request, HttpServletResponse response, String msg)
    {
        log.error("{}:{}",msg, request.getServletPath());
        ServletUtils.renderString(response, JSON.toJSONString(AjaxResult.error("非法请求", msg)));
    }
}
```

参数变量解读：

LOCAL_REQUEST_CHECK：本地请求是否校验，false:不校验   true:校验

TIME_OUT：请求校验过期时间，时间：ms毫秒   目前值：1分钟

#### RequestCryptoUtil加解密工具类：

```java
package com.ruoyi.common.utils.request;

import org.apache.commons.codec.binary.Base64;

import javax.crypto.Cipher;
import javax.crypto.KeyGenerator;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;
import javax.crypto.spec.IvParameterSpec;
import java.security.Key;
import java.security.SecureRandom;
import java.security.spec.AlgorithmParameterSpec;


/**
 * DES对称加密算法
 * @see
 */
public class RequestCryptoUtil {

    public static final String KEY_ALGORITHM = "DES";
    public static final String CIPHER_ALGORITHM = "DES/CBC/PKCS5Padding";
    /*    private static final byte[] rgbKey = { 79, 27, 57, 71, 89, 97, 32, 63 };
        private static final byte[] rgbIV = { 24, 27, 31, 53, 66, 97, 33, 42 };*/
    private static final String keyHex = "Txs5R1lhID8=";
    private static final String ivHex = "GBsfNUJhISo=";


    /**
     * 生成密钥
     * @param seed 密钥
     * @return 字符串
     * @throws Exception 异常
     */
    public static String initkey() throws Exception {
        return initkey(null);
    }

    /**
     * 生成密钥
     * @param seed 密钥
     * @return 字符串
     * @throws Exception 异常
     */
    public static String initkey(String seed) throws Exception {
        SecureRandom secureRandom = null;

        if(seed != null){
            secureRandom = new SecureRandom(Base64.decodeBase64(seed));
        }else{
            secureRandom = new SecureRandom();
        }

        KeyGenerator kg = KeyGenerator.getInstance(KEY_ALGORITHM);
        kg.init(secureRandom);
        SecretKey secretKey = kg.generateKey();

        return Base64.encodeBase64String(secretKey.getEncoded());
    }

//  /**
//   * 转换密钥
//   */
//  private static Key toKey(byte[] key) throws Exception {
//      DESedeKeySpec dks = new DESedeKeySpec(key);
//      SecretKeyFactory keyFactory = SecretKeyFactory.getInstance(KEY_ALGORITHM);
//      SecretKey secretKey = keyFactory.generateSecret(dks);
//      return secretKey;

//  }
    /**
     * 转换密钥
     */
    private static SecretKey toKey(byte[] key) throws Exception {
        DESKeySpec keySpec = new DESKeySpec(key);
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance(KEY_ALGORITHM);
        SecretKey secretKey = keyFactory.generateSecret(keySpec);
        return secretKey;
    }

    /**
     * 加密数据
     * @param data 待加密数据
     * @param key  密钥
     * @return 加密后的数据
     */
    public static String encrypt(String data) throws Exception {
        Key k = toKey(Base64.decodeBase64(keyHex));
        AlgorithmParameterSpec iv = new IvParameterSpec(Base64.decodeBase64(ivHex));
        Cipher cipher = Cipher.getInstance("DES/CBC/PKCS5Padding");
        cipher.init(Cipher.ENCRYPT_MODE, k,iv);
        /*byte[] encryptData = cipher.doFinal(data.getBytes());
        return Base64.encodeBase64String(encryptData);*/
        byte[] encryptData = cipher.doFinal(data.getBytes("utf-8"));
        return Base64.encodeBase64URLSafeString(encryptData);
    }

    /**
     * 解密数据
     * @param data 待解密数据
     * @param key  密钥
     * @return 解密后的数据
     */
    public static String decrypt(String data) throws Exception {
        Key k = toKey(Base64.decodeBase64(keyHex));
        AlgorithmParameterSpec iv = new IvParameterSpec(Base64.decodeBase64(ivHex));
        Cipher cipher = Cipher.getInstance("DES/CBC/PKCS5Padding");
        cipher.init(Cipher.DECRYPT_MODE, k,iv);
        return new String(cipher.doFinal(Base64.decodeBase64(data)));
    }

    public static void main(String[] args) throws Exception {
        String source = "123456";
        System.out.println("原文: " + source);

        String encryptData = encrypt(source);
        System.out.println("加密: " + encryptData);

        String decryptData = decrypt(encryptData);
        System.out.println("解密: " + decryptData);
    }
}
```

#### 前端请求头加密

```js
var traceid = DES_Encrypt_ByQequest(Date.now().toString());
config.headers['traceid'] = traceid;
```

#### 前端工具类

```js
import CryptoJS from 'crypto-js'


//com.ruoyi.gateway.util.RequestCryptoUtil
export  function DES_Encrypt_ByQequest (value) {
  var keyHex = CryptoJS.enc.Base64.parse("Txs5R1lhID8=");
  var ivHex = CryptoJS.enc.Base64.parse("GBsfNUJhISo=");

  var encrypted = CryptoJS.DES.encrypt(value, keyHex, {
    iv:ivHex,
    mode: CryptoJS.mode.CBC,
    padding:CryptoJS.pad.Pkcs7
  });
  return encrypted.toString();
}
```



### 另外一种加密代码

```java
package com.ruoyi.common.utils;
/*
 *  Copyright 2019-2020 Zheng Jie
 *
 *  Licensed under the Apache License, Version 2.0 (the "License");
 *  you may not use this file except in compliance with the License.
 *  You may obtain a copy of the License at
 *
 *  http://www.apache.org/licenses/LICENSE-2.0
 *
 *  Unless required by applicable law or agreed to in writing, software
 *  distributed under the License is distributed on an "AS IS" BASIS,
 *  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 *  See the License for the specific language governing permissions and
 *  limitations under the License.
 */

import javax.crypto.Cipher;
import javax.crypto.SecretKey;
import javax.crypto.SecretKeyFactory;
import javax.crypto.spec.DESKeySpec;
import javax.crypto.spec.IvParameterSpec;
import java.nio.charset.StandardCharsets;

/**
 * 加密
 * @author Zheng Jie
 * @date 2018-11-23
 */

public class EncryptUtils {

    private static final String STR_PARAM = "Passw0rd";

    private static Cipher cipher;

    private static final IvParameterSpec IV = new IvParameterSpec(STR_PARAM.getBytes(StandardCharsets.UTF_8));

    private static DESKeySpec getDesKeySpec(String source) throws Exception {
        if (source == null || source.length() == 0){
            return null;
        }
        cipher = Cipher.getInstance("DES/CBC/PKCS5Padding");
        String strKey = "Passw0rd";
        return new DESKeySpec(strKey.getBytes(StandardCharsets.UTF_8));
    }

    /**
     * 对称加密
     */
    public static String desEncrypt(String source) throws Exception {
        DESKeySpec desKeySpec = getDesKeySpec(source);
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey secretKey = keyFactory.generateSecret(desKeySpec);
        cipher.init(Cipher.ENCRYPT_MODE, secretKey, IV);
        return byte2hex(
                cipher.doFinal(source.getBytes(StandardCharsets.UTF_8))).toUpperCase();
    }

    /**
     * 对称解密
     */
    public static String desDecrypt(String source) throws Exception {
        byte[] src = hex2byte(source.getBytes(StandardCharsets.UTF_8));
        DESKeySpec desKeySpec = getDesKeySpec(source);
        SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
        SecretKey secretKey = keyFactory.generateSecret(desKeySpec);
        cipher.init(Cipher.DECRYPT_MODE, secretKey, IV);
        byte[] retByte = cipher.doFinal(src);
        return new String(retByte);
    }

    private static String byte2hex(byte[] inStr) {
        String stmp;
        StringBuilder out = new StringBuilder(inStr.length * 2);
        for (byte b : inStr) {
            stmp = Integer.toHexString(b & 0xFF);
            if (stmp.length() == 1) {
                // 如果是0至F的单位字符串，则添加0
                out.append("0").append(stmp);
            } else {
                out.append(stmp);
            }
        }
        return out.toString();
    }

    private static byte[] hex2byte(byte[] b) {
        int size = 2;
        if ((b.length % size) != 0){
            throw new IllegalArgumentException("长度不是偶数");
        }
        byte[] b2 = new byte[b.length / 2];
        for (int n = 0; n < b.length; n += size) {
            String item = new String(b, n, 2);
            b2[n / 2] = (byte) Integer.parseInt(item, 16);
        }
        return b2;
    }

    public static void main(String[] args) {
        String tpycjeymomdhocan = null;
        try {
            tpycjeymomdhocan = desEncrypt("TPYCJEYMOMDHOCAN");
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
        System.out.println(tpycjeymomdhocan);
    }
}

```

```javascript
import CryptoJS from 'crypto-js'

//com.ruoyi.common.core.utils.EncryptUtils
export function DES_Encrypt_BySys(word) {
  var key = CryptoJS.enc.Utf8.parse("Qassw0rd");
  var ivHex = CryptoJS.enc.Utf8.parse("Passw0rd");

  var encrypted = CryptoJS.DES.encrypt(word, key, {
    iv: ivHex,
    mode: CryptoJS.mode.CBC,
    padding: CryptoJS.pad.Pkcs7 //这里的pkcs7不会影响pkcs5的计算结果，从某种意义上来说两者太大区别
  });
  return encrypted.ciphertext.toString();
}
```

Why:

 ```java
     /**
      * 对称解密
      */
     public static String desDecrypt(String source) throws Exception {
         byte[] src = hex2byte(source.getBytes(StandardCharsets.UTF_8));
         DESKeySpec desKeySpec = getDesKeySpec(source);
         // desKeySpec: [80, 97, 115, 115, 119, 48, 114, 100]   Passw0rd
         SecretKeyFactory keyFactory = SecretKeyFactory.getInstance("DES");
         SecretKey secretKey = keyFactory.generateSecret(desKeySpec);
         // secretKey: [81, 97, 115, 115, 118, 49, 115, 100]  Qassv1sd
         // IV : [80, 97, 115, 115, 119, 48, 114, 100]  Passw0rd
         cipher.init(Cipher.DECRYPT_MODE, secretKey, IV);
         byte[] retByte = cipher.doFinal(src);
         return new String(retByte);
     }
 // 所以对应的加密key为Qassv1sd
 
 // Qassv1sd：4b3c638aa9ea4f3843575ccc90078d05
 // Qassw0rd：4b3c638aa9ea4f3843575ccc90078d05
 // 加密结果一样
 ```

原因是：DES 秘钥采用，每字节取前七位为有效位，第八位为奇偶校验位，所以只要每字节去前七比特位一样，秘钥就是等效的；       

就是说上面采用等效（或一样）的秘钥

[来源](https://www.jianshu.com/p/860d1f59b7f9)

