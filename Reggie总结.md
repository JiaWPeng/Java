# 使用的技术

- SpringBoot 
- MyBatis/Plus
- MySQL
- Durid连接池
- RESTful
- fastJson
- 阿里云短信验证
- Redis
- Swagger

#### SpringBoot

创建SpringBoot项目，自动导入Spring框架依赖

1. Tomcat
2. MyBatis
3. MySQL驱动
4. Junit
5. Web

![image-20221127174030849](E:\Java课程\学习笔记\Java笔记\assets\image-20221127174030849.png)

#### MyBatis/Plus

无复杂业务，使用MyBatisPlus封装好的CURD方法

定义mapper接口继承BaseMapper接口

![image-20221127174249138](E:\Java课程\学习笔记\Java笔记\assets\image-20221127174249138.png)

可以定义全局自动插入表字段信息

1. ![image-20221127175838857](E:\Java课程\学习笔记\Java笔记\assets\image-20221127175838857.png)

2. 一个请求是同一个线程

   1. 这个字段填充的时候使用了一个ThreadLoacl，它是用来在一个线程中设置一个数据，其他线程无法获取该数据，只能本线程使用

3. 在需要自动填充的字段上加注解：

   - ```java
     1. @TableField(fill = FieldFill.INSERT_UPDATE)
     2. @TableField(fill = FieldFill.INSERT)
     3. @TableField(fill = FieldFill.INSERT_UPDATE)
     ```

#### MySQL&Druid

连接数据库，在application.yml中配置四大属性

![image-20221127174741902](E:\Java课程\学习笔记\Java笔记\assets\image-20221127174741902.png)

#### RESTful

属于org.springframework.web.bind.annotation包下

直接使用即可，不需要导依赖

- @RequestMapping
- @RestController
- @RestControllerAdvice：增强处理器注解，AOP思想（面向切面编程），如全局异常处理
  - ![image-20221127175249690](E:\Java课程\学习笔记\Java笔记\assets\image-20221127175249690.png)
  - 全局异常处理
    - @ExceptionHandler（Exception.class）：代表如果服务器出现Exception类型的异常就会给前端返回该注解修饰的方法的返回内容
- @PostMapping
- @GetMapping
- @DeleteMapping
- @PutMapping

#### fastJson

前后端交互数据自动转为Json格式

##### Long类型精度丢失

```java
在实体类上使用
    @JsonSerialize(using = ToStringSerializer.class)
就可解决
```

##### 格式化日期格式

```java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
```

#### 阿里云短信

