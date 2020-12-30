## 一、模拟验证码服务
#### 1.导入spring boot 的Redis依赖
```xml
<!-- redis缓存 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
#### 2.配置application.yml,配置redis数据源，和自定义redis key,超期时间
```yml
# 配置数据源
spring:
  datasource:
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/test_database
    username: root
    password: xgd@df1996
    redis:
      host: localhost # Redis服务器地址
      database: 0 # Redis数据库索引（默认为0）
      port: 6379 # Redis服务器连接端口
      password: # Redis服务器连接密码（默认为空）
      jedis:
        pool:
          max-active: 8 # 连接池最大连接数（使用负值表示没有限制）
          max-wait: -1ms # 连接池最大阻塞等待时间（使用负值表示没有限制）
          max-idle: 8 # 连接池中的最大空闲连接
          min-idle: 0 # 连接池中的最小空闲连接
      timeout: 3000ms # 连接超时时间（毫秒）
      
# 自定义redis key
redis:
  key:
    prefix:
      authCode: "verification:code:"
    expire:
      authCode: 300 # 验证码超期时间,5分钟
```
#### 3.编写redisService服务接口和实现类：包括存储数据，获取数据，设置删除数据等
```java
/**
 * Redis服务
 *
 * @author wangdf
 * @version 1.0, 2020/12/23
 * @since 1.0.0
 */
public interface IRedisService {

    /**
     * 存储数据
     *
     * @param key 键
     * @param value 值
     */
    void set(String key, String value);

    /**
     * 获取数据
     *
     * @param key 键
     * @return String 值
     */
    String get(String key);

    /**
     * 设置超时时间
     *
     * @param key 键
     * @param expire 时间
     * @return 返回true/false 是否超时
     */
    boolean expire(String key, long expire);

    /**
     * 删除数据
     *
     * @param key 键
     */
    void remove(String key);

    /**
     * 自增操作
     *
     * @param key 键
     * @param delta 自增步长
     * @return Long 自增值
     */
    Long increment(String key, long delta);

}
```
RedisServiceImpl.java
```java
package com.example.demo.service.impl;

import com.example.demo.service.IRedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

/**
 * redis服务实现
 *
 * @author wangdf
 * @version 1.0, 2020/12/23
 * @since 1.0.0
 */
@Service
public class RedisServiceImpl implements IRedisService {


    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    /** {@inheritDoc} */
    @Override
    public void set(String key, String value) {
        stringRedisTemplate.opsForValue().set(key, value);
    }

    /** {@inheritDoc} */
    @Override
    public String get(String key) {
        return stringRedisTemplate.opsForValue().get(key);
    }

    /** {@inheritDoc} */
    @Override
    public boolean expire(String key, long expire) {
        return stringRedisTemplate.expire(key, expire, TimeUnit.SECONDS);
    }

    /** {@inheritDoc} */
    @Override
    public void remove(String key) {
        stringRedisTemplate.delete(key);
    }

    /** {@inheritDoc} */
    @Override
    public Long increment(String key, long delta) {
        return stringRedisTemplate.opsForValue().increment(key, delta);
    }
}
```
#### 4.编写验证码生成和校验的服务接口和实现

```java
package com.example.demo.service;

/**
 *  验证码服务
 *
 * @author wangdf
 * @version 1.0, 2020/12/23
 * @since 1.0.0
 */
public interface IMfaCodeService {

    /**
     * 生成6位数字验证码 有效期 5分钟
     *
     * @param userId 用户id
     * @return 成功 true 失败 false
     */
    boolean generateCode(Long userId);

    /**
     * 校验验证码
     *
     * @param userId 用户id
     * @param code 验证码
     * @return 成功 true 失败 false
     */
    boolean valid(Long userId, String code);
}
```
```java
package com.example.demo.service.impl;

import com.example.demo.service.IMfaCodeService;
import com.example.demo.service.IRedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.thymeleaf.util.StringUtils;

import java.util.Random;

/**
 * Code服务实现
 *
 * @author wangdf
 * @version 1.0, 2020/12/23
 * @since 1.0.0
 */
@Service
public class MfaCodeServiceImpl implements IMfaCodeService {

    @Autowired
    private IRedisService redisService;

    @Value("${redis.key.prefix.authCode}")
    private String VERIFICATION_CODE;

    @Value("${redis.key.expire.authCode}")
    private Long EXPIRE_SECONDS;

    /** {@inheritDoc} */
    @Override
    public boolean generateCode(Long userId) {

        StringBuilder sb = new StringBuilder();
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            sb.append(random.nextInt(10));
        }

        redisService.set(VERIFICATION_CODE + userId, sb.toString());
        return redisService.expire(VERIFICATION_CODE + userId, EXPIRE_SECONDS);
    }

    /** {@inheritDoc} */
    @Override
    public boolean valid(Long userId, String code) {
        if(StringUtils.isEmpty(code)) {
            return false;
        }
        String realCode = redisService.get(VERIFICATION_CODE + userId);
        return code.equals(realCode);
    }
}

```
#### 5. 编写验证码controller来校验生成验证码
```java
package com.example.demo.service.impl;

import com.example.demo.service.IMfaCodeService;
import com.example.demo.service.IRedisService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;
import org.thymeleaf.util.StringUtils;

import java.util.Random;

/**
 * Code服务实现
 *
 * @author wangdf
 * @version 1.0, 2020/12/23
 * @since 1.0.0
 */
@Service
public class MfaCodeServiceImpl implements IMfaCodeService {

    @Autowired
    private IRedisService redisService;

    @Value("${redis.key.prefix.authCode}")
    private String VERIFICATION_CODE;

    @Value("${redis.key.expire.authCode}")
    private Long EXPIRE_SECONDS;

    /** {@inheritDoc} */
    @Override
    public boolean generateCode(Long userId) {

        StringBuilder sb = new StringBuilder();
        Random random = new Random();
        for (int i = 0; i < 6; i++) {
            sb.append(random.nextInt(10));
        }

        redisService.set(VERIFICATION_CODE + userId, sb.toString());
        return redisService.expire(VERIFICATION_CODE + userId, EXPIRE_SECONDS);
    }

    /** {@inheritDoc} */
    @Override
    public boolean valid(Long userId, String code) {
        if(StringUtils.isEmpty(code)) {
            return false;
        }
        String realCode = redisService.get(VERIFICATION_CODE + userId);
        return code.equals(realCode);
    }
}
```

#### 6.结果

* 先生成校验码
```
# 先生成验证码
http://localhost:8090/demo/code/getcode/10

# userId为10：10
# 生成的6位验证码：988774
# 生成结果：成功

{userId: 10, code: 988774, result: true}
```
* 校验验证码
```
# 校验验证码
http://localhost:8090/demo/code/checkcode/10/988774

# true ：验证码正确
# false ：验证码错误或失效
```

## 二、Redis实现分布式锁

**使用Redis实现分布式锁的加锁步骤：**
1. 使用setnx命令来保证互斥条件；
2. 需要设置锁过期时间，以防死锁；
3. setnex和设置过期时间要保持原子性，防止程序崩溃，锁无法释放；
4. 加锁等value值得是唯一标示，可采用UUID；加锁成功后需要把唯一标示返回给客户端来用来客户端进行解锁操作。  

**解锁步骤：**
1. 拿加锁成功的唯一标示要进行解锁，保证加锁和解锁的是同一个客户端；
2. 解锁操作需要比较唯一标示是否相等，相等再执行删除操作。

