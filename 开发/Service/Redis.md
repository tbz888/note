## 基于内存的持久化数据结构
	Redis is what is called a key-value store, often referred to as a NoSQL database.

example:
\> SET name "tbz" 增/改

\> GET name 查

\> DEL name 删

\> EXPIRE name 10 设定时限

\> TTL name 查时限

\> RPUSH friends "Alice" 尾追加list

\> LPUSH friends "Bob" 头追加list

\> LRANGE friends 0 -1 遍历

\> MSET id1 one id2 two 并列写

\> MGET id1 id2 并列读

\> EXISTS id1 是否存在

\> HSET user phone 13211200213 哈希类型写

\> HGET user phone 哈希类型读

## KEY支持的数据结构

​     string, list, set, sorted set, hash, HyperLogLog