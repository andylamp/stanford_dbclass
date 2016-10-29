# SQL Social Network Query Modification Exercises Answers

This is the seemingly correct answers to the SQL Social Network Query Modification 
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `sql` script generating the database for this exercise is located [here][1].

## Q1

It's time for the seniors to graduate. Remove all 12th graders from `Highschooler`. 

```sql
delete from Highschooler
    where Highschooler.grade = 12;
```

## Q2

If two students `A` and `B` are friends, and `A` likes `B` but not vice-versa, 
remove the Likes tuple. 

```sql
delete from Likes
    -- remove those ID, which A and B are friends but not mutually liked
    where ID1 in (
    -- now select those pairs A and B who are friends and A likes B
    select L1.ID1
                    from Friend as F, Likes as L1
                    where F.ID1 = L1.ID1 and F.ID2 = L1.ID2
    -- and now remove from the above those who are friends and are
    -- mutually liked (A likes B and B likes A)
    except
    -- select from A and B who are friends and A likes B
    -- those which are mutually liked; 
    -- i.e. B likes A and A likes B
    select L.ID1 from Likes as L,
        -- select A, B who are friends and A likes B.
        (select L1.ID1, L1.ID2 
                    from Friend as F, Likes as L1
                    where F.ID1 = L1.ID1 and F.ID2 = L1.ID2) as T
        where L.ID1 = T.ID2 and L.ID2 = T.ID1);
```

## Q3

For all cases where `A` is friends with `B`, and `B` is friends with `C`, add a new friendship 
for the pair `A` and `C`. Do not add duplicate friendships, friendships that already exist, or 
friendships with oneself. (This one is a bit challenging; congratulations if you get it right.) 

```sql
insert into Friend
    -- select all the friendships pairs (A, C), (C, A) from the union of
    -- pairs (A, C), (C, A)
    select * from (
    -- select all the Friends A, C that have a common friend B and
    -- return the pair (A, C)
    select distinct F1.ID1, F2.ID2 from Friend as F1, Friend as F2
        where F1.ID2 = F2.ID1 and F1.ID1 < F2.ID2
    
    union
    
    -- select all the friends A, C that have a common friend B as before
    -- but now return the pair (C, A)
    select distinct F2.ID2, F1.ID1 from Friend as F1, Friend as F2
        where F1.ID2 = F2.ID1 and F1.ID1 < F2.ID2)
    
    -- now remove the existing friendships from the union using except
    except

    select * from Friend;
```

[1]: sql-schemas/social.sql
[2]: http://cs.stanford.edu/people/widom/
