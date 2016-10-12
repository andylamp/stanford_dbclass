# Authorization Quiz answers

This is the seemingly correct answers to the Authorization quiz
 questions from [Prof. J. Widom][1] (infamous :) db class.
 
# Question 1

The following `SQL` statement over tables `R(a, b)`, `S(b, c)`, and `T(a, c)` requires 
certain privileges to execute:

```sql
UPDATE R
   SET a = 10
   WHERE b IN (SELECT c FROM S)
   AND NOT EXISTS (SELECT a FROM T WHERE T.a = R.a)
```

Which of the following privileges is **not** useful for execution of this `SQL` statement?

# Q1 Options

A: `UPDATE ON S`

B: `SELECT ON T(a)`

C: `SELECT ON R(a)`

D: `UPDATE ON R`

# Q1 Answer

We can clearly see from the statement that we *do not* need to update `S` but 
only `select` (i.e. *read*) from it; thus option **A** is the correct answer.

 
# Question 2

Consider a set of users `A`, `B`, `C`, `D`, `E`. Suppose user `A` creates a table `T` 
and thus is the owner of `T`. Now suppose the following set of statements is executed 
in order:

1. User `A`: `grant update on T to B,C with grant option`
2. User `B`: `grant update on T to D with grant option`
3. User `C`: `grant update on T to D with grant option`
4. User `D`: `grant update on T to E`
5. User `A`: `revoke update on T from C cascade`

After execution of statement 5, which of the following is **true**?

# Q2 Options


A: `E` no longer has privilege `UPDATE ON T`

B: `D` has privilege `UPDATE ON T`

C: `C` has privilege `UPDATE ON T`

D: `A` no longer has privilege `UPDATE ON T`

# Q2 Answer

Let's examine each one of the given options, first of all option D is completely wrong
as User `A` is the owner of the table... Option A is also false because while we 
revoke privileges from User `C` the user `D` has its privileges granted by *two*
different users `C` and `B` thus since the privileges are removed only from `C`
and not `B` they are retained. Due to that fact `E` does not lose its privileges from
the revoke operation on User `C` even with `cascade`. Option `C` is completely 
wrong... for obvious reasons hence we are only left with option **B** which 
is our answer.
 
# Question 3

The following `SQL` statement over tables `R(c, d)`, `S(f, g)`, and `T(a, b)` requires 
certain privileges to execute:

```sql
UPDATE T
   SET a=1, b=2
   WHERE a <= ALL (SELECT d FROM R)
   OR EXISTS (SELECT f FROM S WHERE f > T.a)
```

Which of the following privileges is **not** useful for execution of this `SQL` statement?

# Q3 Options

A: `UPDATE ON T`

B: `SELECT ON S(g)`

C: `UPDATE ON T(b)`

D: `SELECT ON S(f)`

# Q3 Answer

Similarly to Q1 we can easily see that we don't require to read the `S.g` attribute from
table `S`, hence option **B** is the correct answer.
 
# Question 4

Consider a set of users `U`, `V`, `W`, `X`, and `Y`. Suppose user `U` creates a table `T` 
and thus is the owner of `T`. Now suppose the following set of statements is executed in 
order:

1. User `U`: `grant select on T to V,W with grant option`
2. User `V`: `grant select on T to W`
3. User `W`: `grant select on T to X,Y`
4. User `U`: `grant select on T to Y`
5. User `U`: `revoke select on T from V restrict`
6. User `U`: `revoke select on T from W cascade`

Which of the following statements is true?

# Q4 Options

A: `W` has privilege `SELECT ON T` after statement 6

B: `W` has privilege `SELECT ON T` after statement 5

C: `X` does not have `SELECT ON T` privilege after statement 5

D: `Y` does not have privilege `SELECT ON T` after statement 6

# Q4 Answer

We can easily see that option A is not true as the `revoke` is issued with a `cascade`
option which means that changes will propagate throughout the hierarchy eventually 
removing the `select on T` privilege from user `W`. A Similar story is for options C, D
the only statement that is actually true is option **C** in which `revoke` is 
issued with `restrict` instead of `cascade`. If you recall from the video lectures 
since we have dependencies this statement will fail to execute resulting in no privilege
loss from that statement.

 
[1]: http://cs.stanford.edu/people/widom/ 