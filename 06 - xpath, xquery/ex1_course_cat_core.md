# XPath/XQuery Course Catalog Exercises Answers

This is the seemingly correct answers to the XPath/XQuery Course Catalog Core 
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1].

## Q1

Return all `Title` elements (of both departments and courses). 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return $cat_ref//Title
```

## Q2

Return last names of all department chairs. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return $cat_ref//Chair/Professor/Last_Name
```

## Q3

Return titles of courses with enrollment greater than 500. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return $cat_ref//Course[data(@Enrollment) > 500]/Title
```

## Q4

Return titles of departments that have some course that takes "CS106B" as a prerequisite. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

let $titles := distinct-values(
    for $pre in $cat_ref//Prereq[data(.) = "CS106B"]
    return $pre/parent::Prerequisites/parent::Course/parent::Department/Title)

return (for $t in $titles return <Title>{$t}</Title>)
```

## Q5

Return last names of all professors or lecturers who use a middle initial. Don't 
worry about eliminating duplicates. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return (for $p in $cat_ref//Middle_Initial
        return $p/parent::*/Last_Name)
```

## Q6

Return the count of courses that have a cross-listed course 
i.e., that have "Cross-listed" in their description. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return count($cat_ref//Course[contains(data(Description), 'Cross-listed')])
```

## Q7

Return the average enrollment of all courses in the CS department. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return avg($cat_ref//Course[data(./parent::Department/@Code) = 'CS']/@Enrollment)
```


## Q8

Return last names of instructors teaching at least one course that has "system" in 
its description and enrollment greater than 100. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return $cat_ref//Course[
	contains(data(Description), 'system') and 
	data(@Enrollment) > 100]/Instructors/*/Last_Name
```


## Q9

Return the title of the course with the largest enrollment. 

```xquery
let $cat_ref := doc("courses.xml")/Course_Catalog

return $cat_ref//Course[data(@Enrollment) = max($cat_ref//Course/@Enrollment)]/Title
```


[1]: xml-data/courses-noID.xml
[2]: http://cs.stanford.edu/people/widom/