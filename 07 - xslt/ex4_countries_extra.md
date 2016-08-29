# XSLT World Countries Extra Exercises Answers

This is the seemingly correct answers to the XSLT World Countries Extra (ungraded)
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1].

## Q1

Find all country names containing the string "stan"; return each one within a `Stan` 
element. 

Note: To specify quotes within an already-quoted XPath expression, use `quot;`. 
 
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
    <xsl:template match="//country[contains(data(./@name), 'stan')]">
        <xsl:for-each select=".">
            <Stan>
                <xsl:value-of select="./@name"/>
            </Stan>
        </xsl:for-each>
    </xsl:template>
    <xsl:template match="text()"/>
</xsl:stylesheet>
```

## Q2

Remove from the data all countries with area greater than 40,000 and all countries with no 
cities listed. Otherwise the structure of the data should be the same. 

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

    <xsl:template match="*|@*|text()">
        <xsl:copy>
            <xsl:apply-templates select="*|@*|text()"/>
        </xsl:copy>
    </xsl:template>

    <!-- magic delete trick to filter out the unwanted countries -->
    <xsl:template match="//country">
        <xsl:for-each select=".">
            <xsl:if test="not(./@area > 40000) and count(./city) > 0">
                <xsl:copy-of select="."/>
            </xsl:if>
        </xsl:for-each>
    </xsl:template>
</xsl:stylesheet>
```


[1]: xml-data/countries.xml
[2]: http://cs.stanford.edu/people/widom/