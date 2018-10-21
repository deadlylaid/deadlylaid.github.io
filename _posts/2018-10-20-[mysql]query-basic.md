---
title: "[MySQL] Query Basic"
layout: post
comments: true
excerpt: "Mysql query 기초"
tags:
  - sql
date: 2018-10-20
---

애초에 SQL공부를 열심히 한 적이 없었지만, 최근 ORM에 익숙해지면서 SQL사용이 점점더 힘들어지게 되었다.

해당 포스트는 블로그 포스팅 식으로 진행될 것이 아니라, 쿼리 공부를 통해 지속적으로 업데이트 될 포스트이다.

### 문제 1.

> Query all columns for all American cities in CITY with populations larger than 100000. The CountryCode for America is USA.

| Field       | Type         |
| ----------- | ------------ |
| ID          | NUMBER       |
| NAME        | VARCHAR2(17) |
| COUNTRYCODE | VARCHAR2(3)  |
| DISTRICT    | VARCHAR2(20) |
| POPULATION  | NUMBER       |



~~~sql
select * from CITY where population>100000 and countrycode='USA';
~~~

### 문제 2.

>Query a list of CITY names from STATION with even ID numbers only. You may print the results in any order, but must exclude duplicates from your answer.

| Field       | Type         |
| ----------- | ------------ |
| ID          | NUMBER       |
| CITY        | VARCHAR2(21) |
| COUNTRYCODE | VARCHAR2(2)  |
| LAT_N       | NUMBER       |
| LONG_W      | NUMBER       |

~~~sql
select distinct city from station where id%2=0;
/*OR*/
select city from station where id%2=0 group by city;
~~~

distinct나 group by를 사용해서 해결한다.[^footnote1]

[^footnote1]: [distinct와 group by](http://intomysql.blogspot.com/2011/01/distinct-group-by.html)

### 문제 3.
>Let $$N$$ be the number of CITY entries in STATION, and let $$N'$$ be the number of distinct CITY names in STATION; query the value of $$N - N'$$ from STATION. In other words, find the difference between the total number of CITY entries in the table and the number of distinct CITY entries in the table.

~~~sql
select count(city) - count(distinct city) from station;
~~~



