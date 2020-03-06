# nodejs 下的事件循环

依靠 libuv 驱动，有 6 个阶段：

1. timers: 执行定时器的回调：setTimeout, setInterval
2. I/O callbacks: 执行所有回调（除了 close 事件、定时器、setImmediate）
3. idle, prepare: 内部使用
4. poll: 等待新的 I/O 事件
5. check: 处理 setImmediate 回调
6. close callbacks: 处理 close 回调（如 socket.on('close', ...) 等连接的 close）

重点：**timer, poll, check** 

## poll

poll 阶段有两个主要功能：

1. 处理 poll queue的 callback
2. 执行 timers 的 callback，当到达 timers 的指定时间时

poll 阶段处理逻辑：

- 无 timer
  - poll queue 不为空，则同步执行队列中的 callback，直到为队列空，或执行的 callback 到达系统上限
  - poll queue 为空，则
    - 有 setImmediate，则结束 poll 阶段，进入 check 阶段，并执行 check queue
    - 无 setImmediate，则阻塞，等待新的 callback 进入 poll queue
- 有 timmer
  - poll queue 为空状态时，event loop 将检查 timers
  - 有 timer 时间到达时，event loop 将按循环顺序进入 timers 阶段，并执行 timer queue