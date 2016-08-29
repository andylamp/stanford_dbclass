# SQL Movie Rating Modification Query Exercises Answers

This is the seemingly correct answers to the SQL Movie Rating Modification Query 
exercises from [Prof. J. Widom][2] (infamous :) db class.


The `sql` script generating the database for this exercise is located [here][1].

## Q1

Add the reviewer Roger Ebert to your database, with an `rID` of `209`. 

```sql
insert into Reviewer values(209, 'Roger Ebert');
```

## Q2

Insert 5-star ratings by James Cameron for all movies in the database. 
Leave the review date as `NULL`. 

```sql
insert into Rating
  select Ra.rID, M.mID, 5, null
  from Rating as Ra, Movie as M, Reviewer as Re
  where Ra.rID = Re.rID
  and Re.name = 'James Cameron';
```

## Q3

For all movies that have an average rating of `4` stars or higher, add `25` to the 
release year. (Update the existing tuples; don't insert new tuples.) . 

```sql
update Movie
    set year = year + 25
    where mID in (select R.mID 
                    from Rating as R 
                    group by R.mID 
                    having avg(R.stars) >= 4);
```

## Q4

Remove all ratings where the movie's year is before `1970` or after `2000`, and the 
rating is fewer than `4` stars. 

```sql
delete from Rating
    where Rating.mID in (select M.mID from Movie as M 
                        where M.year < 1970 or M.year > 2000)
          and Rating.stars < 4;
```

[1]: schemas/rating.sql
[2]: http://cs.stanford.edu/people/widom/