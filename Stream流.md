流的核心操作是**过滤**。
结合 Lambda 表达式简化集合、数组的操作。

## **使用步骤**：
 1. 先得到一条 Steam 流。
 2. 利用 Stream 流中的 API 进行过滤、转换、打印等操作。分为**中间方法**和**终结方法**。

## **获取 stream 流**
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260121040202271.png)

> [!注意] 
> - 双列集合中无法直接使用 stream 流，需要进行转换操作。
> - 数组需使用 Arrays 工具类中 stream() 方法。
> - 零散数据需要使用 Stream 接口中的 of（）方法。
```java
public class streamDemo2 {  
    public static void main(String[] args) {  
        //单列集合获取Stream流  
        ArrayList<String> list1 = new ArrayList<>();  
        Collections.addAll(list1,"abc","abc","abc","abc","fy");  
        //使用链式写法  
        list1.stream().forEach(string -> System.out.println(string));   ///abc ...  
  
        //双列集合获取Stream流，以HashMap为例  
        HashMap<String,Integer> hm1 = new HashMap<>();  
        hm1.put("aaa",1);  
        hm1.put("bbb",2);  
        hm1.put("ccc",3);  
        hm1.put("ddd",4);  
        hm1.put("eee",5);  
        //双列集合中没有Stream方法，要先转换为单列集合  
        //方法一：使用keySet()方法获取键的set集合  
        hm1.keySet().stream().forEach(s -> System.out.println(s));  //aaa ccc ...  
        //方法二：使用entrySet()方法获取键的set集合  
        hm1.entrySet().stream().forEach(s -> System.out.println(s)); //aaa=1 ccc=3 ...  
  
        //数组中  
        //使用工具类Arrays中的stream()方法  
        int numArr[] = {5,4,3,2,1};  
        Arrays.stream(numArr).forEach(i -> System.out.println(i));  //5 4 3 ...  
  
        //零散数据 （同一类型）中  
        Stream.of("a","b","c","d","e").forEach(s -> System.out.println(s)); //a b c...  
  
    }  
}
```

## 中间方法
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260121043027022.png)
