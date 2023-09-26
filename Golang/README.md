## Golang Web 开发
- [标准库整理](./standard/README.md)
- [请求](./请求.md)
- [响应](./响应.md)
- [中间件](./中间件.md)
- [gin.Context](./gin.Context.md)
- [gin.Engine](./gin.Engine.md)
- [Redis](./Redis.md)
- [封装Rabbitmq](./rabbitmq.md)

### 第三方包
| 简介 | 主页 |
| ------------ | ------------ |
| GORM  | [https://github.com/go-gorm/gorm](https://github.com/go-gorm/gorm) |
| sqlx | [jmoiron/sqlx](https://github.com/jmoiron/sqlx)  |
| Redis Client |  [go-redis](https://github.com/redis/go-redis) |
| MongoDB Client |  [qiniu/qmgo](https://github.com/qiniu/qmgo) |
| 热更新 | [Air](https://github.com/cosmtrek/air)  |
| GJSON  |  [tidwall/GJSON](https://github.com/tidwall/gjson) |
| 时间处理 Carbon |  [https://github.com/golang-module/carbon](https://github.com/golang-module/carbon) |
| 写桌面应用  |  [fyne-io/fyn](https://github.com/fyne-io/fyne) |
| 日志处理  |  [https://github.com/uber-go/zap](https://github.com/uber-go/zap) |
| 表单验证 |   [go-playground/validator](https://github.com/go-playground/validator) |
| 命令行操作  |  [https://github.com/spf13/cobra](https://github.com/spf13/cobra) |
| http客户端  |  [resty](https://github.com/go-resty/resty) |
| OAuth2 Client  |  [golang/oauth2](https://github.com/golang/oauth2) |
| 支付开发  |  [go-pay/gopay](https://github.com/go-pay/gopay) |
| 汉字转拼音 |  [mozillazg/go-pinyin](https://github.com/mozillazg/go-pinyin) |
| 定时任务 |  [https://github.com/robfig/cron]( https://github.com/robfig/cron) |
| 操作Excel |  [qax-os/excelize](https://github.com/qax-os/excelize) |
| 微信开发 |  [PowerWeChat](https://powerwechat.artisan-cloud.com/zh/start/) |
| 配置管理 |  [https://github.com/spf13/viper](https://github.com/spf13/viper) |
| 硬件信息 |  [shirou/gopsutil](https://github.com/shirou/gopsutil/) |

### Go JSON动态修改字段
```
	structVar := 结构体{
		Code : 200,
		Data : "a message",
	}

	//把Data字段改名list
	m, _ := json.Marshal(structVar)
	var a interface{}
	json.Unmarshal(m, &a)
	b := a.(map[string]interface{})
	b["list"] = b["data"]
	delete(b, "data")

	ctx.Json(200, b)
```