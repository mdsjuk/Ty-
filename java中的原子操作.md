

- 除long和double之外的基本类型的赋值操作除了long和double之外的基本类型的赋值操作
  因为long和double类型是64位的，所以它们的操作在32位机器上不算原子操作，而在64位的机器上是原子操作。
- 所有引用reference赋值操作
- java.concurrent.Atomic.* 包中所有类的一切操作