# kamailio 调试手册

## 先了解目前的脚本是怎么做的

* 接受 sip 注册，认证用 xui 的 users 表，位置信息记录到 kamailio 的 localtion 表

* xui 所有节点的 ip 地址信息放在 dispatcher.dev.list 文件

* 收到 invite 消息之后的处理流程

```
  检查主叫是不是 xui 的 users（查表），如果是，那么先认证  

  检查源 ip 地址是否属于 dispatcher 列表，如果不是，那么 dispatch 到 某个 xui 节点  

  如果是，那么看被叫号码是不是 user, 如果是 user, 那么取出定位信息，再路由  

  如果不是 user, 那么 通过 mtree/drouting/lcr 三种 siptrunk 路由中的一种路由  
```

## kamailio.cfg 的修改

* 修改 kamailio 和 xui db 的 ip 地址

    搜索 "CUSTOMIZE ME" 很容易找到

    如果数据库连接不正常，那么进程很有可能马上结束

* 修改 siptrunk 的路由方式
````
    搜索 "#choose one of three siptrunk routing methods"

    三选一， 比如像下面这样做，选中 mtree， 把另外二个宏注释掉

	#!define WITH_MTREE
	#!define WITH_DROUTING
	##!define WITH_LCR
````

* 根据需要修改 WITH_DEBUG 这个宏

	调试阶段可以使能这个宏，调试结束之后关闭这个宏，同时在 /etc/rsyslog.conf 的文件最后加上一行

	local0.* -/var/log/kamailio.log # systemctl restart rsyslog


## 其他

* 如果修改 dispatcher.dev.list，那么

```
kamctl dispatcher reload
```

* 如果修改了 kamailio.lua, 那么

```
kamctl rpc app_lua.reload
```

* mtree/drouting/lcr 可参考相关文档
