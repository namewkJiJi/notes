# intro to database ： CMU15-445



## Class1

### defination

- database  --- csv file

  - python code to parse the file line by line
  - bad idea -- waste of time to ensure sth.

- database management system

- data models

  1. relational
  2. key/value
  3. graph
  4. array
  5. ....

  

- shema








## sql



​		聚合函数内可以添加条件

```sql
select 
count(if(gender = 'male',1,null)) as male_num , 
avg(if(gender = 'male',gpa,null)) as avg_gpa
from user_profile;
```

牛客sql22

