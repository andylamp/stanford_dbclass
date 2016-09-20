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