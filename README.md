# yapi-docker

use docker run yapi

一个不一样的 yapi docker 镜像，使用 docker-compose 管理，方便快速部署。 docker 仅做环境隔离，所有的数据文件（web 和 db）都在宿主机。

## 使用

1. clone 本项目到某个目录。
1. 将要运行的 yapi 项目代码全部拷贝到 `./yapi_root/yapi` 目录下。
    1. 方式 1： `cd yapi_root && git clone git@github.com:YMFE/yapi.git yapi`
    2. 方式 2： 在 `./yapi_root` 目录下新建 yapi 目录，拷贝代码。
1. 按照 yapi 项目的要求，需要在 `./yapi_root` 修改 `config.json` 文件，更新你的配置。
1. 按照你的配置，同步修改 `docker-compose.yml` 文件中的 暴露的端口号以及 `MONGO_INITDB_ROOT_USERNAME` 和 `MONGO_INITDB_ROOT_PASSWORD` 两个环境变量。
1. 运行 `docker-compose up -d` 启动 yapi 服务。

**还原之前的数据**

如果你之前有在运行的 yapi 的数据，请先运行备份命令.

备份可以使用如下命令：

```bash
mongodump --host <mongo_host> --port <mongo_port> --username <mongo_username> --password <mongo_password> --db <database_name> --out <backup_directory>

# 例如
mongodump --host 127.0.0.1 --port 27017 --username yapi --password yapi -d yapi --out d:/yapi_bak
```

将得到的备份文件拷贝到 [data/backup](data/backup/) 目录下。然后使用如下命令还原数据

```bash
# 进入 mongo 容器内 mongo 按需换成自己的名字
docker exec -it mongo bash

# 进入备份目录 还原数据
cd /data/backup
mongorestore --authenticationDatabase=admin --host 127.0.0.1 --port 27017 --username [username] --password [password] --db yapi ./yapi
# 若库已经存在需要强制还原可以加上 --drop 

# 创建针对库的 用户 
mongosh -u [username] -p [password] --authenticationDatabase=admin --host
use yapi
db.createUser({user: 'yapi', pwd: 'yapi', roles:[{role: "dbOwner" , db:"yapi"}]})

# 退出容器 ctrl + d
# 重启yapiweb
docker restart yapi
```

## FAQ

如果遇到 `Error: EROFS: read-only file system, mkdir '/sys/fs/cgroup/cpu/safeify` 错误请参阅 [yapi #2267](https://github.com/YMFE/yapi/issues/2267)

---

A different Yapi Docker image that can be managed with docker-compose for quick and easy deployment. Docker only provides environment isolation, and all data files (web and db) are on the host machine.

## Usage

1. Clone this project to a directory.

2. Copy all Yapi project code to the

    

   ```
   ./yapi_root/yapi
   ```

    

   directory.

   1. Method 1: `cd yapi_root && git clone git@github.com:YMFE/yapi.git yapi`
   2. Method 2: Create a new yapi directory in `./yapi_root` and copy the code.

3. According to the requirements of the Yapi project, modify the `config.json` file in `./yapi_root` to update your configuration.

4. Based on your configuration, modify the exposed port numbers and the `MONGO_INITDB_ROOT_USERNAME` and `MONGO_INITDB_ROOT_PASSWORD` environment variables in the `docker-compose.yml` file.

5. Run `docker-compose up -d` to start the Yapi service.

**Restore previous data**

If you have data from a previously running Yapi instance, first run the backup command.

You can use the following command for backup:

```bash
mongodump --host <mongo_host> --port <mongo_port> --username <mongo_username> --password <mongo_password> --db <database_name> --out <backup_directory>

# For example
mongodump --host 127.0.0.1 --port 27017 --username yapi --password yapi -d yapi --out d:/yapi_bak
```

Copy the resulting backup file to the [data/backup](https://chat.chends.ink/data/backup/) directory. Then use the following command to restore the data:

```bash
# Enter the mongo container. Replace 'mongo' with your own container name.
docker exec -it mongo bash

# Enter the backup directory and restore the data.
cd /data/backup
mongorestore --authenticationDatabase=admin --host 127.0.0.1 --port 27017 --username [username] --password [password] --db yapi ./yapi
# If the database already exists and you need to force the data restoration, add --drop.

# Create a user for the database.
mongosh -u [username] -p [password] --authenticationDatabase=admin --host
use yapi
db.createUser({user: 'yapi', pwd: 'yapi', roles:[{role: "dbOwner" , db:"yapi"}]})

# Exit the container with Ctrl+D and restart yapiweb.
docker restart yapi
```

## FAQ

If you encounter error `Error: EROFS: read-only file system, mkdir '/sys/fs/cgroup/cpu/safeify` please refer to [yapi #2267](https://github.com/YMFE/yapi/issues/2267) for more information.