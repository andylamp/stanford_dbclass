# XSLT Course Catalog Exercises Answers

This is the seemingly correct answers to the XSLT Course Catalog Core (graded)
exercises from [Prof. J. Widom][2] (infamous :) db class.

The `XML` data is located [here][1].

## Q1

Return a list of the department `Title` elements contained in [1][1]. 

Your solution should fill in the following stylesheet: 

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="...">
    <!-- ... template body ... -->
    </xsl:template>
    <!-- ... more templates as needed ... -->
</xsl:stylesheet>
```

The answer is the following:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0"  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:output method="xml" indent="yes" omit-xml-declaration="no" />
    <xsl:template match="Department">
        <xsl:copy>
            <xsl:apply-templates select="Department" />
        </xsl:copy>
    </xsl:template>

    <xsl:template match="Department[Title]">
        <xsl:copy-of select="Title" />
    </xsl:template>
</xsl:stylesheet>
```

## Q2

Return a list of `Department` elements with no attributes and two sub-elements each: 
the department title and the entire `Chair` sub-element structure. 

Your solution should fill in the following stylesheet: 

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:template match="...">
    <!-- ... template body ... -->
    </xsl:template>
    <!-- ... more templates as needed ... -->
</xsl:stylesheet>
```

The answer is the following:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<xsl:stylesheet version="2.0"  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
<xsl:output method="xml" indent="yes" omit-xml-declaration="no" />
    <xsl:template match="Department">
        <xsl:for-each select=".">
            <Department>
                <xsl:copy-of select="Title"/>
                <xsl:copy-of select="Chair"/>
            </Department>
        </xsl:for-each>
    </xsl:template>
</xsl:stylesheet>
```

[1]: xml-data/courses-noID.xml
[2]: http://cs.stanford.edu/people/widom/