## 声明
forked from [noplay/python-mysql-replication](https://github.com/noplay/python-mysql-replication)
用于解析MySQL binlog存储并生成SQL，详见[nloneday/binlog-repository](https://github.com/nloneday/binlog-repository)

## 定制

修改默认字段解析数据类型，便于转储、生成SQL等

1. decimal
    - `python.Decimal --> str` (没有可用于文本显示的数据类型，float会改变数值)
    ```
    Decimal('11.2') --> '11.2'
    ```

2. date, time, datetime
    - `python.datetime.* --> str (%Y-%m-%d %H:%M:%S.%f`)
    ```
    datetime(1984, 12, 3, 12, 33, 7) --> '1984-12-03 12:33:07'
    ```
    
3. set
    - `python.set --> str`
    ```
    set(['a', 'ba', 'c']) --> 'a,ba,c'
    ```
    
4. json
    - `python.dict(binary key and value) --> python.dict`
    ```
    {b"my_key": b"my_val", b"my_key2": b"my_val2"} --> {"my_key": "my_val", "my_key2": "my_val2"}
    ```
5. blob
    - `python.bytes --> '0x' + python.bytes.hex()`
    ```
    GEOMETRY, TINY_BLOB, MEDIUM_BLOB, BLOB, LONG_BLOB
    ```
## 测试

1. 修改数据库连接信息
```python
# pymysqlreplication/tests/base.py

class PyMySQLReplicationTestCase(base):
    def ignoredEvents(self):
        return []

    def setUp(self):

        db = os.environ.get('DB')
        # default
        self.database = {
            "host": "localhost",
            "user": "root",
            "passwd": "123456",
            "port": 3306,
            "use_unicode": True,
            "charset": "utf8",
            "db": "pymysqlreplication_test"
        }
```    

2. 运行单元测试
```shell
# pymysqlreplication/tests/test_data_type.py

python pymysqlreplication/tests/test_data_type.py
```

## 安装
```shell
pip uninstall -y mysql-replication
python setup.py build
python setup.py install
```
