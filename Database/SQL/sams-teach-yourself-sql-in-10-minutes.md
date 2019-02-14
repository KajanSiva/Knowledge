# Sams teach yourself - SQL in 10 minutes

## 1. Understanding SQL

Main concepts :
- Database : A structured list of data of a specific type.
- Table : A structured list of data of a specific type.
- Schema : Information about database and table layout and properties.
- Column : Information about database and table layout and properties.
- Row : A record in a table
- Primary key: A column (or set of columns) whose values uniquely identify every row in a table.

Each column in a database has an associated datatype. A datatype defines what type of data the column can contain.

---

## 2. Retrieving data

Retrieving individual columns :
```
SELECT prod_id, prod_name, prod_price
FROM Products;
```

By default, the data is not ordered.

Retrieving all columns :
```
SELECT *
FROM Products;
```

Retrieving distinct rows :
```
SELECT *
FROM Products;
```

The DISTINCT keyword applies to all columns, not just the one it precedes.

Limiting results :
```
SELECT prod_name
FROM Products
LIMIT 5;
```

Retrieving next rows :
```
SELECT prod_name
FROM Products
LIMIT 5 OFFSET 5;
```

### Comments

There are three different ways to comment SQL :
- '--' at the end of a line
- '#' at the beginning of a line
- '/*' and '*/' around text to comment

---

## 3. Sorting retrieved data

To explicitly sort data retrieved using a SELECT statement, the ORDER BY clause is used. ORDER BY takes the name of one or more columns by which to sort the output.

```
SELECT prod_name
FROM Products
ORDER BY prod_name;
```

When specifying an ORDER BY clause, be sure that it is the last clause in your SELECT statement. If it is not the last clause, an error will be generated.

Although more often than not the columns used in an ORDER BY clause will be ones selected for display, this is actually not required. It is perfectly legal to sort data by a column that is not retrieved.

To sort by multiple columns, simply specify the column names separated by commas

```
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price, prod_name;
```

In addition to being able to specify sort order using column names, ORDER BY also supports ordering specified by relative column position.

```
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY 2, 3;
```

Data sorting is not limited to ascending sort orders (from A to Z). Although this is the default sort order, the ORDER BY clause can also be used to sort in descending order (from Z to A). To sort by descending order, the keyword DESC must be specified.

```
SELECT prod_id, prod_price, prod_name
FROM Products
ORDER BY prod_price DESC;
```

If you want to sort descending on multiple columns, be sure each column has its own DESC keyword.

---

## 4. Filtering data

Within a SELECT statement, data is filtered by specifying search criteria in the WHERE clause. The WHERE clause is specified right after the table name (the FROM clause).

```
SELECT prod_name, prod_price
FROM Products
WHERE prod_price = 3.49;
```

When using both ORDER BY and WHERE clauses, make sure that ORDER BY comes after the WHERE, otherwise an error will be generated.

### WHERE clause operators

- '='
- '<>'
- '!='
- '<'
- '<='
- '!<'
- '>'
- '>='
- '!>'
- 'BETWEEN'
- 'IS NULL'

