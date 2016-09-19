# Online Analytical Processing (OLAP) Chapter

In this chapter the notions of Online Analytical Processing (OLAP) are introduced. 
Graded assignments include one (1) quiz set, for which answers are provided below.

# Quiz

The quiz is really short and tests really basic stuff about how basic notions of 
OLAP work, like *rolling-up*, *drilling-down* and so on.

## Q1

Consider a *Fact* table which has the following schema:
 
 ```sql
 Sales(saleID, itemID, color, size, qty, unitPrice)
 ```
 
 Additionally consider the following three queries:
 
 
 Query 1:
 ```sql
 select itemID, color, size, sum(qty*unitPrice)
    from Sales
    group by itemID, color, size
 ```
 
 Query 2:
 ```sql
  select itemID, color, size, sum(qty*unitPrice)
     from Sales
     group by itemID, size
 ```

 Query 3:
 ```sql
  select itemID, color, size, sum(qty*unitPrice)
     from Sales
     where size < 10
     group by itemID, size
 ```
 
 Now, depending on the order in which these queries are executed, the *pair* of actions 
 (i.e. `P(Q1 -> Q2)`) can be viewed as an example of *roll-up*, *drill-down* or *slicing*.
  Given the following statements about characterizing the nature of the pair operation, 
  which of them is correct?
 
### Q1 Options

In my instance I had the following options to choose from:
  
  * A: Going from `Q1` to `Q2` is an example of a *roll-up*
  * B: Going from `Q2` to `Q1` is an example of a *drill-down*
  * B: Going from `Q2` to `Q1` is an example of a *slicing*
  * D: Going from `Q1` to `Q2` is an example of *slicing*.
  
  
### Q1 Answer

Now before we jump into the answer let's get our terminology straight.

* Rolling-up means that we **remove** information from our query (e.g. a table).
* Drilling-down means that **add** information to our query (e.g. a table).
* Slicing means filtering data of the cube by **constraining** data across a **single** dimension (e.g. take all items 
whose size is less than 10).
* Dicing is exactly the same as *slicing* by the **constraining** of the data 
happens across **multiple** dimensions, so instead of a slice we get a *chunk* or *dice* of data.
 
Now since we got that out of the way, looking from our possible options to choose from we
see that the only logical solution is the option **A** which says that going
from `Q1 -> Q2` we have a *roll-up*. This is infact correct as we see that the
group by clause in `Q1` has **less** elements than the group by in `Q2`, which based on 
what we said above is rolling-up.


## Q2


Consider a *Fact* table which has the following schema:
 
 ```sql
 Facts(D1, D2, D3, x)
 ```
 
 Additionally consider the following three queries:

 Query 1:
 ```sql
 select D1, D2, D3, sum(x)
    from Facts
    group by D1, D2, D3
 ```
 
 Query 2:
 ```sql
 select D1, D2, D3, sum(x)
    from Facts
    group by D1, D2, D3 with cube
 ```

 Query 3:
 ```sql
 select D1, D2, D3, sum(x)
    from Facts
    group by D1, D2, D3 with rollup
 ```
 
 Now suppose attributes `D1`, `D2` and `D3` have `n1`, `n2` and `n3` different values 
  respectively; additionally assume that each possible combination of values appears *at least*
  once in table `Facts`. The number of tuples in the result of each of the three queries 
  above can be specified as an arithmetic formula involving `n1`, `n2`, and `n3`.
  
  Given the tuples below *T{1..4}(a, b, c, d, e, f)* select the that satisfies when `n1=a`, 
  `n2=b` and `n3=c` then the query result size of `Q1`, `Q2` and `Q3` are equal to `d`, `e` and
  `f` respectively.
  
  Hint: it may be helpful to first write formulates describing how `d`, `e` and `f` relate 
  to `a`, `b` and `c` respectively and then pick the tuple which fits the bill.
  
### Q2 Options

In my instance I had the following options to choose from:

 * A: (4, 7, 3, 84, 87, 117)
 * B: (5, 4, 3, 60, 120, 86) 
 * C: (4, 7, 3, 84, 160, 84)
 * D: (5, 4, 3, 60, 64, 80)

### Q2 Answer

Now as we are advised, and for clarifications' sake let's try to find those inequalities first!
Starting by the easiest one, which is Query 1. 

Now, remember that when you perform a group by operation on a single 
column `C` it puts those values from column `C` that have **the same value into 
one group**.

In our case we have to perform a group by operation on **multiple** columns (say columns: 
`C1..Cn`), it works by putting the the values from **all columns in the group by clause
that have the same value into one group**. That distinction is important, because it let's
put a bound to result.

Let's examine that bound, in the case of grouping by on one 
attribute the absolute minimum is the **number of distinct elements on that column**. 
While on the case of multiple columns the absolute maximum is **increased to discrete 
combinations of the attributes** (i.e. each `D1` with `D2` and each of those with each 
of `D3`). So, in essence in the plain group by case we have an equality 
on the number of tuples of the result, which is shown below,
 
 ```
 d <= n1*n2*n3
 ```
 
 This is evident as all of the provided tuples have their `d` values equal to `a*b*c`,
 specifically:
 
 1) `4*7*3 = 28*3 = 84`
 
 2) `5*4*3 = 20*3 = 60`
 
 3) `4*7*3 = 28*3 = 84`
 
 4) `5*4*3 = 20*3 = 60`
 
 Moving on let's examine the group by version with the cube that's shown in Query 3. Recall
 from the lectures that cube just uses every combination of attributes across every dimension.
 In the cases that we want to do slicing/diving we make use of the null construct.
 
 Since we just have null value in all of the available dimensions it can be viewed as an extra
 value in all of our attributes, so the inequality is the following:
 
 ```
 e <= (n1+1)*(n2+1)*(n3+1)
 ```
 
 
 Now let's finally talk about the group by version with rolling-up that's shown in Query 3. Recall from
 the OLAP demo lecture that *rolling-up* produces tuples the combinations of attributes
 including those that have null (which means that the value isn't taken into account 
 for the specialization). A concrete example would be the following:
 
|   A   |   B   | C    |
| ----  |-------|------|
|   a1  |   b1  |  c1  |
|   a1  |   b1  | null |
|   a1  |  null | null |

This scheme happens for all of the value combinations in the table, notice though the absence 
of a `null` value for the `A` column. This is intentional as in roll-up there is *only one*
one column that has `null` for every column which is the the group that contains 
everything from `A`, `B` and `C`.

Now, we can easily see an emerging pattern; the easiest way to calculate this is to
calculate the regular group by number of tuples and add to that the number of extra 
tuples which are specifically calculated during the roll-up operation. Their number
is given our by the following formula:

```
r <= n1*(n2+1) + 1 
```

This essentially adds the null attribute to the distinct pairs we can have for `D2` and `D3`
while adding one to take into account the single triple that has null values in `D1`, `D2` and
`D3`.

Hence the inequality that must hold is the following:

```
f <= d + r
```

Where `d` is the result of the regular group by as shown previously and `r` the inequality
we just calculated.

Using these inequalities we can easily see that the correct answer is:

```
d = a*b*c = 5*4*3 = 60
e = (a+1)*(b+1)*(c+1) = 6*5*4 = 120
f = d + r = d + a*(b+1) + 1 = 60 + 5*5 + 1 = 60 + 26 = 86
``` 

Thus the correct answer is option **B**.
