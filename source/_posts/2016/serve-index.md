---
title: 更美观的serve静态文件
date: 2016-06-14
---

## With nginx

用 ningx 来 serving 静态文件，一般是这样配置的:

```
location /static {
    root /opt/static;
    autoindex on;
}
```

It works, but it's not pretty and unable to customize.

## Using express and serve-index

为了更美观，可以使用 *nginx* + *express* + *serve-index*。 nginx 在这里用来 serving 静态文件本身，而 express + serve-index 只用来 serving 目录的 index 页面。需要说明的是，在这里 express 并不是必须的，也可以使用 Node 自带的 http server，这里使用 express 只是为了方便。

当然你首先要安装好 express 和 serve-index, 然后这样使用：

```js
#!/home/haishanh/.nvm/versions/node/v5.6.0/bin/node

const express = require('express');
const serveIndex = require('serve-index');
const app = express();

const serveOpt = {
  icons: true,
  view: 'details'
};

app.use('/static', serveIndex('/opt/static', serveOpt));
app.listen(8001);
```

这段内容表示 express + serve-index 会 hosting 目录 `/opt/static`，使用的url路径为`/static`。假设以上内容保存为文件`serve.js`。给这个文件可执行权限，`chmod +x serve.js`，要注意脚本最开头的 *shebang* 要指定好，尤其是当你和我一样是用 nvm 来管理 node 版本的时候。

停止 nginx，只运行以上脚本，访问 `http://localhost:8001/static/` 就可以看到目录 `/opt/static` 中的内容了，界面也很美观。需要注意的是，此时文件本身并没有被 serve，仍然需要使用 nginx 来 serve 文件。

![](http://7fva40.com1.z0.glb.clouddn.com/serve-index.png)

修改 nginx 的配置:

```
location = /static/ {
    proxy_pass http://localhost:8001;
}

location /static {
    root /opt/static;
}
```

运行 `server.js`，并 start/reload nginx。

```sh
/path/to/serve.js
nginx -s reload
```

试试访问 `http://localhost/static/`，其中的文件也应该正常被 serve 了。如果你希望 `/opt/static` 下的所有目录都有 index 页面的话，前面 nginx 配置中的第一个 location 可以改成这样的：

```
location ~ ^/static.*/$ {
    proxy_pass http://localhost:8001;
}
```

## Supervise / Monitor

为了防止 server.js 在运行的过程中意外退出，可以用 *pm2* 管理一下。当然我们首先要全局安装一下 pm2:

```sh
npm install pm2 -g
```

然后就可以用 pm2 来启动 serve.js 了:

```sh
pm2 start /path/to/serve.js --name "serve-index"
```

可以用 `pm2 list` 来查看当前 pm2 管理的进程情况。更多命令和用法可以看看[PM2 cheatsheet][pm2cs]


如果需要开机启动，可以运行以下命令，`pm2`会自动判断操作系统/平台信息，[相应的进行startup配置][system-startup-conf]。

```sh
sudo pm2 startup
```

如果不能识别平台信息，可以尝试手动指定一下，比如我是用的 `systemd`

```sh
sudo pm2 startup systemd
```

`pm2`会自动生成`systemd` service 配置文件，并enable这个service，一般这个文件的路径是 `/etc/systemd/system/pm2.service`。这个服务在 stop 的时候会 dump 当前 pm2 monitor的程序信息，start的时候再 load 这些信息。

[pm2cs]: http://pm2.keymetrics.io/docs/usage/quick-start/#cheatsheet
[system-startup-conf]: http://pm2.keymetrics.io/docs/usage/startup/#startup-systems-support
