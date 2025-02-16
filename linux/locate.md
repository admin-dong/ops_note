### `locate` 命令的特点：

- **快速**: `locate` 使用预先构建的数据库进行搜索，因此比 `find` 命令快得多。
- **简单**: 只需提供文件名作为参数即可。
- **全局搜索**: 默认情况下，`locate` 会搜索整个文件系统。

```
sudo apt-get update
sudo apt-get install mlocate
```

第一次用

```
sudo updatedb
```

假设你想找到 `libaio.so.1` 文件的位置，你可以这样运行命令：

```
sudo locate libaio.so.1
```

