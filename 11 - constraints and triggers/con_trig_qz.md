# Constraint and Trigger Quiz answers

This is the seemingly correct answers to the Relational Algebra quiz
 questions from [Prof. J. Widom][1] (infamous :) db class.

## Q1

Consider the following `SQL` table declaration:

```sql
CREATE TABLE R (a INT, b INT, c INT, CHECK( [fill-in] ));
```

Currently `R` contains the tuples `(1, 4, 14)`, `(2, 3, 15)`, and 
`(3, 3, 16)`. Which of the following tuple-based `CHECK` constraints 
will cause the following insertion to be rejected?

```sql
INSERT INTO R VALUES (4,4,9);
```

Note: When a tuple-based check is invoked for an insert and includes a 
subquery over the same table, the subquery is evaluated on the table 
*including* the inserted tuple.

```sql
A: a < (select max(b) from R)
B: a < all (select c-b from R)
C: c <= all (select b+c from R)
D: c > all (select a+b from R)
```

Before we give the correct answer let's get the representation of `R` just after
the tuple insertion and **before** the check occurs.


| a | b | c |
|---------|------|----|
| 1 | 4 | 14 |
| 2 | 3 | 15 |
| 3 | 3 | 16 |
|---|---|----|
| 4 | 4 | 9  | 
 
Now we can easily see that the correct answer is option **A**, 
as the `max(b)` *after* the insertion will be equal to `4` thus `a` has 
the value of `4` which is equal thus the tuple will be **rejected**.

## Q2

Consider the following trigger over a table `R(a, b)`, specified using 
the trigger language of the `SQL` standard:

```sql
create trigger Rins
after insert on R
referencing new row as new
for each row
insert into R(a, b)
 (select distinct R.a, new.b
  from R
  where R.b = new.a)
  except
  (select distinct a, b from R)
```

Suppose table `R` is empty initially. We issue three commands to insert tuples into 
`R`: first we insert `(1, 2)`, then we insert `(2, 3)`, then we insert `(3, 4)`. 
After some of these inserts, trigger **Rins** may insert further tuples into `R`, 
which may activate the trigger recursively. After all the inserts are done, which 
of these tuples is **NOT** in table `R`?

A: `(2, 4)`
B: `(1, 3)`
C: `(3. 1)`
D: `(1, 4)`

In order to give a complete answer on this we have to evaluate table `R` as
insertions and trigger activations progress, so we will present a chronological
order of events which result in the final `R` table as well.

```sql
(1,2)
---
(trigger does nothing)
---
(1,2)
(2,3)
(1,3) -- (trigger inserts that, first try -- stops here)
---
(1,2)
(2,3)
(1,3)
(3,4)

(2,4) --  trigger adds that, first nest, trying again
(2,2) -- trigger adds that, second next, moving on.
-- not adding any more due to except.

(1,4) -- trigger adds that, first next, trying again
-- not adding any more, nothing matches.

(3,4)
-- nothing else added
```

So based on the above flow as well as from our given options we can easily see
that the correct result is option **C**: `(3, 1)`

## Q3

Consider the following `SQL` table declaration:

```sql
CREATE TABLE Emps (id INT, ssNo INT, name CHAR(20), managerID INT);
```


We would like to extend the table declaration to enforce that each of `id` and 
`ssNo` is a key (by itself), and each value of `managerID` must be one of the 
values that appears in the `id` attribute of the same table. Which of the 
following is **not** a legal addition of `SQL` standard key and/or 
foreign- key constraints? 

Note: The addition does not have to achieve all of the stated goals; it only 
must result in legal `SQL`.

A: Add `PRIMARY KEY` after the first `INT`, and add `REFERENCES Emps(id)` 
before the closing parenthesis.

B: Add `PRIMARY KEY` after the first `INT`, and add `, FOREIGN KEY (managerID) 
REFERENCES Emps(id)` before the closing parenthesis.

