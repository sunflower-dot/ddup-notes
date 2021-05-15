

## JAVA字符串转日期或日期转字符串
> 原文地址：[https://www.cnblogs.com/zhengwanmeixiansen/p/7391411.html](https://www.cnblogs.com/zhengwanmeixiansen/p/7391411.html)

字符串转成时间（Date）对象和时间（Date）对象转换成时间字符串是开发中经常会遇到的，在API中， `java.text.SimpleDateFormat` 下有一个叫 `SimpleDateFormat` 的对象，就是专门用来实现时间这种和字符串之间的互相转换。
用法：
（1）创建SimpleDateFormat对象的实例
　　SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
　　"yyyy-MM-dd HH:mm:ss"这表示确定转换的格式,格式有多种多样，并不局限于这一种，
　　如："yyyy/MM/dd HH:mm:ss","yyyy年MM月dd日 HH时mm分ss秒"等等，这都是可以自定义的，具体自定义格式模式字母看下图：
　![](https://cdn.nlark.com/yuque/0/2021/png/1039463/1617429023996-dcb75d25-a12c-4cd9-a74c-6189e2f39bf9.png#align=left&display=inline&height=425&margin=%5Bobject%20Object%5D&originHeight=425&originWidth=670&size=0&status=done&style=none&width=670)
 
（2）利用sdf实例来进行转换　　
　　①，日期对象转字符串：
　　sdf.format(new Date());    这样就可以获得当前时间"2017-08-18 19:03:25"了， 这里返回的时间字符串的格式是由上面定义格式决定了
　　②，时间字符串转日期对象
　　注意：这里的字符串格式也要和上面sdf对象实例定义的格式一样才可以，
　　sdf.parse("2017-08-18 19:03:25");这样就可以将时间字符串转换成日期对象了
```java
package com.admin.test;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @author 作者: admin
 ** @date 创建时间：2017年8月18日 下午4:12:17
 ** @version 1.0
 */
public class DateTest {
    public static void main(String[] args) {
        
        //创建SimpleDateFormat对象实例并定义好转换格式
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
        System.out.println("把当前时间转换成字符串：" + sdf.format(new Date()));
        
        Date date = null;
        try {
            // 注意格式需要与上面一致，不然会出现异常
            date = sdf.parse("2005-12-15 15:30:23");
        } catch (ParseException e) {
            e.printStackTrace();
        }
        System.out.println("字符串转换成时间:" + date);
    }
}
```
