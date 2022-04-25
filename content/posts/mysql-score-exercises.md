---
title: "MySQL常考学生成绩查询练习题"
subtitle: ""
date: 2020-05-08T09:22:31+00:00
lastmod: 2020-05-08T09:22:31+00:00
author: ""
authorLink: ""
description: "假设现在学校目前只开设了语文、数学、英语三门课，后期还可开设其他门课，请尽量合理的设计班级、学生、考生成绩的数据库表结构并写出查询语句。"

tags: ["MySQL"]
categories: ["MySQL"]

hiddenFromHomePage: false
hiddenFromSearch: false

featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
math:
  enable: false
lightgallery: true
license: ""
---
<!--more-->

![img](https://pic.yqqy.top/blog/20200508172101.png)

### 题目

假设现在学校目前只开设了语文、数学、英语三门课，后期还可开设其他门课，请尽量合理的设计班级、学生、考生成绩的数据库表结构并写出查询语句。

**数据库类图如下：**

![image-20200508171438084](https://pic.yqqy.top/blog/20200508171442.png "类图")

**设计的sql如下：**

```sql
/*
 Navicat Premium Data Transfer

 Source Server         : root
 Source Server Type    : MySQL
 Source Server Version : 50726
 Source Host           : localhost:3306
 Source Schema         : xuexiao

 Target Server Type    : MySQL
 Target Server Version : 50726
 File Encoding         : 65001

 Date: 08/05/2020 16:30:45
*/

SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for t_class
-- ----------------------------
DROP TABLE IF EXISTS `t_class`;
CREATE TABLE `t_class`  (
  `classid` int(10) NOT NULL,
  `classname` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`classid`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_class
-- ----------------------------
INSERT INTO `t_class` VALUES (1701, '一班');
INSERT INTO `t_class` VALUES (1702, '二班');
INSERT INTO `t_class` VALUES (1703, '三班');

-- ----------------------------
-- Table structure for t_course
-- ----------------------------
DROP TABLE IF EXISTS `t_course`;
CREATE TABLE `t_course`  (
  `cid` int(10) NOT NULL,
  `cname` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  PRIMARY KEY (`cid`) USING BTREE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_course
-- ----------------------------
INSERT INTO `t_course` VALUES (101, 'MySQL');
INSERT INTO `t_course` VALUES (102, '网络');
INSERT INTO `t_course` VALUES (103, '系统');

-- ----------------------------
-- Table structure for t_score
-- ----------------------------
DROP TABLE IF EXISTS `t_score`;
CREATE TABLE `t_score`  (
  `sid` int(10) NULL DEFAULT NULL,
  `cid` int(10) NULL DEFAULT NULL,
  `score` int(3) NULL DEFAULT NULL,
  INDEX `s_id`(`sid`) USING BTREE,
  INDEX `c_id`(`cid`) USING BTREE,
  CONSTRAINT `c_id` FOREIGN KEY (`cid`) REFERENCES `t_course` (`cid`) ON DELETE NO ACTION ON UPDATE CASCADE,
  CONSTRAINT `s_id` FOREIGN KEY (`sid`) REFERENCES `t_student` (`sid`) ON DELETE NO ACTION ON UPDATE CASCADE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_score
-- ----------------------------
INSERT INTO `t_score` VALUES (10001, 101, 59);
INSERT INTO `t_score` VALUES (10001, 102, 66);
INSERT INTO `t_score` VALUES (10001, 103, 80);
INSERT INTO `t_score` VALUES (10002, 101, 79);
INSERT INTO `t_score` VALUES (10002, 102, 50);
INSERT INTO `t_score` VALUES (10002, 103, 82);
INSERT INTO `t_score` VALUES (10003, 101, 30);
INSERT INTO `t_score` VALUES (10003, 102, 84);
INSERT INTO `t_score` VALUES (10003, 103, 65);

-- ----------------------------
-- Table structure for t_student
-- ----------------------------
DROP TABLE IF EXISTS `t_student`;
CREATE TABLE `t_student`  (
  `sid` int(10) NOT NULL,
  `sname` varchar(32) CHARACTER SET utf8 COLLATE utf8_general_ci NULL DEFAULT NULL,
  `classid` int(10) NULL DEFAULT NULL,
  PRIMARY KEY (`sid`) USING BTREE,
  INDEX `class_id`(`classid`) USING BTREE,
  CONSTRAINT `class_id` FOREIGN KEY (`classid`) REFERENCES `t_class` (`classid`) ON DELETE NO ACTION ON UPDATE CASCADE
) ENGINE = InnoDB CHARACTER SET = utf8 COLLATE = utf8_general_ci ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of t_student
-- ----------------------------
INSERT INTO `t_student` VALUES (10001, '张三', 1701);
INSERT INTO `t_student` VALUES (10002, '李四', 1701);
INSERT INTO `t_student` VALUES (10003, '王五', 1702);
INSERT INTO `t_student` VALUES (10004, '赵六', 1702);
INSERT INTO `t_student` VALUES (10005, '孙七', 1703);
INSERT INTO `t_student` VALUES (10006, '王八', 1703);

SET FOREIGN_KEY_CHECKS = 1;
```



### 题目一

**统计每个学生成绩和选课数量以及所属班级**

```mysql
SELECT
	d.classid,
	a.sid,
	a.sname,
	SUM( c.score ) totalScore,
	COUNT( c.cid ) courseNum 
FROM
	t_student a,
	t_course b,
	t_score c,
	t_class d 
WHERE
	a.sid = c.sid 
	AND b.cid = c.cid 
	AND a.classid = d.classid 
GROUP BY
	a.sid
```

查询结果：

| classid |  sid  | sname | totalScore | courseNum |
| :-----: | :---: | :---: | :--------: | :-------: |
|  1701   | 10001 | 张三  |    205     |     3     |
|  1701   | 10002 | 李四  |    211     |     3     |
|  1702   | 10003 | 王五  |    179     |     3     |

### 题目二

**统计每个班总成绩在180分以上的学生信息**

```mysql
SELECT
	d.classid,
	a.sid,
	a.sname,
	SUM( c.score ) totalScore 
FROM
	t_student a,
	t_course b,
	t_score c,
	t_class d 
WHERE
	a.sid = c.sid 
	AND b.cid = c.cid 
	AND a.classid = d.classid 
GROUP BY
	a.sid 
HAVING
	( SUM( c.score ) > 180 )
```

查询结果：

| classid |  sid  | sname | totalScore |
| :-----: | :---: | :---: | :--------: |
|  1701   | 10001 | 张三  |    205     |
|  1701   | 10002 | 李四  |    211     |

### 题目三

**统计各个班总成绩在180分以上的学生的数量**

```mysql
SELECT
	e.classid,
	COUNT( classid ) stuNum 
FROM
	(
SELECT
	bj.classid,
	xs.sid,
	SUM( cj.score ) totalScore 
FROM
	t_student xs,
	t_course kc,
	t_score cj,
	t_class bj 
WHERE
	xs.sid = cj.sid 
	AND kc.cid = cj.cid 
	AND xs.classid = bj.classid 
GROUP BY
	xs.sid 
HAVING
	( totalScore > 180 ) 
	) e
```

| classid | stuNum |
| :-----: | :----: |
|  1701   |   2    |

### 题目四

**统计各个班成绩最高的学生姓名，总成绩**

```mysql
SELECT
	e.classid,
	stu.sname,
	MAX(e.totalScore) maxScore
FROM
	t_student stu
	INNER JOIN (
SELECT
	bj.classid,
	xs.sid,
	SUM( cj.score ) totalScore 
FROM
	t_student xs,
	t_course kc,
	t_score cj,
	t_class bj 
WHERE
	xs.sid = cj.sid 
	AND kc.cid = cj.cid 
	AND xs.classid = bj.classid 
GROUP BY
	xs.sid 
	) e ON stu.sid = e.sid
GROUP BY
	classid
```

| classid | sname | maxScore |
| :-----: | :---: | :------: |
|  1701   | 张三  |   211    |
|  1702   | 王五  |   179    |

### 1055错误坑

`1055 - Expression #1 of SELECT list is not in GROUP BY clause and contains nonaggregated column 'd.sname' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by`

解决方法：

1. 在MySQL安装目录找到`my.ini`，在`mysqld`下面添加如下代码：

   `sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION`

2. 重启mysql服务

   ```mysql
   net stop mysql
   
   net start mysql
   ```

   