C:  Add `UNIQUE` after each of the first two `INT`'s, and add `REFERENCES 
Emps(id,ssNo)` before the closing parenthesis.

D: Add `UNIQUE` after each of the first two `INT`'s.

This is easily verifiable if you just type it to and `SQL` syntax verifier but
**C** is obviously not true because we are using a single key that we want to 
reference two attributes (that are not even primary keys). The other versions
are perfectly legal in `SQL` terms but don't satisfy all the constraints stated
in the description.

## Q4

Here are `SQL` declarations for two tables `S` and `T`:

```sql
CREATE TABLE S(c INT PRIMARY KEY, d INT);
CREATE TABLE T(a INT PRIMARY KEY, b INT REFERENCES S(c));
```

Suppose `S(c,d)` contains four tuples: `(2, 10)`, `(3, 11)`, `(4, 12)`, `(5, 13)`. 
Suppose `T(a, b)` contains four tuples: `(0, 4)`, `(1, 5)`, `(2, 4)`, `(3, 5)`. 
As a result of the constraints in the table declarations, certain insertions, 
deletions, and/or updates on `S` and `T` are disallowed. Which of the following 
modifications will **not** violate **any** constraint?

A: Inserting `(4, 10)` into `S`
B: Deleting `(3, 5)` from `T`
C: Inserting `(2, 5)` into `T`
D: Inserting `(1, 2)` into `T`

We can easily see that C, D, and A violate the primary key constraints
imposed in both tables as their values already exist. Thus the correct answer is 
**B**.

## Q5

Here are `SQL` declarations for two tables `S` and `T`:

```sql
CREATE TABLE S(c INT PRIMARY KEY, d INT);
CREATE TABLE T(a INT PRIMARY KEY, b INT, CHECK(b IN (SELECT c FROM S)));
```

Suppose `S(c, d)` contains the four tuples: `(2, 10)`, `(3, 11)`, `(4, 12)`, 
`(5, 13)`. Suppose `T(a, b)` contains the four tuples: `(0, 4)`, `(1, 5)`, 
`(2, 4)`, `(3, 5)`. As a result of the constraints in the table declarations, 
certain insertions, deletions, and/or updates on `S` and `T` are disallowed. 
Which of the following modifications will not violate any constraint?

A: Updating `(2, 4)` in `T` to be `(2, 8)` 
B: Inserting `(7, 3)` into `T`
C: Inserting `(3, 3)` into `T`
D: Inserting `(4, 13)` into `S`

Some are very easy to discard as they violate the primary key constraints of `S`
and `T`; these are options C and D. Option A does not pass the check since 8 is
not a value of `S.c` so the correct answer is option **A**, updating `(2, 4)` in 
`T` to be `(2, 8)`.

## Q6

Consider the following trigger over a table `R(a, b)`, specified using the trigger 
language of the `SQL` standard:

```sql
create trigger Rins
after insert on R
referencing new row as new
for each row
when (new.a * new.b > 10)
insert into R values (new.a - 1, new.b + 1)
```

When we insert a tuple into `R`, the trigger may cause another tuple to be 
inserted, which may cause yet another tuple to be inserted, and so on, until 
finally a tuple is inserted that does not cause the trigger to fire. Suppose 
we begin with table `R` empty. Consider the following possible tuples inserted 
into `R`. After trigger execution completes, which of the insertions results 
in `R` containing **exactly** 3 tuples?

A: `(3, 8)`
B: `(2, 6)`
C: `(2, 9)`
D: `(50, 0)`

Let's evaluate each one of the options separately starting with A,

```sql
(3, 8) -- trigger is activated
(2, 9) -- trigger inserts that and is activated again
(1, 10) -- trigger inserts that, no more activations
```

This seems it's our answer, but for the sake of completeness let's evaluate
the other options as well. Now we have option B.

```sql
(2, 6) -- trigger is activated
(1, 7) -- trigger inserts that, no more activations
```

We end up with three (3) tuples instead, so not our solution. Let's move on with
option C.

```sql
(2, 9) -- trigger is activated
(1, 10) -- trigger inserts that, no more activations
```

Again we end up with two tuples in `R`. Let's move onto our final option which is D.

```sql
(50, 0) -- trigger is not activated.
```

Here we end up with only one tuple...

Thus as we validated previously the correct answer is option **A**: `(3, 8)`.

## Q7

Here are `SQL` declarations for three tables `R`, `S`, and `T`:

```sql
CREATE TABLE R(e INT PRIMARY KEY, f INT);
CREATE TABLE S(c INT PRIMARY KEY, d INT REFERENCES R(e) ON DELETE CASCADE);
CREATE TABLE T(a INT PRIMARY KEY, b INT REFERENCES S(c) ON DELETE CASCADE);
```

Suppose `R(e, f)` contains tuples `(1, 0)`, `(2, 4)`, `(3, 5)`, `(4, 3)`, and 
`(5, 7)`. Suppose `S(c, d)` contains tuples `(1, 5)`, `(2, 2)`, `(3, 3)`, `(4, 5)`,
 and `(5, 4)`. Suppose `T(a, b)` contains tuples `(0, 2)`, `(1, 2)`, `(2, 3)`, 
 `(3, 4)`, and `(4, 4)`. As a result of the referential integrity actions in the 
 table declarations, certain deletions may cause additional deletions to be 
 performed automatically. Which of the following deletions, after all integrity 
 actions, leaves table `T` empty?
 
 A: `delete from R where e+f>6`
 B: `delete from R where e=f-2`
 C: `delete from R where e>f`
 D: `delete from R where e<4`
 
Again in order to completely satisfy this we have to evaluate each option 
separately, so let's start with option A.

```
deleting from R (3, 5)
on trigger delete from S (3, 3)
on trigger delete from T (2, 3)

