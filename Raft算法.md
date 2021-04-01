# Raft

节点状态: 

- leader
- candidate
- follow

## *Leader Election*

​	无leader节点时 每个节点向其他节点索要选票,得票数超过一半的节点选为leader节点 

​	倒计时结束 节点变为candidate 节点 发送vote请求投票

​	每个节点上都有一个倒计时器 (Election Timeout)，时间随机在 150ms 到 300ms 之间。有几种情况会重设 Timeout：

1. 收到选举的请求
2. 收到 Leader 的 Heartbeat 

##  *Log Replication*

每次更新 要保证半数以上的follower完成更新才响应回复 

网络被分区后的节点 会产生多个leader 恢复网络后 leader会降级 只保留一个leader 保证数据修改一致性

<img src="C:\Users\25431\AppData\Roaming\Typora\typora-user-images\image-20210331144856804.png" alt="image-20210331144856804" style="zoom:67%;" />

​	每条日志除了存储状态机的操作指令外（譬如 add 这种赋值指令），还会拥有一个**唯一的整数索引值（log index）\**来表明它在日志集合中的位置。此外，每条日志还会存储一个\** term 号**（日志条目方块最上方的数字，相同颜色 term 号相同），该 term 表示 leader 收到这条指令时的当前任期，term 相同的 log 是由同一个 leader 在其任期内发送的。

当一条日志被 leader 节点认为可以安全的 apply 到状态机时，称这条日志是**committed**（上图中的**committed entries**）。那么什么样的日志可以被 commit 呢？答案是**：当 leader 得知这条日志被集群过半的节点复制成功时**。因此在上图中我们可以看到 (term3, index7) 这条日志以及之前的日志都是 committed，尽管有两个节点拥有的日志并不完整。

raft动画:https://raft.github.io/

leader会向follower发送 心跳包中会携带当前**已经安全复制（我们称之为 committed）**的日志索引，此处为 (term2, index1)。

![image-20210331150142443](C:\Users\25431\AppData\Roaming\Typora\typora-user-images\image-20210331150142443.png)

​	要使得 follower 的日志集合跟自己保持完全一致，leader 必须先找到二者间**最后一次**达成一致的地方。因为一旦这条日志达成一致，在这之前的日志一定也都一致（回忆下前文）。这个确认操作是在 AppendEntries RPC 的一致性检查步骤完成的。

​	Leader 针对每个 follower 都维护一个 **next index**，表示下一条需要发送给该follower 的日志索引。当一个 leader 刚刚上任时，它初始化所有 next index 值为自己最后一条日志的 index+1。但凡某个 follower 的日志跟 leader 不一致，那么下次 AppendEntries RPC 的一致性检查就会失败。在被 follower 拒绝这次 Append Entries RPC 后，**leader 会减少 next index 的值并进行重试。**

​	**最终一定会存在一个 next index 使得 leader 和 follower 在这之前的日志都保持一致。极端情况下 next index 为1，表示 follower 没有任何日志与 leader 一致，leader 必须从第一条日志开始同步。**

​	针对每个 follower，一旦确定了 next index 的值，leader 便开始从该 index 同步日志，follower 会删除掉现存的不一致的日志，保留 leader 最新同步过来的。

​	整个集群的日志会在这个简单的机制下自动趋于一致。此外要注意，**leader 从来不会覆盖或者删除自己的日志**，而是强制 follower 与它保持一致。