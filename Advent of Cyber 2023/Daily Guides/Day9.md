# Day 9
## .NET Malware Analysis

Today, we will be looking at and trying to understand a piece of Malware that is writted in C#. We will be using ```dnSpy``` to parse through the code and make sense of it's functionalities.

### C2 Primer

We have been told that this malware sample has C2 capabilities. C2 capabilities usually exhibits the following behaviours:

- HTTP requests: C2 servers often communicate with compromised assets using HTTP requests. These requests are often used to send commands or receive data.
- Command execution: The most common C2 behavior. Allows the attacker to execute commands on the compromised machine.
- Sleep or delay: C2 malware will often have a sleep or delay method inside it, in order to evade detection.

### Decompiling Malware Samples with dnSpy

```dnSpy``` is a .NET assmbly debugger and editor. It is often used for reverse engineering and code analysis in a user-friendly way. First, we will open ```dnSpy``` and open our file. 

We will first take a look at the ```Main``` method. This is the program's entry point, meaning the lines of code in this function are the ones executed one step at a time. However, we will need to take a look at the other functions in order to understand the ```Main``` function. 

![Alt text](/Advent%20of%20Cyber%202023/Resources/functions.png)

Our first function will be the ```GetIt``` function. Shown below:

![Alt text](/Advent%20of%20Cyber%202023/Resources/GetIt.png)

The ```GetIt``` function uses the ```HttpWebRequest``` class to initate a HTTP request to a remote URL. It accepts a URL as an argument, configures the parameters needed for the ```HTTP GET``` request, and returns the response value.

The next function we will look at it similar to the ```GetIt``` function we previously looked at:

![Alt text](/Advent%20of%20Cyber%202023/Resources/PostIt.png)

 This function is named ```PostIt``` and it also uses the ```HttpWebRequest``` class but instead the ```METHOD``` property is set to ```POST```. This means the function accepts an additional argument as it's ```POST``` data, submitted to the target URL, and returns the response it received.

Our next function is named ```Sleeper```. This is a very simple function:

![Alt text](/Advent%20of%20Cyber%202023/Resources/sleeper.png)

The ```Thread.Sleep``` function inside this method accepts and integer as an argument and makes the program pause based on the value passed to it. This is used in order to evade detection.

Next, we will look at ```ExecuteCommand```:



We can see the ```Process``` class, and based on the contents of this, we can determine this is used to spawn a process. Looking at the properties, we can see the file is used to execute ```cmd.exe``` and that the command arguments are also being passed. We can also note that the ```WindowStyle``` property is set to ```Process.WindowStyle.Hidden``` meaning the process will run without a Window or UI. This is a good way to hide the malware's command exectuon. 

```Encyptor``` will be the next function we will look at:



We can see that AES is used by looking at the ```AesManaged``` vaiable. The function also accepts and argument and encrypts is using the hardcoded KEY and IV values. It also encodes the encrypted bytes into Base64 using the ```Convert.ToBase64String``` function. This function encrypts plaintext using an ```AES cipher``` and returns the encoded ```Base64``` value of the encrypted string.

```Decryptor``` is the opposite of the ```Encryptor``` function:



It simply expects a ```Base64``` string, decodes it, and proceeds to decrypt it to recieve the plaintext string.


Lastly, we will look at the ```Implant``` function:



We see that it accepts a URL as a string, initiates an HTTP requests to the URL argument, and decodes it with ```Base64```. It also receives the ```AppData``` path and attempts to write the contents of the decoded ```Base64``` into a file. It also returns the location of the file if written successfully, if not it returns an empty string.

## Building the Malware Execution Pipeline

We will go back and look at the ```Main``` function of the code. We will first look at the code before the ```for loop```:

![Alt text](/Advent%20of%20Cyber%202023/Resources/beforeforloop.png)

Things to note in this section is the ```Dns.GetHostName()``` which retrieves the victim machines host names. This helps the attacker distrinugish the compromised machines based on the hostname.

We can also note the ```PostIt``` function we previously looked at appears here. We can see the function sends the hostname of the victim machine to the C2 server to establish a connection..

Now, we can look at the fod inside the for loop (before the first ```if``` statement):

![Alt text](/Advent%20of%20Cyber%202023/Resources/beforeifstatement.png)

Here we can see the for loop being set to ```;;``` meaning it will run indefinitely until a break statement is used.

We see the ```Sleeper``` function being called where the ```count``` variable is being passed that was initialized before the for-loop.

We can see two string variables being used, where ```str2``` is passed to the ```GetIt``` function and stored in the ```it``` variable. 

Lastly, we see that the ```if``` statement only runs if the ```it``` variable is ```NOT``` empty. 

Now we can look at the code inside the first ```if``` statement:

![Alt text](/Advent%20of%20Cyber%202023/Resources/firstifstatement.png)

Important things to note in this code block:

The ```Decryptor``` funciton decrypts the ```it``` variable and splits it with a space character. The ```a``` variable value is the first element of the resulting array. The ```text``` variable combines all the elements in the array excluding the first element. 

We can now look at the nested conditional satement:

![Alt text](/Advent%20of%20Cyber%202023/Resources/nested.png)

 All the conditions in this statement are set to ```!``` meaning ```NOT```. This code block checks to see if the ```a``` variable is not equal to the condition. If it doesn't equal the condition, it is pass to the next statement. Otherwise, it will jump to its counterpart ```else``` statement.

 Below is a table with the code block summary of each conditional statement:

 ![Alt text](/Advent%20of%20Cyber%202023/Resources/codeblocktable.png)

 Finally, the last conditional statement checks if the flag variable is set to ```true```. If so, it will execute a ```break``` statement.

 ## Conclusion

 In today's room, we went over a simple piece of malware and broke it down function by function, before walking through the ```Main``` function to figure out what exactly the malware is doing.

