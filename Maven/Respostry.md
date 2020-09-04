# Respostry（存储库）

## Artifact Respositories （工件存储库）
Maven 中存储库保存不同类型的生成工件和依赖项。

准确的说是两种类型的存储库：本地存储库和远程存储库

本地存储库是电脑上的一个目录。它里面缓存了远程下载以及还没有发布的临时生成工件。

远程存储库属于另一个类型的存储库，通过一系列的访问协议(file:// https://),这些存储库可以是真实的第三方提供的用来下载的远程存储库，(例如：repo.maven.apache.org);也可以是公司内部的，通过file或 http server访问的内部的存储库。

本地和远程存储库的结构相同，以便脚本可以在任意一侧运行，也可以同步以脱机使用。存储库的布局对于Maven 用户是完全透明的。

## 应用存储库
一般来说，你不需要定期的对本地存储库做任何操作，除非磁盘不够用，清理磁盘。

对于远程存储库，它们只能用来下载或上传(前提你要有权限)

## 从远程存储库下载

当项目中声明的依赖在本地不存在(或对于SNAPSHOT，远程存在较新的存储库)时，会触发maven的下载操作。默认情况下，Maven 将从中央存储库下载。

如果不想从中央存储库下载，就需要指定一个镜像，可以参考 ‘配置.md’

## 脱机构建
如果您暂时与互联网断开连接，并且需要脱机构建项目，可以在命令行使用脱机参数

    mvn -o package

许多插件尊重离线设置，不执行任何连接到互联网的操作。

## 工件上传到存储库

你需要有足够的权限

## 内部存储库
在使用maven时，特别是在企业内部应用时，连接到互联网下载依赖项在安全性、下载速度、或是带宽原因是不可接受的。有必要建立一个内部的存储库来承载工件副本，用来内部发布工件。

这样的内部存储库能够通过http或文件系统(file://) 进行下载。使用scp,ftp或文件拷贝进行上传。

就 Maven 而言，此存储库没有什么特别之处：它是另一个包含要下载到用户本地缓存的项目的远程存储库，是项目发布的发布目标。

另外，你也许想要分享你的存储库，可以参考 '创建站点.md'

## 配置内部存储库
要设置内部存储库，只需您有地方放置它，然后使用与远程存储库（如repo.maven.apache.org）中的布局相同的布局将所需项目复制到那里。

不建议建立中央数据的全部副本，数据量太大。可以使用存储仓库管理工具来充当存储仓库服务器，下载工件，并保存到自己的存储库中，用来提供内部的快速下载。
## 使用内部存储库
使用内部存储库相当的简单，只需在pom中添加 repositories 元素

        <project>
        ...
        <repositories>
            <repository>
            <id>my-internal-site</id>
            <url>http://myserver/repo</url>
            </repository>
        </repositories>
        ...
        </project>

如果你的内部存储库需要权限验证，id 元素能被用来在你的setting文件中指定登录信息。

## 测试

在pom文件中添加了一个不存在的url,

        <repositories>
            <repository>
            <id>myrepository</id>
            <url>http://127.0.0.1/myrepository</url>
            </repository>
        </repositories>

然后在项目中添加一个 mysql jdbc的依赖试试看：

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.25</version>
        </dependency>

编译一下：

    mvn verify

从输出看：

    Downloading from myrepository: http://127.0.0.1/myrepository/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.pom
    Downloading from alimaven: http://maven.aliyun.com/nexus/content/groups/public/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.pom
    Downloaded from alimaven: http://maven.aliyun.com/nexus/content/groups/public/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.pom (1.1 kB at 2.5 kB/s)
    Downloading from myrepository: http://127.0.0.1/myrepository/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.jar
    Downloading from alimaven: http://maven.aliyun.com/nexus/content/groups/public/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.jar
    Downloaded from alimaven: http://maven.aliyun.com/nexus/content/groups/public/mysql/mysql-connector-java/5.1.25/mysql-connector-java-5.1.25.jar (848 kB at 1.1 MB/s)


起作用了，先是去本地存储库下载，本地存储库不存在，又去了阿里镜像下载。