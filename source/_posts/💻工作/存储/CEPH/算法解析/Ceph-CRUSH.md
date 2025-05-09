---
title: Ceph CRUSH 算法
toc: true
tags:
  - ceph
categories:
  - "\U0001F4BB 工作"
  - 存储
  - CEPH
  - 算法解析
date: 2020-05-23 11:02:28
---

# 1. 数据分布算法挑战
 - 数据分布和负载均衡
   - 数据分布均衡，使数据能均匀的分布到各个节点上；
   - 负载均衡，使数据访问读写操作的负载在各个节点和磁盘的负载均衡；
 - 灵活应对集群伸缩
   - 系统可以方便的增加或者删除节点设备，并且对节点失效进行处理；
   - 增加或者删除节点设备后，能自动实现数据的均衡，并且尽可能少的迁移数据；
- 支持大规模集群
   - 要求数据分布算法维护的元数据相对较小，并且计算量不能太大。随着集群规模的增加，数据分布算法开销相对比较小；

# 2. Ceph CRUSH 算法说明
 - CRUSH 算法的全称为：Controlled Scalable Decentralized Placement of Replicated Data，可控的、可扩展的、分布式的副本数据放置算法；
 - pg 到 OSD 的映射的过程算法叫做 CRUSH 算法(一个 Object 需要保存三个副本，也就是需要保存在三个 osd 上)；
 - CRUSH 算法是一个伪随机的过程，他可以从所有的 OSD 中，随机性选择一个 OSD 集合，但是同一个 PG 每次随机选择的结果是不变的，也就是映射的 OSD 集合是固定的；

# 3. Ceph CRUSH 算法原理
**CRUSH 算法因子：**

 - 层次化的 Cluster Map
反映了存储系统层级的物理拓扑结构，定义了 OSD 集群具有层级关系的静态拓扑结构，OSD 层级使得 CRUSH 算法在选择 OSD 时实现了机架感知能力，也就是通过规则定义， 使得副本可以分布在不同的机架、不同的机房中、提供数据的安全性 ；
 - Placement Rules
决定了一个 PG 的对象副本如何选择的规则，通过这些可以自己设定规则，用户可以自定义设置副本在集群中的分布；

**cluster map**

Ceph 依赖于 Ceph 客户端和 OSD ，因为它们知道集群的拓扑，这个拓扑由 5 张图共同描述，统称为“集群运行图”：

1. **Montior Map：** 包含集群的 `fsid` 、位置、名字、地址和端口，也包括当前版本、创建时间、最近修改时间；使用 `ceph mon dump --format json-pretty` 查看 monitor map；
2. **OSD Map：** 包含集群 `fsid` 、创建时间、最近修改时间、存储池列表、副本数量、归置组数量、 OSD 列表及其状态（如 `up` 、 `in` ）；使用 `ceph osd dump --format json-pretty`  查看 osd map；
3. **PG Map：**：包含归置组版本、其时间戳、最新的 OSD 运行图版本、占满率、以及各归置组详情，像归置组 ID 、 up set 、 acting set 、 PG 状态（如 `active+clean` ），和各存储池的数据使用情况统计；
4. **CRUSH Map：**：包含存储设备列表、故障域树状结构（如设备、主机、机架、行、房间、等等）、和存储数据时如何利用此树状结构的规则；要查看 CRUSH 规则，执行 `ceph osd getcrushmap -o {filename}` 命令；然后用 `crushtool -d {comp-crushmap-filename} -o {decomp-crushmap-filename}` 反编译；然后就可以用 `cat` 或编辑器查看了；
5. **MDS Map：** 包含当前 MDS 图的版本、创建时间、最近修改时间，还包含了存储元数据的存储池、元数据服务器列表、还有哪些元数据服务器是 `up` 且 `in` 的。要查看 MDS 图，执行 `ceph mds dump --format json-pretty` ；

各运行图维护着各自运营状态的变更， Ceph monitor 维护着一份集群运行图的主拷贝，包括集群成员、状态、变更、以及 Ceph 存储集群的整体健康状况。

## 3.1 层级化的 Cluster Map
![ceph_crush.png](https://upload-images.jianshu.io/upload_images/2099201-f0f7321a9e37361f.png)

CRUSH Map 是一个树形结构，OSDMap 更多记录的是 OSDMap 的属性(epoch/fsid/pool 信息以及 osd 的 ip 等等)；

叶子节点是 device（也就是 osd），其他的节点称为 bucket 节点，这些 bucket 都是虚构的节点，可以根据物理结构进行抽象，当然树形结构只有一个最终的根节点称之为 root 节点，中间虚拟的 bucket 节点可以是数据中心抽象、机房抽象、机架抽象、主机抽象等；


## 3.2 数据分布策略 Placement Rules
**数据分布策略 Placement Rules 主要有特点：**

- 从 CRUSH Map 中的哪个节点开始查找
- 使用那个节点作为故障隔离域
- 定位副本的搜索模式（广度优先 or 深度优先）

```plain
rule replicated_ruleset  #规则集的命名，创建pool时可以指定rule集
{
    ruleset 0                #rules集的编号，顺序编即可   
    type replicated          #定义pool类型为replicated(还有erasure模式)   
    min_size 1                #pool中最小指定的副本数量不能小1
    max_size 10               #pool中最大指定的副本数量不能大于10       
    step take default         #查找bucket入口点，一般是root类型的bucket    
    step chooseleaf  firstn  0  type  host #选择一个host,并递归选择叶子节点osd     
    step emit        #结束
}
```

## 3.3 Bucket 随机算法类型
![ceph_bucket.png](https://upload-images.jianshu.io/upload_images/2099201-ac18dabc9fb44d20.png)

 - 一般的 buckets：适合所有子节点权重相同，而且很少添加删除 item。
 - list buckets：适用于集群扩展类型。增加 item，产生最优的数据移动，查找 item，时间复杂度 O(n)。
 - tree buckets：查找负责度是 O (log n), 添加删除叶子节点时，其他节点 node_id 不变。
 - straw buckets：允许所有项通过类似抽签的方式来与其他项公平“竞争”。定位副本时，bucket 中的每一项都对应一个随机长度的 straw，且拥有最长长度的 straw 会获得胜利（被选中），添加或者重新计算，子树之间的数据移动提供最优的解决方案。


# 4. CRUSH 算法案例
**说明：**
集群中有部分 sas 和 ssd 磁盘，现在有个业务线性能及可用性优先级高于其他业务线，能否让这个高优业务线的数据都存放在 ssd 磁盘上。

**普通用户：**
![ceph_sas.png](https://upload-images.jianshu.io/upload_images/2099201-1bd6980a2141bc51.png)

**高优用户：**
![ssd.png](https://upload-images.jianshu.io/upload_images/2099201-127c6f8a40938233.png)

**配置规则：**
![ceph_crush1.png](https://upload-images.jianshu.io/upload_images/2099201-0084962b3a7847b4.png)
