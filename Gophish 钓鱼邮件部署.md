# Gophish 钓鱼邮件部署

1. 系统初始化：

   apt update && apt upgrade -y

   apt install curl wget perl screen -y

   

2. 下载gophis

   ```shell
   wget https://github.com/gophish/gophish/releases/download/v0.10.1/gophish-v0.10.1-linux-64bit.zip
   mkdir gophish
   apt install unzip
   unzip gophish-v0.10.1-linux-64bit.zip -d ./gophish
   ```

3. 修改配置文件

   ```shell
   cd gophish
   vim config.json
   ```

修改config.json文件中admin_server的listen_url为0.0.0.0:3636，用于远程登录服务器gophish进行管理，3636为自定义端口号。

自定义端口号后记得在云服务器中放通对应端口。

```json
{
        "admin_server": {
                "listen_url": "0.0.0.0:3636",
                "use_tls": true,
                "cert_path": "gophish_admin.crt",
                "key_path": "gophish_admin.key"
        },
        "phish_server": {
                "listen_url": "0.0.0.0:80",
                "use_tls": false,
                "cert_path": "example.crt",
                "key_path": "example.key"
        },
        "db_name": "sqlite3",
        "db_path": "gophish.db",
        "migrations_prefix": "db/db_",
        "contact_address": "",
        "logging": {
                "filename": "",
                "level": ""
        }
}
```

4. 启动gophish

   ```shell
   脚本赋权：chmod +x gophish
   服务启动：./gophish
   ```

   使用https://ip:3636访问gophish

   ![](https://ChihungSan.github.io/picx-images-hosting/gophish/image.39lq6m0lt8.webp)

   