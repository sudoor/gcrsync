# gcrsync [![Build Status](https://travis-ci.org/sudoor/gcrsync.svg?branch=master)](https://travis-ci.org/sudoor/gcrsync)

A docker image sync tool for Google container registry (gcr.io)

## 安装

工具采用 go 编写，安装可直接从 release 页下载对应平台二进制文件即可；如预编译文件不含有您的平台，
可自行 build:

```bash
go get github.com/sudoor/gcrsync
```

## 使用

```bash
A docker image sync tool for Google container registry (gcr.io).

Usage:
  gcrsync [flags]
  gcrsync [command]

Available Commands:
  compare     Compare gcr registry and private registry
  help        Help about any command
  monitor     Monitor sync images
  sync        Sync gcr images
  test        Test sync

Flags:
      --debug                  debug mode
      --githubrepo string      github commit repo (default "mritd/gcr")
      --githubtoken string     github commit token
  -h, --help                   help for gcrsync
      --httptimeout duration   http request timeout (default 10s)
      --namespace string       google container registry namespace (default "google-containers")
      --password string        docker registry user password
      --processlimit int       image process limit (default 10)
      --proxy string           http client proxy
      --querylimit int         http query limit (default 50)
      --user string            docker registry user

Use "gcrsync [command] --help" for more information about a command.
```

### compare 命令

该命令用于对比 Docker Hub 指定用户镜像与 `gcr.io` 指定 namesapce 下的镜像差异，同时生成已经同步的 json 文件

### monitor 命令

该命令与 compare 类似，只不过不生成任何文件，实时在控制台显示对比差异；一般用于监测同步进度

### sync 命令

该命令进行真正的同步操作，工作流程如下:

- 首先使用 `--githubtoken` 给定的 token 克隆 `--githubrepo` 给定的仓库到本地(这个仓库应当为元数据仓库)
- 获取 `gcr.io` 下由 `--namespace` 给定命名空间下的所有镜像
- 读取克隆的元数据仓库内对应的命名空间文件
- 对比两者差异，得出待同步镜像
- 执行 `pull`、`tag`、`push` 操作，将其推送到由 `--user` 给定的 Docker Hub 用户仓库中
- 追加元数据仓库内对应的命名空间 json 文件
- 生成 CHANGELOG.md 并推送元数据仓库到远程

### test 命令

该命令与 sync 命令基本行为一致，只不过不进行真正的同步，会生成 CHANGELOG，但不会推送到远程

## 其他说明

该工具并不建议个人使用，因为同步镜像会有很多；目前镜像正在同步到了 Docker Hub 的 `uwangjia` 用户下，可直接使用；这个工具开源目的是为了确保
`uwangjia` 用户下的镜像安全得到保证
