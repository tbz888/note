> 命令(command): 单一的功能函数   [命令名 + 选项 + 参数]

## 任务管理
- bg 查看后台任务
- Ctrl + Z suspend
- Ctrl + C kill
- fg 后台任务切换至前台

## 文件管理
- find 实时查找
- ls 列出当前目录下的文件 [ll = ls -l]
- cd 目录的切换
- touch/mkdir 创建空文件/空文件夹
- cp 复制
- rm 删除
- mv 移动
- pwd 查看绝对路径
- ln -s 软链接(快捷方式)
- chgrp 改变所属群组
- chown 改变拥有者
- chmod 改变权限
- zip/unzip 压缩/解压.zip
- tar 压缩/解压.tar .gz .tgz等

## 文件内容
- cat 合并查看
- more 分页查看
- grep 在文件中以正则表达式查找并显示 [包含匹配]
- tailf 自动刷新尾部内容
- wc 统计

## 网络
- telnet/ssh 远程登录
- ifconfig 网络配置
- nslookup 域名查询
- curl HTTP请求
- netstat  网络连接管理

## 资源监控
- free 内存
- top 进程(详细)
- ps 进程(简化)

## CLI
- stdin重定向：<
- stdout重定向：>覆盖， >>追加，（选项：1正确输出，2错误输出，&两者）
- | (管道符)：以标准输入stdin，作为下一个命令的输入参数
- xargs：将管道的标准输入stdin分批、转换成命令行参数列表args，作为下一个命令的输入参数
- history 命令历史记录
- Ctrl + r 搜索命令的历史记录
- env 显示所有环境变量
- export 修改环境变量
- Ctrl + u 删除整行
- bash -x 调试模式执行脚本
- alias 别名(快捷方式)

## 玩具
- bc 计算器
- cal 日历
- date 时间
- sl 跑火车动画
- cmatrix 黑客帝国动画
