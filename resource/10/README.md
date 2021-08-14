![在这里插入图片描述](https://img-blog.csdnimg.cn/2021031519154825.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhbmdwdWd1aQ==,size_16,color_FFFFFF,t_70)
timers 阶段：这个阶段执行 setTimeout 和 setInterval 预定的 callback

I/O callbacks 阶段：执行除了 close事件的 callbacks、被 timers 设定的callbacks、setImmediate() 设定的 callbacks 这些之外的 callbacks

idle, prepare 阶段：仅 node 内部使用

poll 阶段：获取新的 I/O 事件, 适当的条件下 node 将阻塞在这里

check 阶段：执行 setImmediate() 设定的 callbacks

close callbacks 阶段：执行 socket.on('close', ...) 这些 callback

`process.nextTick()有可能插入在Event Loop各个阶段中`