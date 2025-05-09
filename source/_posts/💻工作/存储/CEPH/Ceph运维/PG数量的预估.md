---
title: PG 数量相关
toc: true
categories:
  - "\U0001F4BB 工作"
  - 存储
  - CEPH
  - Ceph 运维
---
PG 数量的设置牵扯到数据分布的均匀性问题。

预设 Ceph 集群中的 PG 数至关重要，公式如下: `（**结果必须舍入到最接近2的N次幂的值)**`

```plain
PG 总数 = (OSD 数 * 100) / 最大副本数
 ```

集群中单个池的 PG 数计算公式如下：`(**结果必须舍入到最接近2的N次幂的值)**`

```plain
PG 总数 = (OSD 数 * 100) / 最大副本数 / 池数
```

PGP 是为了实现定位而设计的 PG，PGP 的值应该和 PG 数量保持一致；pgp_num 数值才是 CRUSH 算法采用的真实值。
虽然 pg_num 的增加引起了 PG 的分割，但是只有当 pgp_num 增加以后，数据才会被迁移到新 PG 中，这样才会重新开始平衡。

**pg_num 计算器**

[https://ceph.com/pgcalc/](https://ceph.com/pgcalc/)
