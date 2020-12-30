### step1:导入依赖

```xml
<!-- redis缓存 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
	<groupId>org.redisson</groupId>
	<artifactId>redisson</artifactId>
	<version>3.10.6</version>
</dependency>

<dependency>
	<groupId>org.redisson</groupId>
	<artifactId>redisson-spring-boot-starter</artifactId>
	<version>3.10.6</version>
</dependency>
```

### step2:配置redisson.yaml,这里配置的单节点
```yaml
# 单节点配置
singleServerConfig:
  # 连接空闲超时，单位：毫秒
  idleConnectionTimeout: 10000
  # 连接超时，单位：毫秒
  connectTimeout: 10000
  # 命令等待超时，单位：毫秒
  timeout: 3000
  # 命令失败重试次数,如果尝试达到 retryAttempts（命令失败重试次数） 仍然不能将命令发送至某个指定的节点时，将抛出错误。
  # 如果尝试在此限制之内发送成功，则开始启用 timeout（命令等待超时） 计时。
  retryAttempts: 3
  # 命令重试发送时间间隔，单位：毫秒
  retryInterval: 1500
  #  # 重新连接时间间隔，单位：毫秒
  #  reconnectionTimeout: 3000
  #  # 执行失败最大次数
  #  failedAttempts: 3
  # 密码
  password:
  # 单个连接最大订阅数量
  subscriptionsPerConnection: 5
  # 客户端名称
  clientName: null
  #  # 节点地址
  address: redis://127.0.0.1:6379
  # 发布和订阅连接的最小空闲连接数
  subscriptionConnectionMinimumIdleSize: 1
  # 发布和订阅连接池大小
  subscriptionConnectionPoolSize: 50
  # 最小空闲连接数
  connectionMinimumIdleSize: 32
  # 连接池大小
  connectionPoolSize: 64
  # 数据库编号
  database: 0
  # DNS监测时间间隔，单位：毫秒
  dnsMonitoringInterval: 5000
# 线程池数量,默认值: 当前处理核数量 * 2
threads: 0
# Netty线程池数量,默认值: 当前处理核数量 * 2
nettyThreads: 0
# 编码
codec: !<org.redisson.codec.JsonJacksonCodec> {}
# 传输模式
transportMode : "NIO"

```
### step3:编写redisson配置类

```java
import java.io.IOException;

/**
 * Redisson配置类
 *
 * @author wangdf
 * @version 1.0, 2020/12/25
 * @since 1.0.0
 */

@Configuration
public class RedissonConfig {

    @Bean
    public RedissonClient redisson() throws IOException {
        // 本例子使用的是yaml格式的配置文件，读取使用Config.fromYAML，如果是Json文件，则使用Config.fromJSON
        Config config = Config.fromYAML(RedissonConfig.class.getClassLoader().getResource("redisson.yaml"));
        return Redisson.create(config);
    }
}
```

### step4:在之前的IUserService中增加邮箱注册方法及实现
```java
IUserService.java
/**
 * 邮箱登陆：分布式锁练习
 *
 * @param email 邮箱
 * @return SignUpResponseDTO 返回登陆响应对象
 */
SignUpResponseDTO signUp(String email);
```
UserServiceImpl.java
```java
    /** {@inheritDoc} */
    @Override
    public SignUpResponseDTO signUp(String email) {

        SignUpResponseDTO signUpResponseDTO = new SignUpResponseDTO();
        UUID userId = UUID.randomUUID();
        signUpResponseDTO.setUserId(userId.toString());

        lock.lock(10, TimeUnit.SECONDS);
        boolean isLock;
        try {
            isLock = lock.tryLock(100, 10, TimeUnit.SECONDS);
            if (isLock) {
                //邮箱注册,
                if (redisService.exit(email)) {
                    signUpResponseDTO.setSuccess(false);
                    signUpResponseDTO.setMessage("邮箱已注册：可直接登陆！");
                } else {
                    redisService.set(email, userId.toString());
                    signUpResponseDTO.setSuccess(true);
                    signUpResponseDTO.setMessage("注册成功");
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
        return signUpResponseDTO;
    }
```
由于上面需要验证redis中是否存在邮箱，所以还需要在rendis服务中增加exit()方法，来返回是否存在该邮箱
```java
    /** {@inheritDoc} */
    @Override
    public boolean exit(String key) {
        return stringRedisTemplate.hasKey(key);
    }
```

### step5:编写邮箱注册控制类
```java
@RestController
@RequestMapping("/signup")
public class RedissonLockController {

    @Autowired
    private IUserService userService;

    @GetMapping(path = "/lock/{email}")
    @ResponseBody
    public String signUpByEmail(@PathVariable("email") String email) {
        SignUpResponseDTO resultDto = userService.signUp(email);
        return resultDto.toString();
    }
}
```
### step6:测试结果

```
http://localhost:8090/demo/signup/lock/231@aaa.com
> SignUpResponseDTO(success=true, userId=9656bcb2-2623-43bb-b699-8ab4cd34a597, message=注册成功)

http://localhost:8090/demo/signup/lock/123@163.com
> SignUpResponseDTO(success=false, userId=4097b6a9-c30e-4448-ab53-f5501ae0976d, message=邮箱已注册：可直接登陆！)
```