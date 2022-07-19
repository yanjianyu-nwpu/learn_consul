# 具体使用

## 0 go实践

### 0.1go是 注册

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

fwef
