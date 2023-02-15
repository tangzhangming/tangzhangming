## Redis 签到功能实现

redis实现签到功能依赖Redis位图，落库依业务实现，定时或签到时直接入库，可用Redis命令:

| 命令 | 说明 |
| ------- | ------- |
|    [SETBIT](http://redisdoc.com/bitmap/setbit.html)    |    |
|    [GETBIT](http://redisdoc.com/bitmap/getbit.html)    |    |
|    [BITCOUNT](http://redisdoc.com/bitmap/bitcount.html)  | 计算给定字符串中，被设置为 1 的比特位的数量 |
|    [BITPOS](http://redisdoc.com/bitmap/bitop.html)    |    |
|    [BITOP](http://redisdoc.com/bitmap/bitop.html)     |    |
|    [BITFIELD](http://redisdoc.com/bitmap/bitfield.html)  |    |


### 设置某天的签到数据
```PHP
	$key = "signin_10001_202202"; //10001为用户ID，202202为月份
	$day = 15;                    //设置 2022年02月15号已签到
	Redis::setbit($key, $day, 1); //1为签到，0为未签订，且位图仅可设置0和1
```

### 读取某天的签到数据
```PHP
	//读取2022年02月14号是否已签到 
	$key = "signin_10001_202202";
	Redis::getbit($key, 14); //返回int0或1
```

### 读取某月的签到总天数
```PHP
	//读取2022年02月总签到天数(比特位为1的总数量)
	$key = "signin_10001_202202";
	Redis::bitcount($key);
```

### 如何计算连续签到
连续签到次数 : 需要循环读取统计期，比如月，读取该月所有天数，然后手工计算。
当前连续签到了几天 : 从当前一天往前读取比特位为1的天数，直到读取到0。

