# 分布式系统 - 分布式文件系统

## 1. 分布式文件系统的目标：

- 位置透明：统一的文件命名空间，其不包含文件物理的信息
- 并发透明：一个用户的文件操作不会干扰（interfered）的其它用户
- 失效透明：当系统的一部分失效的时候，剩下部分的系统仍然可以提供文件系统。
- 一致性（Heterogeneity）：在不同的操作系统和沟通协议之间互操作
- 伸缩性：系统性能不会随着系统尺寸的增长而恶化（deteriorate）

## 2. 分布式文件系统组件：

- 扁平文件服务：提供一组简单的通用操作集合，其可以访问文件的属性和数据，被UFID索引
- 目录服务：从UFID映射到文本名
- 客户端模块：允许访问文件和目录服务，通过一种API
- ![]()
- 在客户端模块构造层次化（hierarchical）文件系统
    - 一个层次化文件名称系统可以被在客户端使用名称和目录服务构造
    - 目录树的根是一个著名 `UFID` 目录
    - 路径解析功能由客户端模块提供

## 3. 独立文件系统和分布式文件系统的区别：

- 文件服务和目录服务的分离：
    - 文件服务被构造地更加通用、目录服务的设计更加灵活
    - 其从文件服务器上卸载（off load）目录服务
- 服务器无状态，从客户端模拟：
    - 文件服务器被轻量化地构造，将大多数地工作留到客户端
    - 服务器能够从失败中高效地恢复，不需要恢复客户端状态
    - 客户端模块模拟（emulate）传统的文件系统接口
- 可重复的操作：
    - 文件操作是幂等的（idempotent），允许使用最少使用一次 `RPC语义`（semantics）
    - 操作 `create` 是额外的（但对用户无副作用）
- 能力（Capabilities）和访问控制：
    - 客户端通过 `RPC` 和目录服务、文件服务交互。有一个安全漏洞（loophole），允许未授权的用户来使用其他 `ID` 非法访问文件
    - 能力（以一种 `UFID` 的形式）是一个数字键，被在分布式系统客户端用来鉴定和访问文件
    - 能力包含信息，包括文件位置（`group ID`）、`FID` 和访问控制
        - 
        - `capacity = id + location + security + access control`
    - 目录服务器客户端ID和访问权限生成文件的 `capacity`
    - `capacity` 被用以进行文件的访问控制
    - `capacity` 必须拥有加密域，来防止被他人修改或者误用。加密密钥在文件服务和目录服务之间共享。

## 4. 文件表示：

