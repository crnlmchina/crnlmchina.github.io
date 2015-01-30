## The Mostly Concurrent Collectors

> Java8中有两种并发收集器的实现：

- [CMS](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#concurrent_mark_sweep_cms_collector) 提供低的GC停顿，但应用需要与GC共享处理器资源
- [G1](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/g1_gc.html#garbage_first_garbage_collection) 适合多处理器上的大数据量应用，它会尽量使GC停顿时间接近期望值

### 并发收集器的代价
> 并发收集器以与应用共享处理器资源为代价缩短GC停顿时间。最明显的代价是在并发收集阶段使用1个或多个处理器。
> 在一个`N`核的系统中，占用的处理器比例中`K/N`，`1<=K<=ceiling(N/4)`。
> 由于在并发收集阶段与应用共享了处理器资源，从而实现了GC停顿时间的缩短，但应用的处理时间相较其它垃圾回收器会有少量的延迟。

> 在多核的系统中，应用的线程有可用的处理器资源，所以并发收集器不会“停止”应用。
> 这导致了低的GC停顿和可预见的应用的相对延迟，在处理器资源使用比较饱和的场景中更明显。
> 随着`N`变大，这种相对延迟会变小，使用并发收集器的收益会变大。
> 这种扩展也存在潜在的边界，参考[Concurrent Mode Failure](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#concurrent_mode_failure)。

> 因为至少有一核在并发收集阶段会被使用，所以在单核机器上并发收集器不会提供任何收益。但是有一种模式使和CMS或G1在单核或双核系统中能获得短暂的停顿，参考[Incremental Mode ](http://docs.oracle.com/javase/8/docs/technotes/guides/vm/gctuning/cms.html#CJAGIIEJ)。
> 这个特性在Java8中已被废弃，并且在将来的数个主版本后会被删除。