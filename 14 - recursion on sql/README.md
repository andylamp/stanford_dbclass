# SQL Recursion Chapter

In this chapter the notions of recursion in the context of the SQL language are 
introduced. Graded assignments include one (1) quiz set, for which answers are 
provided below.


# Quiz

The quiz is really short and tests really basic stuff about how basic notions of 
recursion work.


## Q1

Let's consider a table `T(A)` containing a set of positive integers with *no duplicates*, 
and the following *recursive* SQL query. Note that this query includes *nonlinear recursion*, 
which technically is not permitted in the strict SQL standard.

```sql
With Recursive Mystery(X,Y) As
  (Select A As X, A As Y From T
    Union
    Select m1.X, m2.Y
    From Mystery m1, Mystery m2
    Where m2.X = m1.Y + 1)
Select Max(Y-X) + 1 From Mystery
```

While the definition looks complicated, the query in fact computes a property of `T` that can be 
stated very succinctly. First try to determine what Mystery is computing from `T`. Then choose 
which of the following is a correct statement about the final query result.

### Q1 Options

In my instance I had the following options to choose from:

 * A: If `T` = {1, 5, 9, 10, 11, 12, 15} then the query returns 3.
 * B: If `T` = {1, 3, 4, 5, 10, 11, 12} then the query returns 2.
 * C: If `T` = {2, 4, 5, 6, 8, 10, 11} then the query returns 3.
 * D: If `T` = {1, 5, 9, 10, 12, 15} then the query returns 6.

### Q1 Answer

## Q2

Consider a relation Manager(manager,employee) where a tuple `(m,e)` in Manager specifies that 
person `m` is the manager of person `e`. The only key for Manager is both attributes together. 
The following recursive SQL query computes the relation `Peer(X,Y)`.

```sql
With Recursive Peer(X,Y) As
  (Select M1.employee, M2.employee
    From Manager M1, Manager M2
    Where M1.manager = M2.manager AND M1.employee < M2.employee
      Union
    Select M1.employee, M2.employee
    From Peer S, Manager M1, Manager M2
    Where S.X = M1.manager AND S.Y = M2.manager
    And M1.employee < M2.employee)
Select * from Peer
```

Given the above statement, suppose the Manager has the following snapshot: 

| manager | employee |
|---------|----------|
|    10   |     9    |
|    10   |     8    |
|    9    |     7    |
|    9    |     6    |
|    8    |     6    |
|    8    |     5    |
|    7    |     4    |
|    7    |     3    |
|    6    |     3    |
|    6    |     2    |
|    5    |     2    |
|    5    |     1    |

Consider the computation of Peer in the recursive query. Let the base case -- the first 
term of the Union -- be "round 1." Let each subsequent round of the recursion 
be "round 2," "round 3," and so on. Which of the following is a correct statement about 
when a pair gets added to Peer? 

Hint: You may find it helpful to draw a figure that shows the Manager relationships.

### Q2 Options

In my instance I had the following options to choose from:

 * A: `(3,5)` is added in round 2.
 * B: `(1,4)` is added in round 2.
 * C: `(1,4)` is added in round 3.
 * D: `(6,7)` is added in round 2.

### Q2 Answer

In order to better understand how the recursion advances we will look at the manager
hierarchy, which is illustrated in the following figure (you can also download a 
vector version of this [here][1]).

![manager hierarchy][myimage]

Now, observe the that fact that in each step the recursion *descends* one level of the manager
tree and locates the peer tuples which are added to the final result. Thus in iteration level
1 we have the employees that have the same manager marked as peers; thus the *initial* 
seed `Peer` table for our recursion is the following:

 `Peer0`:
 
|   X  |   Y   |
|------|-------|
| 9 | 8 |
| 7 | 6 |
| 6 | 5 |
| 4 | 3 |
| 3 | 2 |
| 2 | 1 |



## Q3

For the final question let's consider a relation `Parent(par,child)`, where a tuple `(p,c)` 
in Parent specifies that person `p` is the parent of person `c`. The only key for `Parent` 
is both attributes together. We are interested in writing a recursive SQL query to find 
all descendants of the person named "Eve." Here are six possible definitions of a 
recursive relation `Ancestor(X,Y)`. Note that some of the definitions *include nonlinear* 
recursion, which technically is not permitted in the strict SQL standard.

```sql
1: With Recursive Ancestor(X,Y) As
      (Select par, child From Parent
       Union
       Select Ancestor.X, Parent.child
       From Ancestor, Parent
       Where Ancestor.Y = Parent.par)
2: With Recursive Ancestor(X,Y) As
      (Select par, child From Parent Where par = 'Eve'
       Union
       Select Ancestor.X, Parent.child
       From Ancestor, Parent
       Where Ancestor.Y = Parent.par)
3: With Recursive Ancestor(X,Y) As
      (Select par, child From Parent
       Union
       Select Parent.par, Ancestor.Y
       From Parent, Ancestor
       Where Parent.child = Ancestor.X)
4: With Recursive Ancestor(X,Y) As
      (Select par, child From Parent
       Union
       Select Parent.par, Ancestor.Y
       From Parent, Ancestor
       Where Parent.child = Ancestor.X and Parent.par = 'Eve')
5: With Recursive Ancestor(X,Y) As
      (Select par, child From Parent Where par = 'Eve'
       Union
       Select A1.X, A2.Y
       From Ancestor A1, Ancestor A2
       Where A1.Y = A2.X)
6: With Recursive Ancestor(X,Y) As
      (Select par, child From Parent
       Union
       Select A1.X, A2.Y
       From Ancestor A1, Ancestor A2
       Where A1.Y = A2.X and A1.X = 'Eve')
```

Consider two possible queries that can be used to complete any of the `WITH` 
statements (1)-(6):

```sql
A: Select Y From Ancestor
B: Select Y From Ancestor Where X = 'Eve'
```

Which of the following combinations correctly computes the descendants of "Eve"?

### Q3 Options

In my instance I had the following options to choose from:

 * A: (B) gives the correct answer with (1), (2), and (3) only.
 * B: The only choices for which both (A) and (B) both give the correct answer are (1) and (2). 
 * C: (B) gives the correct answer with (1), (2), (3), and (6) only.
 * D: Neither (A) nor (B) give the correct answer with (4) and (6).

### Q3 Answer

[myimage]: manager_hierarchy.jpg 
[1]: manager_hierarchy.pdf