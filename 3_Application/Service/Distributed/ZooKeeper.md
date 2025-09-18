> ### distributed metadata database
## Znode
- Hierarchy
```bash
/
	/brokers
		/brokers/ids
	/tbz
		/tbz/node-1
		/tbz/node-2
		/tbz/node-3
```
- Model
  - stat

    | Key            | Value                            |
    | -------------- | -------------------------------- |
    | cZxid          | 创建的事务ID                     |
    | ctime          | 创建时间                         |
    | mZxid          | 最近一次修改的事务ID             |
    | mtime          | 最近一次修改的时间               |
    | pZxid          | 最近一次children列表变更的事务ID |
    | cversion       | children列表变更的版本号         |
    | dataVersion    | data版本号                       |
    | aclVersion     | Access Control List版本号        |
    | ephemeralOwner | 创建临时znode的sessionId         |
    | dataLength     | data长度                         |
    | numChildren    | 子节点个数                       |

  - data
  - children
  - watcher 数据变更的监听器
## Primitive
- ls /brokers/ids 查看子节点
- create -e /tbz "tbz" 创建session有效期的znode
- create -s /test "123" 创建永久的znode，-s表示带编号
- set /test "ttt" 修改znode的数据
- get /test 读取znode的数据
- delete /test 删除znode
- getAcl /test 获取访问控制权限
## Zookeeper Atomic Broadcast Protocol
- Leader: 唯一一个同时支持读写能力的节点，参与选举

  Follower: 只提供读能力的节点，参与选举

  Observer: 只提供读能力的节点，不参与选举

- Synchronization
## 运维
| Configuration     | Explain                  |
| ----------------- | ------------------------ |
| clientPort        | 监听端口                 |
| clientPortAddress | 监听IP                   |
| secureClientPort  | SSL端口                  |
| ssl.protocol      | SSL协议                  |
| dataDir           | 数据快照与事务日志的位置 |
| tickTime          | 基本单位时间，毫秒       |
