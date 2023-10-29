CREATE SEQUENCE 
	tid_seq
INCREMENT 1
MINVALUE 1
MAXVALUE 9223372036854775807
START WITH 1
CACHE 1; 

INCREMENT BY ： 每次序列增加（或减少）的步长
MINVALUE ： 序列最小值，NO MINVALUE表示没有最小值
MAXVALUE ： 序列最大值，NO MAXVALUE表示没有最大值
START WITH ：以什么序列值开始
CYCLE ： 序列是否循环使用
OWNED BY ： 可以直接指定一个表的字段，也可以不指定

nextval('test_id_seq')


alter table 
	tuser
alter column 
	id 
set default nextval(
	'tid_seq'
);

 
### 查看已经创建的序列/SEQUENCE
```SQL
SELECT c.relname FROM pg_class c WHERE c.relkind = 'S';
```