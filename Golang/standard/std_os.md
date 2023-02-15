### Golang 标准库 os
[官方文档](https://golang.google.cn/pkg/os/) 


### 常用方法
| 方法名称 | 返回类型 | 支持版本 | 方法说明 |
| --- | --- | --- | --- |
| Chdir(s string)  | error | | 将当前工作目录更改为指定目录,如果有错误，它将是 *PathError 类型 |
| Chmod(name string , mode FileMode )  | error | | 将指定文件的模式更改为 mode |
| Chown(name string, uid, gid int)  | error | | 更改指定文件的数字 uid 和 gid |
| Chtimes(name string , atime time . Time , mtime time . Time )  | error | | 更改命名文件的访问和修改时间 |
| DirFS()  | error | 1.16 | 以目录 dir 为根的文件树返回一个文件系统 |
| Executable()  | (string, error) | | 可执行文件返回启动当前进程的可执行文件的路径名 |
| Exit(code int)  |  | | Exit 导致当前程序以给定的状态代码退出。按照惯例，代码零表示成功，非零表示错误。程序立即终止；延迟函数不运行。为了可移植性，状态码应该在 [0, 125] 范围内 |
| Expand(s string , mapping func( string ) string )  | error | string | Expand 根据映射函数替换字符串中的 ${var} 或 $var。例如，os.ExpandEnv(s) 等同于 os.Expand(s, os.Getenv) |
| Getegid()  | int | | Getegid 返回调用者的数字有效组 ID (windows返回-1) |
| Geteuid()   | int | | Getgid 返回呼叫者的数字组 ID |
| Getgroups()  | ([]int, error) | | Getgroups 返回调用者所属组的数字 ID 列表 (windows不同) |
| Getpagesize() | int | | Getpagesize 返回底层系统的内存页面大小 |
| Getpid()  | int | | Getpid 返回调用者的进程 ID |
| Getppid()  | int | | Getppid 返回调用者父进程的进程 ID |
| Getuid()  | error | | Getuid 返回调用者的数字用户 ID (在 Windows 上，它返回 -1) |
| Getwd()   | (dir string, err error) | | Getwd 返回对应于当前目录的根路径名。如果可以通过多个路径（由于符号链接）到达当前目录，则 Getwd 可能会返回其中任何一个 |
| Hostname() | (name string, err error) | | Hostname 返回内核报告的主机名 |
| IsExist(err error) | bool | | IsExist 返回一个布尔值，指示是否已知错误报告文件或目录已存在 |
| IsTimeout(err error) | bool | 1.10 | IsTimeout 返回一个布尔值，指示是否已知错误以报告超时发生 |
| Mkdir(name string, perm FileMode) | error | | Mkdir 创建一个具有指定名称和权限位的新目录（在 umask 之前）。如果有错误，它将是 *PathError 类型 |
| MkdirAll(path string, perm FileMode)  | error | | MkdirAll 创建一个名为 path 的目录，以及任何必要的父目录，并返回 nil，否则返回错误 |
| ReadFile(name string) | ([]byte, error) | | ReadFile 读取命名文件并返回内容 |
| Remove(name string)  | error | | 删除命名文件或（空）目录 |
| RemoveAll(path string)  | error | | 删除路径及其包含的所有子项 |
| Rename(oldpath, newpath string) | error | | 命名将旧路径重命名（移动）为新路径 |
| WriteFile(name string, data []byte, perm FileMode)  | error | | 将数据写入指定文件，必要时创建它 |
| ReadDir(name string) | ([]DirEntry, error) | | ReadDir 读取命名目录，返回其所有按文件名排序的目录条目。如果读取目录时发生错误，ReadDir 会返回在错误发生之前它能够读取的条目以及错误 |
| Create(name string) | (*File, error) | | 创建或截断命名文件，如果该文件已经存在，则会被截断 |
| 环境变量 |
| Setenv(key, value string)  | error | | 设置由键命名的环境变量的值 |
| Unsetenv(key string) | error | | 取消设置单个环境变量 |
| Environ()  | []string | | 以“key=value”的形式返回代表环境的字符串副本 |
| ExpandEnv(s string )  | string | | ExpandEnv 根据当前环境变量的值替换字符串中的${var} 或$var。对未定义变量的引用将替换为空字符串 |
| Getenv(key string)  | string | | Getenv 检索由键命名的环境变量的值。它返回值，如果变量不存在，该值将为空。要区分空值和未设置的值，请​​使用 LookupEnv |
| Clearenv()  | error | | 删除所有环境变量 |
| LookupEnv(key string)   | (string, bool) | | LookupEnv 检索由键命名的环境变量的值。如果变量存在于环境中，则返回值（可能为空）并且布尔值为真。否则返回值将为空且布尔值将为假 |

### os.File 结构体
| 方法名称 | 返回类型 | 支持版本 | 方法说明 |
| --- | --- | --- | --- |
| Close() | error | | 关闭文件，使其无法用于 I/O |
| Name() | string | | 返回呈现给 Open 的文件的名称 |
| Read(b []byte) | (n int, err error) | | 从文件中读取最多 len(b) 个字节并将它们存储在 b 中。它返回读取的字节数和遇到的任何错误。在文件末尾，Read 返回 0，io.EOF |
| ReadAt(b [] byte , off int64 ) | (n int , err error ) | | ReadAt 从字节偏移量 off 开始从文件中读取 len(b) 个字节。它返回读取的字节数和错误（如果有）。当 n < len(b) 时，ReadAt 总是返回一个非零错误。在文件末尾，错误是 io.EOF |
| ReadDir(n int) | ([]DirEntry, error) | | ReadDir 读取与文件 f 关联的目录的内容，并按目录顺序返回一片 DirEntry 值 |
| [Readdir(n int)](https://golang.google.cn/pkg/os/#File.Readdir)  | ([]FileInfo, error) | | Readdir 读取与文件关联的目录的内容，并返回一个包含最多 n 个 FileInfo 值的切片，就像 Lstat 按目录顺序返回的一样 |
| Sync() | error | | 将文件的当前内容提交到稳定存储。通常，这意味着将文件系统最近写入数据的内存副本刷新到磁盘。 |
| Write(b []byte) | (n int, err error) | | Write 将 len(b) 个字节从 b 写入文件。它返回写入的字节数和错误（如果有）。当 n != len(b) 时，Write 返回一个非零错误 |
| WriteAt(b []byte, off int64) | (n int, err error) | | WriteAt 从字节偏移处开始将 len(b) 个字节写入文件 |
| WriteString(s string) | (n int, err error) | | 写入字符串 |