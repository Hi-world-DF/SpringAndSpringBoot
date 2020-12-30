### javax.validation的一系列注解可以帮我们完成参数校验,免去繁琐的串行校验
```java
// 常用的一些注解
1.@NotNull：不能为null，但可以为empty(""," ","   ")      
2.@NotEmpty：不能为null，而且长度必须大于0 (" ","  ")
3.@NotBlank：只能作用在String上，不能为null，而且调用trim()后，长度必须大于0("test")
4.@AssertFalse/@AssertTrue 验证注解的元素值是false/true
5.@NotNull/@Null
6.@Min(value=值)/@Max(value=值)
7.@Future 验证注解的元素值（日期类型）比当前时间晚
8.@NotBlank @NotBlank只应用于字符串且在比较时会去除字符串的首位空格
9.@NotEmpty 验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0）
10.@Email(regexp=正则表达式,flag=标志的模式)
11.@Pattern(regexp=正则表达式,flag=标志的模式)
```

### 练习
#### 1. @Validated 声明要检查的参数
```java
Optional<User> getUserById(@Validated Long id) {
    return iUserService.getUserById(id);
}
```
#### 2. 对参数的字段进行注解标注
```java
package com.example.demo.dto;

import lombok.Data;
import org.hibernate.validator.constraints.Length;

import javax.validation.constraints.Email;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.Pattern;
import java.io.Serializable;
import java.time.LocalDateTime;

/**
 * User传输对象
 *
 * @author wangdf
 * @version 1.0, 2020/12/23
 * @since 1.0.0
 */
@Data
public class UserDTO implements Serializable {

    /** 用户id */
    private Long id;

    /** 用户手机号 */
    @NotBlank(message = "用户手机号不能为空")
    @Pattern(regexp = "^[1][3,4,5,6,7,8,9][0-9]{9}$", message = "手机号格式有误")
    private String mobile;

    /** 用户邮箱 */
    @NotBlank(message = "用户邮箱不能为空")
    @Email(message = "邮箱格式不对")
    private String email;

    /** 用户名 */
    @NotBlank(message = "用户名不能为空")
    @Length(max = 20, message = "用户名不能超过20个字符")
    private String username;

    /** 用户昵称 */
    private String nickname;

    /** 用户密码 */
    @NotBlank(message = "用户密码不能为空")
    private String password;

    /** 创建时间 */
    private LocalDateTime created;

}
```
#### 3.测试

* 出现问题：访问controller的url地址出问题
* 对postman使用不熟悉
* 只通过了一些测试，解决了Controller访问问题