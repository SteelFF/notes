在数据库中，进行 join 操作时，通常会有一个驱动表（Driving Table）和一个被驱动表（Driven Table）。驱动表是在 join 操作中起主导作用的表，被驱动表则根据驱动表的连接条件进行匹配。

1. **驱动表（Driving Table）**：
    
    - 驱动表是在 join 操作中起主导作用的表，它提供了连接条件的基础。
    - 驱动表的数据会被保留，而且通常在 join 过程中会被广播到其他节点或者用于其他操作。
2. **被驱动表（Driven Table）**：
    
    - 被驱动表是根据驱动表的连接条件进行匹配的表，它的数据会根据连接条件进行匹配并加入到结果集中。
    - 被驱动表的数据通常不会被广播到其他节点，而是在本地节点上进行匹配操作。

在实际数据库查询中，通常优化器会根据查询条件、索引情况、数据量等因素来确定哪个表作为驱动表，哪个表作为被驱动表。选择合适的驱动表可以帮助优化查询性能，减少数据传输和处理的开销。