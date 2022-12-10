---
title: 关于java实现ocr文字识别功能
date: 2022-09-20 13:25:26
tags: Java;ocr
cover: https://tvax4.sinaimg.cn/large/008waiCQgy1h8iv7f1ggij32yo1o0u10.jpg
categories: java
description: 在一个暑假期间老师布置的任务，也算是粗略完成了。
---



# 关于Java实现ocr文字识别功能



### 方法一：调用百度云接口实现图片文字识别功能

目录：

一、首先进入[百度云](http://ai.baidu.com/)，进行注册账户

二、在idea中新建一个maven项目，并且配置pom文件

三、在百度云中申请ocr识别的API Key和Secret Key

四、使用java代码根据API Key和Secret Key进行获取token

五、使用token和图片进行测试接口



步骤：①首先进入[百度云](http://ai.baidu.com/)，进行注册账户



第一步，注册账户好以后，进入控制台，点击左上角列表信息，然后点击产品服务，然后点击文字识别

![](./img/2.png)



②在idea中新建一个maven项目，并且配置pom文件

第一步，创建maven项目

第二部=步，配置pom.xml文件

使用maven依赖：

添加以下依赖即可，其中版本号可在[maven官网](http://search.maven.org/#search|ga|1|aip)查询

```text
<dependency>
    <groupId>com.baidu.aip</groupId>
    <artifactId>java-sdk</artifactId>
//  <version>${version}</version>
    <version>4.16.8</version>	#在这里我们用的是4.16.8的版本
</dependency>
```



③在百度云中申请ocr识别的API Key和Secret Key

首先进行领取免费资源，然后进行创建应用，通过代码进行通用服务

![](./img/3.png)

如何创建应用？

![](./img/4.png)



然后点击创建应用即可，进入里面以后给应用起一个名字，然后直接创建，创建完成以后会给你一个信息。



![](./img/6.png)

在这里我们就获取到了API Key和Secret Key。



④使用java代码根据API Key和Secret Key进行获取token

第一步，创建工具Base64Util类，FileUtil类，HttpUtil类

Base64Util类，代码如下：

```java
package com.baidu.ai.aip.utils;

/**
 * Base64 工具类
 */
public class Base64Util {
    private static final char last2byte = (char) Integer.parseInt("00000011", 2);
    private static final char last4byte = (char) Integer.parseInt("00001111", 2);
    private static final char last6byte = (char) Integer.parseInt("00111111", 2);
    private static final char lead6byte = (char) Integer.parseInt("11111100", 2);
    private static final char lead4byte = (char) Integer.parseInt("11110000", 2);
    private static final char lead2byte = (char) Integer.parseInt("11000000", 2);
    private static final char[] encodeTable = new char[]{'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J', 'K', 'L', 'M', 'N', 'O', 'P', 'Q', 'R', 'S', 'T', 'U', 'V', 'W', 'X', 'Y', 'Z', 'a', 'b', 'c', 'd', 'e', 'f', 'g', 'h', 'i', 'j', 'k', 'l', 'm', 'n', 'o', 'p', 'q', 'r', 's', 't', 'u', 'v', 'w', 'x', 'y', 'z', '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '/'};

    public Base64Util() {
    }

    public static String encode(byte[] from) {
        StringBuilder to = new StringBuilder((int) ((double) from.length * 1.34D) + 3);
        int num = 0;
        char currentByte = 0;

        int i;
        for (i = 0; i < from.length; ++i) {
            for (num %= 8; num < 8; num += 6) {
                switch (num) {
                    case 0:
                        currentByte = (char) (from[i] & lead6byte);
                        currentByte = (char) (currentByte >>> 2);
                    case 1:
                    case 3:
                    case 5:
                    default:
                        break;
                    case 2:
                        currentByte = (char) (from[i] & last6byte);
                        break;
                    case 4:
                        currentByte = (char) (from[i] & last4byte);
                        currentByte = (char) (currentByte << 2);
                        if (i + 1 < from.length) {
                            currentByte = (char) (currentByte | (from[i + 1] & lead2byte) >>> 6);
                        }
                        break;
                    case 6:
                        currentByte = (char) (from[i] & last2byte);
                        currentByte = (char) (currentByte << 4);
                        if (i + 1 < from.length) {
                            currentByte = (char) (currentByte | (from[i + 1] & lead4byte) >>> 4);
                        }
                }

                to.append(encodeTable[currentByte]);
            }
        }

        if (to.length() % 4 != 0) {
            for (i = 4 - to.length() % 4; i > 0; --i) {
                to.append("=");
            }
        }

        return to.toString();
    }
}

```

FileUtil类，代码如下：

```java
package com.baidu.ai.aip.utils;

import java.io.*;

/**
 * 文件读取工具类
 */
public class FileUtil {

    /**
     * 读取文件内容，作为字符串返回
     */
    public static String readFileAsString(String filePath) throws IOException {
        File file = new File(filePath);
        if (!file.exists()) {
            throw new FileNotFoundException(filePath);
        } 

        if (file.length() > 1024 * 1024 * 1024) {
            throw new IOException("File is too large");
        } 

        StringBuilder sb = new StringBuilder((int) (file.length()));
        // 创建字节输入流  
        FileInputStream fis = new FileInputStream(filePath);  
        // 创建一个长度为10240的Buffer
        byte[] bbuf = new byte[10240];  
        // 用于保存实际读取的字节数  
        int hasRead = 0;  
        while ( (hasRead = fis.read(bbuf)) > 0 ) {  
            sb.append(new String(bbuf, 0, hasRead));  
        }  
        fis.close();  
        return sb.toString();
    }

    /**
     * 根据文件路径读取byte[] 数组
     */
    public static byte[] readFileByBytes(String filePath) throws IOException {
        File file = new File(filePath);
        if (!file.exists()) {
            throw new FileNotFoundException(filePath);
        } else {
            ByteArrayOutputStream bos = new ByteArrayOutputStream((int) file.length());
            BufferedInputStream in = null;

            try {
                in = new BufferedInputStream(new FileInputStream(file));
                short bufSize = 1024;
                byte[] buffer = new byte[bufSize];
                int len1;
                while (-1 != (len1 = in.read(buffer, 0, bufSize))) {
                    bos.write(buffer, 0, len1);
                }

                byte[] var7 = bos.toByteArray();
                return var7;
            } finally {
                try {
                    if (in != null) {
                        in.close();
                    }
                } catch (IOException var14) {
                    var14.printStackTrace();
                }

                bos.close();
            }
        }
    }
}

```

HttpUtil类，代码如下：

```java
package com.baidu.ai.aip.utils;

import java.io.BufferedReader;
import java.io.DataOutputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.List;
import java.util.Map;

/**
 * http 工具类
 */
public class HttpUtil {

    public static String post(String requestUrl, String accessToken, String params)
            throws Exception {
        String contentType = "application/x-www-form-urlencoded";
        return HttpUtil.post(requestUrl, accessToken, contentType, params);
    }

    public static String post(String requestUrl, String accessToken, String contentType, String params)
            throws Exception {
        String encoding = "UTF-8";
        if (requestUrl.contains("nlp")) {
            encoding = "GBK";
        }
        return HttpUtil.post(requestUrl, accessToken, contentType, params, encoding);
    }

    public static String post(String requestUrl, String accessToken, String contentType, String params, String encoding)
            throws Exception {
        String url = requestUrl + "?access_token=" + accessToken;
        return HttpUtil.postGeneralUrl(url, contentType, params, encoding);
    }

    public static String postGeneralUrl(String generalUrl, String contentType, String params, String encoding)
            throws Exception {
        URL url = new URL(generalUrl);
        // 打开和URL之间的连接
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        // 设置通用的请求属性
        connection.setRequestProperty("Content-Type", contentType);
        connection.setRequestProperty("Connection", "Keep-Alive");
        connection.setUseCaches(false);
        connection.setDoOutput(true);
        connection.setDoInput(true);

        // 得到请求的输出流对象
        DataOutputStream out = new DataOutputStream(connection.getOutputStream());
        out.write(params.getBytes(encoding));
        out.flush();
        out.close();

        // 建立实际的连接
        connection.connect();
        // 获取所有响应头字段
        Map<String, List<String>> headers = connection.getHeaderFields();
        // 遍历所有的响应头字段
        for (String key : headers.keySet()) {
            System.err.println(key + "--->" + headers.get(key));
        }
        // 定义 BufferedReader输入流来读取URL的响应
        BufferedReader in = null;
        in = new BufferedReader(
                new InputStreamReader(connection.getInputStream(), encoding));
        String result = "";
        String getLine;
        while ((getLine = in.readLine()) != null) {
            result += getLine;
        }
        in.close();
        System.err.println("result:" + result);
        return result;
    }
}

```



第二步，创建AuthService类通过API Key和Secret Key进行获取token

在这里只需要填写API Key和Secret Key即可。

代码如下：

```java
package com.baidu.ai.aip.utils;
/**
 * @Author YiXin
 * @CreateTime 2022/7/1  10:29
 */


import org.json.JSONObject;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.List;
import java.util.Map;

/**
 * 获取token类
 */
public class AuthService {
    public static void main(String[] args){
        String token=getAuth();
        System.out.println(token);
    }
    /**
     * 获取权限token
     * @return 返回示例：
     * {
     * "access_token": "24.460da4889caad24cccdb1fea17221975.2592000.1491995545.282335-1234567",
     * "expires_in": 2592000
     * }
     */
    public static String getAuth() {
        // 官网获取的 API Key 更新为你注册的
        String clientId = " ";
        // 官网获取的 Secret Key 更新为你注册的
        String clientSecret = " ";
        return getAuth(clientId, clientSecret);
    }

    /**
     * 获取API访问token
     * 该token有一定的有效期，需要自行管理，当失效时需重新获取.
     * @param ak - 百度云官网获取的 API Key
     * @param sk - 百度云官网获取的 Securet Key
     * @return assess_token 示例：
     * "24.460da4889caad24cccdb1fea17221975.2592000.1491995545.282335-1234567"
     */
    public static String getAuth(String ak, String sk) {
        // 获取token地址
        String authHost = "https://aip.baidubce.com/oauth/2.0/token?";
        String getAccessTokenUrl = authHost
                // 1. grant_type为固定参数
                + "grant_type=client_credentials"
                // 2. 官网获取的 API Key
                + "&client_id=" + ak
                // 3. 官网获取的 Secret Key
                + "&client_secret=" + sk;
        try {
            URL realUrl = new URL(getAccessTokenUrl);
            // 打开和URL之间的连接
            HttpURLConnection connection = (HttpURLConnection) realUrl.openConnection();
            connection.setRequestMethod("GET");
            connection.connect();
            // 获取所有响应头字段
            Map<String, List<String>> map = connection.getHeaderFields();
            // 遍历所有的响应头字段
            for (String key : map.keySet()) {
                System.err.println(key + "--->" + map.get(key));
            }
            // 定义 BufferedReader输入流来读取URL的响应
            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream()));
            String result = "";
            String line;
            while ((line = in.readLine()) != null) {
                result += line;
            }
            /**
             * 返回结果示例
             */
            System.err.println("result:" + result);
            JSONObject jsonObject = new JSONObject(result);
            String access_token = jsonObject.getString("access_token");
            return access_token;
        } catch (Exception e) {
            System.err.printf("获取token失败！");
            e.printStackTrace(System.err);
        }
        return null;
    }

}
```

⑤使用token和图片进行测试接口

获取好token以后，进行代码测试图片用例。

编写AccurateBasic测试类。

在这里只需要填写图片路径，和token即可。

代码如下：

```java
package com.baidu.ai.aip;

import com.baidu.ai.aip.utils.Base64Util;
import com.baidu.ai.aip.utils.FileUtil;
import com.baidu.ai.aip.utils.HttpUtil;

import java.net.URLEncoder;

/**
 * 通用文字识别
 */
public class AccurateBasic {

    public static String generalBasic() {
        // 请求url
        String url = "https://aip.baidubce.com/rest/2.0/ocr/v1/general_basic";
        try {
            // 本地文件路径
            String filePath = "本地文件路径";
            byte[] imgData = FileUtil.readFileByBytes(filePath);
            String imgStr = Base64Util.encode(imgData);
            String imgParam = URLEncoder.encode(imgStr, "UTF-8");

            String param = "image=" + imgParam;

            // 注意这里仅为了简化编码每一次请求都去获取access_token，线上环境access_token有过期时间， 客户端可自行缓存，过期后重新获取。
            String accessToken = "填写AuthService类运行以后的token";

            String result = HttpUtil.post(url, accessToken, param);
            System.out.println(result);
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static void main(String[] args) {
        AccurateBasic.generalBasic();
    }
}
```

到此，测试用例已全部完成。



**返回参数说明**

| 字段               | 是否必选 | 类型    | 说明                                                         |
| ------------------ | -------- | ------- | ------------------------------------------------------------ |
| direction          | 否       | int32   | 图像方向，当 detect_direction=true 时返回该字段。 - - 1：未定义， - 0：正向， - 1：逆时针90度， - 2：逆时针180度， - 3：逆时针270度 |
| log_id             | 是       | uint64  | 唯一的log id，用于问题定位                                   |
| words_result_num   | 是       | uint32  | 识别结果数，表示words_result的元素个数                       |
| words_result       | 是       | array[] | 识别结果数组                                                 |
| + words            | 否       | string  | 识别结果字符串                                               |
| + probability      | 否       | object  | 识别结果中每一行的置信度值，包含average：行置信度平均值，variance：行置信度方差，min：行置信度最小值，当 probability=true 时返回该字段 |
| paragraphs_result  | 否       | array[] | 段落检测结果，当 paragraph=true 时返回该字段                 |
| + words_result_idx | 否       | array[] | 一个段落包含的行序号，当 paragraph=true 时返回该字段         |
| language           | 否       | int32   | 当 detect_language=true 时返回该字段                         |
| pdf_file_size      | 否       | string  | 传入PDF文件的总页数，当 pdf_file 参数有效时返回该字段        |

**返回示例**



```json
{
    "log_id": 2471272194, 
    "words_result_num": 2,
    "words_result": 
	    [
		    {"words": " TSINGTAO"}, 
		    {"words": "青岛啤酒"}
	    ]
}
```





##### 测试用例

如：识别下图

![](./img/文字.png)



##### 测试结果：

![](./img/测试结果.png)



















### 方法二，使用tesseract库来实现ocr文字识别



1.tesseract简介：

***\*Tesseract\****，一款由HP实验室开发由Google维护的开源OCR（Optical Character Recognition , 光学字符识别）库，目前由谷歌赞助，它可以通过训练识别出任何字体，我们可以不断的训练的库，使图像转换文本的能力不断增强；



2.tesseract的安装：

下载地址：https://digi.bib.uni-mannheim.de/tesseract/，带有dev的表示开发版本，相对稳定，在这里我们选择的是

![](./img/安装.png)

同时需要把**chi_sim** 中文语言包加入到tessdate文件下

3.坏境变量的配置：

​	①为了在全局使用方便，比如安装路径为:D:\ocr\Tesseract-OCR，将该路径添加到环境变量的path中。
​	②路径：高级系统设置——>环境变量——>系统变量中path路径——>将D:\ocr\Tesseract-OCR添加进去。

​	③配置完成后在cmd中输入tesseract -v，如果出现如下图所示，说明环境变量配置成功。

​	④同时在环境变量中直接加入TESSDATA_PREFIX，路径填写例如：D:\ocr\Tesseract-OCR\tessdate

4.java代码编写

①首先编写OCRHelper工具类

```java
/**
 * @Author YiXin
 * @CreateTime 2022/7/7  13:41
 */



import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class OCRHelper {

    private final String LANG_OPTION = "-l";
    private final String EOL = System.getProperty("line.separator");

    /**
     *  Tesseract-OCR的安装路径
     */
    private String tessPath = "D:/ocr/Tesseract-OCR";

    /**
     * @param imageFile   传入的图像文件
     * @return 识别后的字符串
     */
    public String recognizeText(File imageFile) throws Exception {
        /**
         * 设置输出文件的保存的文件目录
         */
        File outputFile = new File(imageFile.getParentFile(), "output");

        StringBuffer strB = new StringBuffer();
        List<String> cmd = new ArrayList<String>();

        cmd.add(tessPath + "\\tesseract");

        cmd.add("");
        cmd.add(outputFile.getName());
        cmd.add(LANG_OPTION);
        //在这里eng代表英文，chi_sim代表中文，识别哪个用哪个即可
        cmd.add("chi_sim");
        //cmd.add("eng");

        ProcessBuilder pb = new ProcessBuilder();
        pb.directory(imageFile.getParentFile());
        cmd.set(1, imageFile.getName());
        pb.command(cmd);
        pb.redirectErrorStream(true);
        Process process = pb.start();
        int w = process.waitFor();
        if (w == 0)// 0代表正常退出
        {
            BufferedReader in = new BufferedReader(new InputStreamReader(
                    new FileInputStream(outputFile.getAbsolutePath() + ".txt"),
                    "UTF-8"));
            String str;

            while ((str = in.readLine()) != null) {
                strB.append(str).append(EOL);
            }
            in.close();
        } else {
            String msg;
            switch (w) {
                case 1:
                    msg = "错误  Errors accessing files. There may be spaces in your image's filename.";
                    break;
                case 29:
                    msg = "Cannot recognize the image or its selected region.";
                    break;
                case 31:
                    msg = "Unsupported image format.";
                    break;
                default:
                    msg = "Errors occurred.";
            }
            throw new RuntimeException(msg);
        }
        new File(outputFile.getAbsolutePath() + ".txt").delete();
        return strB.toString().replaceAll("\\s*", "");
    }
}

```



②然后开始编写测试类

```java
![2](D:\Users\作业\知识点\图片\2.png)/**
 * @Author YiXin
 * @CreateTime 2022/7/7  13:41
 */



import java.io.File;
import java.io.IOException;
import java.util.Scanner;


/**
 * 测试主类
 */

public class Test {
    public static void main(String[] args) {
//        System.out.println("输入你识别的图片");
//        Scanner scanner = new Scanner(System.in);
//        String next = scanner.next();
        try {
            //图片文件：此图片是需要被识别的图片，此处括号内填写图片路径
            File imageFile = new File("D:/2.jpg");
            String recognizeText = new OCRHelper().recognizeText(imageFile);
            System.out.print(recognizeText + "\t");

        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

}



```





##### 测试用例：

![](./img/文字.png)



##### 测试结果：

![](./img/测试结果2.png)





对比：第一种采用百度云接口，识别率相对比较高，但是使用次数有限制

第二种采用外部库，识别率不是比较高，需要加多训练进行订正。

