# Day 4
## Brute-Forcing with CeWL

On day 4, we will be using a custom wordlist generator, CeWL, to brute force a vulnerable web application. Our learning objectives for today are to answer the following:

- What is CeWL?
- What are the capabilities of CeWL?
- How can we leverage CeWL to generate a custom wordlist from website?
- How can we customise the tool's output for specific tasks?

### CeWL Overview

CeWL is a custom word list generator that spiders websites to create word lists based on the site's content. Spidering refers to the process of automatically navigating a website's content to retrieve the site structure, content, and other details. CeWL can also comile a list of email addresses (or usernames) based on a team members' page links. Data like this is important in penetration testing and can be used for brute-forcing.

### Using CeWL
Looking at the CeWL man page, we get a list of all the options we can use and their descriptors:

![Alt text](/Advent%20of%20Cyber%202023/Resources/cewlman.png)

To generate a basic wordlist from a website and output to a file, we use the following syntax:

```cewl http://<TARGET IP ADDRESS> -w output.txt```

We can use CeWL to customise our output based on the tast. We can:

- Specify spidering depth. For example, -d 2 tells the command to spider 2 links deep
- Set maximum and minimum word lengths using -x and -m respectively
- Handle authentication using -a for form-based authentication
- Follow external links using --offsite

## Capstone

Using what we learned today using CeWL we will attampt to gain access to a portal located at:

```http://<TARGET IP>/login.php```

First, we will create a password list using CeWL using the following command:

```cewl -d 2 -m 5 -w passwords.txt http://<TARGET IP> --with-number```

The above command uses: 

- -d 2 for 2 links deep for spidering
- -m 5 for minimum 5 characters
- -w passwords.txt for the output file
- --with-numbers to append numbers to words

Here is a snippet of the output of this list:

![Alt text](/Advent%20of%20Cyber%202023/Resources/day4pw.png)

Next, we will create a list of potential usernames from the team members pausing a modified command:

```cewl -d 0 -m 5 -w usernames.txt http://<TARGET IP>/team.php```

Below is a snippet from the usernames.txt file that we created using CeWL:

![Alt text](/Advent%20of%20Cyber%202023/Resources/day4usernames.png)

Finally, we can use ```wfuzz``` to brute-force the /login.php page.

```wfuzz``` is a tool designed for brute-forcing web applications.

We will use the following options in our command:

- 'z file, usernames.txt' loads the usernames list we generated
- 'z file, passwords.txt' loads the passwords list we generated
- --hs "Please enter the correct credentials" hides responses containing the aforementioned string, which is the message displayed on this web app for wrong login attempts.
- -u specifies the target URL
- f "username=FUZZ&password=FUZ2Z" provides the POST data format where ```FUZZ``` will be replaced by usernames and ```FUZ2Z``` will be replaced by passwords

Our command looks like the following:

```wfuzz -c -z file,usernames.txt -z file,passwords.tx --hs "Please enter the correct credntials" -u http//:10.10.11.153/login.php -d "username=FUZZ&password=FUZ2Z```

After running this command, we are greeted with the following output, which we will use to log onto the web application:

![Alt text](/Advent%20of%20Cyber%202023/Resources/fuzzoutput.png)

Once into the web application, we are able to find the flag for today's challenge.

```THM{m3rrY4nt4arct1crAft$}```

## Conclusion

Today we expanded on our knowledge of brute-forcing and Web-enumeration. This time we used CeWL in order to create custom wordlists for both usernames and passwords. We then used wfuzz to brute-force the web app to find the correct combination.