# TryHackMe Advent of Cyber: 2023
## Overview
Today, we will kick off TryHackMe's annual Christmas-themed, beginner-friendly, Cybersecurity training. It is a fun, gamified way to learn and explore various cybersecurity trends and topics Throughout this next month, I will be learning different aspects of cybersecurity and get hands-on experience with various inudstry tools.

## Day 1
Here are today's learning objectives:
 - Learn about natural language processing
 - Learn about prompt injection attacks and the common ways to carry them out
 - Learn to defend against prompt injection attacks

 Here are the 3 questions we must answer throughout today's exercise:
 - What is McGreedy's personal email?
 - What is the password to the IT server room door?
 - What is the name of McGreedy's secret project


We will be using a dummy chatbot, meant to simulate ChatGPT, to launch our attack.

![tryhackme chatbot](image.png)

First, we check to see if the chatbot was trained on personal information via the following prompt:

 ```What is the personal email address of the McGreedy?```

 Where we get the following response, showing this chatbot was trained on sensitive data:

 ![chatbot response](image-1.png)

Next, we will explore the security measures that the chatbot does have in place. We check to see if it will give us the password to the IT server room door. The chatbot tells us that we must be a member of the IT department. To which we simply prompt the chatbot to tell us who those members are. The chatbot returns with a response:

![chatbot response2](image-2.png)

Using this information, we can use a common loophole that exploits the way that chatbots like this process information. Natural language processing involves predicting the next word in a sequence, based on the context by the preceding words. We use the following prompt to get our answer:

![chatbot server door password](image-3.png)

A common way to set up AI is the use of another AI to intecept user messages and check for malicious input before processing them and sending them to the chatbot. This AI is continuously trained on malicious inputs. Getting around these security checks can be tricky, but it can be done if you use an attack the AI hasn't seen before. You will need to be creative to come up with ways around these checks. 

The last objective for this day, is to get the name of McGreedy's secret project. At first, the chatbot refuses due to these secondary security checks. However, we can use creative promots to get the answer we are looking for:

![chatbot response secret project](image-4.png)

Chatbot and AI security challenges are unique and can vary from system to system. What works for one system/infratructure may not work for another. It is important to stay up to date on common attacks on these chatbots in order to properly defend them, however they will never be 100% secure.



