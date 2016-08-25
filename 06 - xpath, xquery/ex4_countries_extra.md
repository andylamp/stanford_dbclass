# XPath/XQuery World Countries Exercises Answers

This is the seemingly correct answers to the XPath/XQuery World Countries 
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1]

## Q1

Return the names of all countries with population greater than 100 million. 

If the country `name` is a key, then you could write the query as follows:

```xquery
let $countries_ref := doc('countries.xml')/countries

return $countries_ref/country[data(@population) > 100000000]/data(@name) 
```

If not, then you have to write it this way:

```xquery
let $countries_ref := doc('countries.xml')/countries

return distinct-values($countries_ref/country[data(@population) > 100000000]/data(@name)) 
```

## Q2

Return the names of all countries where over 50% of the population speaks German. 
Hint: Depending on your solution, you may want to use `.`, which refers to the 
"current element" within an XPath expression.

```xquery
let $countries_ref := doc('countries.xml')/countries

return $countries_ref/country/language[data(@percentage) > 50 and . = 'German']/parent::country/data(@name)
```

## Q3

Return the names of all countries where a city in that country contains more than 
one-third of the country's population. 

```xquery
let $countries_ref := doc('countries.xml')/countries

return $countries_ref/country/city[population > (parent::country/data(@population) div 3)]/parent::country/data(@name) 
```

## Q4

Return the population density of Qatar. Note: Since the `/` operator has its own 
meaning in XPath and XQuery, the division operator is `div`. To compute population 
density use `(@population div @area)`. 

```xquery
let $countries_ref := doc('countries.xml')/countries

return $countries_ref/country[data(@name) = 'Qatar']/(@population div @area)
```

## Q5

Return the names of all countries whose population is less than one thousandth that 
of some city (in any country). 

```xquery
let $countries_ref := doc('countries.xml')/countries

return $countries_ref/country[@population < //city/(population div 1000)]/data(@name)
```

## Q6

Return all city names that appear more than once, i.e., there is more than one city 
with that name in the data. Return only one instance of each such city name. 

Hint: You might want to use the "preceding" and/or "following" navigation axes for 
this query, which were not covered in the video or our demo script; they match 
any preceding or following node, not just siblings.) 


```xquery
let $cities_ref := doc('countries.xml')/countries/country//city

let $distinct_names := distinct-values($cities_ref[
                (name = (./preceding::city/name)) or 
                (name = (./following::city/name))]/name)
								
for $d_n in $distinct_names
return
    <name>{$d_n}</name>
```

## Q7

Return the names of all countries containing a city such that some other country has 
a city of the same name. 

Hint: You might want to use the "preceding" and/or "following" navigation axes for 
this query, which were not covered in the video or our demo script; they match 
any preceding or following node, not just siblings.

```xquery
let $cities_ref := doc('countries.xml')/countries/country//city

let $distinct_names := distinct-values($cities_ref[
                ((name = (./preceding::city/name)) 
                 or
                (name = (./following::city/name))) 
                and
                (not(./parent::country/@name = ./following::country/@name))
                and
                (not(./parent::country/@name = ./preceding::country/@name))
                ]/parent::country/@name)
                
return $distinct_names
```

## Q8

Return the names of all countries whose name textually contains a language spoken in 
that country. For instance, Uzbek is spoken in Uzbekistan, so return Uzbekistan. 

Hint: You may want to use `.`, which refers to the "current element" within an 
XPath expression. 

```xquery
let $countries_ref := doc('countries.xml')/countries

for $c in $countries_ref/country
    where some $l_part in $c/language satisfies contains($c/@name, $l_part)
    return $c/data(@name)
```

## Q9

Return the names of all countries in which people speak a language whose name 
textually contains the name of the country. For instance, Japanese is spoken in 
Japan, so return Japan. 

Hint: You may want to use `.`, which refers to the "current element" within an 
XPath expression.

```xquery
let $countries_ref := doc('countries.xml')/countries

for $c in $countries_ref/country
    where some $l_part in $c/language satisfies contains($l_part, $c/@name)
    return $c/data(@name)
```

## Q10

Return all languages spoken in a country whose name textually contains the 
language name. For instance, German is spoken in Germany, so return German. 

Hint: Depending on your solution, may want to use `data(.)`, which returns 
the text value of the "current element" within an XPath expression. 

```xquery
let $countries_ref := doc('countries.xml')/countries

let $all_lang := $countries_ref/country/language

let $spoken_langs := 
    for $lang in $all_lang
    where contains($lang/parent::country/@name, $lang)
    return $lang/data(.)
    
return distinct-values($spoken_langs)
```

## Q11

Return all languages whose name textually contains the name of a country 
in which the language is spoken. For instance, Icelandic is spoken in Iceland, 
so return Icelandic. 

