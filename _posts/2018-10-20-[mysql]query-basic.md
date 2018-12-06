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

### 문제 8.(문제 5~7 까지는 거의 비슷한 문제라서 제외한다.)

> STATION 테이블에서 CITY 이름의 양 끝에 모음(a,e,i,o,u)로 시작되는 값들을 구하라. 중복된 값이 있어선 안된다.

~~~sql
select distinct city from station where left(city, 1) in ("a", "e", "i", "o", "u") and right(city, 1) in ("a", "e", "i", "o", "u")
~~~

### 문제 9.

> STATION 테이블에서 CITY 이름의 양 끝에 모음(a,e,i,o,u)로 시작되지 않는 값들을 구하라. 중복된 값이 있어선 안된다.

~~~sql
select distinct city from station where left(city, 1) not in ("a", "e", "i", "o", "u")
~~~

### 문제 11.

> STATION 테이블에서 모음으로 시작하거나 혹은 모음으로 끝나는 CITY 값들을 구하라. 중복된 값이 있어선 안된다.

~~~sql
select distinct city from station where left(city, 1) not in ("a","e","i","o","u") or right(city, 1) not in ("a", "e", "i","o","u")
~~~

### 문제 75점보다 높다.

> STUDENTS 테이블에서 75점보다 높은 학생들의 이름을 출력하라. 이름의 뒷자리에서부터 3자리의 철차를 이용해 오름차순으로 정렬하고, 해당 조건에서도 우선순위를 결정지을 수 없다면 ID값으로 오름차순 정렬하라

~~~sql
select Name from STUDENTS where Marks>75 order by right(Name, 3), ID
~~~

### 문제 직원 월급.

> Employee 테이블에서 10개월 미만의 근무자 중, 월급이 $2000 보다 많은 직원들의 이름을 출력하라. employee_id 으로 오름차순 계산하라

~~~sql
select name from Employee where months<10 and salary>2000 order by employee_id
~~~

### 문제. 일본 인구

> 일본 도시 인구의 총합을 구하라.

~~~sql
select sum(POPULATION) from CITY where COUNTRYCODE='JPN';
~~~

### 문제. 인구차

> 인구가 가장 많은 도시와 적은 도시의 인구수 차를 구하라

~~~sql
select max(POPULATION) - min(POPULATION) from CITY;
~~~

### 문제. 큰 실수를 저지르다

> Samantha는 전 직원들의 평균 월급을 계산하는 업무를 진행하고 있었다. 계산을 다 마치고 나서야 그녀의 키보드에서 `0` 키가 고장났다는 사실을 깨닳았다. 실제 평균 급료와 잘못 계산된 급료의 차이를 구하라.

~~~sql
select CEIL((SUM(salary)/count(salary)) - (sum(replace(salary,'0',''))/count(salary))) from EMPLOYEES;
~~~