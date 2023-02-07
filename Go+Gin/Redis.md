## Redis

Golang生态中最常用的两个Redis client分别是
- [go-redis](https://github.com/redis/go-redis) (16.4K Star)
- [redigo](https://github.com/gomodule/redigo) (9.4K Star)

这两个Redis客户端的区别是go-redis自带连接池，可以复用连接; 
而redigo每次使用一个新的redis连接，使用完成后需要手动关闭;
因为redigo发展更早，我们在B站泄露的源码中可以看到B站使用的是redigo，不过新项目建议使用go-redis。
以下内容针对 [go-redis](https://github.com/redis/go-redis)。

### 一些go-redis的操作
详细文档请查阅 [go-redis官网文档](https://redis.uptrace.dev/guide/go-redis.html)

### 执行lua脚本例子
``` go
func (r redis_cache) Add(key string, val string, seconds uint) bool {
	var incrBy = redis.NewScript("return redis.call('exists',KEYS[1])<1 and redis.call('setex',KEYS[1],ARGV[2],ARGV[1])")

	keys := []string{key}
	values := []interface{}{val, seconds}

	err := incrBy.Run(r.context, r.redis, keys, values).Err()
	if err != nil {
		return false
	} else {
		return true
	}
}
```

### 切换数据库
在php-fpm中(或redigo)，由于每次都使用一个新的redis连接，不会干扰到其他进程。 而在go-redis中，由于redis连接是复用的，如果你在某个http请求中使用 redis 的 select 命令来切换数据库，
复用这个redis连接的其他http请求，得到的redis连接将是被你之前的请求切换了数据库的连接。

比如默认1号库，请求A通过select命令选择了2号库，请求B获取到请求A相同的redis连接时，默认操作的将会是2号库，这个问题在复用连接的情况下普遍存在，比如PHP生态中的swoole和webman。

- 解决方案1，每个库设定一个连接池 (推荐)。
- 解决方案2，通过管道一次提交多个命令，保护切换库、业务操作、切换回默认库。

```go
//解决方案2实例
rdb := Redis连接

pipe := rdb.Pipeline()      //创建管道
pipe.Do("select", 8)        //前往目标库
_, _ = pipe.Get("redis_key").Result() //执行业务
pipe.Do("select", 1)        //返回默认库
cmders, err := pipe.Exec()  //将上面的三条redis命名通过管道提交 一次性执行

//如果你的以上命令中保护了返回结果，通过如下方式获取
strMap := redis.GetCmdResult(cmders)
did, _ = strMap[1].(string)
```

### 如果管理多个Redis连接
```go
// project/pkg/redis 简单封装调用
package redis

import (
	"context"
	redis "github.com/go-redis/redis/v8"
)

/**
 * 自定义的Redis结构体
 */
type RedisClient struct {
	Client  *redis.Client
	Context context.Context
}

/**
 * 连接储存器 The Redis connections
 */
var connections map[string]*RedisClient

/**
 * 根据 name 获取一个 RedisClient
 */
func Conn(name string) *RedisClient {}

/**
 * 二次封装Get方法
 */
func (rds RedisClient) Get(key string) *redis.StringCmd {
	/**
	 * 由于go-redis每个命令都要传入上下文context，比较繁琐
	 * 你可以在redis初始化时定义一个content，按此方法二次封装把它省略掉
	 */
	return rds.Client.Get(rds.Context, key)
}
```


```go
//调用
rds := redis.Conn("default")

//使用二次封装过的Get
rds.Get("key").Result()

//使用 go-redis 提供的 Get 方法
rds.Client.Get(ctx, "key").Result()

```
