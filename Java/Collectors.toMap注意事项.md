Collectors.toMap 是 Java Stream API 中的一个收集器，用于将 Stream 元素收集到一个 Map 中。它允许你将流中的数据转换为键值对的形式，这样你就可以根据需要生成一个 Map。

# 基本形式
```
public static <T, K, U>
Collector<T, ?, Map<K,U>> toMap(Function<? super T, ? extends K> keyMapper,
                                Function<? super T, ? extends U> valueMapper) {
    return toMap(keyMapper, valueMapper, throwingMerger(), HashMap::new);
}

public static <T, K, U, M extends Map<K, U>>
Collector<T, ?, M> toMap(Function<? super T, ? extends K> keyMapper,
                            Function<? super T, ? extends U> valueMapper,
                            BinaryOperator<U> mergeFunction,
                            Supplier<M> mapSupplier) {
    BiConsumer<M, T> accumulator
            = (map, element) -> map.merge(keyMapper.apply(element),
                                            valueMapper.apply(element), mergeFunction);
    return new CollectorImpl<>(mapSupplier, accumulator, mapMerger(mergeFunction), CH_ID);
}
```
第一个参数：keyMapper，用于提取 Map 的键。  
第二个参数：valueMapper，用于提取 Map 的值。  
第三个参数：mergeFunction，用于处理键重复时的合并策略。此参数是可选的，可以处理相同键的值合并的逻辑。  
第四个参数：mapSupplier，用于指定返回的 Map 类型的构造器。此参数是可选的，默认为HashMap。

# 示例
```java
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

/**
 * Stream流Collectors.toMap方法测试
 *
 * @since 2025-03-28
 */
public class StreamCollectorsToMapTest {
    public static void main(String[] args) {
        Student student1 = new Student("张三", 15);
        Student student2 = new Student("李四", 20);
        List<Student> students = new ArrayList<>();
        students.add(student1);
        students.add(student2);
        // 根据学生集合获取名字与年龄的映射
        Map<String, Integer> map = students.stream().collect(Collectors.toMap(Student::getName, Student::getAge));
        System.out.println(map); // {李四=20, 张三=15}
    }
}
```
使用Collectors.toMap时，可能出现两种异常：1、java.lang.NullPointerException 2、java.lang.IllegalStateException

# java.lang.NullPointerException

当toMap方法获取到的value为null时，会发生空指针异常：
![image.png](/image/1cd933e0-f9a6-49c8-95b0-aad1291a8b2d.png)
阅读Hashmap源码1226行发现，在merge方法中不允许value为null。

![image.png](/image/b2ae27ab-e837-4934-ae06-8f7e13a07017.png)

# java.lang.IllegalStateException

当出现重复的key时，会发生非法状态异常：
![image.png](/image/e24c01b0-b5ab-4f05-86f9-05905b142e9f.png)

因为toMap对于重复key的处理策略默认为throwingMerger方法。

![image.png](/image/a3f0d36f-33c7-4d74-89d1-5a26b8c6ffb5.png)
