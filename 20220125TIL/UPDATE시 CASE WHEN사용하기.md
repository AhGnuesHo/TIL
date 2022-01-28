다른 컬럼을 참조하여 테이블을 업데이트 해야 하는 경우가 생겼다.

```
UPDATE emp
   SET sal = 2000
 WHERE empno IN (SELECT a.empno
                   FROM emp a
                      , dept b
                  WHERE a.job = 'MANAGER'
                    and a.deptno = b.deptno)
```

위처럼 서브쿼리를 사용하면
job이 MANAGER인 사람들의 sal을 2000으로 지정할 수 있다

다만 MANAGER가 아닌 사람들의 sal을 지정하려면 어떻게 해야할까?

일일히 조건을 바꿔가면서 update쿼리를 한줄씩 수행해도되지만 case when을 사용하면 하나의 쿼리만으로도 조건에 맞게 update가 가능하다.


> **UPDATE** 테이블명 **SET** 컬럼명 = **CASE**
**WHEN** 컬럼명1 = 비교값1 **THAN** 반환값1
**WHEN** 컬럼명2 = 비교값2 **THAN** 반환값2
**ELSE** 반환값3 
**END**;

위와 같이 업데이트문을 수행하면 한번의 쿼리로 여러 조건을 수정할 수 있다. 

적용예제는 아래와 같다 
```
UPDATE emp
   SET sal = case when job = 'MANAGER' than 2000
   when job = 'ANALYST than 3000
   else 0000
   end;
```
위와같이 하면 manager의 sal은 2000, analyst의 sal은 3000으로 업데이트 될 것이다. 






























