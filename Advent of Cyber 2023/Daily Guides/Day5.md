# Day 5
## Navigating Legacy Systems

In today's room, we will be learning how to navigate DOS to perform file system analysis. Today's learning objectives are:
- Experience how to navigate and unfamiliar legacy system
- Learn about DOS and its connection to its contemporary, the Windows Command Prompt
- Discover the significance of file signatures and magic bytes in data recovery and file system analysis

### DOS Overview
DOS stands for Disk Operating System and was the dominant operating system in the early days of personal computing. Microsoft then tweaked DOS to make their own variant - MS-DOS. Still today, the fundamentals, file management, and command syntax of DOS are used in the command prompt and Powershell of today's modern Windows systems.

### Using DOS

Upon launching our DOS VM through an emulator, we are greeted with a welcome screen:

![Alt text](/Advent%20of%20Cyber%202023/Resources/DOSWelcome.png)

Reading into the commands used in DOS, they are a very familiar syntax to the Windows command prompt. 

First, we use ```CLS``` to clear our screen.cls, before we list the directories on this machine using ```DIR```. 

![Alt text](/Advent%20of%20Cyber%202023/Resources/dirlisting.png)

Next, we will output the content of a file using ```TYPE```. We can also use ```CD``` to change directory, as well as use ```CD ..``` to move back to the parent directory. Lastly, we can use ```HELP``` to list all available commands:

![Alt Text](/Advent%20of%20Cyber%202023/Resources/doshelp.png)

## Restoring a Corrupted File

Using DOS, we will look at and attempt to restore a corrupted file. We will use the ```BUMASTER.EXE``` located in ```C:\TOOLS\BACKUP``` to insprect the file.

We get the following output:

![Alt text](/Advent%20of%20Cyber%202023/Resources/bumastererror.png)

The output says there is an error in the file's signature and gives us troubleshooting info in README.TXT

When we use the ```TYPE``` command to print this to the screen, the troubleshooting section says we are have a file signature error. The file says the first bytes of our signature should be "41 43".

Reading more into the file signature, the first bytes of a files are called "magic bytes" and identify the content type and its format. These bytes have corresponding ASCII characters as well. The identification helps software applications determine where a file is in a format they can handle. 

To see this in  action, we will created our own DOS executable using the Borland Turbo C Compilier. 

On this VM, we have some files available to use to use for this. We can navigate to ```C:\DEV\HELLO```. We can open the ```HELLO.C``` file using the aforementioned compiler with the ```TC``` command. We can used the "Compile" menu within this and select "Build all" to start the compilation process.

![Alt text](/Advent%20of%20Cyber%202023/Resources/compiler.png)

We can use the ```DIR``` command to see our new executable in the current directory. We can open this using ```EDIT HELLO.EXE``` to show us the executable in text form. Here, we can see the "magic bytes", signaling that it is a Windows or DOS executable:

![Alt text](/Advent%20of%20Cyber%202023/Resources/hellomb.png)

### Fixing Our File

Now, we can go back to the ```AC2023.BAK``` file we looked at earlier using the ```EDIT``` command. The magic bytes in this file are incorrectly listed as "XX". We know from the troubleshooting guide we looked at earlier, that this needs to read "41 43". However, we must conmver that to ASCII. We use a simple HEX to ASCII converter below:

![Alt text](/Advent%20of%20Cyber%202023/Resources/ASCII.png)

And see that our "magic bytes" need to equal ```AC```. We simply remove the ```XX``` and replace them with ```AC```. After saving the file, we can go back and use ```BUMASTER.EXE``` to restore the backup and give us our flag for today's challenge:

![Alt text](/Advent%20of%20Cyber%202023/Resources/DAY5FLAG.png)


## Conclusion

Today, we used DOS in order to navigate legacy systems, as well as learned about "magic bytes" in files in order to repair a corrupted backup file. We also performed a simple version

