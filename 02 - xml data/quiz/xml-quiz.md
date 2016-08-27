# JSON Quiz answers

In this section the answer of the `XML` quiz. Graded assignments include six 
trivial `XML`-related answers, which can be viewed below. It has to be noted that 
the quiz generator will create *different* versions of the available selections as 
well as *mess up the order*; thus your mileage may vary.

## Q1

We're interested in *well-formed* `XML` that satisfies the following conditions:

* It has a root element `tasklist`
* The root element has 3 `task` sub-elements
* Each of the `task` sub-elements has an *attribute* named `name`
* The values of the `name` attributes for the 3 tasks are `eat`, `drink`, and `play`

Then select, from the choices below, the *well-formed* `XML` that meets the 
above requirements.

The *four* generated options in this instance were the following:

A:

```xml
<tasklist>
  <task name="eat"/>
  <task name="drink"/>
  <task name="play"/>
<tasklist>
```

B:

```xml
<tasklist>
  <task name="eat"/>
</tasklist>
<tasklist>
  <task name="drink"/>
</tasklist>
<tasklist>
  <task name="play"/>
</tasklist>
```

C:

```xml
<tasklist>
  <task name="eat"/>
  <task name="drink"/>
  <task name="play"/>
</tasklist>
```

D:

```xml
<tasklist>
  <task name="eat">
  <task name="drink">
  <task name="play">
</tasklist>
```

The correct answer in this instance was to select option **C**.

## Q2

An `XML` document contains the following portion:

```xml
<INFO>
    <ADDR>101 Maple St.</ADDR>
    <PHONE>555-1212</PHONE>
    <PHONE>555-4567</PHONE>
</INFO>
```

Which of the following could be the `INFO` element specification in a `DTD` that the 
document matches?

The *four* generated options in this instance were the following:

A:

```xml
<!ELEMENT INFO (ADDR,PHONE)>
```

B:

```xml
 <!ELEMENT INFO (#PCDATA)>
```

C:

```xml
<!ELEMENT INFO (NAME*,ADDR,PHONE+)>
```

D:

```xml
<!ELEMENT INFO (PHONE,ADDR,PHONE)>
```

The correct answer in this instance was to select option **C**.

## Q3

An `XML` document contains the following portion:

```xml
<EMP name = "Kermit">
    <ADDR>123 Sesame St.</ADDR>
    <PHONE type = "cell">555-1212</PHONE>
</EMP>
```

The *four* generated options in this instance were the following:

A:

```xml
<!ATTLIST PHONE type IDREFS #REQUIRED>
```

B:

```xml
<!ATTLIST PHONE owner IDREF #REQUIRED>
```

C:

```xml
<!ATTLIST ADDR zip CDATA #IMPLIED>
```

D:

```xml
<!ATTLIST PHONE type IDREF #IMPLIED>
```

The correct answer in this instance was to select option **B**.

## Q4

Here is a `DTD`:

```xml
<!DOCTYPE A [
    <!ELEMENT A (B+, C)>
    <!ELEMENT B (#PCDATA)>
    <!ELEMENT C (B?, D)>
    <!ELEMENT D (#PCDATA)>
]>
```

Which of the following sequences of opening and closing tags matches 
this `DTD`? 

Note: In actual `XML`, opening and closing tags would be enclosed in angle 
brackets, and some elements might have text sub-elements. This quiz focuses on 
the element sequencing and interleaving specified by the `DTD`.

The *four* generated options in this instance were the following:

A:

`A C B /B D /D /C /A`

B:

`A B /B B /B C B /B D /D /C /A`

C:

`A B /B B /B C B /B D /D /A`

D:

`A B /B C D /D /C B /B /A`

The correct answer in this instance was to select option **B**.

## Q5

Here is an `XML` `DTD`:

```xml
<!DOCTYPE meal [
    <!ELEMENT meal (person*,food*,eats*)>
    <!ELEMENT person EMPTY>
    <!ELEMENT food EMPTY>
    <!ELEMENT eats EMPTY>
    <!ATTLIST person name ID #REQUIRED>
    <!ATTLIST food name ID #REQUIRED>
    <!ATTLIST eats diner IDREF #REQUIRED dish IDREF #REQUIRED>
]>
```

Which of the following documents *match* the `DTD`?

A:

```xml
<meal>
  <person name="Alice"/>
  <food name="salad"/>
  <eats diner="Alice" dish="salad"/>
  <person name="Bob"/>
  <food name="salad"/>
  <eats diner="Bob" dish="salad"/>
  <person name="Carol"/>
  <food name="sandwich"/>
  <eats diner="Carol" dish="sandwich"/>
</meal>
```

B:

```xml
<meal>
  <person name="Alice"/>
  <person name="Bob"/>
  <person name="Carol"/>
  <person name="Dave"/>
  <food name="salad"/>
  <food name="turkey"/>
  <food name="sandwich"/>
  <eats diner="Alice" dish="turkey"/>
  <eats diner="Bob" dish="salad"/>
  <eats diner="turkey" dish="Dave"/>
</meal>
```

C:

```xml
<meal>
  <person name="Alice"/>
  <person name="Bob"/>
  <food name="salad"/>
  <eats diner="Alice" dish="food"/>
  <eats diner="Bob" dish="food"/>
</meal>
```

The *four* generated options in this instance were the following:

1) Only options A and B match
2) *All* three options match
3) Only the second
4) Only the first

The correct answer in this instance was to select option **3**.

## Q6

Study the following `XML` Schema specification:

```xml
<xs:schema xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:element name="person">
    <xs:complexType>
      <xs:sequence>
        <xs:element name="fname" type="xs:string"/>
        <xs:element name="initial" type="xs:string"
            minOccurs="0"/>
        <xs:element name="lname" type="xs:string"/>
        <xs:element name="address" type="xs:string"
            maxOccurs="2"/>
        <xs:choice>
          <xs:element name="major" type="xs:string"/>
          <xs:element name="minor" type="xs:string"
              minOccurs="2" maxOccurs="2"/>
        </xs:choice>
      </xs:sequence>
    </xs:complexType>
  </xs:element>
</xs:schema>
```

Then select, from the choices below, the `XML` that is valid according to the 
`XML` Schema specification above.

The *four* generated options in this instance were the following:

A:

```xml
  <person>
    <fname>John</fname>
    <initial>Q</initial>
    <lname>Public</lname>
    <address>123 Public Avenue, Seattle, WA 98001</address>
    <major>Computer Science</major>
  </person>
```

B:

```xml
  <person>
    <lname>Public</lname>
    <fname>John</fname>
    <initial>Q</initial>
    <title>Sr.</title>
    <address>123 Public Avenue, Seattle, WA 98001</address>
    <major>Computer Science</major>
  </person>
```

C:

```xml
  <person>
    <fname>John</fname>
    <initial>Q.</initial>
    <lname>Public</lname>
    <address>123 Public Avenue</address>
    <address>Seattle</address>
    <address>WA 98001</address>
    <major>Computer Science</major>
  </person>
```

D:

```xml
  <person>
    <fname>John</fname>
    <initial>Q.</initial>
    <lname>Public</lname>
    <address>123 Public Avenue</address>
    <address>Seattle</address>
    <address>WA 98001</address>
    <minor>Computer Science</minor>
    <minor>History</minor>
    <minor>Psychology</minor>
  </person>
```

The correct answer in this instance was to select option **A**.