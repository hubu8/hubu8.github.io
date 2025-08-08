# Date Formatting中YYYY与yyyy的区别




### 写在最前面

测试代码展示YYYY与yyyy的区别：

```java
public class Test {
    public static void main(String[] args) {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String dateStr = "2023-12-31 23:59:59";
        try {
            Date date = sdf.parse(dateStr);

            String yearLowCase = sdf.format(date);
            System.out.println("LowYearCase: " + yearLowCase);

            SimpleDateFormat sdf2 = new SimpleDateFormat("YYYY-MM-dd HH:mm:ss");
            String yearUpperCase = sdf2.format(date);
            System.out.println("UpperYearCase: " + yearUpperCase);
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
    }
}
```

输出结果：

```
LowYearCase: 2023-12-31 23:59:59
UpperYearCase: 2024-12-31 23:59:59
```

很明显，使用YYYY时，2023年变成了2024年，在正常情况下可能没问题，但是在跨年的时候大概率就会有问题了。

### 原因

**与小写的yyyy不同，大写的YYYY表示一个基于周的年份。它是根据周计算的年份，而不是基于日历的年份。通常情况下，两者的结果是相同的，但在跨年的第一周或最后一周可能会有差异。**



**区别：**

- `yyyy`使用的是`日历年`，我们应该使用`日历年`这种方式
- `YYYY`使用的是`当前周所在的年份（Week of Year）`
- 大部分情况下这两种方式的值是一样的



避免这个问题的方法也十分简单，要有公用的格式类，所有使用日期格式的地方都引用这个类，这个类中就定义好yyyy-MM-dd想给的格式即可，这样就不会出现有人手误给大家埋雷了。