- 一个目录块（`index block`) (类似于 `UNIX` 中的 `inode`）被用以从每个文件指向数据块
- 定位目录块使用 `UFID` ，为了高效的搜索，UFID可以使用B树来阻止：
    - 从 `UFID` 到对应的目录块的索引被储存在磁盘中，其尺寸对于磁盘来说太大
- 服务器位置（文件的组ID）被集成在 `UFID` 中。客户端需要知晓服务器位置来使用 `RPC` 进行文件访问。它可以以下列方式对任何 `RPC` 到任何服务器：
    - `PortID = GetServerPort(FileGroupID)`
- 空间泄露（Space Leaks）：
    - 某些文件仍然在文件服务器上存在，但是其目录可能已经被移除，导致它再也不会被访问。
    - 该问题被忽视，或者在大多数情况下被预防：
    - “生存时间”方法旨在解决该问题：
        - 目录服务器周期性地访问所有文件，例如：为在目录服务器注册的全体文件设置生命周期
        - 文件服务器以某个时间周期来减低所有文件的生命周期，每次访问都会更新该周期
        - 生命周期到0的文件将会被移除

## 5. 案例：

### 5.1 谷歌文件系统

- `GFS` 旨在提供错误容忍和对巨型文件的并发访问。它包含 `GFS客户端`、单个`GFS master`和许多`GFS块服务`器。
- 该客户端要求 `master`（目录服务器）以文件名和块目录（从用户的比特偏移到文件和定长块尺寸的翻译，`64MB`）
- `master`复制块句柄和位置到副本
- 客户端请求具有块句柄和块内字节范围的副本之一进行数据访问。
- ![]()

### 5.2 Sun网络文件系统（NFS）

#### 5.3.1 特点：

- NFS被开发来允许将远程文件系统挂载到本地文件夹上。它有助于在网络上（facilitates）快速、无缝（seemless）共享文件。
- 它是拥有整洁和简单接口的开放标准。
- 它允许为分布式文件系统定义的抽象文件服务模型。

#### 5.3.2 服务操作：

![]()

#### 5.3.3 NFS 架构：

![]()

#### 5.3.4 NFS 特征

- `NFS` 客户端模块驻留在 `UNIX` 内核中。它模拟了 `UNIX` 文件系统的语义（semantics）
    - 应用程序可以在 `NFS` 中没有任何改变地运行
    - 一个客户端模块可以服务所有的用户进程。
- `NFS` 的客户端和服务器模块通过 `NFS协议` 和 `mount协议`（通过 `Sun PRCs`）
- `NFS` 服务器是无状态的。它提供了可靠文件操作。

#### 5.3.5 NFS 虚拟文件系统：

- `NFS` 引入了虚拟文件系统（`VFS`）层。它为每个现在打开、挂载的文件维护虚拟 `i-node` 节点。
- `V-node` 是操作系统中维护的数据结构，而不是存储在磁盘中。它指示（indicates）是本地还是远程节点：
    - 对于本地文件，它包含 `i-node`
    - 对于远程文件，它包含以下文件句柄：
        - ![]()

#### 5.3.6 挂载服务：

- 每个 `NFS` 服务在应用层运行一个挂载进程
- 客户端要求使用远程主机名、路径和本地文件夹挂载远程文件系统
- 服务端挂载进程和客户端挂载进程使用挂载协议（`RPCs`），返回客户端挂载文件夹的文件句柄
- 客户端在 `v-node` 为文件访问子序列维持着挂载文件夹的文件句柄

#### 5.3.7 文件分享（导出挂载）：服务端

- `share`：导出、或者使得某个资源做好挂载的准备，通过某个被指定为 `FSType` 的类型，或在 `/etc/dfs/fstype` 文件中列出的第一个files-system-file：
    - `/etc/dfs/fstype`: 安装在系统中基础工具的列表。对每个已安装的 `DFS 类型`，都会以其文件系统类型开始，后跟表述性文本
    - `/etc/dfs/dfstab`: 在启动期被执行的共享命令列表
    - `/etc/dfs/sharetab`: 共享文件系统的系统记录
- `dfshares`：来自远程或者本地系统的可用资源列表
    - 对于每个资源，这些域都是资源服务器访问透明的

#### 5.3.8 挂载远程文件：客户端

- `mount` `mountall`
- 挂载进程将一个命名目录在路径名位置挂载点挂载到文件系统层次
- `/etc/vfstab`: 对每个文件系统默认参数的列表
    - 每个项包含空间分割的域
        - 要挂载的设备，要fsck的设备，挂载点，文件系统类型，fsck遍数，，启动挂载，挂载选项
    - 这些文件系统当其启动挂载域为是的时候，会在启动时由 `mountall` 启动
- `/etc/mnttab`: 当前已经挂载的文件系统记录
    - 每个项包含着 TAB 分割的域
        - 声明，挂载点，文件系统类型，选项，时间

#### 5.3.9 自动挂载：

- 函数：`automount`
- 静态挂载的问题
    - 如果客户端拥有多个远程资源待加载，初始化时候可能耗时很长（excessive）
- 自动挂载：应客户端的需求来进行挂载和解挂在来响应
    - 自动挂载程序根据配置文件（`/etc/auto_master`和`/etc/auto_home`) 来运作
    - 当空挂载点被引用：
        - 系统向每个服务器发送一个探测（probe）消息
        - 第一回复将会获胜：接着使用挂载服务器来挂载第一个响应的服务器
    - 每 10 分钟尝试解挂
- 自动挂载器（`/usr/lib/autofs/automountd`) 在系统启动时启动，其是运行在客户端计算机的守护进程
