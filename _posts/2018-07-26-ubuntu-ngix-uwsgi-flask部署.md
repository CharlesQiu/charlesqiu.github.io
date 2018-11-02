---
category: python
layout: post
tags: 'ubuntu,ngix,uwsgi,flask'
title: 'ubuntu-ngix-uwsgi-flask部署'
---

-   [Flask](#flask)
-   [Nginx](#nginx)
-   [uWSGI](#uwsgi)
-   [uWSGI Emperor(Ubuntu
    16.04之前版本)](#uwsgi-emperorubuntu-16.04之前版本)
-   [问题解决](#问题解决)
-   [托管多个应用](#托管多个应用)


### Flask

1.  创建工程目录

    ``` {.shell}
    sudo mkdir /var/www
    sudo mkdir /var/www/zlktapp
    # 修改目录权限 例子中的用户为 ubuntu
    sudo chown -R ubuntu:ubuntu /var/www/zlktapp/
    ```

2.  创建并激活一个虚拟环境，在其中安装`Flask`和插件：

        cd /var/www/zlktapp
        export LC_ALL=C # 若报错 locale.Error: unsupported locale setting 时执行
        virtualenv venv 
        . venv/bin/activate
        pip install flask
        pip install flask-script
        pip install flask-migrate
        pip install flask-mysql
        pip install flask-sqlalchemy

3.  使用下面的代码创建`hello.py`文件：

    ``` {.python}
    from flask import Flask
    app = Flask(__name__)

    @app.route("/")
    def hello():
        return "Hello World!"

    if __name__ == "__main__":
        app.run(host='0.0.0.0', port=8080)
    ```

    现在你可以通过浏览器访问你服务器的8080端口, 应该能看到页面显示 Hello
    World!

    现在应用是由Flask内置的web服务托管的，对于开发和调试这确实是个不错的工具，但不推荐在生产环境中使用。

------------------------------------------------------------------------

### Nginx

首先删除掉Nginx的默认配置文件：

    sudo rm /etc/nginx/sites-enabled/default

**注意：如果你安装了其他版本的Nginx，默认配置文件可能在/etc/nginx/conf.d文件夹下。**

创建一个我们应用使用的新配置文件**/var/www/zlktapp/zlktapp\_nginx.conf**：

    server {
        listen      80;
        server_name localhost;
        charset     utf-8;
        client_max_body_size 75M;
    
        location / { try_files $uri @yourapplication; }
        location @yourapplication {
            include uwsgi_params;
            uwsgi_pass unix:/var/www/zlktapp/zlktapp_uwsgi.sock;
        }
    }

将刚建立的配置文件使用符号链接到Nginx配置文件文件夹中，重启Nginx：

    sudo ln -s /var/www/zlktapp/zlktapp_nginx.conf /etc/nginx/conf.d/
    sudo /etc/init.d/nginx restart

此时，访问服务器会出现 `502 Bad Gateway`错误，这是正常的。

它代表Nginx已经使用了我们新创建的配置文件，但在链接到我们的Python应用网关uWSGI时遇到了问题。到uWSGI的链接在Nginx配置文件的第10行定义：

    uwsgi_pass unix:/var/www/zlktapp/zlktapp_uwsgi.sock;

这代表Nginx和uWSGI之间的链接是通过一个socket文件，这个文件位于*/var/www/zlktapp/zlktapp\_uwsgi.sock*。因为我们还没有配置uWSGI，所以这个文件还不存在，因此Nginx返回"bad
gateway"错误，让我们马上修正它吧。

------------------------------------------------------------------------

### uWSGI

1.  创建一个新的uWSGI配置文件**/var/www/zlktapp/zlktapp\_uwsgi.ini**：

<!-- -->
​     [uwsgi]
​     #application's base folder
​     base = /var/www/zlktapp/zlktqa

     #python module to import
     app = zlktqa
     module = %(app)
     
     home = /var/www/zlktapp/venv
     pythonpath = %(base)
     
     #socket file's location
     socket = /var/www/zlktapp/%n.sock
     
     #permissions for the socket file
     chmod-socket    = 666
     
     #the variable that holds a flask application inside the module imported at line #6
     callable = app
     
     #location of log files
     logto = /var/log/uwsgi/%n.log

创建一个新文件夹存放uWSGI日志，更改文件夹的所有权：

       sudo mkdir -p /var/log/uwsgi
       sudo chown -R ubuntu:ubuntu /var/log/uwsgi

执行uWSGI，用新创建的配置文件作为参数：

       uwsgi --ini /var/www/zlktapp/zlktapp_uwsgi.ini

我们现在基本完成了，唯一剩下的事情是配置uWSGI在后台运行，这是uWSGI
Emperor的职责。

### uWSGI Emperor(Ubuntu 16.04之前版本)

uWSGI Emperor
负责读取配置文件并且生成uWSGI进程来执行它们。创建一个初始配置来运行emperor - **/etc/init/uwsgi.conf**：

``` {.shell}
description "uWSGI"
start on runlevel [2345]
stop on runlevel [06]
respawn

env UWSGI=/usr/local/bin/uwsgi
env LOGTO=/var/log/uwsgi/emperor.log

exec $UWSGI --master --emperor /etc/uwsgi/vassals --die-on-term --uid www-data --gid www-data --logto $LOGTO
```

最后一行运行uWSGI守护进程并让它到**/etc/uwsgi/vassals**文件夹查找配置文件。创建这个文件夹，在其中建立一个到链到我们刚创建配置文件的符号链接。

``` {.shell}
sudo mkdir /etc/uwsgi && sudo mkdir /etc/uwsgi/vassals
sudo ln -s /var/www/zlktapp/zlktapp_uwsgi.ini /etc/uwsgi/vassals
```

同时，最后一行说明用来运行守护进程的用户是www-data。为简单起见，将这个用户设置成应用和日志文件夹的所有者。

``` {.bash}
sudo chown -R www-data:www-data /var/www/zlktapp/
sudo chown -R www-data:www-data /var/log/uwsgi/
```

**注意：我们先前安装的Nginx版本使用"www-data"这个用户来运行Nginx，其他Nginx版本的可能使用\"Nginx**\"**这个替代用户。**

由于Nginx和uWSGI都由同一个用户运行，我们可以在uWSGI配置中添加一个安全提升项。打开uWSGI配置文件，将chmod-socket值由**666**更改为**644**：

``` {.shell}
...
#permissions for the socket file
chmod-socket    = 644
```

现在我们可以运行uWSGI了：

``` {.shell}
sudo start uwsgi
```

最后，Nginx和uWSGI被配置成启动后立即对外提供我们的应用服务。

------------------------------------------------------------------------

### 问题解决

如果出现错误的话，第一个检查的地方是日志文件。Nginx默认将错误信息写到**/var/log/nginx/errors.log**文件。

我们已经配置了uWSGI
emperor将日志写到**/var/log/uwsgi/emperor.log**。这个文件夹还包含着每个配置应用的单独日志。我们的例子是
- **/var/log/uwsgi/zlktapp\_uwsgi.log**。

### 托管多个应用

如果你想在一台服务器上托管多个Flask应用，为每个应用创建一个单独的文件夹，像我们前面所做的一样，创建Nginx
及 uWSGI 配置文件到应用文件夹的符号链接。

*参考资料*: *\[1\]* *[在 Ubuntu 上使用 Nginx 部署 Flask
应用](https://www.oschina.net/translate/serving-flask-with-nginx-on-ubuntu)*
*\[2\]* *[问题解决笔记 -- 在 Ubuntu 16 上使用 Nginx 部署 Flask
应用](https://blog.csdn.net/qq_17121501/article/details/70833659)*
