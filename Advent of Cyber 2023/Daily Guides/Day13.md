# Day 13
## Intrusion Detection

Today, we will be learning about the Diamond model, firewalls, and honeypots.

Our learning objective's include:

- Learn to understand incident analysis through the Diamond Model
- Identify defensive strategies that can be applied to the Diamond Model
- Learn to set up firewall rules and a honeypot as defensive strategies

## Incident Analysis

The ```Diamond Model``` is a security analysis framework that we can use to identify the elements of an intrustion. It covers four facets:

- Adversary: Malicious threat actor responsible for cyber attacks
- Victim: The target of an adversary's attacks
- Infrastructure: The physical and logical tools that an adversary will use
- Capability: The tactics and techniques that an adversary will use, such as phishing. social engineering, malware, inside threats, and DoS attacks

## Defensive Diamond Model

The Defensive Diamond Model has 2 main components: capability and infrastructure. 

### Defensive Capabaility

The two main components of Defensive Capability are threat hunting and vulnerability management. 

- ```Threat hunting``` is a proactive process to search for signs of malicious activies or security weaknesses with an organization. 
- ```Vulnerability management``` is a structured process that involves scanning to identify weaknesses, followed by prioritizing and patching.

### Defensive Infrastructure

#### Firewalls

A firewall is a network security device that monitors and controls the inflows and outflows of network traffic based upon a configured ruleset. Firewalls can be hardware based, software based, or a combination of the two. The most common firewall types include:

- ```Stateless firewalls```: the most straightforward. Filters network packets based on a set of rules that would point to a source/destination IP address, or ports and protocols. 
- ```Stateful firewall```: This firewall is more sophisticated. It is used to track the state of network connections to make filtering decisions. If a packet is part of an established conenction, the firewall will let it through. If there is no active connection, the packet will be blocked.
- ```Proxy service```: Protects the network by filtering traffic at the application layer. Can block access to certain websites or the transmission of certain files
- ```Web application firewall```: Designed to protect web applications against SQL injections, XSS attacks, and DoS attacks
- ```Next generation firewall```: Combines the functionality of all the aforementioned firewalls, with features such as intrusion detection and content filtering

#### Configuring Firewalls to Block Traffic

We will be using the ```uncomplicated firewall (ufw)``` to configure our firewall rules, which is the default firewall configuration tool on Ubintu hosts.

We will access the VM with the firewall from our Kali machine via SSH. We have been given credntials to access this machine. 

The firewall is turned off by default, and we can check the status using 

```sudo ufw status```

![Alt text](/Advent%20of%20Cyber%202023/Resources/fwstatus.png)

We will apply two default rules to allow outgoing connections and deny all inbound connections:

```sudo ufw default allow outgoing```

```sudo ufw default deny incoming```

![Alt text](/Advent%20of%20Cyber%202023/Resources/fwdefault.png)

Next, we will allow connections for SSH over port 22:

```sudo ufw allow 22/tcp```

![Alt text](/Advent%20of%20Cyber%202023/Resources/fw%20ssh.png)

We can also get more specific by incorporating specific IP addresses, subnets, or even specific network interfaces. For example:

```sudo ufw deny from <IP_ADDRESS>```

```sudo ufw deny in on eth0 from <IP_ADDRESS>```

Now, we can enable our service and look at the ruleset:

```sudo ufw enable```

```sudo ufw status verbose```

![Alt text](/Advent%20of%20Cyber%202023/Resources/fwruleset.png)

We can reset the firewall and revert it to its default state using:

```sudo ufw reset```

#### HoneyPot

Honeypots are essentially a trap laid for malicious actors. It presents itself as an alluring target, yet it only draws them away from the true information they are after. There are two types of honeypots:

- ```Low interaction```: Mimic simpkle systems like web servers or databases. Gather intelligience on attacker behavior and new attack techniques
- ```High interaction```: Emulate complex systems like operation systems or even networks. Collect meticulous detail on attacker behaviour and study their techniques.

We will be using ```PenTBox``` to set up our honeypot:

![Alt text](/Advent%20of%20Cyber%202023/Resources/pentbox.png)

We will select ```Option 2``` to select ```Network Tools```, followed by ```Option 3``` to select ```Honeypot```

We will select ```Option 2``` to manually configure our honeypot.

![Alt text](/Advent%20of%20Cyber%202023/Resources/honeypot.png)

We configured our honeypot with the following options:

![Alt text](/Advent%20of%20Cyber%202023/Resources/honeypotconfig.png)

## Conclusion

Today we learned at some network hardening devices and configured them. We looked at firewalls to allow/deny traffic as well as honeypots to attract attackers and learn their techniques.



