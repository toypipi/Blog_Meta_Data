---
title: Python Mysql 数据库连接和查询
categories:
  - 技术
tags:
  - Mysql
  - 查询
  - 连接
date: 2020-04-17 10:14:23
---

---
# 使用 PyMySQL
<!-- more -->
```python
import pymysql

db = pymysql.Connect(
    host="192.168.1.123",
    port=3306,
    user="root",
    password="Dp330651!",
    db="taobao",
    charset="utf8"
)

# 查询
cur = db.cursor()
sql = 'select * from sale_data where 位置 in(%s,%s,%s)'
cur.execute(sql, ("江苏", "浙江", "上海"))
result = cur.fetchall()
for item in result:
    print(item)

# 删除
sql = 'delete from sale_data where 价格 < 100'
cur.execute(sql)
db.commit()

# 修改
sql = 'update sale_data set 位置=%s where 位置 in (%s,%s,%s)'
cur.execute(sql,("江浙沪","江苏", "浙江", "上海"))
db.commit()

# 插入
sql = 'insert into sale_data(宝贝,价格,成交量,卖家,位置) values(%s,%s,%s,%s,%s)'
cur.execute(sql,("2017夏季妈妈装 40-50 岁中老年女装连衣裙",298,10000,"xxx 天猫旗舰店","北京"))
db.commit()

# 关闭游标和数据库连接
cur.close()
db.close()
```
# 使用 SQLAlchemy
```python
from sqlalchemy import Column, String, create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base


class Procuct(object):
    def __init__(self, id, name, type):
        self.id = id
        self.name = name
        self.type = type


# 创建对象的基类
Base = declarative_base()


# 定义 Product 对象
class Product(Base):
    # 表名
    __tablename__ = 'Product'
    # 表结构
    id = Column(String(20), primary_key=True)
    name = Column(String(20))
    type = Column(String(20))


# 初始化数据库连接
engine = create_engine('mysql+pymysql://root:Dp330651!@192.168.1.123:3306/taobao')
# 创建 DBSession 类型
DBSession = sessionmaker(bind=engine)
# 插入一条记录
session = DBSession()
new_user = Product(id='19558276', name='北京5天4晚自由行 含双早 入住前门商业街豪华酒店 含双人故宫电子门票', type='景+酒')
session.add(new_user)
session.commit()
# 查询
student = session.query(Product).filter(Product.id=='19558276').one()
print('name: ', student.name)
print('type: ', student.type)
# 更新
session.query(Product).filter(Product.id=='19558276').update({Product.name:"北京5天4晚自由行，景+酒套餐"})
session.commit()
# 删除数据
session.query(Product).filter(Product.id=='19558276').delete()
session.commit()
session.close()
```