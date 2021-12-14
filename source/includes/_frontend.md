## Frontend

### Install Node

You can download and install it by [this](https://nodejs.org/en/download/) link(suggested version >= 14).

### Clone Gin Web Vue

> Clone Gin Web Vue:

```shell
git clone https://github.com/piupuer/gin-web-vue
```

You can clone the frontend repo by git clone.

### Install libraries

> Install libraries:

```shell
# change registry if you need
# npm config set registry https://registry.npm.taobao.org
npm install
```

You should run command to download libraries before run.


### Change default config

> Change default config:

```shell
vim gin-web-vue/.env.development
# backend api:
VUE_APP_BASE_API = 'http://127.0.0.1:10000/api/v1'
# backend websocket:
VUE_APP_BASE_WS = 'ws://127.0.0.1:10000/api/v1'
```

If you not use [nginx](#nginx), you should change the api url prefix.

### Run in Terminal

> Run in Terminal:

```shell
npm run serve
```

Success example:

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/vue_run_success.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/vue_run_success.jpeg?raw=true" width="600" />
</a>
