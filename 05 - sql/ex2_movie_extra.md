# SQL Movie-Rating Query Extra Exercises Answers

This is the seemingly correct answers to the SQL Movie Rating Query Extra exercises from 
[Prof. J. Widom][2] (infamous :) db class.

The `sql` script generating the database for this exercise is located [here][1].

## Q1

Find the names of all reviewers who rated Gone with the Wind. 

```sql
select distinct Re.name
    from Reviewer as Re, Rating as R, Movie as M
    where Re.rID = R.rID and R.mID = M.mID and 
    M.title = 'Gone with the Wind';
```

## Q2

For any rating where the reviewer is the same as the director of 
the movie, return the reviewer name, movie title, and number of stars. 

```sql
select Re.name, M.title, Ra.stars
    from Movie as M, Rating as Ra, Reviewer as Re
    where Ra.rID = Re.rID and Re.name = M.director and Ra.mID = M.mID;
```

## Q3

Return all reviewer names and movie names together in a single list, 
alphabetized. Sorting by the first name of the reviewer and first word 
in the title is fine; no need for special processing on last names or 
removing "The". 

```sql
    select title as name
        from Movie 
union    
    select name 
        from Reviewer
order by name;
```

## Q4

Find the titles of all movies not reviewed by Chris Jackson. 

```sql
select 
    M.title from Movie as M 
except
select distinct M.title
    from Movie as M, Reviewer as Re, Rating as Ra
    where M.mID = Ra.mID and Re.rID = Ra.rID and Re.name = 'Chris Jackson';
```

## Q5

For all pairs of reviewers such that both reviewers gave a rating to the same 
movie, return the names of both reviewers. Eliminate duplicates, don't pair 
reviewers with themselves, and include each pair only once. For each pair, 
return the names in the pair in alphabetical order. 

```sql
select distinct Re1.name, Re2.name
    from  Reviewer as Re1, Reviewer as Re2, 
					Rating as Ra1, Rating as Ra2
    where Re1.rID = Ra1.rID and Re2.rID = Ra2.rID and 
					Ra1.mID = Ra2.mID and Re1.name < Re2.name;
```

## Q6

For each rating that is the lowest (fewest stars) currently in the database, 
return the reviewer name, movie title, and number of stars. 

```sql
select Re.name, M.title, T.stars
    from Movie as M, Reviewer as Re,
        (select Ra.mID, Ra.rID, Ra.stars
        from Rating as Ra
        where stars in 
            (select min(stars) from Rating)) as T
     where M.mID = T.mID and Re.rID = T.rID;
```

## Q7

List movie titles and average ratings, from highest-rated to lowest-rated. If two 
or more movies have the same average rating, list them in alphabetical order. 


```sql
select M.title, avg(stars) as avg_rating
    from Movie as M, Rating as R
    where R.mID = M.mID
    group by R.mID
    order by avg_rating desc, M.title
```

## Q8

Find the names of all reviewers who have contributed three or more ratings. 
As an extra challenge, try writing the query without `having` or without `count`. 

```sql
select distinct Re.name
    from Reviewer as Re, Rating Ra
    where Re.rID = Ra.rID
    group by Re.rID
    having count(Ra.mID) >= 3;
```

Now, another way of going about it without using `count` and having `having` functionality
is the following:

```sql
select distinct Re.name
    from Reviewer as Re, 
        (select R.rID, sum(1) as agg 
            from Rating as R
            group by R.rID) as T
    where Re.rID = T.rID and T.agg >= 3;
```

## Q9

Some directors directed more than one movie. For all such directors, return the titles of all 
movies directed by them, along with the director name. Sort by director name, then movie title. 
As an extra challenge, try writing the query both with and without `count`. 

```sql
select M.title, M.director
    from Movie as M,
    (select M.director
        from Movie as M
        group by M.director
        having count(*) > 1) as T
    where M.director = T.director
    order by M.director, M.title;
```

Now, another way of going about it without using `count` and having `having` functionality
is the following:

```sql
select M.title, M.director
    from Movie as M,
    (select M.director, sum(1) as agg
        from Movie as M
        group by M.director) as T
    where M.director = T.director and T.agg > 1
    order by M.director, M.title;
```

## Q10

```sql

Find the movie(s) with the highest average rating. Return the movie title(s) and average rating.
 
Hint: This query is more difficult to write in SQLite than other systems; you might think of it as 
finding the highest average rating and then choosing the movie(s) with that average rating. 

select M.title, T2.avg_rating
    from Movie as M,
    (select T1.mID, T1.avg_rating from  
        (select R.mID, avg(R.stars) as avg_rating
            from Rating as R
            group by R.mID) as T1
        where avg_rating = 
        (select max(avg_rating) from 
                (select R.mID, avg(R.stars) as avg_rating
                    from Rating as R
                    group by R.mID))
    ) as T2
    where M.mID = T2.mID;

```

## Q11

Find the movie(s) with the lowest average rating. Return the movie title(s) and average rating. 

Hint: This query may be more difficult to write in SQLite than other systems; you might think of 
it as finding the lowest average rating and then choosing the movie(s) with that average rating. 

```sql
select M.title, T2.avg_rating
    from Movie as M,
    (select T1.mID, T1.avg_rating from  
        (select R.mID, avg(R.stars) as avg_rating
            from Rating as R
            group by R.mID) as T1
        where avg_rating = 
        (select min(avg_rating) from 
                (select R.mID, avg(R.stars) as avg_rating
                    from Rating as R
                    group by R.mID))
    ) as T2
    where M.mID = T2.mID;
```

## Q12

For each director, return the director's name together with the title(s) of the movie(s) they 
directed that received the highest rating among all of their movies, and the value of that rating. 
Ignore movies whose director is `NULL`. 

A common pitfall that gives the correct answer here is the following:

```sql
select M.director, M.title, max(stars)
    from Movie as M, Rating as Ra
    where M.mID = Ra.mID and M.director not null
    group by M.director;
```

Although due to current data, the result is correct it this particular query doesn't do what 
we want. Concretely what is does is gives out the *single* movie that has the highest rating
out of all but in our case we want all movies in case more than one have the max score. So,
a correct implementation would be the following.

```sql
select distinct M.director, M.title, T.max_stars
    from 
        Movie as M, Rating as Ra,
        (select director, max(stars) as max_stars
            from Movie as M, Rating as R
            where M.mID = R.mID
            group by M.director) as T
    where Ra.stars = T.max_stars and M.director = T.director and 
          M.mID = Ra.mID;
```


[1]: schemas/rating.sql
[2]: http://cs.stanford.edu/people/widom/