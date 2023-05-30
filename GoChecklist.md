## Go Checklist

### sql相关
- gorm 在model作为参数的时候，如果结构体为空，会默认不参与sql语句构建，导致where相当于是没有的，参考[p神的知识星球](https://t.zsxq.com/0egDEJzko)
- 数据库操作First函数注入
```go
db.First(&user, c.Query("id"))
```
