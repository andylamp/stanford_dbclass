# Indexes and Transactions Chapter

In this chapter the notions of database Indexes and Transactions are covered. Graded 
assignments include two (2) quiz sets.

# Quizzes

The quizzes are about Indexes and Transactions; in this version of the course both 
are graded. Answers for both quizzes are provided below.


# Indexes Quiz

## Question 1

Consider the following relational schema:

```
Course(courseName unique, department, instrID)
Instructor(instrID unique, office)
Student(studentID unique, major)
Enroll(studentID, courseName, unique (studentID,courseName))
```

Let's now suppose that there are five (5) types of queries commonly asked on this schema:

* Given a course name, find the department offering that course.
* List all `studentID`s together with all of the departments they are taking courses in.
* Given a `studentID`, find the names of all courses the student is enrolled in.
* List the offices of instructors teaching at least one course.
* Given a major, return the studentIDs of students in that major.

Which of the following indexes could **NOT** be useful in speeding up 
execution of one or more of the above queries?

### Q1 options

In my instance I had the following options to choose from:

 * A: Index on `Course.courseName`
 * B: Index on `Course.instrID` 
 * C: Index on `Enroll.studentID`
 * D: Index on `Course.department`
 
### Q1 Answer

Given our queries, we can easily see that the only "useless" index is the one in department;
this is evident as all of our queries are **not** directly affected by the `Course.department`
column. Thus the correct answer from the above options is **D**.


## Question 2

For the second question, consider a table storing temperature readings taken 
by sensors:

```
Temps(sensorID, time, temp)
```

Now assume the pair of attributes `(sensorID, time)` is a key of the relation; then
consider the following query:

```sql
select * from Temps
   where sensorID = 'sensor541'
   and time = '05:11:02'
```

Based on the above query consider the following scenarios:

 * A: No index is present on any attribute of `Temps`  
 * B: An index is present on attribute `sensorID` only 
 * C: An index is present on attribute `time` only 
 * D: Separate indexes are present on attributes `sensorID` and `time` 
 * E: A multi-attribute index is present on `(sensorID, time)` 
 
Suppose table `Temps` has 50 unique `sensorID`'s and each `sensorID` has exactly 20 
readings. Furthermore there are exactly 10 readings for every unique `time` in `Temps`. 
 
For each scenario A-E, determine the maximum number of tuples that might be accessed 
to answer the query, assuming one "best" index is used whenever possible. 
(Don't count the number of index accesses.) 

Which of the following combinations of values is correct?


### Q2 options

In my instance I had the following options to choose from:

 * A: `A:1000`, `B:20`, `E:10`
 * B: `A:1000`, `C:1000`, `D:10` 
 * C: `B:20`, `C:10`, `E:1`
 * D: `B:1000`, `C:10`, `D:10`
 
### Q2 Answer

Again, we can easily deduce that the combination of values that is correct is **C** let's 
see why. First of we know that `Temps` table has 50 unique `sensorID`'s and each `sensorID`
has **exactly** 20 readings. 

Let's start with `B:20`, we know that there is an index present only on attribute `sensorID`, we
also know that `sensorID` is comprised out of *unique* attributes so the index should be a 
hash table. Since we have a hash table index we can retrieve the `sensorID` we want 
('sensor541') by fetching just one tuple. Then we know that each `sensorID` has 
20 readings and each unique `time` has 10 values. Since there is *no* present index, we
have to traverse the readings in sequence, so in the worse case we have to traverse all of 
them; hence 20.

Now moving on to `C:10`, we know that each unique `time` has 10 attributes, thus we assume 
that to find which sensor has our requested `sensorID` we have to traverse all of them,
hence 10.

Finally let's tackle `E:1`, in this case we have a multi-attribute key on 
`(sensorID, time)`; we can see that the query result is *uniquely* identified by that 
combination, so since we assume an index presence and due to the unique constraint must be
 a hash table the tuples required to be traversed would be only ourselves -- hence 1.

I'll leave it as an exercise to the creative reader (and using the above method as a 
guide of course) to find why the other options provided are wrong.

