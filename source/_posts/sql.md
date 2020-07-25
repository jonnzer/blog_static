---
title: sql
date: 2018-05-02 22:29:06
tags: sql

---

#### 为了新项目的sql存储，我觉得还是很有必要学下sql
    so～～ 开始！

    刚开始就报了错，Access denied for user 'root'@'localhost' (using password: NO)
    用这个命令(登录MYSQL的意思)，mysql -uroot -p
    然后重新输一遍密码


#### 语法
    (1) sql命令必须带;分号，否则提示sql语句错误。
    (2) 退出mysql： exit;
    (3) MYSQL删除表 语法：drop table 'table_name';
    (4) 创建数据库： create database databaseNname;
    (5) show databases;  是用来看登陆后的用户有多少关联的数据库
    (6) use database's name; 进入该数据库
    (7) show tables; 查看该数据库的表格
    (8) desc table's name; 查看该表的结构




#### 调试方案（基于mac os）

```sql
        mysql -u root -p
        create database name;
        use name;
```
        之后，可以在命令行 source (将sql文件拖拽进来，去掉file://)
        所以你可以本地写好sql文件，在命令行执行并检验

(2) 用sequel pro工具来查看sql图形化表格，对照看在命令行写的sql是否起效


#### 数据类型
<img src='./package.png' style="width:50%;height:60%;">
参考链接 http://www.runoob.com/mysql/mysql-data-types.html

#### 常规操作
```sql
        CREATE TABLE IF NOT EXISTS `movie` (
            `id` INT(11) NOT NULL  AUTO_INCREMENT,
            `movie_name` CHAR(255) NOT NULL,
            `high_image` CHAR(255) NOT NULL,
            `content` CHAR(255) NOT NULL,
            PRIMARY KEY (`id`)
        ) ENGINE=InnoDB AUTO_INCREMENT = 3 DEFAULT CHARSET=utf8;
```
下面是增删改查，最重要的部分：
### 增加，批量
```sql (结构：INSERT INTO 表名 （要增加的列名）VALUES （对应的值）)
        INSERT INTO movie (movie_name,high_image,content) VALUES
            ('头号玩家','www.baidu.com/a.jpg','VR高级模拟现实游戏'),
            ('斗破苍穹','www.baidu.com/b.jpg','热血国漫'),
            ('仙剑奇侠传','www.baidu.com/c.jpg','良心国产仙侠');
```

### 改 (结构：UPDATE 表名 SET 要修改的列,逗号隔开 WHERE 匹配条件 )
```sql
     UPDATE movie SET high_image='www.touhaowanjia.com/1.jpg' WHERE movie_name='头号玩家';
```

### 删 (结构：DELETE FROM 表名 WHERE 匹配条件  )
```sql
    DELETE FROM movie WHERE id=9;

```

### 查 (结构：SELECT 列 FROM 表名 WHERE 匹配条件  )
（1）查询所有列
```sql
    SELECT * FROM movie;
```
（2）查询指定列
```sql
   SELECT content FROM movie;

```

### 报错处理：
    具体就看报错语句，提示在哪里附近报错，第几行，near 什么