[阿里云官网短信服务](https://dysms.console.aliyun.com/quickstart)

导入依赖

```xml
<!-- 阿里云短信 -->
		<dependency>
			<groupId>com.aliyun</groupId>
			<artifactId>aliyun-java-sdk-core</artifactId>
			<version>4.0.6</version> <!-- 注：如提示报错，先升级基础包版，无法解决可联系技术支持 -->
		</dependency>
		<dependency>
			<groupId>com.aliyun</groupId>
			<artifactId>aliyun-java-sdk-dysmsapi</artifactId>
			<version>1.1.0</version>
		</dependency>
		<dependency>
			<groupId>com.aliyun</groupId>
			<artifactId>alibabacloud-dysmsapi20170525</artifactId>
			<version>2.0.22</version>
		</dependency>
```

按需求创建发送短信工具类

#### Redis

解决高并发，MySQL不支持高并发

##### 好处

1. **速度快，因为数据存在内存中**，类似于HashMap，HashMap的优势就是查找和操作的时间复杂度都很低
2. 支持丰富数据类型，支持string，list，set，sorted set，hash
3. 支持事务，操作都是原子性，所谓的原子性就是对数据的更改要么全部执行，要么全部不执行
4. 丰富的特性：可用于缓存，消息，按key设置过期时间，过期后将会自动删除

Redis是一共中间件，是将数据库高并发的**数据放在内存上**，数据使用键值对存储（类似于HashMap），Value的值默认是字节形式存储，Key为了方便查看我们使用String类型存储![image-20221127180954911](E:\Java课程\学习笔记\Java笔记\assets\image-20221127180954911.png)



默认Key![image-20221127181245047](E:\Java课程\学习笔记\Java笔记\assets\image-20221127181245047.png)

默认Value![image-20221127181308426](E:\Java课程\学习笔记\Java笔记\assets\image-20221127181308426.png)

**Value存储的时候是以按序列化形式按字节写入，读取的时候是以反序列化读出**

Spring注解开发存的是Hash结构的Key Value![image-20221128103816196](E:\Java课程\学习笔记\Java笔记\assets\image-20221128103816196.png)

##### 使用方式

看Redis.Xmind

#### Swagger

自动生成接口文档

1. 导入knife4j的maven坐标

   ```
   <dependency>
   
        <groupId>com.github.xiaoymin</groupId>
   
        <artifactId>knife4j-spring-boot-starter</artifactId>
   
        <version>3.0.2</version>
   
   </dependency>
   ```

2. ![image-20221128222158510](E:\Java课程\学习笔记\Java笔记\assets\image-20221128222158510.png)

3. ![image-20221128222226069](E:\Java课程\学习笔记\Java笔记\assets\image-20221128222226069.png)

4. ![image-20221128222235374](E:\Java课程\学习笔记\Java笔记\assets\image-20221128222235374.png)

5. 测试地址http://localhost:8080/doc.html



![image-20221128222017493](E:\Java课程\学习笔记\Java笔记\assets\image-20221128222017493.png)

#### 七牛云上传图片

[七牛云开发者](https://developer.qiniu.com/kodo/1239/java#5)

##### 流程图

![Qiniuyun](.\assets\Qiniuyun.png)

##### 导入依赖

```
		<dependency>
            <groupId>com.qiniu</groupId>
            <artifactId>qiniu-java-sdk</artifactId>
            <version>7.2.25</version>
        </dependency>
```

##### 创建工具类

```
import java.io.InputStream;

/**
 * @author https://github.com/JiaWPeng
 * @version 1.0
 * @description:
 * @date 2022/12/1 21:01
 */
@Slf4j
@Data
public class QiniuYunUtils {
//    @Value("${qiniu.urlPrefix}")
    private String urlPrefix ;
//    @Value("${qiniu.accessKey}")
    private String accessKey;
//    @Value("${qiniu.secretKey}")
    private String secretKey;
//    @Value("${qiniu.bucket}")
    private String bucket;

    /**
     * 上传到七牛云
     *
     * @param is             上传内容的输入流
     * @param uploadFileName
     */
    public void upload2Qiniu(InputStream is, String uploadFileName) {
        //构造一个带指定Zone对象的配置类
        Configuration cfg = new Configuration(Region.autoRegion());
        //...其他参数参考类注释
        UploadManager uploadManager = new UploadManager(cfg);

        Auth auth = Auth.create(accessKey, secretKey);
        String upToken = auth.uploadToken(bucket);
        try {
            Response response = uploadManager.put(is, uploadFileName, upToken, null, null);
            //解析上传成功的结果
            log.info(response.bodyString());
            // 访问路径
            log.info("{}/{}", urlPrefix, uploadFileName);
        } catch (QiniuException ex) {
            Response r = ex.response;
            log.error(r.toString());
            try {
                log.error(r.bodyString());
            } catch (QiniuException ex2) {
                //ignore
                log.error("", ex2);
            }
            throw new RuntimeException(ex);
        }
    }

    /**
     * 删除指定文件
     *
     * @param fileName
     */
    public void deleteFileFromQiniu(String fileName) {
        //构造一个带指定Zone对象的配置类
        Configuration cfg = new Configuration(Region.autoRegion());
        String key = fileName;
        Auth auth = Auth.create(accessKey, secretKey);
        BucketManager bucketManager = new BucketManager(auth, cfg);
        try {
            bucketManager.delete(bucket, key);
        } catch (QiniuException ex) {
            if (612 == ex.code()) {
                // 文件不存在，则无需任何操作，直接返回
                log.info("[七牛云工具类-删除]重复删除，跳过:{}", fileName);
            } else {
                //如果遇到异常，说明删除失败
                log.error("code:{}", ex.code());
                log.error(ex.response.toString());
                throw new RuntimeException(ex);
            }
        }
    }
}
```

##### 配置参数信息

```sql
# application.yml中添加
qiniu:
  urlPrefix: http://rm7nm15l9.hb-bkt.clouddn.com/
  accessKey: 8QvB_4wtPGbFEoKZXfqHoLRyqjKz2IZWWLsZl4eD
  secretKey: JSyUcBVg84avHuGTmmkVg1kFucyo4-6CWCtJSqxJ
  bucket: czjk-image
```

##### 交个Spring管理

```
import com.itheima.health.common.QiniuYunUtils;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @author https://github.com/JiaWPeng
 * @version 1.0
 * @description:
 * @date 2022/12/1 20:46
 */
@Configuration
public class QiniuYunConfig {

    @Bean
    /**
     * 配置属性参数，在yml文件中寻找
     */
    @ConfigurationProperties("qiniu")
    public QiniuYunUtils qiniuYunUtils(){
        return new QiniuYunUtils();
    }
}
```

