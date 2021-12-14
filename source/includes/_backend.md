# Installation

## Backend

### Install Golang

You can download and install it by [this](https://go.dev/dl/) link(suggested version >= 1.14).

### Clone Gin Web

> Clone Gin Web:

```shell
git clone https://github.com/piupuer/gin-web
```

You can clone the backend repo by git clone.

### Enable GOPROXY

> For current session:

```shell
export GOPROXY=https://goproxy.cn,direct
```

#### For current session

> For system variable(Unix example):

```shell
# append one line
echo ~/.bashrc >> 'export GOPROXY=https://goproxy.cn,direct'
# for current session
source .bashrc
# reboot for every session
reboot
```

#### For system variable

### Change default config

> Change default config:

```shell
cd gin-web/conf

# change config file
vim config.dev.yml
```

Select a yml based on your run mode:  
development: config.dev.yml  
staging: config.stage.yml  
production: config.prod.yml

### Run in Terminal

> Run in Terminal:

```shell
cd gin-web

# project use go modules, run will auto download libraries
go run main.go
```

project use go modules, `go run` will auto download libraries.

### Run in Idea

#### 1. Set GOROOT

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_goroot.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_goroot.jpeg?raw=true" width="600" />
</a>

#### 2. Enable GOPROXY

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_goproxy.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_goproxy.jpeg?raw=true" width="600" />
</a>

#### 3. Run

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_run_main.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_run_main.jpeg?raw=true" width="600" />
</a>

Success example:

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_run_success.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/idea_run_success.jpeg?raw=true" width="600" />
</a>
