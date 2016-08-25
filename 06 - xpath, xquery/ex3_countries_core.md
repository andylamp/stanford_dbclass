# XPath/XQuery World Countries Exercises Answers

This is the seemingly correct answers to the XPath/XQuery World Countries 
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1]

## Q1

Return the area of Mongolia. 

Note: Your solution will need to reference `doc("countries.xml")` to access the data. 
**Reminder**: To return the value of an attribute `attr`, you must use `data(@attr)`, 
although just `@attr` may be used in comparisons. You will need to remember this for 
some later questions as well. 


```xquery
doc("countries.xml") /countries/country[@name = 'Mongolia']/data(@area)
```

Return the names of all cities that have the same name as the country 
in which they are located. 

## Q2

```xquery
let $countries_ref := doc('countries.xml')/countries
return $countries_ref//city[name = parent::country/data(@name)]/name
```

## Q3

Return the average population of Russian-speaking countries. 

```xquery
let $countries_ref := doc('countries.xml')/countries
return avg($countries_ref/country[language = 'Russian']/data(@population))
```

## Q4

Return the names of all countries that have at least three cities with 
population greater than 3 million. 

Note: This is *hard*.

```xquery
let $countries_ref := doc('countries.xml')/countries

for $c in $countries_ref/country
  where count($c/city[data(population) > 3000000]) > 3
  return data($c/@name)
```

## Q5

Create a list of French-speaking and German-speaking countries. The result 
should take the form:

```xml
<result>
  <French>
    <country>country-name</country>
    <country>country-name</country>
    ...
  </French>
  <German>
    <country>country-name</country>
    <country>country-name</country>
    ...
  </German>
</result>
```

The answer is:

```xquery
let $countries_ref := doc('countries.xml')/countries

return 
        <result>
        <French>
            {
                for $f_c in $countries_ref/country[language = 'French']
                return 
                    <country>{data($f_c/@name)}</country>
            }
        </French>
        <German>
            {
                for $g_c in $countries_ref/country[language = 'German']
                return 
                    <country>{data($g_c/@name)}</country>
            }
        </German>
    </result>
```

## Q6

Return the countries with the highest and lowest population densities. Note that 
because the "/" operator has its own meaning in XPath and XQuery, the division 
operator is infix "div". To compute population density use `(@population div @area)`. 
You can assume density values are unique. The result should take the form:

```xml
<result>
  <highest density="value">country-name</highest>
  <lowest density="value">country-name</lowest>
</result>
```

Again... this is *hard*. The answer is:

```xquery
let $countries_ref := doc('countries.xml')/countries

let $high_res := (for $max_c in $countries_ref/country
                    where $max_c/data(@population div @area) =
                            max($countries_ref/country/data(@population div @area)) 
                            return $max_c)
                            
let $min_res := (for $min_c in $countries_ref/country
                where $min_c/data(@population div @area) = 
                        min($countries_ref/country/data(@population div @area)) 
                        return $min_c)
return
    <result>
        <highest density="{$high_res/data(@population div @area)}">{$high_res/data(@name)}</highest>
        <lowest density="{$min_res/data(@population div @area)}">{$min_res/data(@name)}</lowest>
    </result>
```

[1]: xml-data/countries.xml
[2]: http://cs.stanford.edu/people/widom/