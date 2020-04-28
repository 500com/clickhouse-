# 删除表节点
1.  Local表节点drop删除
    *   删除表结构
    *   删除节点数据目录
2.  Replica表节点drop删除
    *   删除表结构
    *   删除节点数据目录
    *   删除zk上对应此replica的节点信息（如果只有一个replica 删除整个表指定的zk路径）
3.  Distribute节点drop删除
    *   对Distribute表删除不影响底层表
    *   如果对底层表删除，如果此底层表只有一个副本，查询Distribute表将报异常
    *   可以对remote_server配置进行更改，增删shard和replica


# 节点失败-Local表还原
1.  复制ck数据目录里的metadata中保存的schema信息到新节点
2.  复制ck数据目录里的data目录中对应表的数据到新节点
3.  新节点启动

# Local和Replica表转化
1.  local -> Replica
    将Local表的数据目录partitions copy到Replica表的Detach目录，然后做Partition的attach操作
2.  Replica -> local
    直接将Replica数据目录copy到local表的数据目录
     
# Volume和Disk
> Disk 块设备
> Default Disk 在config.xml里设置的path
> Volume 顺序的Disk集合，写入时在disks之间进行round robin
> Storage policy 在建表时指定setting设置


```
<yandex>
<storage_configuration>
    <disks>
        <disk1> <!-- disk name -->
            <path>/data1/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk1>
        <disk2>
            <path>/data2/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk2>
        <disk3>
            <path>/data3/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk3>
        <disk4>
            <path>/data4/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk4>
        <disk5>
            <path>/data5/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk5>
        <disk6>
            <path>/data6/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk6>
        <disk7>
            <path>/data7/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk7>
        <disk8>
            <path>/data8/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk8>
        <disk9>
            <path>/data9/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk9>
        <disk>
            <path>/data/clickhouse/</path>
            <keep_free_space_bytes>10485760</keep_free_space_bytes>
        </disk>
    </disks>

    <policies>
        <hdd_round_robin>
            <volumes>
                <single> <!-- volume name -->
                    <disk>disk1</disk>
                    <disk>disk2</disk>
                    <disk>disk3</disk>
                    <disk>disk4</disk>
                    <disk>disk5</disk>
                    <disk>disk6</disk>
                    <disk>disk7</disk>
                    <disk>disk8</disk>
                    <disk>disk9</disk>
                    <disk>disk</disk>
                </single>
            </volumes>
        </hdd_round_robin>
        <!--
        <moving_from_ssd_to_hdd>
            <volumes>
                <hot>
                    <disk>disk1</disk>
                    <max_data_part_size_bytes>1073741824</max_data_part_size_bytes>
                </hot>
                <cold>
                    <disk>disk2</disk>
                </cold>
            </volumes>
            <move_factor>0.2</move_factor>
        </moving_from_ssd_to_hdd>
        -->
    </policies>

</storage_configuration>
</yandex>
```

Policy里的Volumes有顺序，满足一定条件会将数据移动到下一个Volumes，或者手动执行移动操作。

# 表备份
1.  clickhouse-copier 官方工具
2.  clickhouse-backup 第三方 只支持local表
