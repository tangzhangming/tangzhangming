```go
//使用中间件
c.Use(func(c *gin.Context){
     
})

//定义404处理器 NotFound
c.NoRoute(func(c *gin.Context){
    
})

//定义500处理器 InternalServerError
c.Use(func(c *gin.Context){
     //以中间件模式处理500
})

//HTTP方法不存在处理器
c.NoMethod(func(c *gin.Context){
    //有一样的路由 但是HTTP方法不匹配
})

//加载模板
c.LoadHTMLGlob()
c.LoadHTMLFiles()
c.SetHTMLTemplate()

//模板引擎
Delims(left, right string) // 给创建好的gin实例指定模板引擎的左右分割符




c.SetFuncMap()


//运行gin路由器
c.Run("127.0.0.1:8080")
c.RunTLS("127.0.0.1:8080", "证书", "")





```