Some of the operators are redundant (for example, <> is the same as !=. !< [not less than] accomplishes the same effect as >= [greater than or equal to]. Not all of these operators are supported by all DBMSs.

Checking for a range of values :
```
SELECT prod_name, prod_price
FROM Products
WHERE prod_price BETWEEN 5 AND 10;
```

To determine if a value is NULL, you cannot simply check to see if = NULL. Instead, the SELECT statement has a special WHERE clause that can be used to check for columns with NULL values—the IS NULL clause. The syntax looks like this:

```
SELECT prod_name
FROM Products
WHERE prod_price IS NULL;
```

You might expect that when you filter to select all rows that do not have a particular value, rows with a NULL will be returned. But they will not. Because of the special meaning of unknown, the database does not know whether or not they match, and so they are not returned when filtering for matches or when filtering for non-matches.

When filtering data, make sure to verify that the rows with a NULL in the filtered column are really present in the returned data.

---

## 5. Advanced data filtering

All the WHERE clauses introduced in Lesson 4, “Filtering Data,” filter data using a single criteria. For a greater degree of filter control, SQL lets you specify multiple WHERE clauses. These clauses may be used in two ways: as AND clauses or as OR clauses.

Using the AND operator :
```
SELECT prod_id, prod_price, prod_name
FROM Products
WHERE vend_id = 'DLL01' AND prod_price <= 4;
```

Using the OR operator :
```
SELECT prod_name, prod_price
FROM Products
WHERE vend_id = 'DLL01' OR vend_id = 'BRS01';
```

SQL (like most languages) processes AND operators before OR operators.

Whenever you write WHERE clauses that use both AND and OR operators, use parentheses to explicitly group operators. Don’t ever rely on the default evaluation order, even if it is exactly what you want. There is no downside to using parentheses, and you are always better off eliminating any ambiguity.

### Using the IN operator

The IN operator is used to specify a range of conditions, any of which can be matched. IN takes a comma-delimited list of valid values, all enclosed within parentheses.

```
SELECT prod_name, prod_price
FROM Products
WHERE vend_id  IN ('DLL01','BRS01')
ORDER BY prod_name;
```

The biggest advantage of IN is that the IN operator can contain another SELECT statement, enabling you to build highly dynamic WHERE clauses.

### Using the NOT operator

The WHERE clause’s NOT operator has one function and one function only—NOT negates whatever condition comes next.

```
SELECT prod_name
FROM Products
WHERE NOT vend_id  = 'DLL01'
ORDER BY prod_name;
```

---

## 6. Using wildcard filtering

Wildcards : Special characters used to match parts of a value.

Search pattern : A search condition made up of literal text, wildcard characters, or any combination of the above.

To use wildcards in search clauses, the LIKE operator must be used. LIKE instructs the DBMS that the following search pattern is to be compared using a wildcard match rather than a straight equality match.

Wildcard searching can only be used with text fields (strings), you can’t use wildcards to search fields of non-text datatypes.

### The Percent Sign '%' Wildcard

Within a search string, % means, match any number of occurrences of any character.

```
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE 'Fish%';
```

Wildcards can be used anywhere within the search pattern, and multiple wildcards may be used as well.

It may seem that the % wildcard matches anything, there is one exception, NULL. Not even the clause WHERE prod_name LIKE '%' will match a row with the value NULL as the product name.

### The underscore '_' Wildcard

Another useful wildcard is the underscore (_). The underscore is used just like %, but instead of matching multiple characters the underscore matches just a single character.

```
SELECT prod_id, prod_name
FROM Products
WHERE prod_name LIKE '__ inch teddy bear';
```

Unlike % which can match 0 characters, _ always matches 1 character—no more and no less.

### The brackets '[]' Wildcard

The brackets ([]) wildcard is used to specify a set of characters, any one of which must match a character in the specified position (the location of the wildcard).

Unlike the wildcards describes thus far, the use of [] to create sets is not supported by all DBMSs. Sets are supported by Microsoft Access and Microsoft SQL Server.

```
SELECT cust_contact
FROM Customers
WHERE cust_contact LIKE '[JM]%'
ORDER BY cust_contact;
```

### Tips

As you can see, SQL’s wildcards are extremely powerful. But that power comes with a price: Wildcard searches typically take far longer to process than any other search types discussed previously.

- Don’t overuse wildcards. If another search operator will do, use it instead.
- When you do use wildcards, try to not use them at the beginning of the search pattern unless absolutely necessary. Search patterns that begin with wildcards are the slowest to process.
- Pay careful attention to the placement of the wildcard symbols. If they are misplaced, you might not return the data you intended.

---

## 7. Creating calculated fields

### Concatenating fields

The solution is to concatenate the three columns. In SQL SELECT statements, you can concatenate columns using a special operator. Depending on what DBMS you are using, this can be a plus sign (+) or two pipes (||).

```
SELECT vend_name || ' (' || vend_country || ')'
FROM Vendors
ORDER BY vend_name;
```

spaces. Many databases (although not all) save text values padded to the column width, so your own results may indeed not contain those extraneous spaces. To return the data formatted properly, you must trim those padded spaces. This can be done using the SQL RTRIM() function, as follows:

```
SELECT RTRIM(vend_name) || ' (' || RTRIM(vend_country) || ')'
FROM Vendors
ORDER BY vend_name;
```

### Using aliases

Calculated fields has no name, it's simply a value. But an unnamed column cannot be used within a client application because there is no way for the client to refer to that column.

To solve this problem, SQL supports column aliases. An alias is just that, an alternate name for a field or value. Aliases are assigned with the AS keyword.

```
SELECT RTRIM(vend_name) + ' (' + RTRIM(vend_country) + ')'
       AS vend_title
FROM Vendors
ORDER BY vend_name;
```

### Performing mathematical calculations

Another frequent use for calculated fields is performing mathematical calculations on retrieved data.

```
SELECT prod_id,
       quantity,
       item_price,
       quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;
```

### Tips

SELECT provides a great way to test and experiment with functions and calculations. Although SELECT is usually used to retrieve data from a table, the FROM clause may be omitted to simply access and work with expressions.

For example, SELECT 3 * 2; would return 6, SELECT Trim(' abc '); would return abc, and SELECT Now(); uses the Now() function to return the current date and time. You get the idea—use SELECT to experiment as needed.

---

## 8. Using data manipulation functions

Like almost any other computer language, SQL supports the use of functions to manipulate data. Functions are operations that are usually performed on data, usually to facilitate conversion and manipulation.

Unlike SQL statements (for example, SELECT), which for the most part are supported by all DBMSs equally, functions tend to be very DBMS specific. In fact, very few functions are supported identically by all major DBMSs. Although all types of functionality are usually available in each DBMS, the function name or syntax can differ greatly.

Most SQL implementations support the following types of functions:
- Text functions are used to manipulate strings of text (for example, trimming or padding values and converting values to upper and lowercase).
- Numeric functions are used to perform mathematical operations on numeric data (for example, returning absolute numbers and performing algebraic calculations).
- Date and time functions are used to manipulate date and time values and to extract specific components from these values (for example, returning differences between dates, and checking date validity).
- System functions return information specific to the DBMS being used (for example, returning user login information).

### Text manipulation functions

Commonly used text manipulation functions :
- LEFT() : Returns characters from left of string
- LENGTH() or DATALENGTH() or LEN() : Returns the length of a string
- LOWER() or LCASE() : Converts string to lowercase
- LTRIM()
- RIGHT()
- RTRIM()
- SOUNDEX() : Returns a string SOUNDEX value
- UPPER() or UCASE()

SOUNDEX is an algorithm that converts any string of text into an alphanumeric pattern describing the phonetic representation of that text. SOUNDEX takes into account similar sounding characters and syllables, enabling strings to be compared by how they sound rather than how they have been typed. Although SOUNDEX is not a SQL concept, most DBMSs do offer SOUNDEX support.

SOUNDEX() is not supported by Microsoft Access or PostgreSQL, and so the following example will not work on those DBMSs.

### Date and time manipulation functions

The format used to store dates and times is usually of no use to your applications, and so date and time functions are almost always used to read, expand, and manipulate these values. Because of this, date and time manipulation functions are some of the most important functions in the SQL language. Unfortunately, they also tend to be the least consistent and least portable.

To retrieve a list of all orders made in 2012 in PostgreSQL, do the following:

```
SELECT order_num
FROM Orders
WHERE DATE_PART('year', order_date) = 2012;
```

DBMSs typically offer far more than simple date part extraction. Most have functions for comparing dates, performing date based arithmetic, options for formatting dates, and more. But, as you have seen, date-time manipulation functions are particularly DBMS specific. Refer to your DBMS documentation for the list of the date-time manipulation functions it supports.

### Numeric manipulation functions

Commonly used numeric manipulation functions :
- ABS()
- COS()
- EXP()
- PI()
- SIN()
- SQRT()
- TAN()

---

## 9. Summarizing data

