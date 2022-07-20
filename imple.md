# 具体使用

## 0 go实践

### 0.1 go服务注册

```
// @author Walker
// @time 2020/11/13 15:28
package main

import (
	"github.com/gin-gonic/gin"
	"github.com/micro/go-plugins/registry/consul"
	"github.com/micro/go-micro/registry"
	"github.com/micro/go-micro/web"
	"net/http"
)

func main() {
	// 连接到 Consul
	c := consul.NewRegistry(
		registry.Addrs("127.0.0.1:8500"),
	)

	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(http.StatusOK, "gin server...")
	})

	s := web.NewService(
		// 定义服务名称
		web.Name("goodService"),

		// 注册到 Consul
		web.Registry(c),

		web.Address(":8001"),
		web.Metadata(map[string]string{"protocol": "http"}),
		web.Handler(r),
)

	s.Run()
}
```

### 0.2 共gpoo服务发现

```
// @author Walker
// @time 2020/11/13 15:56
package main

import (
	"fmt"
	"github.com/micro/go-micro/client/selector"
	"github.com/micro/go-micro/registry"
	"github.com/micro/go-plugins/registry/consul"
	"log"
	"time"
)

func main() {
	// 连接 Consul
	c := consul.NewRegistry(registry.Addrs("127.0.0.1:8500"))

	// 根据前面定义的服务名称获取
	// 前面我们定义的 server name 为 "goodService"
	s, err := c.GetService("goodService")

	if err != nil {
		log.Fatal("server gain fail..")
	}

	// 这里随机获取其中一个实例
	// 目前只有一个实例，后面一篇会通过构建多个服务，需要关注到
	// go-micro 的负载均衡算法提供了两种
	// 	RoundRobin(轮询算法)
	//	Random(随机算法)
	//	这里使用 Random() 来演示
	n := selector.Random(s)

	// 返回的是一个 func()
	sv, e := n()

	if e != nil {
		log.Fatal("selector fail..")
	}

	fmt.Println("Id:", sv.Id)
	fmt.Println("Address:", sv.Address)
	fmt.Println("Metadata:", sv.Metadata)
	//	睡眠1s，观察结果
	time.Sleep(time.Second * 1)
}

```



怎么搞




