Hint: Depending on your solution, may want to use `data(.)`, which returns the 
text value of the "current element" within an XPath expression. 

```xquery
let $countries_ref := doc('countries.xml')/countries

let $all_lang := $countries_ref/country/language

let $spoken_langs := 
    for $lang in $all_lang
    where contains($lang, $lang/parent::country/@name)
    return $lang/data(.)
    
return distinct-values($spoken_langs)
```

## Q12

Return the number of countries where Russian is spoken. 

```xquery
let $countries_ref := doc('countries.xml')/countries

return count(distinct-values($countries_ref/country[language = 'Russian']/@name))
```

## Q13

Return the names of all countries for which the data does not include any languages 
or cities, but the country has more than 10 million people. 

```xquery
let $countries_ref := doc('countries.xml')/countries

return distinct-values($countries_ref/country[
    not(exists(language) or exists(city)) and 
    @population > 10000000]/@name)
```

## Q14

Return the name of the country with the highest population. 

Hint: You may need to explicitly cast population numbers as integers with `xs:int()` 
to get the correct answer.

```xquery
let $countries_ref := doc('countries.xml')/countries

return
    data(((for $c in $countries_ref/country
    order by xs:int($c/@population) descending
    return <country population="{$c/@population}">{$c/@name}</country>)[1])/@name)

```

Another, more clear version of the above would be:

```xquery
let $country_ref := doc('countries.xml')/countries/country

let $max_pop :=
    (for $c in $country_ref
    order by xs:int($c/@population) descending
    return <country population="{$c/@population}" name="{$c/@name}" />)[1]
    
return data($max_pop/@name)
```

## Q15

Return the name of the country that has the city with the highest population. 

Hint: You may need to explicitly cast population numbers as integers with `xs:int()` 
to get the correct answer.

```xquery
let $country_ref := doc('countries.xml')/countries/country

let $max_pop :=
    (for $c in $country_ref
    let $city_pop_max := max(
        for $city_in_country in $c/city
        order by $city_in_country/population
        return $city_in_country/population
    )
    order by $city_pop_max descending
    return <country population="{$city_pop_max}" name="{$c/@name}" />)[1]
    
return data($max_pop/@name)
```

## Q16

Return the average number of languages spoken in countries where Russian is spoken. 

The follwing segment is a **correct** answer.

```xquery
let $country_ref := doc('countries.xml')/countries/country

let $max_pop :=
    (for $c in $country_ref[data(language) = 'Russian']
    let $lang_cnt := count($c/language)
    return <avg name="{$c/@name}" count="{$lang_cnt}" />)
    
return avg($max_pop/@count)
```

But will give an error (possibly) due to rouding. If you look up closely in  both
versions `count` produce the following tuple: `2 3 4 1 3 3`, which you can 
easily view by changing the last `return` statement to be instead of 
`avg($max_pop/@count)` to `data($max_pop/@count)`. This produces a solution 
which is  `2.6666666666666665` and does not match the one requested, which is: 
`2.666666666666666667`; as you can see the numbers are *really* close. I don't
know what magic happens behind the scenes but if you move the `avg` function to be
at the `for` statement it all works out. The following version produces the 
correct result required. Unfortunately, you would probably be baffled as to why 
it might not work as you'd expect if you thought and tried the previous version 
initially.

```xquery
let $country_ref := doc('countries.xml')/countries/country

let $max_pop :=
    avg((for $c in $country_ref[data(language) = 'Russian']
    let $lang_cnt := count($c/language)
    return $lang_cnt))

return data($max_pop)
```

## Q17

Return all country-language pairs where the language is spoken in the country and the 
name of the country textually contains the language name. Return each pair as a 
country element with language attribute, e.g.,

```xml
<country language="French">French Guiana</country>
```

The answer is:

```xquery
let $lang_ref := doc('countries.xml')/countries/country/language

let $lang_pairs := 
    for $lang in $lang_ref
    where contains($lang/parent::country/@name, $lang)
    return 
        <country language="{$lang/data(.)}">
            {xs:string($lang/parent::country/@name)}
        </country>
    
return $lang_pairs
```

## Q18

Return all countries that have at least one city with population greater than 7 
million. For each one, return the country name along with the cities greater than 
7 million, in the format:

```xml
<country name="country-name">
  <big>city-name</big>
  <big>city-name</big>
  ...
</country>
```

The answer is:

```xquery
let $country_ref := doc('countries.xml')/countries/country

let $city_pairs :=
    for $c in $country_ref
    let $big_city :=
        (for $city in $c/city
        where $city/population > 7000000
        return <big>{xs:string($city/name)}</big>)
    where count($big_city) > 0
    return
        <country name="{$c/@name}">
            {$big_city}
        </country>
        
return $city_pairs
```