deleting from R (4, 3)
on trigger delete from S (5, 4)
-- nothing happens on T

deleting from R (5, 7)
on trigger delete from S (4, 5)
on trigger delete from T (3, 4)
on trigger delete from T (4, 4)
```

So in the end, `T` table is left with two tuples `(0, 2)` and `(1 ,2)` so that's
not our answer, let's now move to option B.

```
deleting from R (2, 4)
on trigger delete from S (2, 2)
on trigger delete from T (0, 2)
on trigger delete from T (1, 2)

deleting from R (3, 5)
on trigger delete from S (3, 3)
on trigger delete from T (2, 3)

deleting from R (5, 7)
on trigger delete from S (4, 5)
on trigger delete from T (3, 4)
on trigger delete from T (4, 4)
```

We can easily see that now `T` is empty, so that's our answer but again for
the sake of completeness we will evaluate the remaining options as well. We
will now evaluate option C.

```
deleting from R (1, 0)
-- nothing happens on S, T
deleting from R (4, 3)
on trigger delete from S (5, 4)
-- nothing happens on T
```

This didn't affect `T` at all, so it's not emptied; we will now move on to evaluate
the last option which is D.

```
deleting from R (1, 0)
-- nothing happens on S, T
deleting from R (2, 4)
on trigger delete from S (2, 2)
on trigger delete from T (0, 2)
on trigger delete from T (1, 2)

deleting from R (3, 5)
on trigger delete from S (3, 3)
on trigger delete from T (2, 3)
```

We can easily see that `T` is again not empty and is again left with 
two remaining tuples which are `(3, 4)` and `(4, 4)`.

Finally and as we previously proved the correct answer is option 
**B**, `delete from R where e=f-2`.

## Q8

Here are `SQL` declarations for three tables `R`, `S`, and `T`:

```sql
CREATE TABLE R(e INT PRIMARY KEY, f INT);
CREATE TABLE S(c INT PRIMARY KEY REFERENCES R(e) ON UPDATE CASCADE, d INT);
CREATE TABLE T(a INT PRIMARY KEY, b INT REFERENCES S(c) ON UPDATE CASCADE);
```
Suppose `R(e, f)` contains tuples `(1, 1)`, `(3, 4)`, `(5, 6)`, and `(7, 2)`. 
Suppose `S(c, d)` contains tuples `(1, 7)`, `(3, 2)`, `(5, 1)` and `(7, 5)`. 
Suppose `T(a, b)` contains tuples `(1, 1)`, `(2, 5)`, `(3, 5)`, and `(4, 3)`. 
As a result of the referential integrity actions in the table declarations, 
certain updates may cause additional updates to be performed automatically. 
Which of the following updates, after all integrity actions, leaves table 
`T` in a state such the `sum` of its `b` values is *greater* than 11 but 
less than 18?

A: `update R set e=e-3 where f>1`
B: `update R set e=e+3 where e<3`
C: `update R set e=e+1`
D: `update R set e=e+3 where e>=1`

Again we will evaluate the different options until we find the correct answer
starting with A:

```
update in R (3, 4) -> (0, 4)
on trigger update in S (3, 2) -> (0, 2)
on trigger update in T (4, 3) -> (4, 0)

update in R (5, 6) -> (2, 6)
on trigger update in S (5, 1) -> (2, 1)
on trigger update in T (2, 5) -> (2, 2)
on trigger update in T (3, 5) -> (3, 2)

update in R (7, 2) -> (4, 2)
on trigger update in S (7, 1) -> (4, 1)
-- nothing to update on T
```

We can easily see that the `sum` of `T.b` is `(0 + 1 + 2 + 2) = 5` which is not
within our required range of values. We continue on to evaluate option B:

```
update in R (1, 1) -> (4, 1)
on trigger update in S (1, 7) -> (4, 7)
on trigger update in T (1, 1) -> (1, 4)
```

We can easily see that the `sum` of `T.b` is `(4 + 5 + 5 + 3) = 17` which *is*
within our required range of values; hence it's our answer! The other options 
are evaluated in a similar way but of course are not correct.
 
[1]: http://cs.stanford.edu/people/widom/ 