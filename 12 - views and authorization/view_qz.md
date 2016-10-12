# Views Quiz answers

This is the seemingly correct answers to the Views quiz
 questions from [Prof. J. Widom][1] (infamous :) db class.
 
# Question 1

Consider the following base tables. Capitalized attributes are primary keys. 
All non-key attributes are permitted to be `NULL`.

```sql
MovieStar(NAME, address, gender, birthdate)
MovieExecutive(LICENSE#, name, address, netWorth)
Studio(NAME, address, presidentLicense#)
```

Each of the choices describes, in English, a view that could be created with a 
query on these tables. Which one can be written as a `SQL` view that is 
updatable according to the `SQL` standard?

# Q1 Options

In my instance I had the following options:

A: A view "GenderBalance" containing the number of male and number of female movie stars.

B: A view "NewYorkStudios" containing the names and addresses of all studios with addresses containing "New York". 

C: A view "NewYorkWealth" containing the average net worth of movie executives whose address 
contains "New York".

D: A view "ExecutiveStar" containing the name, gender, and executive license number of all individuals who are 
both stars and executives.

# Q1 Answer

If you recall from the lectures we are **not** allowed to have **any** aggregation if we desire an updatable view, this
obviously eliminates options A, C while D is not valid due to the fact that it extracts information based on two 
predicates -- stars and executives which is not allowed. This easily leaves the only valid option to be **B**.

# Question 2

Consider the following schema:

```sql
Book(ISBN, title, year) -- ISBN and title cannot be NULL
Author(ISBN, name) -- ISBN and name cannot be NULL
```

```sql
Create View V as
  Select Book.ISBN, count(*)
  From Book, Author
  Where Book.ISBN = Author.ISBN
  And Author.name Like 'A%'
  And Book.year > 2000
  Group By Book.ISBN
```

This view is not updatable according to the `SQL` standard, for a number of reasons. Which of the following is a 
valid reason for the view being non-updatable according to the standard?

# Q2 Options

In my instance I had the following options:

A: `NULL` values are not permitted in `Book.ISBN`

B: The condition `Author.name Like 'A%'`

C: Use of aggregate function `COUNT`

D: `Book.year` is omitted from the view

# Q2 Answer

Based on what we discussed in Q1 previously, A, B, and C are valid statements for updatable views but not aggregation
which is expressed as a `count` function. Thus the correct answer is option **C**.

# Question 3

Suppose a table `T(A, B, C)` has the following tuples: `(1, 1, 3)`, `(1, 2, 3)`, `(2, 1, 4)`, `(2, 3, 5)`, 
`(2, 4, 1)`, `(3, 2, 4)`, and `(3, 3, 6)`. Consider the following view definition:

```sql
Create View V as
  Select A+B as D, C
  From T
```

Now consider the following query over `V`:

```sql
Select D, sum(C)
   From V
   Group By D
   Having Count(*) <> 1
```

# Q2 Options

In my instance I had the following options:

A: `(5, 9)`

B: `(5, 7)`

C: `(5, 11)`

D: `(2, 7)`

# Q3 Answer

Before we proceed any further let us get a snapshot of the `T` table  as is defined above.

T

|  A  |   B   |   C   |
|--------|--------|--------|
| 1 | 1 | 3 |
| 1 | 2 | 3 |
| 2 | 1 | 4 |
| 2 | 3 | 5 |
| 2 | 4 | 1 |
| 3 | 2 | 4 |
| 3 | 3 | 6 |

Now the view is defined over `T` but the first column is the `sum` of `T.A` and `T.B` thus the view would have the
following snapshot given `T`:

V

|  D  |  C  |
|-----|-----|
|  2  |  3  |
|  3  |  3  |
|  3  |  4  |
|  5  |  5  |
|  5  |  4  |
|  6  |  6  |

Now let's examine the query over the view, it takes the summation of `C` based on the groups of `V.D` that have more
than one tuple in them, thus the result of that query would be the following:

Q1

|  D  | sum(C) |
|-----|-----|
|  3  |  7  |
|  5  |  9  |

Hence, based on the result we can easily see that the only valid answer is option **A**, `(5, 9)`.

 
[1]: http://cs.stanford.edu/people/widom/ 