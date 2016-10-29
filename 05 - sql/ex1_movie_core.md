# SQL Movie-Rating Query Exercises Answers

This is the seemingly correct answers to the SQL Movie Rating Query exercises from 
[Prof. J. Widom][2] (infamous :) db class.

The `sql` script generating the database for this exercise is located [here][1].

##Q1

Find the titles of all movies directed by Steven Spielberg.

```sql
select title from Movie where director = 'Steven Spielberg';
```

##Q2

Find all years that have a movie that received a rating of 4 or 5, and sort 
them in increasing order. 

```sql
select distinct year
    from Movie, Rating
    where (stars = 4 or stars = 5) and Movie.mID = Rating.mID
    order by year asc;
```

##Q3
Find the titles of all movies that have no ratings. 

```sql
select distinct title
    from Movie as M, Rating as R
    where M.mID not in 
        (select distinct M.mID from Movie as M, Rating as R where M.mID = R.mID);
```

##Q4

Some reviewers didn't provide a date with their rating. Find the names of all reviewers 
who have ratings with a NULL value for the date. 

```sql
select distinct Re.name
    from Reviewer as Re, Rating as Ra
    where Ra.ratingDate is null and Re.rID = Ra.rID;
```

##Q5

Write a query to return the ratings data in a more readable format: reviewer name, movie title, 
stars, and ratingDate. Also, sort the data, first by reviewer name, then by movie title, and 
lastly by number of stars. 

```sql
select Re.name, M.title, Ra.stars, Ra.ratingDate
    from Reviewer as Re, Rating as Ra, Movie as M
    where Re.rID = Ra.rID and M.mID = Ra.mID
    order by Re.name, M.title, Ra.stars;
```

##Q6

For all cases where the same reviewer rated the same movie twice and gave it a higher 
rating the second time, return the reviewer's name and the title of the movie. 

```sql
select R.name, M.title
    from Reviewer as R, Movie as M, Rating as Ra1, Rating as Ra2
    where Ra1.stars < Ra2.stars and Ra1.ratingDate < Ra2.ratingDate and
          Ra1.mID = Ra2.mID and R.rID = Ra1.rID and R.rID = Ra2.rID and 
          M.mID = Ra1.mID and M.mID = Ra2.mID;
```

##Q7

For each movie that has at least one rating, find the highest number of stars that movie 
received. Return the movie title and number of stars. Sort by movie title. 

```sql
    select T.title, T.s
    from
        (select M.title, R.mID, count(*) as ratCount, max(R.stars) as s from Movie as M, Rating as R
        where M.mID = R.mID
        group by R.mID) as T
    where T.ratCount > 0
    order by T.title;
```

##Q8

For each movie, return the title and the 'rating spread', that is, the difference between 
highest and lowest ratings given to that movie. Sort by rating spread from highest to 
lowest, then by movie title. 

```sql
select M.title, (max(R.stars) - min(R.stars)) as spread
    from Rating as R, Movie as M
    where R.mID = M.mID
    group by R.mID
    order by spread desc, M.title;
```

##Q9

Find the difference between the average rating of movies released before 1980 and the 
average rating of movies released after 1980. (Make sure to calculate the average rating 
for each movie, then the average of those averages for movies before 1980 and movies 
after. Don't just calculate the overall average rating before and after 1980.) 

```sql
select abs(
    (select avg(T1.s) from
    (select avg(R.stars) as s
        from Movie as M, Rating as R
        where M.mID = R.mID and M.year > 1980
        group by M.mID) as T1)
    -
    (select avg(T1.s) from
    (select avg(R.stars) as s
        from Movie as M, Rating as R
        where M.mID = R.mID and M.year < 1980
        group by M.mID) as T1)
    )
    ;
```

[1]: sql-schemas/rating.sql
[2]: http://cs.stanford.edu/people/widom/