# JSON Chapter

In this chapter the notions of `JSON` and it's syntax is covered. Graded 
assignments include some trivial quiz answers, which can be viewed below.
It has to be noted that the quiz generator will create *different* versions
of the available selections as well as *mess up the order*; thus your mileage 
may vary.

# JSON Quiz answers

We won't have a separate file for this one as the quiz is quite small 
(only four questions) so there's no point. Without further delay here are
the quiz questions as well as their answers.

## Q1

Which of the following is NOT a valid `JSON` object?

The *four* generated options in this instance were the following:

A:

```json
{ "name": "Smiley",
  "age": 20,
  "phone": {},
  "email": "smiley@xyz.com",
  "happy": true }
```

B:

```json
{ "name": "Smiley",
  "age": 20,
  "phone": "888-123-4567",
  "email": "smiley@xyz.com",
  "happy": yes }
```

C:

```json
{ "name": "Smiley",
  "age": 20,
  "phone": [ "888-123-4567", "888-765-4321" ],
  "email": "smiley@xyz.com",
  "happy": true }
```

D:

```json
{ "name": "Smiley",
  "age": 20,
  "phone": null,
  "email": null,
  "happy": true }
```

The correct answer in this instance was to select option **B**.

## Q2

Which of the following is NOT a valid `JSON` array?

The *four* generated options in this instance were the following:

A:

```json

[ 1, 2, "dog", "cat", true, false, [],
  {"pet":"dog", "fun":true} ]
```

B:

```json
[ [1, 2], ["dog", "cat"], [true, false], [1, "dog", null],
  {"pet":"dog", "fun":true} ]
```

C:

```json
[ {1}, {2}, {"dog"}, {"cat"}, {true}, {false},
  [1, "dog", null], {"pet":"dog", "fun":true} ]
```

D:

```json
[ [1], [2], ["dog"], ["cat"], [true], [false], [1], ["dog"],
  [null], {"pet":"dog", "fun":true} ]
```

The correct answer in this instance was to select option **C**.

## Q3

Consider the following `JSON` Schema specification:

```json
{
  "type": "object",
  "properties":
    { "ItemID": { "type":"string", "pattern":"Item*" },
      "ItemName": { "type":"string" },
      "Price": { "type":"integer", "minimum":10, "maximum":100 },
      "Sellers": { "type":"array", "maxItems":3,
                   "items": { "type":"string" }},
      "Ratings": { "type":"array",
                   "items":
                      { "type": "object",
                        "properties": {"Rater":
                                       {"type": "string", "optional": true},
                                       "Score":
                                       {"type": "integer", "minimum":1,
                                        "maxiumum":5}}}},
      "AvgRating": { "type":"number", "optional":true },
      "FreeShipping": {"type":"boolean" }
    }
}
```

Then select, from the choices below, the `JSON` data that *is valid* according to 
the `JSON` Schema specification above.

The *four* generated options in this instance were the following:

A:

```json
{ "ItemID": "Item123",
  "ItemName": "desk",
  "Price": 50,
  "Sellers": ["Amy","Ben"],
  "Ratings": [{"Rater":"Amy", "Score":5}, {"Score":1},
              {"Rater":"Carl", "Score":4}],
  "AvgRating": 3.33,
  "FreeShipping": [true] }
```

B:

```json
{ "ItemID": "Item123",
  "ItemName": "desk",
  "Price": 50,
  "Sellers": ["Amy","Ben"],
  "Ratings": [{"Rater":"Amy", "Score":5}, {"Score":1},
              {"Rater":"Carl", "Score":4}],
  "AvgRating": 3.33,
  "FreeShipping": "yes" }
```

C:

```json
{ "ItemID": "Item123",
  "ItemName": "desk",
  "Price": 50,
  "Sellers": ["Amy","Ben"],
  "Ratings": [{"Rater":"Amy", "Score":5}, {"Score":1},
              {"Rater":"Carl", "Score":4}],
  "FreeShipping": true }
```

D:

```json
{ "ItemID": "Item123",
  "ItemName": "desk",
  "Price": 200,
  "Sellers": ["Amy","Ben"],
  "Ratings": [{"Rater":"Amy", "Score":5}, {"Score":1},
              {"Rater":"Carl", "Score":4}],
  "AvgRating": 3.33,
  "FreeShipping": true }
```

The correct answer in this instance was to select option **C**.

## Q4

Consider the following `JSON` data:

```json
{ "A": [1,1,2,2], "B": {"C":3, "D":4}, "E":[5,6,true], "F": {"G": [null,7]} }
```

Which of the following could NOT be included as part of a `JSON` Schema specification 
that is satisfied by the JSON data above? Assume that every 
letter `("A", "B", "C", ...)` appears in the `JSON` Schema specification *exactly once*.

The *four* generated options in this instance were the following:

A: 

```json
"G": {"type":"array", "items": {"type":"integer"}}
```

B:

```json
"C": {"type":"integer"}
```

C:
 
```json
"G": {"type":"array", "items": {"type":["null","integer"]}}
```
 
D::
  
```json
"F": {"type":"object",
      "properties": {"G": {"type":"array",
                           "items": {"type":["null","integer"]}}}}  
```


The correct answer in this instance was to select option **A**.