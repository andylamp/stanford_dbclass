# XSLT Course Catalog Extra Exercises Answers

This is the seemingly correct answers to the XSLT Course Catalog Extra (ungraded)
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1].

## Q1

Return all courses with enrollment greater than 500. Retain the structure of 
`Course` elements from the original data. 

Your solution should fill in the following stylesheet: 

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="...">
    <! -- ... template body ... -->
    </xsl:template>
    <!-- ... more templates as needed ... -->
</xsl:stylesheet>
```

The answer is the following:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0"  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:output method="xml" indent="yes" omit-xml-declaration="no" />
    <xsl:template match="Course[@Enrollment &gt; 500]">
        <xsl:copy-of select="."/>
    </xsl:template>
    
    <xsl:template match="text()"/>
</xsl:stylesheet>
```

## Q2

Remove from the data all courses with enrollment greater than 60, or with no 
enrollment listed. Otherwise the structure of the data should be the same. 

Your solution should fill in the following stylesheet: 

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="...">
    <! -- ... template body ... -->
    </xsl:template>
    <!-- ... more templates as needed ... -->
</xsl:stylesheet>
```

The answer is the following:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0"  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

    <xsl:template match="*|@*|text()">
        <xsl:copy>
            <xsl:apply-templates select="*|@*|text()"/>
        </xsl:copy>
    </xsl:template>

    <xsl:template match="Course[@Enrollment &gt; 60 or not(@Enrollment)]"/>
</xsl:stylesheet>
```

## Q3

Create a summarized version of the EE part of the course catalog. For each course in EE, 
return a `Course` element, with its `Number` and `Title` as attributes, its 
`Description` as a sub-element, and the last name of each instructor as an 
`Instructor` sub-element. Discard all information about department titles, 
chairs, enrollment, and prerequisites, as well as all courses in departments 
other than EE. 

Note: To specify quotes within an already-quoted XPath expression, use quot;. 

Your solution should fill in the following stylesheet: 

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="...">
    <! -- ... template body ... -->
    </xsl:template>
    <!-- ... more templates as needed ... -->
</xsl:stylesheet>
```

The answer is the following:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0"  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

    <xsl:template match="/">
        <xsl:for-each select="//Department[@Code = 'EE']//Course">
            <Course Number="{./@Number}" Title="{./Title}">
                <xsl:copy-of select="./Description"/>
                <xsl:for-each select="./Instructors/(Professor | Lecturer)//Last_Name">
                    <Instructor>
                        <xsl:value-of select="."/>
                    </Instructor>
                </xsl:for-each>
            </Course>
        </xsl:for-each>
    </xsl:template>

    <xsl:template match='text()' />
</xsl:stylesheet>
```

## Q4

Create an `HTML` table with one-pixel border that lists all CS department courses 
with enrollment greater than 200. Each row should contain three cells: 

1) the course number in italics 
2) course title in bold, and 
3) enrollment. 

Sort the rows alphabetically by course title. No header is needed. 

Notes: For formatting, just use `"table border=1`, and `<b>` and `<i>` tags for 
bold and italics respectively. To specify quotes within an already-quoted 
XPath expression, use `quot;`. 

Your solution should fill in the following stylesheet: 

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="...">
    <! -- ... template body ... -->
    </xsl:template>
    <!-- ... more templates as needed ... -->
</xsl:stylesheet>
```

The answer is the following:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0"  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">

    <xsl:template match="/">
            <table border="1">
                <xsl:for-each select="//Course[@Enrollment > 200]">
                    <xsl:sort select="./Title" data-type="text"/>
                    <tr>
                        <td><i><xsl:value-of select="./@Number"/></i></td>
                        <td><b><xsl:value-of select="./Title" /></b></td>
                        <td><xsl:value-of select="./@Enrollment"/></td>
                    </tr>
                </xsl:for-each>
            </table>
    </xsl:template>

    <xsl:template match='text()' />
</xsl:stylesheet>
```

[1]: xml-data/courses-noID.xml
[2]: http://cs.stanford.edu/people/widom/