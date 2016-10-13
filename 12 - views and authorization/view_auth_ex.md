# Views and Authorization exercise answers

This is the seemingly correct answers to the Views and Authorization exercise
 questions from [Prof. J. Widom][1] (infamous :) db class. You can grab the
 complete database that's used for this exercises from [here][2].
 
# Exercise 1

Write an instead-of trigger that enables updates to the title attribute of view `LateRating`. 

**Policy**: Updates to attribute `title` in `LateRating` should update `Movie.title` for the 
corresponding movie. (You may assume attribute `mID` is a key for table `Movie`.) Make sure the 
`mID` attribute of view `LateRating` has not also been updated -- if it has been updated, don't 
make any changes. Don't worry about updates to `stars` or `ratingDate`.

## Exercise 1 Answer

This a basic one, we have to monitor the attribnute `title` from `LateRating` view and then
we go on to the actual `Movie` table to update the `title` attribute where, which will then
be reflected to the movie. This behavior is reflected using the trigger which is shown below.

```sql
create trigger ex1
instead of update of title on LateRating
begin
    update Movie
    set title = new.title
    where mID = new.mID;
end
```

# Exercise 2

Write an instead-of trigger that enables updates to the `stars` attribute of view `LateRating`. 

**Policy**: Updates to attribute `stars` in `LateRating` should update `Rating.stars` for the corresponding 
movie rating. (You may assume attributes `[mID, ratingDate]` together are a key for table `Rating`.) Make 
sure the `mID` and `ratingDate` attributes of view `LateRating` have not also been updated -- if either 
one has been updated, don't make any changes. Don't worry about updates to `title`.

## Exercise 2 Answer

This is a very similar trigger to the previous exercise... the desired behavior is accomplished by
the code that follows.

```sql
create trigger ex2
instead of update of stars on LateRating
when old.mID = new.mID and old.ratingDate = new.ratingDate
begin
    update Rating
    set stars = new.stars
    where mID = new.mID and ratingDate = new.ratingDate;
end
```

# Exercise 3

Write an instead-of trigger that enables updates to the `mID` attribute of view `LateRating`. 

**Policy**: Updates to attribute `mID` in `LateRating` should update `Movie.mID` and `Rating.mID` for 
the corresponding movie. Update all Rating tuples with the old `mID`, not just the ones contributing to 
the view. Don't worry about updates to `title`, `stars`, or `ratingDate`.

## Exercise 3 Answer

This is another simple query we just have to update an attribute using two distinct statements each
of which change that particular attribute on each of the specified tables. This behavior is accomplished
by the trigger that follows.

```sql
create trigger ex3
instead of update of mID on LateRating
begin
    update Rating
    set mID = new.mID
    where mID = old.mID;
    
    update Movie
    set mID = new.mID
    where mID = old.mID;
end
```

# Exercise 4

Finally, write a single instead-of trigger that *combines* all three of the previous triggers to 
enable simultaneous updates to attributes `mID`, `title`, and/or `stars` in view `LateRating`. 
Combine the view-update policies of the three previous problems, with the exception that `mID` 
may now be updated. Make sure the `ratingDate` attribute of view `LateRating` has not also been updated 
-- if it has been updated, don't make any changes.

## Exercise 4 Answer

This is just a trigger that combines the previous ones taking care of the `ratingDate` modification 
constraint.

```sql
create trigger ex4
instead of update on LateRating
when new.ratingDate = old.ratingDate
begin
    update Rating
    set mID = new.mID
    where mID = old.mID and old.mID <> new.mID;
    
    update Movie
    set mID = new.mID
    where mID = old.mID and old.mID <> new.mID;
    
    update Rating
    set stars = new.stars
    where mID = new.mID and ratingDate = new.ratingDate and 
    old.stars <> new.stars;
    
    update Movie
    set title = new.title
    where mID = new.mID and new.title <> old.title;
end
```

# Exercise 5

Write an instead-of trigger that enables deletions from view `HighlyRated`. 

**Policy**: Deletions from view `HighlyRated` should delete all ratings for the corresponding 
movie that have `stars > 3`.

## Exercise 5 Answer

This is an easy deletion trigger which removes the highly ranked movie ratings given a specific `mID`.

```sql
create trigger ex5
instead of delete on HighlyRated
begin
    delete from Rating where mID = old.mID and stars > 3;
end
```

# Exercise 6

Write an instead-of trigger that enables deletions from view `HighlyRated`. 

**Policy**: Deletions from view HighlyRated should update all ratings for the corresponding movie that 
have `stars > 3` so they now have `stars = 3`.

## Exercise 6 Answer

This is again a very easy trigger, we just use the previous one but in instance we update the highly
ranked movies instead of just removing them.

```sql
create trigger ex6
instead of delete on HighlyRated
begin
    update Rating set stars = 3 where mID = old.mID and stars > 3;
end
```

# Exercise 7

Write an instead-of trigger that enables insertions into view `HighlyRated`. 

**Policy**: An insertion should be accepted only when the `(mID, title)` pair already exists in the `Movie` 
table. (Otherwise, do nothing.) Insertions into view `HighlyRated` should add a new rating for the 
inserted movie with `rID = 201`, `stars = 5`, and `NULL` for `ratingDate`.

## Exercise 7 Answer

This is an interesting trigger, we have to check using the `when` clause that we do actually have
the pair inside the `Movie` table; to do this we call the `count` function and we *require* that
the result is greater than zero (0). The insertion process is really straightforward...

```sql
create trigger ex7
instead of insert on HighlyRated
when ((select count(*) from Movie as M where M.mID = new.mID and M.title = new.title) > 0)
begin
    insert into Rating values (201, new.mID, 5, NULL);
end
```

# Exercise 8

Write an instead-of trigger that enables deletions from view `NoRating`. 

**Policy**: Deletions from view `NoRating` should delete the corresponding movie from the `Movie` table.

## Exercise 8 Answer

Again, we use the same trick as in the previous question but in this instance we just delete all of
the ratings in `Rating` table.

```sql
create trigger ex8
instead of insert on NoRating
when ((select count(*) from Movie as M where M.mID = new.mID and M.title = new.title) > 0)
begin
    delete from Rating where mID = new.mID; 
end
```

# Exercise 9

Write an instead-of trigger that enables deletions from view `NoRating`. 

**Policy**: Deletions from view `NoRating` should delete the corresponding movie from the `Movie` table.

## Exercise 9 Answer

Again this a very simple trigger... on delete we just delete that particular movie from the `Movie` table.

```sql
create trigger ex9
instead of delete on NoRating
begin
    delete from Movie where mID = old.mID; 
end
```

# Exercise 10

Write an instead-of trigger that enables deletions from view `NoRating`. 

**Policy**: Deletions from view `NoRating` should add a new rating for the deleted movie with 
`rID = 201`, `stars = 1`, and `NULL` for `ratingDate`.

## Exercise 10 Answer

Similarly to the previous triggers, we just insert into the `Rating` table the specified tuple only
if the movie pair already exists in the `Movie` table.

```sql
create trigger ex10
instead of delete on NoRating
when ((select count(*) from Movie as M where M.mID = old.mID and M.title = old.title) > 0)
begin
    insert into Rating values (201, old.mID, 1, NULL);
end
```

[1]: http://cs.stanford.edu/people/widom/ 
[2]: viewdb/viewmovie.sql