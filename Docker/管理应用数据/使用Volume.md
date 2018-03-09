# 使用 volumes
卷是持久化由Docker容器生成和使用的数据的首选机制。绑定挂载依赖于主机的目录结构，卷则完全由Docker管理。与绑定挂载相比，卷有几个优点：

* 卷比绑定挂载更容易备份或迁移。
* 可以使用DockerCLI命令或DockerAPI管理卷。
* 卷在Linux和Windows容器上都可以工作。
* 卷可以更安全地在多个容器之间共享。
* 卷驱动程序允许您在远程主机或云提供程序上存储卷、加密卷的内容或添加其他功能。
* 新卷的内容可以由容器预先填充.

此外，卷通常比在容器的可写层中持久化数据更好，因为使用卷不会增加使用卷的容器的大小，而且卷的内容存在于给定容器的生命周期之外。
<img src=../images/types-of-mounts-volume.png />

如果您的容器生成非持久状态数据，请考虑使用tmpfs挂载来避免将数据永久存储在任何地方，并避免写入容器的可写层来提高容器的性能。

卷使用*rprivate* 绑定扩展，而绑定扩展对于卷是不可配置的。

## 选择 -v 还是 --mount 
最初，-v或--volume标志用于独立的容器，而--mount标志用于群集服务。但是，从Docker 17.06开始，您也可以使用--mount于独立的容器。一般来说，--mount更明确和详细。最大的区别是-v语法将所有选项组合在一个字段中，而--mount语法将它们分开。下面是每个标志语法的比较。

    提示：新用户应该使用--mount语法。经验丰富的用户可能更熟悉-v或--volume语法，但鼓励使用--mount，因为研究表明它更容易使用.
如果需要指定卷驱动程序选项，则必须使用--mount。

* -v or --volume 由三个字段组成，用冒号字符(:)分隔。字段必须按正确的顺序排列，并且每个字段的含义并不是很明显的。
    * 在命名卷的情况下，第一个字段是卷的名称，在给定的主机上是唯一的。对于匿名卷，省略第一个字段。
    * 第二个字段是将文件或目录挂载在容器中的路径。
    * 第三个字段是可选的，是一个逗号分隔的选项列表，如 *or* 。下面讨论这些选项。

* --mount 由多个键值对组成，以逗号分隔，每个键由<key>=<value>元组组成。mount语法比-v或--volume更冗长，但键的顺序并不重要，而且标志的值更容易理解。
    * type 挂载的类型，可以是bind、volume或tmpfs。本主题讨volume，因此类型总是volume。
    * source 挂载的源头。对于命名卷，这是卷的名称。对于匿名卷，省略此字段。可以指定为source或src。
    * destination 在容器中的挂载点。可以指定为destination、dst或target。
    * readonly 如果存在只读选项，则导致绑定挂载以只读形式安装到容器中。
    * volume-opt 选项可以多次指定，它接收key-value对作为选项的名称和值。

与bind mounts不同，volume的所有选项都可用于--mount和-v标志。当volume与service一起使用时，只支持--mount。

## 创建和管理volumes
与bind mounts不同，您可以在任何容器的作用域之外创建和管理卷。

**创建 volume**
```
wayne@ubuntu1604:~$ docker volume create my-vol
my-vol
```

**列举 volumes**
```
wayne@ubuntu1604:~$ docker volume ls
DRIVER              VOLUME NAME
local               my-vol
```
**查看 volume**
```
wayne@ubuntu1604:~$ docker volume inspect my-vol
[
    {
        "CreatedAt": "2018-02-26T15:19:34+08:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]
```

**删除 volume**
```
wayne@ubuntu1604:~$ docker volume rm my-vol
my-vol
```

## 启动一个带有 volume的container
如果您使用尚不存在的卷启动容器，Docker将为您创建卷。下面的示例将myvol2卷挂载到容器中的/app/中。

```
$ docker run -d \
  --name devtest \
  --mount source=myvol2,target=/app \
  nginx:latest
```

使用 docker inspect devtest 以验证卷是否已创建并正确安装。寻找Mounts 节：

           "Mounts": [
            {
                "Type": "volume",
                "Name": "myvol2",
                "Source": "/var/lib/docker/volumes/myvol2/_data",
                "Destination": "/app",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],

这表明mount是一个volume，它显示了正确的源和目的地，并且这个挂载是读写的。

删除container 和 volume
```
wayne@ubuntu1604:~/docker$ docker container ls -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                         PORTS               NAMES
03f5e94ec285        9b8ad2ed28cc        "/bin/bash"         About an hour ago   Exited (0) About an hour ago                       devtest
wayne@ubuntu1604:~/docker$ docker container rm devtest
devtest
wayne@ubuntu1604:~/docker$ docker volume rm myvol2 
myvol2
```
