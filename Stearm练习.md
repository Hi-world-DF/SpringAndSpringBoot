### Stream练习

```java
package com.xgd;

import java.util.*;
import java.util.stream.Collectors;
import java.util.stream.Stream;

public class Java8StreamPractice {

    /**
     * 题目要求：对于输入的字符串s，忽略大小写，
     * 返回按照英文字母出现频率从高到低排序的小写字符串，对于出现次数相同的字母，按照字典序排序。
     * 如果输入的字符串不包含英文字母，返回空字符串。注意数字，标点，空格都不是英文字母。
     */

    String mostFrequentLetters(String s) {

        
        //1.先做切分，每个字符切分出来
        //2.然后映射到map，统计每个字符的出现频率
        Map<String, Long> map = Stream.of(s)
                .map(o -> o.split(""))
                .flatMap(Arrays::stream)
                .map(String::toLowerCase)
                .collect(Collectors.groupingBy(o -> o, Collectors.counting()));

        //按照value排序
        map = map.entrySet().stream()
                .sorted((e1, e2) -> {
                    if (e1.getValue().equals(e2.getValue())) {
                        //如果值相等，那么就按键值的字典序排
                        return e1.getKey().compareTo(e2.getKey());
                    } else {
                        //否则按照值逆序，从大到小排
                        return e2.getValue().compareTo(e1.getValue());
                    }
                }).collect(Collectors.toMap(
                        Map.Entry::getKey,
                        Map.Entry::getValue,
                        (key, value) -> key,
                        LinkedHashMap::new));

        //遍历map中的key值，将字母按照要求输出，其他字符过滤掉
        StringBuilder sb = new StringBuilder();
        for (String s1 : map.keySet()) {
            char c = s1.charAt(0);
            if (c >= 97 && c <= 122) {
                sb.append(c);
            }
        }
        return sb.toString();
    }

}
```
测试
```java
package com.xgd;

import org.junit.jupiter.api.Assertions;
import org.junit.Test;

public class Java8StreamTest {

    @Test
    public void testAllString() {

        Java8StringPractice js = new Java8StringPractice();

        String s1 = "1234322523";
        String s2 = "12,abADecer";
        String s3 = "     ";
        String s4 = "ABC";
        String s5 = "yzxyaadh";
        String s6 = "aaaaaaaaaaaaaaa";

        String res1 = js.mostFrequentLetters(s1);
        String res2 = js.mostFrequentLetters(s2);
        String res3 = js.mostFrequentLetters(s3);
        String res4 = js.mostFrequentLetters(s4);
        String res5 = js.mostFrequentLetters(s5);
        String res6 = js.mostFrequentLetters(s6);

        Assertions.assertEquals("",res1);
        Assertions.assertEquals("aebcdr",res2);
        Assertions.assertEquals("",res3);
        Assertions.assertEquals("abc",res4);
        Assertions.assertEquals("aydhxz",res5);
        Assertions.assertEquals("a",res6);

    }

}
```

### 优化：修改后
Java8StreamPractice：

```java
package com.facemagic.backend;

import java.util.Map;
import java.util.function.Function;
import java.util.stream.Collectors;

/**
 * 关于Stream的练习
 *
 * @author wangdf
 * @version 1.0, 2020/12/28
 * @since 1.0.0
 */
public class Java8StreamPractice {

    /**
     * 按照字母出现频率排序
     *
     * @param s 输入字符串
     * @return 返回结果字符串
     */
    public String mostFrequentLetters(String s) {

        return s.chars()
            .map(Character::toLowerCase)
            .filter(c -> c <= 'z' && c >= 'a')
            .mapToObj(a -> (char) a)
            .collect(Collectors.groupingBy(Function.identity(), Collectors.counting()))
            .entrySet().stream()
            .sorted(Map.Entry.<Character, Long>comparingByValue().reversed().thenComparing(Map.Entry.comparingByKey()))
            .map(o -> o.getKey().toString())
            .collect(Collectors.joining());

    }

}
```
测试：
```java
package com.facemagic.backend;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;


/**
 * Java8 流练习测试{@link Java8StreamPractice}
 *
 * @author wangdf
 * @version 1.0, 2020/12/29
 * @since 1.0.0
 */
class Java8StreamPracticeTest {

    /** 实例一个对象来实现方法调用 */
    private final Java8StreamPractice practice = new Java8StreamPractice();

    /**
     * 测试正常情况
     */
    @Test
    void testMostFrequentLetters_normal() {
        Assertions.assertEquals("lodehrw", practice.mostFrequentLetters("Hello World"));
    }

    /**
     * 测试包含多种字符情况
     */
    @Test
    public void testMostFrequentLetters_specialCharacters() {
        Assertions.assertEquals("adfsy", practice.mostFrequentLetters("Y,aA31（*））——&d s,f dfs ya1。。2aAA"));
    }

    /**
     * 测试空字符串
     */
    @Test
    public void testMostFrequentLetters_blank() {
        Assertions.assertEquals("", practice.mostFrequentLetters(""));
    }

    /**
     * 测试全是空格
     */
    @Test
    void testMostFrequentLetters_allSpace() {
        Assertions.assertEquals("", practice.mostFrequentLetters("    "));
    }

    /**
     * 测试全是数字情况
     */
    @Test
    public void testMostFrequentLetters_allNumber() {
        Assertions.assertEquals("", practice.mostFrequentLetters("123456789012312423152514351455"));
    }

    /**
     * 测试全是特殊字符情况
     */
    @Test
    public void testMostFrequentLetters_allSpecial() {
        Assertions.assertEquals("", practice.mostFrequentLetters("([,&*$,#@},)"));
    }

    /**
     * 测试全是大写字母情况
     */
    @Test
    public void testMostFrequentLetters_allUpper() {
        Assertions.assertEquals("efgabcd", practice.mostFrequentLetters("ABCDEFGEFG"));
    }

    /**
     * 测试全是小写字母情况
     */
    @Test
    public void testMostFrequentLetters_allLower() {
        Assertions.assertEquals("abcdefg", practice.mostFrequentLetters("abcdefgfedcba"));
    }

    /**
     * 测试只有同一字母（大小写）情况
     */
    @Test
    public void testMostFrequentLetters_same() {
        Assertions.assertEquals("a", practice.mostFrequentLetters("AAAAaaaaAaAAaaAAAaaa"));
    }

}
```