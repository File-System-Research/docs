## 构建

本项目使用 CMake 作为项目构建工具。

### CMake 使用步骤

项目中 CMake 工具主要有两个使用阶段：

1. CMake Configuration：生成项目构建脚本。此阶段需要添加 CMake Configurate 参数。
2. CMake Build：编译生成项目目标文件。

### CMake Configurate 参数

1. `ROCKSDB_PLUGINS` ：字符串，以空格分隔的插件列表。可用的插件：
   1. `zenfs` ：原版 ZenFS
   2. `aquafs` ：初赛版本 AquaFS，小写
   3. `AquaFS` ：之后开发版本 AquaFS
   4. 举例：
      1. `ROCKSDB_PLUGINS="zenfs aquafs"` 加载原版和初赛版本 AquaFS 作为插件
      2. `ROCKSDB_PLUGINS="zenfs AquaFS"` 加载原版和之后开发版本 AquaFS 作为插件
2. `AQUAFS_EXPORT_PROMETHEUS` ：是否开启 Prometheus 数据采集。`AQUAFS_EXPORT_PROMETHEUS=1`
3. `WITH_LIBURING` ：RocksDB 的 `PosixFilesystem` 是否开启 io-uring 加速。与 AquaFS 冲突，务必关闭。`WITH_LIBURING=OFF`
4. `WITH_SNAPPY` ：使用 libsnappy 压缩 SST，需要开启。

如果需要使用之后开发版本的 AquaFS，使用的参数为：

```shell
-DROCKSDB_PLUGINS="AquaFS zenfs" -DWITH_SNAPPY=1 -DAQUAFS_EXPORT_PROMETHEUS=1 -DWITH_LIBURING=OFF
```

如果需要使用初赛版本 AquaFS，使用参数为：

```shell
-DROCKSDB_PLUGINS="aquafs zenfs" -DWITH_SNAPPY=1 -DAQUAFS_EXPORT_PROMETHEUS=1 -DWITH_LIBURING=OFF
```

### CMake Configurate

#### 命令行方式

```shell
# 运行目录：rocksdb
# cmake -B build -S . <CMake Configurate 参数>
# for newer version
cmake -B build -S . -DROCKSDB_PLUGINS="AquaFS zenfs" -DWITH_SNAPPY=1 -DAQUAFS_EXPORT_PROMETHEUS=1 -DWITH_LIBURING=OFF
# for old version
cmake -B build -S . -DROCKSDB_PLUGINS="aquafs zenfs" -DWITH_SNAPPY=1 -DAQUAFS_EXPORT_PROMETHEUS=1 -DWITH_LIBURING=OFF
```

#### VsCode CMake 插件方式

见 JSON 文件。TBD。

### CMake Build

#### 命令行

```shell
# 运行目录：rocksdb
cmake --build build
```

#### VsCode CMake 插件方式

TBD。
