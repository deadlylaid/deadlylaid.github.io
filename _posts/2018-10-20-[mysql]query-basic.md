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

> CITY테이블에서 총 인구소가 100000을 초과하는 American city들을 모두 구하라. America의 CountryCode는 USA이다.

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

> STATION의 ID값이 짝수인 CITY 이름을 모두 구하라. 중복은 제거되어야한다. 

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
> STATION테이블에 있는 모든 CITY의 갯수를 $$N$$ 이라고 가정하고, and 중복된 이름들을 제거했을 때의 갯수는 $$N'$$ 이라고 가정하자; $$N - N'$$ 의 값을 구하라. 좀 더 자세히 설명하자면, 전체 CITY의 갯수와 중복값을 제거한 CITY의 갯수를 구하라.

~~~sql
select count(city) - count(distinct city) from station;
~~~

### 문제 4.

> STATION 테이블에서 CITY의 이름이 가장 긴 값과 가장 짧은 두 개를 구하라, 각각의 길이 역시 구해야한다. (즉, 철자의 갯수). 가장 작거나 큰 길이를 갖는 이름들이 여러개가 있다면, 알파벳 순서상 오름차순에 있는 이름을 선택해라.


~~~sql
(select city, length(city) from station order by length(city) city limit 1) union (select city, length(city) from station order by length(city) desc, city limit 1)
~~~

union은 두 query의 결과를 합칠 때 이용한다.
order by의 뒤에 오는 인자를 통해 정렬 순서를 결정한다. 다수의 인자일 경우 모두 반영한다.



### 문제 5.

> STATION 테이블에서 CITY의 이름이 모음 (i.e., a, e, i, o, or u)으로 시작하는 값을 구하라. 중복된 값이 있어선 안된다.

~~~sql
select distinct city from station where city like "a%" or city like "e%" or city like "i%" or city like "o%" or city like "u%";
~~~
