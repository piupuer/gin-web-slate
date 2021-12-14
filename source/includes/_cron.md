# Distribute Cron Task

## Redis

I assume you have redis installed, or click [this](#gin-web-docker-redis-sentinel-initialization) to initialization.

## Go Helper

```shell
go get -u github.com/piupuer/go-helper
```

Download Go Helper by `go get`.

## Example

```go
package main

import (
  "context"
  "fmt"
  "github.com/piupuer/go-helper/pkg/constant"
  "github.com/piupuer/go-helper/pkg/job"
)

func main() {
  // parse redis URI
  client, _ := job.ParseRedisURI("redis://127.0.0.1:6379/0")

  j, err := job.New(
    job.Config{
      RedisClient: client,
    },
    job.WithAutoRequestId(true),
  )
  if err != nil {
    panic(err)
  }
  // add task and start
  j.AddTask(job.GoodTask{
    Name: "task",
    Expr: "@every 5s",
    Func: task,
  }).Start()

  ch := make(chan int)
  <-ch
}

func task(ctx context.Context) error {
  requestId := ctx.Value(constant.MiddlewareRequestIdCtxKey)
  fmt.Println(requestId, "task running...")
  return nil
}
```

Run result:

<a target="_blank" href="https://github.com/piupuer/gin-web-images/blob/master/docs/cron/run.jpeg?raw=true">
<img src="https://github.com/piupuer/gin-web-images/blob/master/docs/cron/run.jpeg?raw=true" width="600" />
</a>

### New

#### AutoRequestId

If set it true, request id is automatically generated each time the task is executed.

### AddTask

#### Name

Task name must be unique.

#### Expr

Task expression is support:

* 0 30 * * * * (Every hour on the half hour)
* @hourly (Every hour)
* @every 1h30m (Every hour thirty)

More: [robfig/cron](https://github.com/robfig/cron)
