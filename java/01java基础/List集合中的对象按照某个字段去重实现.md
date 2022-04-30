## 不使用Java8
```java
package com.liying.banana.user;

import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;
import java.util.Set;
import java.util.TreeSet;

import com.liying.banana.order.vo.OrderVO;

/**
 * List集合中的对象按照某个字段去重实现
 * 
 * @author jqlin
 *
 */
public class DuplicateOrderTest {
    public static void main(String[] args) {
        List<OrderVO> orderList = new ArrayList<OrderVO>();
        OrderVO orderVO = new OrderVO();
        orderVO.setOrderNo("11");
        orderVO.setUserId("aa");
        orderList.add(orderVO);

        orderVO = new OrderVO();
        orderVO.setOrderNo("22");
        orderVO.setUserId("bb");
        orderList.add(orderVO);

        orderVO = new OrderVO();
        orderVO.setOrderNo("33");
        orderVO.setUserId("aa");
        orderList.add(orderVO);
        
        List<OrderVO> dataList = removeDuplicateOrder(orderList);
        for(OrderVO data : dataList){
            System.out.println(data.getOrderNo() + ":" + data.getUserId());
        }
    }

    /**
     * 去重
     * 
     * @param orderList
     * @return
     * @author jqlin
     */
    private static List<OrderVO> removeDuplicateOrder(List<OrderVO> orderList) {
        Set<OrderVO> set = new TreeSet<OrderVO>(new Comparator<OrderVO>() {
            @Override
            public int compare(OrderVO a, OrderVO b) {
                // 字符串则按照asicc码升序排列
                return a.getUserId().compareTo(b.getUserId());
            }
        });
        
        set.addAll(orderList);
        return new ArrayList<OrderVO>(set);
    }
}
```
## 使用Java8 stream
```java
     List<Person> unique = persons.stream().collect(
                Collectors.collectingAndThen(
                        Collectors.toCollection(() -> new TreeSet<>(Comparator.comparing(Person::getName))), ArrayList::new)
        );
        unique.forEach(p -> System.out.println(p));

```


```java
        List<String> names = new ArrayList<>();//用来临时存储person的id

        List<Person> personList = persons.stream().filter(// 过滤去重
                v -> {
                    boolean flag = !names.contains(v.getName());
                    names.add(v.getName());
                    return flag;
                }
        ).collect(Collectors.toList());

```