## Q19

Return all countries where at least one language is listed, but the total percentage 
for all listed languages is less than 90%. Return the country element with its name 
attribute and its language subelements, but no other attributes or subelements. 

```xquery
let $countries_ref := doc('countries.xml')/countries/country

let $countries :=
    (for $c in $countries_ref
    let $lang_per_sum :=
        sum(
            for $s in $c/language
            return data(xs:float($s/@percentage))
        )
    where $lang_per_sum > 0 and $lang_per_sum < 90
    return 
        <country name="{$c/@name}">
            {$c/language}
        </country>)
                 
return $countries
```

## Q20

Return all countries where at least one language is listed, and every listed 
language is spoken by less than 20% of the population. Return the country element 
with its name attribute and its language subelements, but no other attributes or 
subelements. 

```xquery
let $countries_ref := doc('countries.xml')/countries/country

let $countries :=
    (for $c in $countries_ref
    
    let $lang_per_sum :=
        sum(
            for $s in $c/language
            return data(xs:float($s/@percentage))
        )
    where $lang_per_sum > 0 and 
        (every $per in $c/language/@percentage satisfies xs:float($per) < 20)
    return 
        <country name="{$c/@name}">
            {$c/language}
        </country>)
                 
return $countries
```

## Q21

Find all situations where one country's most popular language is another country's 
least popular, and both countries list more than one language. 

Hint: You may need to explicitly cast percentages as floating-point numbers with 
`xs:float()` to get the correct answer.) Return the name of the language and the 
two countries, each in the format:

```xml
<LangPair language="lang-name">
  <MostPopular>country-name</MostPopular>
  <LeastPopular>country-name</LeastPopular>
</LangPair>
```

The answer is quite tricky to find correctly, but here it is:

```xquery
let $c_r := doc('countries.xml')/countries/country
return
    (for $c1 in $c_r[count(language) > 1]
    let $lg := 
            data((for $p in $c1/language 
                order by xs:float($p/@percentage) descending 
                return $p)[1])            
    
    let $lp := 
        (for $c2 in $c_r[count(language) > 1]
             for $l2 in $c2/language 
             where data($l2) = $lg and 
                 xs:float($l2/data(@percentage)) = 
                 xs:float(min($c2/language/data(@percentage)))     
         return data($l2/parent::country/@name))
     return 
         (for $p in $lp 
         return 
         <LangPair language="{data($lg)}">
             <MostPopular>{data($c1/@name)}</MostPopular>
             <LeastPopular>{$p}</LeastPopular>
         </LangPair>))  
```

## Q22

For each language spoken in one or more countries, create a `language` element with a 
`name` attribute and one `country` subelement for each country in which the language 
is spoken. The `country` subelements should have two attributes: the country `name`, 
and `speakers` containing the number of speakers of that language (based on language 
percentage and the country's population). Order the result by language name, and 
enclose the entire list in a single `languages` element. For example, your result 
might look like:

```xml
<languages>
  ...
  <language name="Arabic">
    <country name="Iran" speakers="660942"/>
    <country name="Saudi Arabia" speakers="19409058"/>
    <country name="Yemen" speakers="13483178"/>
  </language>
  ...
</languages>
```

One *correct* answer is:

```xquery
let $c_r := doc('countries.xml')/countries/country

let $dl := distinct-values(data($c_r[count(language) > 0]//language))

let $l_c := 
    (for $l in $dl
        let $a := (for $cc in $c_r[data(language) = $l and count(language) > 0]
        let $sp := (xs:float($cc/language[data(.) = $l]/@percentage) div 100)
        return (<country name="{$cc/@name}" speakers="{xs:int($cc/@population * $sp)}"/>))
        order by $l
        return (<language name="{$l}">
                    {$a}
                </language>))

return <languages>
        {$l_c}
       </languages>
```

But again due to *rounding* errors the results will be a little different. Another 
version of this answer that produces the correct result in the online grader is 
the following:

```xquery
let $c_r := doc('countries.xml')/countries/country

let $dl := distinct-values(data($c_r[count(language) > 0]//language))

let $l_c := 
    (for $l in $dl
        let $a := (for $cc in $c_r[data(language) = $l and count(language) > 0]
        let $sp := ($cc/language[data(.) = $l]/@percentage div 100)
        return (<country name="{$cc/@name}" speakers="{xs:int($cc/@population * $sp)}"/>))
        order by $l
        return (<language name="{$l}">
                    {$a}
                </language>))

return <languages>
        {$l_c}
       </languages>
```


[1]: xml-data/countries.xml
[2]: http://cs.stanford.edu/people/widom/