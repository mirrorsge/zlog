# zlog
日志包，采用全局 logger 变量，直接调用包方法即可，也可获取 logger 单例后调用对应方法


## 使用方式
`go get mygit.aphrolime.top/middlewares/zlog`

以一下日志格式作为示范,目前日志的格式为:
```json
{
"level":"info",
"timestamp":"2021-11-16T14:27:43.178+0800",
"label":"ss-dispatcher@v0.1.1/dispatcher.go:78",
"message":" 新增信息",
"service":"service_1"
}
```
其中 zap 默认的 time、caller、msg 字段被替换为 timestamp、label、message ，
且添加了全局唯一的字段 service，使用 zlog 的方式有两种，分别如下:
### 一、返回全局 logger 变量方式
```golang
import "mygit.aphrolime.top/middlewares/zlog"

// 初始化全局logger
levelType := InfoLevel
globalFields := Fields{
"service": "service_name",
}
Init(
//打开控制台日志，默认关闭
WithConsoleStdout(),
//默认 level 为 info
WithLevel(levelType),
//设置关闭自动压缩文件，默认打开
WithOffCompress(),
//日志文件位置，默认 ./log.log
WithFileLocation("test.log"),
// 设置日志保存天数，默认30
WithLogMaxAge(30),
//设置最大文件大小（MB），默认256
WithLogMaxSize(250),
//设置全局自定义字段
WithCustomizedGlobalField(globalFields),
//设置覆盖默认字段
WithCoverDefaultKey(CoverDefaultKey{
TimeKey:    "timestamp",
CallerKey:  "label",
MessageKey: "message"}),
)

// 日志打印
glog.C(ctx).Debug("test debug")
glog.C(ctx).Infof("test: %s","info")

// 也支持打印时新加字段，但仅影响本次调用，不会影响全局字段，仅支持打印 info 日志
glog.C(ctx).InfoWithField(map[string]interface{}{
"temp_field":"glog is good "
}, "msg1","msg2")

```

### 二、直接调用包方法
```go
//初始化流程如上，此处不再重复

// 日志打印
glog.Debug(ctx,"test debug")
glog.Infof(ctx,"test: %s","info")

// 也支持打印时新加字段，但仅影响本次调用，不会影响全局字段，仅支持打印 info 日志
glog.InfoWithField(ctx,map[string]interface{}{
"temp_field":"glog is good "
}, "msg1","msg2")

```

## 性能

goos: darwin

goarch: amd64

cpu: Intel(R) Core(TM) i7-4770HQ CPU @ 2.20GHz

打印两个全局变量加一个msg变量:

| package | Time | Mem Per op| MemAlloc |
| :---- | :---- | :---- |:---- |
| glog  | ~9000 ns/op    | 1858 B/op  | 12 allocs/op |







