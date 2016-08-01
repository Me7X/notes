Trigger
===




# **概述**

### **概念**

保证数据完整性的一种方法，是与表时间相关的特殊的存储过程

它的执行是由事件来触发的，如，当对一个表进行操作(insert, delete, update)时就会激活触发器

### **触发器类型：**

* DDL触发器

* DML触发器


### **触发器附加的事件：**

* INSERT触发器

* DELETE触发器

* UPDATE触发器

### **使用触发器实施数据完整性规则**

1. 处理来自于其他表的需求
2. 使用触发器来检查更新的变化
3. 将触发器用于自定义错误消息
4. 以上任意类型的混合

# **创建**

    CREATE TRIGGER <trigger_name>
        on [<schema name.>]<table or view name>
        [WITH ENCRYPTION | EXECUTE AS <CALLER | SELF | <user>>]
        {{{FOR|AFTER} <[DELETE] [,] [INSERT} [,} [UPDATE]>} | INSTEAD OF}
        [WITH APPEND]
        [NOT FOR REPLICATION]
    AS
        <<sql statements> | EXTERNAL NAME <assemnly method specifier>>
   
电影表、电影类别表
```sql
//insert 插入记录到电影表中，若该记录所属类别在电影类别表中不存在，则无法插入
//inserted表存储当前所要插入的数据信息，作用域仅为当前的trigger
create tigger trg_电影_insert on 电影 for insert
as
    begin
        declare @类别ID int
        select @类别ID=类别ID from inserted
        if not exists(select id from 电影类别 where ID=@类别ID)
            begin
                raiserror('no exit',16,1)
                rollback
            end
    end
    
insert into 电影 (类别ID，电影名称) value (90, 'tomorrow')
go

//delete 删除电影类别表中的某一纪录，电影表中的先关信息同时被删除
//deleted表存储当前所要删除的数据信息，作用域仅为当前的trigger
create trigger trg_电影类别_delete on 电影类别 for delete 
as
    begin
        delete from 电影 where 类别ID in (select ID from deleted)
    end
go

//update 对电影类别表中的值进行修改，与之相关的电影表中的信息进行修改(ID：电影类别表的主键)
//SQL Server中并不存在对应的updated表用于存储相应的update的信息。SQL Server执行修改功能时，先delete，后insert
create trigger trg_电影类别_update on 电影类别 for update 
as
    begin
        declare @类别ID_old int, @类别ID_new int
        select @类别ID_old=ID from deleted
        select @类别ID_new=ID from inserted
        update 电影 set 类别ID=@类别ID_new where 类别ID=@类别ID_old
    end
```
