# SQL Social Network Extra Query Exercises Answers

This is the seemingly correct answers to the SQL Movie Rating Modification Query 
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `sql` script generating the database for this exercise is located [here][1]

## Q1

For every situation where student `A` likes student `B`, but student `B` likes a different 
student `C`, return the names and grades of `A`, `B`, and `C`. 

```sql
select H1.name, H1.grade, H2.name, H2.grade, H3.name, H3.grade  
        from Highschooler H1, Highschooler H2, Highschooler H3,
            Likes as L1, Likes as L2
        where L1.ID2 = L2.ID1 and L1.ID1 <> L2.ID2
              and H1.ID = L1.ID1 and H2.ID = L1.ID2 and H3.ID = L2.ID2;
```

## Q2

Find those students for whom all of their friends are in different grades from themselves. 
Return the students' names and grades. 

```sql
-- select all students who have friends from different grades
-- and subtract those students who have friends in the same grade to get the result
select H1.name, H1.grade from Friend as F, Highschooler as H1, Highschooler as H2
            where F.ID1 = H1.ID and F.ID2 = H2.ID and H1.grade <> H2.grade
except
-- select all students who have friends in the same grade
select H1.name, H1.grade from Friend as F, Highschooler as H1, Highschooler as H2
            where F.ID1 = H1.ID and F.ID2 = H2.ID and H1.grade == H2.grade;
```

## Q3

What is the average number of friends per student? (Your result should be just one number.) 

```sql
-- we get the result by averaging on the group by aggregated
-- number of friends per ID1 on Friend table
select avg(T.c) from (
        -- aggregate friends of each student using group by on ID1 
        -- and counting each group number of entries
        select F.ID1, count(*) as c 
        from Friend as F group by F.ID1) as T;
```
 
## Q4

Find the number of students who are either friends with Cassandra or are friends of friends of 
Cassandra. Do not count Cassandra, even though technically she is a friend of a friend. 


```sql
-- after the union, count the number. There is no need to check for
-- duplicates as union takes care of that.
select count(*) from (
-- get the ID of friends of Cassandra
select F1.ID2 as ID from Friend as F1,
                -- grab the ID of Cassandra
                (select H.ID from Highschooler as H where H.name = 'Cassandra') as C
                where F1.ID1 = C.ID
union
-- use this to get the ID's of friends of friends of Cassandra
select F2.ID2 from Friend as F2
    where F2.ID1 in
        -- now get the ID's of friends of Cassandra
        (select F1.ID1 as ID from Friend as F1,
            -- grab the ID of Cassandra
            (select H.ID from Highschooler as H where H.name = 'Cassandra') as C
         where F1.ID2 = C.ID) and 
                 -- remove Cassandra as a friend of a friend
                 F2.ID2 <> (select H.ID from Highschooler as H where H.name = 'Cassandra'));
```

## Q5

Find the name and grade of the student(s) with the greatest number of friends. 


```sql
select H.name, H.grade from
        Highschooler as H,
        -- aggregate friends of each student using group by on ID1 
        -- and counting each group number of entries
        (select F.ID1, count(*) as c 
        from Friend as F group by F.ID1) as T
        where H.ID = T.ID1 and T.c =
            -- calculate the same table to get the max.
            (select max(c) from (select F.ID1, count(*) as c 
                from Friend as F group by F.ID1) as T);
```

[1]: schemas/social.sql
[2]: http://cs.stanford.edu/people/widom/