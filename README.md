# YCSB OBHBase 测试工具

YCSB (Yahoo! Cloud System Benchmark) 是一个用于测试云数据库性能的基准测试工具。本项目是YCSB的OBHBase绑定版本，用于测试OceanBase HBase兼容模式的性能。

## 项目结构

```
YCSB/
├── core/                    # YCSB核心模块
├── obhbase/                 # OBHBase绑定模块
├── workloads/               # 工作负载配置文件
├── build.sh                 # 编译打包脚本
├── run.sh                   # 运行测试脚本
└── build/                   # 构建输出目录
    └── obhbase-1.0-SNAPSHOT-jar-with-dependencies.jar
```

## 快速开始

### 1. 环境要求

- **Java**: JDK 1.7 或更高版本
- **Maven**: 3.x 版本
- **操作系统**: Linux/macOS/Windows

### 2. 编译打包

```bash
# 编译打包项目
./build.sh

# 仅清理构建产物
./build.sh clean
```

### 3. 配置workload

在使用前，需要配置workload文件中的OceanBase连接参数。编辑对应的workload文件（如`workloads/workload_put`）：
- put: workloads/workload_put
- read: workloads/workload_read
- scan: workloads/workload_scan
- load: workloads/workload_load

```bash
# 编辑workload文件
vim workloads/workload_put
```

#### OceanBase连接参数（必填）

| 参数名 | 说明 | 必填 |
|--------|------|------|
| `hbase.oceanbase.odpMode` | 连接模式选择 | 是 |
| `hbase.oceanbase.odpAddr` | ODP代理地址 | ODP模式必填 |
| `hbase.oceanbase.odpPort` | ODP代理端口 | ODP模式必填 |
| `hbase.oceanbase.paramURL` | 直连模式连接URL | 直连模式必填 |
| `hbase.oceanbase.sysUserName` | 系统租户用户名 | 直连模式必填 |
| `hbase.oceanbase.sysPassword` | 系统租户密码 | 直连模式必填 |
| `hbase.oceanbase.fullUserName` | 业务租户用户名 | 是 |
| `hbase.oceanbase.password` | 业务租户密码 | 是 |
| `hbase.oceanbase.database` | 数据库名 | 是 |
| `hbase.oceanbase.table` | 表名 | 是 |
| `hbase.oceanbase.columnFamily` | 列族名 | 是 |
PS:其他客户端参数设置，可以参考obkv-hbase-client和obkv-table-client支持的参数设置

#### YCSB测试参数

| 参数名 | 说明 | 默认值 |
|--------|------|--------|
| `operationcount` | 操作总数 | - |
| `recordcount` | 记录总数 | - |
| `insertstart` | 插入起始位置 | 0 |
| `insertcount` | 插入记录数 | 0 |
| `requestdistribution` | 请求分布模式 | uniform |
| `load` | 数据加载模式 | - |

注意：没有指定insertstart和insertcount的情况下
- put测试写入数据的起点是从recordcount开始
- load测试载入数据的起点是从0开始

### 4. 快速运行测试

```bash
# 数据加载
./run_fast_test.sh load

# 写入测试
./run_fast_test.sh put

# 读取测试
./run_fast_test.sh read

# 扫描测试
./run_fast_test.sh scan
```

## 脚本说明

### build.sh - 编译打包脚本

**功能**: 编译打包生成可执行的jar包

**参数**:
- 无参数: 执行完整编译打包流程
- `clean`: 仅执行清理操作

**输出**:
- `build/obhbase-1.0-SNAPSHOT-jar-with-dependencies.jar`

### run.sh - 运行测试脚本

**功能**: 运行YCSB性能测试

**参数**:
- `load`: 数据加载测试
- `put`: 写入性能测试
- `read`: 读取性能测试
- `scan`: 扫描性能测试

**命令格式**:
```bash
java -jar build/obhbase-1.0-SNAPSHOT-jar-with-dependencies.jar -P workloads/workload_xxx
```

## 自定义你的测试
参考这里以下说明：
```
> java -jar build/obhbase-1.0-SNAPSHOT-jar-with-dependencies.jar -h 
Usage: java com.yahoo.ycsb.Client [options]
Options:
  -threads n: execute using n threads (default: 1) - can also be specified as the 
        "threadcount" property using -p
  -target n: attempt to do n operations per second (default: unlimited) - can also
       be specified as the "target" property using -p
  -load:  run the loading phase of the workload
  -t:  run the transactions phase of the workload (default)
  -db dbname: specify the name of the DB to use (default: com.yahoo.ycsb.BasicDB) - 
        can also be specified as the "db" property using -p
  -P propertyfile: load properties from the given file. Multiple files can
           be specified, and will be processed in the order specified
  -p name=value:  specify a property to be passed to the DB and workloads;
          multiple properties can be specified, and override any
          values in the propertyfile
  -s:  show status during run (default: no status)
  -l label:  use label for status (e.g. to label one experiment out of a whole batch)

Required properties:
  workload: the name of the workload class to use (e.g. com.yahoo.ycsb.workloads.CoreWorkload)

To run the transaction phase from multiple servers, start a separate client on each.
To run the load phase from multiple servers, start a separate client on each; additionally,
use the "insertcount" and "insertstart" properties to divide up the records to be inserted
Unknown option -h
```
