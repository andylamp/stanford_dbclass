# XPath/XQuery Course Catalog Extra Exercises Answers

This is the seemingly correct answers to the XPath/XQuery Course Catalog Extra 
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1]

## Q1

Return the course number of the course that is cross-listed as `LING180`. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return data($cat_ref//Course[contains(data(Description), 'LING180')]/@Number)
```

## Q2

Return course numbers of courses that have the same title as some other course. 

Hint: You might want to use the `preceding` and `following` navigation axes for 
this query, which were not covered in the video or our demo script; they match 
any preceding or following node, not just siblings. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return data($cat_ref//Course[
            (./following::Course/Title = ./Title) or 
            (./preceding::Course/Title = ./Title)]/@Number)
```

## Q3

Return course numbers of courses taught by an instructor with first 
name `Daphne` or `Julie`. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return data($cat_ref//Course/Instructors/(Lecturer | Professor)[
            ./First_Name = 'Daphne' or 
						./First_Name = 'Julie'
						]/parent::*/parent::Course/@Number)
```

## Q4

Return the number (`count`) of courses that have no lecturers as instructors. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return count($cat_ref//Course/Instructors[count(Lecturer) = 0])
```

## Q5

Return titles of courses taught by the chair of a department. For this question, you 
may assume that all professors have distinct last names. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return $cat_ref//Course/Instructors/Professor[./Last_Name = 
    ./parent::Instructors/parent::Course/parent::Department/Chair/Professor/Last_Name
    ]/parent::Instructors/parent::Course/Title
```

## Q6

Return titles of courses that have both a lecturer and a professor as instructors. 
Return each title only once.  

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

let $courses := distinct-values($cat_ref//Course/Instructors[
        count(Lecturer) > 0 and count(Professor) > 0
        ]/parent::Course/Title)

return
        (for $c in $courses 
         return <Title>{$c}</Title>)
```

## Q7

Return titles of courses taught by a professor with the last name `Ng` but not 
by a professor with the last name `Thrun`. 

```xquery
let $c_ref := doc("courses.xml")/Course_Catalog//Course

return $c_ref/Instructors/Professor[./Last_Name = 'Ng' and 
        not(./following-sibling::*/Last_Name = 'Thrun') and
        not(./preceding-sibling::*/Last_Name = 'Thrun')]/parent::*/parent::*/Title
```


## Q8

Return course numbers of courses that have a course taught by Eric Roberts 
as a prerequisite. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

let $er_c := data($cat_ref//Course/Instructors/Professor[
                ./Last_Name = 'Roberts' and ./First_Name = 'Eric'
              ]/parent::*/parent::*/@Number)

let $c_num := (for $pre in $cat_ref//Prereq
                  for $num in $er_c
                  where $num = $pre
                  return data($pre/parent::*/parent::*/@Number))

return $c_num
```


## Q9

Create a summary of CS classes: List all CS department courses in 
order of enrollment. For each course include only its Enrollment 
(as an attribute) and its Title (as a sub-element). 


```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

let $courses := (for $c in $cat_ref//Course[
				./parent::Department/@Code = 'CS' and ./@Enrollment > 0]
        order by xs:int($c/@Enrollment)
        return 
            <Course Enrollment="{$c/@Enrollment}">
                {$c/Title}
            </Course>
        )
        
        
return <Summary>{$courses}</Summary>
```

## Q10

Return a `Professors` element that contains as sub-elements a listing of all professors in all 
departments, sorted by last name with each professor appearing once. The `Professor` 
sub-elements should have the same structure as in the original data. For this question, 
you may assume that all professors have distinct last names. Watch out -- the presence/absence 
of middle initials may require some special handling. 

This problem is quite challenging; congratulations if you get it right. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

let $p_last_names := distinct-values($cat_ref//Professor/Last_Name)

return <Professors>
          {for $lname in $p_last_names
          let $c_p := ($cat_ref//Professor[./Last_Name = $lname])[1]
          order by $lname
          return $c_p}
       </Professors>
```

## Q11

Expanding on the previous question (**Q10**), create an inverted course listing: Return an 
`Inverted_Course_Catalog` element that contains as sub-elements professors together with 
the courses they teach, sorted by last name. You may still assume that all professors have 
distinct last names. The `Professor` sub-elements should have the same structure as in the 
original data, with an additional single `Courses` sub-element under `Professor`, containing 
a further `Course` sub-element for each course number taught by that professor. `Professors` 
who do not teach any courses should have no `Courses` sub-element at all. 

This problem is very challenging; extra congratulations if you get it right.

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

let $p_last_names := distinct-values($cat_ref//Professor/Last_Name)

let $prof := 
          (for $lname in $p_last_names
          let $c_p := ($cat_ref//Professor[./Last_Name = $lname])[1]
          let $p_course := 
              (for $c_p in $cat_ref//Course[./Instructors/Professor/Last_Name = $lname]
                  return <Course>{data($c_p/@Number)}</Course>)
          order by $lname
          return 
              <Professor>
                  {$c_p/First_Name}
                  {$c_p/Middle_Initial}
                  {$c_p/Last_Name}
                  {
                      if(count($p_course) > 0) 
                      then <Courses>{$p_course}</Courses> 
                      else ()}
              </Professor>)
return 
    <Inverted_Course_Catalog>
        {$prof}
    </Inverted_Course_Catalog>
```

[1]: xml-data/courses-noID.xml
[2]: http://cs.stanford.edu/people/widom/