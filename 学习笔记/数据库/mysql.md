# Mysql数据库

## 创建库

```mysql
create database dsz_dev charset='utf8'
```

## 创建表

```mysql
create table t_resource_info (
	id int not null auto_increment comment '主键' primary key,
    resource_id varchar(64) not null comment '资源id',
    l1_department varchar(128) default null comment '一级部门',
    l2_department varchar(128) default null comment '二级部门',
    owner varchar(64) default null comment '主owner',
    is_autonomy enum('yes', 'no') default 'no' comment '自治标志',
    create_time date not null comment '机器创建时间',
    modified_time timestamp default current_timestamp on update current_timestamp comment '更新时间',
    constraint uniq_resource_id unique (resource_id)
)
	comment 'VM资源信息';
```

## 创建索引

```mysql
create index idx_resource_id on t_resource_info (resource_id);
```

## 添加表字段

```mysql
alter table t_resource_info
add is_dsz enum('yes', 'no') default 'no' comment '降配标志',
add is_white enum('yes', 'no') default 'no' comment '白名单标志',
add cpu int default 0 comment 'cpu核数',
add ram int default 0 comment 'ram大小';
```

