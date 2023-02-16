
```go
/**
 * 路由对应处理方法
 * 例如 : github.com/tangzhangming/ginadmin/api/v1.Order_list"
 */ 
c.HandlerName()

//ContentType返回请求的ContentType标头
c.ContentType()

//返回流数据 ([]byte, error)
c.GetRawData()  

//获得请求头 以下方法相等
c.GetHeader("sec-ch-ua-platform")
c.requestHeader("sec-ch-ua-platform")
c.Request.Header.Get("sec-ch-ua-platform")

/**
 * 中止后续请求 常用于中间件
 */
c.Abort()
c.AbortWithStatus(409)
c.AbortWithError(code int, err error)
c.AbortWithStatusJSON(200, gin.H{})  // = c.Abort() + c.JSON()

//响应文件下载
c.File("a.txt")
c.FileAttachment("a.txt", "我就要重命名.txt")
```


