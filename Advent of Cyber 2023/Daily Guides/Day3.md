# Day 3
## Brute Forcing
Today, we will be using Crunch and Hydra to brute force a simple password. 

Our learning objectives for the day include:
- Understand password complexity and the number of possible combinations
- Understand how the number of possible combinations affects the feasibility of brute force attacks
- Generate password combinations using Crunch
- Try out passwords automatically using Hydra

### Feasibility of Brute Force
In this section, we will consider a simple bank PIN to figure out how many different PIN codes there are, the amount of different passwords we can generate, and the time it would take the find the password via a brute force attack.

Consider a scenario where we have a simple 4 digit PIN. There would be 10,000 different pin code, ranging from 0000-9999. (or 10^4)

Adding characters such as an uppercase letter, a lowercase letter, and a number drastically increases the amount of possible combinations and increases the time it would take to brute force these passwords. For example, consider a 12 digit password, with a complexity requirement similar to the one previously stated. We woud have 10 digits (0-9), 26 uppercase letters (A-Z), and 26 lowercase letters (a-z). This means every character in this password can be one of 62 different posibilities. Thereforce, the number of possible passwords would be 62^12 or 3,226,277,767,397,899,821,056 possible passwords.

#### How long does it take to Brute Force a Password?

For this example, we will stick with the same password complexity, however we will shorten the required length to only 4 characters (or 62^4). Lets say, hypothetically speaking, trying a singular password takes 0.001 seconds (or 1000 passwords a second, however very few systems would allow us to go this fast). We can take our number above, 62^4, and multiply it by 0.001 to find the number of seconds it would hypothetically take to brute force a password of this length and complexity. After some quick math, we get 14,776 seconds or about 4.1 hours. Overall, this is not a very long time. Our scenario also doesn't account for the placement of the password within our list, meaning the password could be in the very beginning thus taking less time to crack. Using this info, we can increase security by requiring a minimum password length and character variety (12 characters, uppercase and lowercase letters, a number, as well as a symbol).

## Capstone
In today's capstone, we will attempt to crack the password on a numeric keypad. Looking at the login page of the VM, we noticed that the password includes 3 characters, which includes numbers 0-9, as well as letters A-F. 

We will prepare a password list using the tool Crunch, before putting it to the test in our brute force attack using Hydra. We will be using our Kali machine for this attack.

### Crunch
Looking at the man page for crunch, we see we can create a wordlist based on the criteria we specify. The syntax is the following

```crunch <min> <max>```

Where min and max are the desired password length. We will also be using the -o flag to specify our output.

With Crunch, we will use the following command:

```crunch 3 3 0123456789ABCDEF -o 3digits.txt```

![Alt text](/Advent%20of%20Cyber%202023/Resources/crunchcmd1.png)

- The first 3 is our minimum length
- The second 3 is our maximum length
- 0123456789ABCDEF is our character set used to generate our passwords
- -o 3digits.txt outputs our result to 3digits.txt


After executing our command, we have our wordlists and are ready to brute force the password.

### Hydra
Before we can use Hyra, we must collect some information for the .php page where we are putting our password in. Looking through the HTML of the page, we can gether some info:

![Alt text](/Advent%20of%20Cyber%202023/Resources/day3HTML.png)

- The method is ```post```
- The URL is ```http://10.10.113.109:8000/login.php```
- The PIN code value is sent with the name ```pin```

We can deduct that the main login page recieves the input from the user, and sends it to /login.php using the name pin to refer to the user input.

Looking at the Hydra man page, we see the following syntax:

![Alt text](/Advent%20of%20Cyber%202023/Resources/hydramanpage.png)

Therefore, our command to brute-force this site is the following:

```hydra - l '' -P 3digits.txt -f -v 10.10.113.109 http-post-form "/login.php:pin=^PASS^:Access denied" -s 8000```

The command above specifies the following:

- -l '' indicates that the login name is blank
- -P 3digitx.txt is our password file
- -f stops Hydra after finding the working password
- -v provides verbose output and is helpful for catching errors
- 10.10.113.109 is our target IP address
- /login.php is the page where the PIN is ultimately submitted
- pin=^PASS^ Hyrdra will replace ^PASS^ with the values from our password list
- 'Access Denied' indicates that invalid passwords will lead to a page that cotains the text 'Access Denied'
- -s 8000 is the port number on our target

Once we run our brute force attack, about 2 minutes passes till we are gifted the valid password for this machine:

```6F5```

![Alt text](/Advent%20of%20Cyber%202023/Resources/day3PIN.png)

Once we have this, we can return to the machines login page to use the found PIN and retrieve the flag for today's challenge:

```THM{pin-code-brute-force}```

## Conclusion
Today we learned about password complexity and the need for strong requirements. We also learned how to build wordlists with specified characters and lengths, as well as use these wordlists to brute force a vulnerable machine. 