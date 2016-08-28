# Relational Algebra Exercises Answers

This is the seemingly correct answers to the Relational Algebra 
exercises from [Prof. J. Widom][1] (infamous :) db class. As an evaluator
we use the `RA` Interpreter from Jun Yang et al. which you can grab [here][2]
as well as get a syntax overview of the `RA` language [here][3]

The `SQL` data used to execute the `RA` queries upon is located [here][4]

## Q1

Find all pizzas eaten by at least one female over the age of 20. 

```
\project_{pizza} (\select_{age > 20 and gender = 'female'} 
    (Person \join_{name = n} (\rename_{n, pizza} Eats)))
```

## Q2

Find the names of all females who eat at least one pizza served by Straw Hat. 

Note: The pizza need not be eaten at Straw Hat.
 
```
\project_{name} (
    (\select_{gender = 'female'} Person)
        \join_{name = e_n}
    ((\rename_{e_n, e_p} Eats) 
        \join_{e_p = pizza and pizzeria = 'Straw Hat'} 
        Serves))
```

## Q3

Find all pizzerias that serve at least one pizza for less than $10 that 
*either* Amy or Fay (or both) eat. 

```
\project_{pizzeria}(
    (\rename_{name, e_pizza} (\select_{name = 'Amy' or name = 'Fay'} Eats))
        \join_{e_pizza = pizza and price < 10}
     Serves)
```

## Q4

Find all pizzerias that serve at least one pizza for less than $10 that 
*both* Amy and Fay eat. 

```
\project_{pizzeria} ((\rename_{p} ((\project_{pizza} (\select_{name = 'Amy'} Eats)) 
    \intersect 
(\project_{pizza} (\select_{name = 'Fay' } Eats))))
    \join_{p = pizza and price < 10}
Serves)
```

## Q5

Find the names of all people who eat at least one pizza served by Dominos but 
who do not frequent Dominos.

```
(\project_{name} (
    Eats 
    \join_{pizza = s_p and pizzeria = 'Dominos'} 
    (\rename_{pizzeria, s_p, price} Serves)))
    \diff    
(\project_{name} (\select_{pizzeria = 'Dominos'} Frequents)) 

```

## Q6

Find all pizzas that are eaten only by people younger than 24, or that cost less 
than $10 everywhere they're served. 

```
((\project_{pizza}  ( Person \join_{name = e_n and age < 24} (\rename_{e_n, pizza} Eats) ))
    \diff
(\project_{pizza} ( Person \join_{name = e_n and age >= 24} (\rename_{e_n, pizza} Eats) )))

\union

((\project_{pizza} Serves) \diff (\project_{pizza} (\select_{price > 10} Serves)))
```

## Q7

Find the age of the oldest person (or people) who eat mushroom pizza. 

This query is quite challenging; congratulations if you get it right.

```
\project_{age} (
(Person \join_{name = e_p and pizza = 'mushroom'} (\rename_{e_p, pizza} Eats) ) 
\cross
(Person \join_{name = e_p and pizza = 'mushroom'} (\rename_{e_p, pizza} Eats) )
)

\diff


\rename_{age} (
\project_{a1} (
\select_{a1 < a2} (
    (\rename_{a1} 
        (\project_{age} 
    (Person \join_{name = e_p and pizza = 'mushroom'} (\rename_{e_p, pizza} Eats))) )
    \cross
        (\rename_{a2}
        (\project_{age}
        (Person \join_{name = e_p and pizza = 'mushroom'} (\rename_{e_p, pizza} Eats))) )
)))
```

## Q8

Find all pizzerias that serve only pizzas eaten by people over 30. 

This query is quite challenging; congratulations if you get it right.

```
\project_{pizzeria} Serves

\diff

\project_{pizzeria} (
    Serves
        \join
        ((\project_{pizza} Serves)
        \diff
        (\project_{pizza}((\select_{age>'30'} Person) \join Eats)))
)
```

## Q9

Find all pizzerias that serve every pizza eaten by people over 30. 

This query is very challenging; extra congratulations if you get it right.

My tip: This known as the *division operation*; quite intimidating at first for sure. 
You can learn a bit more about it using [this][5] and [this][6]. Hope that helps!

```
(\project_{pizzeria} Serves)
    \diff
\project_{pizzeria} (

((\project_{pizzeria} Serves) 
        \cross 
        (\project_{pizza}(\select_{age>'30'}Person \join Eats)))
        
        \diff
        
        
(\project_{pizzeria, pizza} (((\select_{age > 30} Person) \join Eats) \join Serves)))
```


[1]: http://cs.stanford.edu/people/widom/
[2]: https://github.com/junyang/RA
[3]: https://users.cs.duke.edu/~junyang/ra/
[4]: schemas/pizza.sql
[5]: http://www.cs.arizona.edu/~mccann/research/divpresentation.pdf 
[6]: https://users.dcc.uchile.cl/~cgutierr/cursos/BD/divisionSQL.pdf