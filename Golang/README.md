### Golang


### PHP常用的函数对应的Go方法
| PHP函数 | Go对应 | 说明 |
| --- | --- | --- |
| count() | len(数组、切片、map、channel、字符串)  | 返回数组中元素的数目 |
| substr() | str[0:3] | Go直接把字符串当数组操作，但是无法像PHP一样反向取值 |
| explode() | strings.Split() | 字符串拆为数组 |
| implode() | strings.Join() | 数组合并为字符串 |
| str_replace() | strings.Replace | 字符串替换 |
| strlen() | len("字符串") | 返回字符串长度 |
| [strpos()](https://www.runoob.com/php/func-string-strpos.html) | [strings.Index(s, sep string)](https://golang.google.cn/pkg/strings/#Index) | 字符串中第一次出现的位置 |
| [strtolower](https://www.runoob.com/php/func-string-strtolower.html) | strings.ToLower() | 把所有字符转换为小写 |
| [strtoupper](https://www.runoob.com/php/func-string-strtoupper.html) | strings.ToUpper() | 把所有字符转换为大写 |
| 文件系统 |
| [mkdir](https://www.runoob.com/php/func-filesystem-mkdir.html) | os.Mkdir() | 创建目录 |
| [fopen](https://www.runoob.com/php/func-filesystem-fopen.html) | os.Open() | 打开文件 |
| [fclose](https://www.runoob.com/php/func-filesystem-fopen.html) | (f *File) Close() | 打开文件 |
| [rename](https://www.runoob.com/php/func-filesystem-rename.html) | os.Rename() | 重命名文件/目录 |
| [unlink](https://www.runoob.com/php/func-filesystem-unlink.html) | os.Remove() | 删除文件 |
| [is_dir]() |  | 检查指定的文件是否是一个目录 |
| [basename](https://www.runoob.com/php/func-filesystem-basename.html) |  | 返回路径中的文件名部分 |
| 其他函数 |
| [preg_match()](https://www.runoob.com/php/php-preg_match.html) |  | preg_match 函数用于执行一个正则表达式匹配 |
| [sprintf](https://www.runoob.com/php/func-string-sprintf.html) | 参考fmt包 | 把百分号（%）符号替换成一个作为参数进行传递的变量 |
| [file_exists](https://www.runoob.com/php/func-filesystem-file-exists.html) |  | 检查文件或目录是否存在 |
| [is_string]() |  |   |
| [preg_replace]() |  |   |
| [file_get_contents]() |  |   |
| [array_key_exists]() |  |   |
| [array_keys]() |  |   |
| [dirname]() |  |   |
| [function_exists]() |  |   |
| [array_map]() |  |   |
| [get_class]() |  |   |
| [class_exists]() |  |   |
| [is_object]() |  |   |
| [time]() |  |   |
| [json_encode]() |  |   |
| [array_values]() |  |  |
| [md5]() |  |  |
| [intval](https://www.runoob.com/php/php-intval-function.html) |  | 字符串转整型、进制转换 |
| [str_repeat](https://www.runoob.com/php/func-string-str-repeat.html) | strings.Repeat() | 重复字符串 |
| [serialize](https://www.runoob.com/php/php-serialize-function.html) |  | 序列化 |
| [unserialize](https://www.runoob.com/php/php-unserialize-function.html) |  | 反序列化 |
| [base64_encode]() | ```go base64.StdEncoding.EncodeToString([]byte("hello, world"))``` | "encoding/base64"包 |

| is_array() |  |  |
| array_merge() |  | 合并数组 |



### 标准库
| Name | Synopsis |
| --- | --- |
| [fmt](./std_fmt.md) | 包fmt使用类似于C的printf和scanf的函数实现格式化的I/O |
| [time](./std_time.md) | |
| [strings](./std_strings.md) | 实现操作UTF-8编码字符串的简单函数 |
| [os](./std_os.md) | |
| [strconv](./std_strconv.md) | 实现与基本数据类型的字符串表示之间的转换 |
| [net](./std_net.md) | |
| --[http](./std_net_http.md) | |