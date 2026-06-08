# 建立数据库连接

**连接时发生了什么？**

```
import ( 
	"gorm.io/driver/mysql" 
	"gorm.io/gorm" 
) 
func main() { 
	dsn:="user:pass@tcp(127.0.0.1:3306)
	/dbnamecharset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{}) }
```

```
//gorm.Open函数签名
func Open(dialector Dialector, opts ...Option)(db *DB, err error)
```

mysql.Open 解析dsn然后返回一个dialector

在gorm.Open中 执行Dialector.Initialize() 建立实际连接返回gorm.DB

实际上在Initialize中还是通过sql标准库建立连接

# 模型&表，gorm.Find()时发生了什么

模型可以通过生成或自定义的规则得到表名，默认下MTest -> m_tests

gorm.Find()查询表:

Find函数的第一个参数是接收查询结果的参数，而并不是通过该参数指定的数据表。当没有显式的指定Model时，gorm的查询会自动地将Dest参数值赋值给Model。然后，查询函数会从Model解析表名。如果从Model中解析不到对应的表名，就会报错。

Find函数查询一行和多行数据的区别,其本质上是扫描符合条件的所有数据，最后根据是否是切片类型来返回数据而已。


# Find(),First(),Last(),Take()

直接通过sql语句理解，按顺序依次是：

接收的sql语句:SELECT * FROM `m_test`

接收的sql语句:SELECT * FROM `m_test` ORDER BY `m_test`.`id` LIMIT 1

接收的sql语句:SELECT * FROM `m_test` ORDER BY `m_test`.`id` DESC LIMIT 1

接收的sql语句:SELECT * FROM `m_test` LIMIT 1
