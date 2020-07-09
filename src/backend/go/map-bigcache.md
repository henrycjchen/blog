# go 中如何处理高并发大容量的内存级缓存

当我们在 go 中使用内存缓存，自然会想到 map，但由于 go 自身的限制，map 在并发情况下操作 map 必须加上读写锁，以保证线程安全。

map + 读写锁的实现通常如下：

```go
type SafeMap struct {
	Lock sync.RWMutex
	Data map[string]string
}

func (sm SafeMap) Get(k string) string {
	sm.Lock.RLock()
	defer sm.Lock.RUnlock()
	return sm.Data[k]
}

func (sm SafeMap) Set(k string, v string) {
	sm.Lock.Lock()
	defer sm.Lock.Unlock()
	sm.Data[k] = v
}

var safeMap = SafeMap{
	Data: make(map[string]string),
}
```

（PS：go 有两种锁：`Mutex` 和 `RWMutex`，`Mutex` 一个时刻内只能存在一个读或一个写，而 `RWMutex` 同一时刻支持多个读或一个写，由于缓存通常是读多于写，因此这里采用 `RWMutex`）

以 map 作缓存存在两个问题

1. 当 map 非常大时，会带来非常大的 GC 开销，性能损耗较大（在 go 1.5 后，如果 map 的 key value 都不包含指针，那么 GC 会忽略这个 map）
2. 前面针对并发读写冲突采用的是 RWMutex，支持了并发读，但写只能是串行，对有大量写的情况存在性能瓶颈

针对 map 的两点限制（无指针 & 串行写），BigCache 做了两点优化

1. 把 map 的 key value 转换成无指针存储（将缓存的 value 序列化成二进制的 []byte，并将此二进制追加一个全局的 []byte 中，map 中实际存储的是该二进制在全局 []byte 的下标）
2. 将 key 通过 hash 打散分散到各个子 map 中，减小锁的粒度，从而支持子 map 间并行写