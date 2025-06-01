# 关于分布式文件的思考


<!--more-->

## 关于分布式文件的思考

### 谈一谈Ruoy

ruoyi框架在实现分布式文件系统时给了用户三种选择：本地存储，minio以及FastDFS。

在具体的使用中，只能指定一种存储方式，不能多源存储，在三种存储方式中，之后后面两种支持分布式系统。

@Primary:意思是在众多相同的bean中，优先使用用@Primary注解的bean。

​	在架构上，通过三个不同子类FastDfsSysFileServiceImpl.java、MinioSysFileServiceImpl.java、LocalSysFileServiceImpl.java实现同一个基类ISysFileService.java，默认LocalSysFileServiceImpl.java作为默认存储方式。

​	在依赖注入时，只注入默认的文件服务

```java
@RestController
public class SysFileController
{
    private static final Logger log = LoggerFactory.getLogger(SysFileController.class);

    @Autowired
    private ISysFileService sysFileService;

    /**
     * 文件上传请求
     */
    @PostMapping("upload")
    public R<SysFile> upload(MultipartFile file)
    {
        try
        {
            // 上传并返回访问地址
            String url = sysFileService.uploadFile(file);
            SysFile sysFile = new SysFile();
            sysFile.setName(FileUtils.getName(url));
            sysFile.setUrl(url);
            return R.ok(sysFile);
        }
        catch (Exception e)
        {
            log.error("上传文件失败", e);
            return R.fail(e.getMessage());
        }
    }
}
```

存在的缺点：

1、不能在controller层实现调用不同的文件服务，无法在调用层实现文件服务的切换；

2、切换数据源需要改代码；

3、不能多源存储，实现逻辑为一份文件实现多个数据源同时存储，可以存储在数据库的不同字段，以便系统容错

### 1、实现文件服务工厂类

