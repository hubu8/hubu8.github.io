# 骰子等概率1-7与数组


<!--more-->

## 问题一：一个骰子怎么产生1/7的概率

问题等价于：一个骰子6个面均匀，现在有7个互斥事件，怎么扔能使7个事件均匀分布

答：一个骰子掷两次，共36种等概率的结果。

令其中的35种对应1-7，剩下的一种对应『再掷两次』。

比如：第一个骰子不为6时，第二个骰子掷出几就输出几（1-6，每种有五个可能）。第一个骰子为6，且第二个骰子不为6，输出7（有五个可能）。两个骰子都是6，重掷。于是1-7的概率都是5/36。而且永远掷6的概率是0，一定会在有限次内得到输出。

设第一次的投掷点数为x，第二次的投掷点数为y，则有f(x,y) = (x - 1) * 6 + y。

f(x,y)的取值范围是1~36，按照上述规则，可以达到题目要求


## 问题二：找出数组中出现次数超过一半的数

现在有一个数组，已知一个数出现的次数超过了一半，请用O(n)的复杂度的算法找出这个数。

**提示：出现次数超过一半的数一定只有一个**

**方法一：**创建一个unordered_map，key为数组中的数，value为此数出现的次数。遍历一遍数组，用unordered_map统计每个数出现的次数，并用两个值存储目前出现次数最多的数和对应出现的次数。

这个解法是最简单的，我们来分析一下时间复杂度和空间复杂度,由于这个map底层实现使用hash算法来实现的，故时间复杂度可以为O（n），那么空间复杂度呢？很明显也是O(n)的。

```java
public class HalfTimes {
    public static void main(String args[]){
        int array[]={0,1,2,3,4,1,2,1,1,1,1,0,1,0,1,2,1,1,1,1};
        int i = solution1(array);
        System.out.printf("数组中出现次数超过一半的数为："+i);
    }
    public static int solution1(int arrray[]){
        Map<Integer,Integer> unordered_map=new HashMap<>();
        for(int item:arrray){
            if(unordered_map.containsKey(item)){
                Integer value=unordered_map.get(item)+1;
                unordered_map.replace(item,value);
            }else{
                unordered_map.put(item,1);
            }
        }
        Integer num=0;
        Integer times=arrray.length/2;
        for(Integer key:unordered_map.keySet()){
            if(unordered_map.get(key)>times){
                num=key;
                times=unordered_map.get(key);
            }
        }
        return num;
    }
}
```

那能不能把空间复杂度降下来呢？
**方法二：**使用两个变量A和B，其中A存储某个数组中的数，B用来计数。开始时将B初始化为0。
遍历数组，如果B=0，则令A等于当前数，令B等于1；如果当前数与A相同，则B=B+1；如果当前数与A不同，则令B=B-1。遍历结束时，A中的数就是要找的数。为什么呢？因为题目中说了，那个元素一定是占了数组元素个数的一半以上，所以说，当我们每次删除两个不同的数，剩下存储A的值就是我们要求的结果值。
这个算法的时间复杂度是O(n)，空间复杂度为O(1)。

```java
public static int solution2(int array[]){
        int a=0;
        int b=0;
        for(int item:array){
            if(b==0){
                a=item;
                b=1;
            }else if(a==item){
                b++;
            }else{
                b--;
            }
        }
        return a;
    }
```

```
{0,1,2,3,4,1,2,1,1,1,1,0,1,0,1,2,1,1,1,1};
一共20个数，最多的数为1，共12个，其他数8个
必存在才正确
```

| item | a    | b    |
| ---- | ---- | ---- |
| 0    | 0    | 1    |
| 1    | 0    | 0    |
| 2    | 2    | 1    |
| 3    | 2    | 0    |
| 4    | 4    | 1    |
| 1    | 4    | 0    |
| 2    | 2    | 1    |
| 1    | 2    | 0    |
| 1    | 1    | 1    |
| 1    | 1    | 2    |
| 1    | 1    | 3    |
| 0    | 1    | 2    |
| 1    | 1    | 3    |
| 0    | 1    | 2    |
| 1    | 1    | 3    |
| 2    | 1    | 2    |
| 1    | 1    | 3    |
| 1    | 1    | 4    |
| 1    | 1    | 5    |
| 1    | 1    | 6    |


