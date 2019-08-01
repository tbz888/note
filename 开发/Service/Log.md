##  日志
初衷：准确还原案发现场
参考要素：时间 - IP - 进程 - 线程 - 函数 - 分支 - 级别 - 当前状态 - 描述信息

## 指导准则
1. 对程序的运行轨迹进行监控和记录
2. 随时可详细了解程序内部的运行状态
3. 对系统性能的影响尽量小

## 建议
- 函数跳转
- 选择分支
- 循环结构
- 请求参数、响应内容
- 分级：fatal - error - warn - info - debug - trace

## 生产问题
- 服务端代码
	- 日志(slf4j, log4j, logback)
	- 线程、堆栈、GC情况(jstack, jmap, jstat)
- 数据库
	- 数据表快照
	- 锁
- 网络
	- HTTP请求记录(nginx, Chrome Dev)
	- 端口
- 操作系统
	- CPU
	- Memory
	- IO

## 编码建议
- 封装成业务含义的异常，如：ExportException extends SQLException
- 打印频率的控制：重复的异常，适当加以计数
- 配置：严格保存最近15天，而不是保存最近150MB