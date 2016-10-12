# Constraint and Trigger Quiz answers

This is the seemingly correct answers to the Relational Algebra exercise
 questions from [Prof. J. Widom][1] (infamous :) db class.
 
## Question 1

Write a trigger that makes new students named "Friendly" automatically like 
everyone else in their grade. That is, after the trigger runs, we should have 
`(Friendly, A)` in the Likes table for every other Highschooler A in the same 
grade as "Friendly".
 
### Q1 Answer

```sql
create trigger q1
after insert on Highschooler
when (new.name = 'Friendly')
begin
    insert into Likes 
        select distinct new.ID, H.ID from Highschooler as H 
        where H.grade = new.grade and new.ID <> H.ID;
end
```

## Question 2

Write one or more triggers to manage the grade attribute of new Highschoolers. 
If the inserted tuple has a value less than 9 or greater than 12, change the 
value to `NULL`. On the other hand, if the inserted tuple has a null value 
for grade, change it to 9. 
 
### Q2 Answer

```sql
create trigger q2_1
after insert on Highschooler
for each row
when new.grade < 9 or new.grade > 12
begin
    update Highschooler set grade = null where Highschooler.ID = new.ID;
end

|

create trigger q2_2
after insert on Highschooler
when new.grade is null
begin
    update Highschooler set grade = 9 where Highschooler.ID = new.ID;
end
```


## Question 3

Write one or more triggers to maintain symmetry in friend relationships. 
Specifically, if `(A, B)` is deleted from `Friend`, then `(B, A)` should 
be deleted too. If `(A, B)` is inserted into Friend then `(B, A)` should 
be inserted too. Don't worry about updates to the Friend table.
 
### Q3 Answer

```sql
create trigger q3_1
after delete on Friend
begin
    delete from Friend where Friend.ID1 = old.ID2 and Friend.ID2 = old.ID1;
end

|

create trigger q3_2
after insert on Friend
begin
    insert into Friend values (new.ID2, new.ID1);
end
```


## Question 4

Write a trigger that automatically deletes students when they graduate, 
i.e., when their grade is updated to exceed 12. 
 
### Q4 Answer

```sql
create trigger q4
after update of grade on Highschooler
begin
    delete from Highschooler where Highschooler.grade > 12;
end
```


## Question 5

Write a trigger that automatically deletes students when they graduate, 
i.e., when their grade is updated to exceed 12 (same as Question 4). In 
addition, write a trigger so when a student is moved ahead one grade, then 
so are all of his or her friends. 
 
### Q5 Answer

```sql
create trigger q5_1
after update of grade on Highschooler
begin
    delete from Highschooler where grade > 12;
end

|

create trigger q5_2
after update of grade on Highschooler
begin
    update Highschooler set grade = grade + 1 where Highschooler.ID in 
    (select ID2 from Friend where Friend.ID1 = new.ID);
end
```


## Question 6

Write a trigger to enforce the following behavior: If `A` liked `B` but is 
updated to `A` liking `C` instead, and `B` and `C` were friends, make `B` 
and `C` no longer friends. Don't forget to delete the friendship in both 
directions, and make sure the trigger only runs when the "liked" 
(`ID2`) person is changed but the "liking" (`ID1`) person is not changed. 
 
### Q6 Answer
 
```sql
create trigger q6
after update of ID2 on Likes
when new.ID1 = old.ID1 and new.ID2 <> old.ID2
begin
    delete from Friend where ID1 = new.ID2 and ID2 = old.ID2;
    delete from Friend where ID1 = old.ID2 and ID2 = new.ID2;
end
```