# Transactions Quiz


## Question 1

Consider tables `R(A)` and `S(B)` and the following transaction `T1`:

```sql
T1: set transaction isolation level repeatable read;
    select * from R;
    select * from R;
    select * from S;
    commit;
```

Suppose table `R` initially has one tuple with value `A=3` and table `S` 
initially has one tuple with value `B=6`. Consider the following possible 
transactions `T2`, executed around the same time as `T1`. Which one of them 
can cause the two transactions to exhibit **non-serializable** behavior?

### Q1 options


In my instance I had the following options to choose from:

 * A: `T2`: `set transaction isolation level serializable; update S set B=5; delete from R; commit;`
 * B: `T2`: `set transaction isolation level serializable; update R set A=4; update S set B=5 where B<5; commit;` 
 * C: `T2`: `set transaction isolation level serializable; delete from S; insert into S values (6); commit;`
 * D: `T2`: `set transaction isolation level serializable; insert into S values (5); delete from R where A=2; commit;`

### Q1 Answer

Now, we will examine each answer separately to determine which is the correct answer and why; let's start 
with option `A`. This option says that we update `S` and perform a delete of all the records in `R`. This
actually looks like a really good candidate for our answer as *both tables* are *modified* in this transaction; 
but for the sake of completeness let's examine the other options as well. Option `B` updates table `R` and
`S` but with a catch; it only updates the table `S` when `B < 5` holds, which given our table snapshots
is never. So it seemingly it is indeed violating the serialization rules in theory, in practice it does not.
Let's now look at option `C`, now again we see that we have two modification operations but this time they are
both on the same table, `S`. This leads us to see due to the rules of repeatable read, only either state will be
used as the same value is modified twice, so given our guarantees we still don't have any unexpected results. 
Lastly, option `D` again modifies *both* tables but like option `B` the second statement has no effect in practice
given our current table snapshots thus it does not have any ill effects.

## Question 2

Consider a relation `R(x)` containing integers. Suppose Alice runs a transaction 
that is a query:

```sql
select sum(x) from R;
commit;
```

Betty's transaction is a sequence of inserts:

```sql
insert into R values (10);
insert into R values (20);
insert into R values (30);
commit;
```

Carol's transaction is a sequence of deletes:

```sql
delete from R where x=30;
delete from R where x=20;
commit;
```

Before any of these transactions execute, the sum of the integers in `R` is 1000, and 
none of the integers are 10, 20, or 30. If Alice's, Betty's, and Carol's transactions 
run at about the same time, and each runs under isolation level *READ COMMITTED*, which 
of the following sums could be produced by Alice's transaction?

### Q2 options

In my instance I had the following options to choose from:

 * A: 950
 * B: 1050
 * C: 1030
 * D: 1040
 
### Q2 Answer

This is an interesting problem; as due to its nature there can be many outcomes. Let's examine which one
of the provided options makes sense in our context. We know, by definition that the integers that previously summed
up to 1000 do *not* contain 10, 20, and 30. We also know that our transactions run with *READ COMMITTED* isolation 
level, this one *read* has to be *committed* before it is read. Now before we start performing our calculations we have
to find the possible interleaving points for Alice's transaction, and judging from our permission levels 
the obvious cases can be the following six (6).

Let Alice's transaction be: `T1`, Betty's: `T2`, and Carol's: `T3`

Thus `T1` can read from the following points:
 
 1. At the start of `T2` and *before* execution of `T3`
 2. At the start of `T2` and *after* execution of `T3`
 3. At the end of `T2` and *before* execution of `T3`
 4. At the end of `T2` and *after* execution of `T3`
 5. At the start of `T3` and *before* execution of `T2`
 6. At the end of `T3` and *after* execution of `T2`

