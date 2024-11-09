---
title: 14.FastDFS及头像上传
category: Django框架
tags:
  - framework
  - django
  - fast dfs
  - avatar
  - file
  - system
  - distributed
  - docker
date: 2023-1-14 00:00:00
---



[FastDFS](https://github.com/happyfish100/fastdfs)（Fast Distributed File System）是一个开源的轻量级分布式文件系统，它对文件进行管理，包括文件存储、同步、访问等等，解决了大容量存储和负载均衡的问题，特别适合以文件为载体的在线服务。

### 组成

* tracker server：跟踪服务（监听和调度各个存储服务）。

* storage server：存储服务（存储相关文件）。

### 大致流程

1. 客户端通过FastDFS提供的客户端API上传文件，将文件拆分成若干个小块并分别上传到多个存储服务器中的某一个卷中。
2. 存储服务器接收到文件块后，将其保存在本地磁盘中，并返回文件块的唯一标识符（file_id）以及所属卷的信息。
3. 客户端获取到所有文件块的file_id和所属卷的信息后，将其发送给Tracker服务器。
4. Tracker服务器根据文件块的信息计算出文件所在的Storage服务器的地址，并返回给客户端。
5. 客户端通过Storage服务器地址和file_id请求下载文件块，Storage服务器将文件块读取出来并返回给客户端。
6. 客户端获取所有文件块后，可将其组合成完整的文件。

### 安装

这里我们使用docker镜像部署FastDFS。

1. 拉取镜像

```bash
docker pull delron/fastdfs
```

> 自带Nginx（8888端口）。

2. 部署tracker

```bash
docker run -dit --network=host --name fdfs-tracker -v /Users/matiantian/docker_volumes/fdfs/tracker:/var/fdfs delron/fastdfs tracker
```

3. 部署storage

```bash
docker run -dit --network=host --name storage01 -e TRACKER_SERVER=10.0.2.15:22122 -v /var/fdfs/storage:/var/fdfs delron/fastdfs storage
```

> 此处需要使用`ifconfig`查看IP地址。
>
> 默认的tracker服务端口是22122。

### 配置

```ini utils/fdfs/client.ini
# connect timeout in seconds
# default value is 30s
# Note: in the intranet network (LAN), 2 seconds is enough.
connect_timeout = 5

# network timeout in seconds
# default value is 30s
network_timeout = 60

# the base path to store log files
base_path = utils/fastdfs/logs

# tracker_server can ocur more than once for multi tracker servers.
# the value format of tracker_server is "HOST:PORT",
#   the HOST can be hostname or ip address,
#   and the HOST can be dual IPs or hostnames seperated by comma,
#   the dual IPS must be an inner (intranet) IP and an outer (extranet) IP,
#   or two different types of inner (intranet) IPs.
#   for example: 192.168.2.100,122.244.141.46:22122
#   another eg.: 192.168.1.10,172.17.4.21:22122

tracker_server = 10.9.2.15:22122
# tracker_server = 192.168.0.197:22122

#standard log level as syslog, case insensitive, value list:
### emerg for emergency
### alert
### crit for critical
### error
### warn for warning
### notice
### info
### debug
log_level = info

# if use connection pool
# default value is false
# since V4.05
use_connection_pool = false

# connections whose the idle time exceeds this time will be closed
# unit: second
# default value is 3600
# since V4.05
connection_pool_max_idle_time = 3600

# if load FastDFS parameters from tracker server
# since V4.05
# default value is false
load_fdfs_parameters_from_tracker = false

# if use storage ID instead of IP address
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# default value is false
# since V4.05
use_storage_id = false

# specify storage ids filename, can use relative or absolute path
# same as tracker.conf
# valid only when load_fdfs_parameters_from_tracker is false
# since V4.05
storage_ids_filename = storage_ids.conf


#HTTP settings
http.tracker_server_port = 80

#use "#include" directive to include HTTP other settiongs
##include http.conf
```

> 需要对以下配置进行修改：
>
> ```ini
> # line-11，日志路径
> base_path = utils/fastdfs/logs
> 
> # line-22，tracker server的IP和端口
> tracker_server = 10.0.2.15:22122
> ```

### 依赖

```bash
pip3 install py3Fdfs

pip3 install requests

pip3 install mutagen
```

> 详情参考[py3Fdfs · PyPI](https://pypi.org/project/py3Fdfs/)。

### 测试

进入到配置文件目录下，使用python交互式终端依次执行以下代码。

```python
from fdfs_client.client import get_tracker_conf, Fdfs_client

# 读取配置文件
tracker_path = get_tracker_conf('client.ini')
# 创建客户端
client = Fdfs_client(tracker_path)
# 上传文件
ret = client.upload_by_filename('client.ini')
# 输出ret
ret
```

> ret结构如下：
>
> ```python
> {
> 'Group name': b'group1',
> 'Remote file_id': b'group1/M00/00/00/CgAYCWQ2tGOAbjVdAAAHbS7EvIQ482.ini',
> 'Status': 'Upload successed.',
> 'Local file name': 'client.ini',
> 'Uploaded size': '1.86KB',
> 'Storage IP': b'10.0.24.9'
> }
> ```
>
> 文件的访问地址为：nginx地址 + file_id，如`http://127.0.0.1:8888/group1/M00/00/00/CgAYCWQ2tGOAbjVdAAAHbS7EvIQ482.ini`。

### 接口编写

1. 配置服务主机及端口

```python config/fdfs_config.py
FASTDFS_SERVER_DOMAIN = 'http://127.0.0.1:8888/'
```

2. 编写视图

```python views.py
from fdfs_client.client import get_tracker_conf, Fdfs_client
from config.fdfs_config import FASTDFS_SERVER_DOMAIN

# 读取配置文件
tracker_path = get_tracker_conf('utils/fdfs/client.ini')
# 创建客户端
client = Fdfs_client(tracker_path)


@action(methods=['post'], detail=True)
def avatar(self, request, pk):
  	
    # 获取当前用户
    try:
        user = User.objects.get(id=pk)
    except User.DoesNotExist:
        return Response(status=HTTP_404_NOT_FOUND)

    # 获取上传的文件数据
    avatar_file = request.FILES.get('file')
    # 判断文件是否有数据、是否是图片
    if not avatar_file or avatar_file.content_type not in (
        'image/jpeg', 'image/png', 'image/gif', 'image/bmp', 'image/jpg'):
        logger.warning('上传图片格式不正确')
        return Response(status=HTTP_400_BAD_REQUEST)
    # 获取文件后缀，如果没有，则指定为png
    try:
        file_ext_name = avatar_file.name.split('.')[-1]
    except Exception as e:
        logger.info(f'图片后缀名异常{e}')
        file_ext_name = 'png'

        # 通过字节流上传图片数据
        try:
            upload_res = client.upload_by_buffer(avatar_file.read(), file_ext_name=file_ext_name)
        except Exception as e:
            logger.error(f'图片上传错误{e}')
            return Response(HTTP_500_INTERNAL_SERVER_ERROR)
        else:
            if upload_res.get('Status') != 'Upload successed.':
                logger.warning('图片上传失败')
                return Response(status=HTTP_500_INTERNAL_SERVER_ERROR)

            # 得到返回的file_id
            image_name = upload_res.get('Remote file_id').decode()
            image_url = FASTDFS_SERVER_DOMAIN + image_name

            # 判断是否有详情表数据
            if hasattr(user, 'userdetail'):
                # 如果有，则修改
                user_detail = user.userdetail
                user_detail.avatar = image_url
                user_detail.save()
            else:
                # 如果没有，则创建
                UserDetail.objects.create(avatar=image_url, user=user)
                # 返回图片地址
                return Response({'data': image_url})
```

> 大致思路如下：
>
> 1. 获取用户对象及上传的文件。
> 2. 判断文件类型并获取文件后缀。
> 3. 通过[py3Fdfs API](https://pypi.org/project/py3Fdfs)上传文件并返回上传结果。
> 4. 将完整的文件链接（fdfs服务 + file_id）保存到数据库对应用户头像的字段。
> 5. 返回响应数据。