Now, if `T1` gets executed as per case 1, then none of the statements of `T1`, `T2` would have be executed so `T1`
 would return the initial sum value, which was 1000. Let's now look at case 2, which is after `T3` has executed by
 `T2` has not, remember that the integers contained in the summation *before* these transactions did not contain
 10, 20, or 30; this would in turn render `T3` useless as no values matched the statement, hence the value would 
  again be equal to the initial summation, 1000. Moving on, we examine case 3, in this case `T1` is executed after `T2`
  but before `T3`, so the values have been inserted in `R` but not removed; thus the summation would include *all* the 
  new values plus the old summation value, which would be 1060. Now, we take a look at case 4, which assumes `T1` is
  executed after both `T2` and `T3`, but in this instance `T3` has been executed *before* `T2` so there were no 
  values to be removed, hence the result is the same as in the previous case, 1060. Let's now focus on case 5, which
  assumes that `T1` will be executed before `T3` and before `T2`, this means that none of the table altering statements
  have been executed, so the summation will be the same, 1000. Finally let's look at case 6, which is assumed that 'T1'
  will execute after the execution of 'T3' and 'T2' where 'T2' execution proceeded the execution of 'T3'; in this case
  the summation will be 1010. We see that none of the obvious cases are in our options, so we have to dig a bit deeper
  to find out why this is the case.
  
There is one more scenario to consider, which is the one that `T3` executes in parallel with `T2` and the first 
statement of `T3` is executed before starting `T2` (which is perfectly legal based on our isolation configuration) and
the remaining statement of `T3` will be executed *after* `T2` has been committed. Thus we get the following sequence:

```
 T3: remove 30 (not exists)
 T2: insert 10
 T2: insert 20
 T2: insert 30
 T2: commits
 T3: remove 20
 T3: commits
 T1: calculate sum -> 1040
```

This can also go both ways... `T2` can start to execute before `T3` and a similar scenario would occur as is shown
below.

```
 T2: insert 10
 T3: remove 30 (not exists)
 T3: remove 20 (not exists)
 T3: commits
 T2: insert 20
 T2: insert 30
 T2: commits
 T1: calculate sum -> 1060
```

Thus based on the above we can easily deduce that the correct answer is option **D**, 1040.

## Question 3

Consider a relation `R(x)` containing integers. Suppose Alice runs a transaction 
that is a query:

```sql
select sum(x) from R;
commit;
```

Betty's transaction is a sequence of inserts:

```sql
insert into R values (10);
insert into R values (20);
insert into R values (30);
commit;
```

Carol's transaction is a sequence of deletes:

```sql
delete from R where x=30;
delete from R where x=20;
commit;
```

Before any of these transactions execute, the sum of the integers in *R* is 1000, and 
none of these integers are 10, 20, or 30. Alice's, Betty's, and Carol's transactions 
run at about the same time. Which of the following sums could be returned by Alice's 
transaction if all three transactions run under isolation level *READ UNCOMMITTED*, but 
not if all three run under isolation level *SERIALIZABLE*?

### Q3 options

In my instance I had the following options to choose from:

 * A: 1060
 * B: 1040
 * C: 1020
 * D: 1010
 
### Q3 Answer

Now based on what was described above, we can see that the question is essentially the same but with one little twist;
the isolation level for each transaction is changed from *read committed* to *read uncommitted*, which means that 
*dirty-reads* **are allowed**. As a refresher *dirty-reads*, are the data written by an *uncommitted* transaction.

Now using the same terminology as the previous question but moving the commits a bit, we can see that the option of 
1040 is the only one that makes sense.

As before, let Alice's transaction be: `T1`, Betty's: `T2`, and Carol's: `T3`

```
 T3: remove 30 (not exists)
 T2: insert 10
 T2: insert 20
 T2: insert 30
 T3: remove 20
 T3: commits
 T2: commits
 T1: calculate sum -> 1040
```

Now 1010 is the correct answer if everything is run in a serial fashion like so: `T2` -> `T3` -> `T1`, 1020 is not a 
correct option as we would have inserted either 10 or 30 as well. Finally 1060 is again another option if the 
transactions run serially  in the following order: `T3` -> `T2` -> `T1`

Thus based on the above we can deduce that the correct answer is option **B**, 1040 which incidentally is the same 
amount as in the previous question.