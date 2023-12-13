# Day 11
## Attacking Active Directory

Today, we will be attacking some common vulnerabilites within Active Directory. Our learning objectics include:

- Understanding Active Directory
- Introduction to Windows Hello for Business
- Prerequisites for exploiting GenericWrite privilege
- How the Shadow Credntials attack works
- How to exploit the vulnerability

## Active Directory 101

Active Directory (AD) is used by business in Windows enviroments for central authentication. The Domain Controller is at the heart of AD and manages data storage, authentication, and authorizaton within a domain.

AD includes digital objects such as users, groups, and computers each with specific attributes and permissions. 

## Windows Hello for Business

Windows Hello is a modern and secure way to replaced conventional password based authentication. Windows Hello uses cryptohgraphic keys for user verification, whether that's through using a PIN or biometrics which are connected to the public and private keys. These keys help to prove the identity of the entiry to which they belong. The ```msDS-KeyCredentialLink``` is an attribute used by the DC to store the Windows Hello public key for enrolling a new user device. Here is the process for stroing a new pair of certificates with Windows Hello for Business:

1. The TPM creates a public-private key pair for the user's account when they enroll. The private key NEVER leaves the TPM and is never disclosed.
2. The client initiates a certificate request to recieve a trustworthy certificate. The oranization's CA recieves this request and provides a valid certificate
3. The user account's ```msDS-KeyCredentialLink``` attribute will be set.

Here is the authentication process:

1. the DC decrypts the client's pre-authorization data using the raw public key stored in the ```msDS-KeyCredntialLink``` attribute of the user's account
2. The certificate is created for the user by the DC and can be sent back to the client
3. The client can then log in to the domain using the certificate.

Important: An attacker capable of overriding the ```msDS-KeyCredentialLink``` of a specific vulnerable user can compromise it

## Enumeration

We are going to be checking an Active Directory environment for any security misconfigurations. First, we will enumerate the AD to see if an user has write capabilities over another user on the domain. We can do this using the following command on our vulnerable machine which has the Powerview script on the desktop:

```. .\Powerview.ps1```

```Find-InterstingDomainAcl -ResolveGuids```

We are looking for the current user ```hr```. Therefore, we can filter using:

```Where-Object {$_.IndentifyReferenceName -eq "hr"}``` piped in after the orignal command. However, we can filter even more precisely by only using the fields we are intersted in:

``` Find-InterestingDomainAcl -ResolveGuids | Where-Object { $_.IdentityReferenceName -eq "hr" } | Select-Object IdentityReferenceName, ObjectDN, ActiveDirectoryRights```

We can see that the user "hr" has the ```GenericWrite``` permission over the administrator object: 

![Alt text](/Advent%20of%20Cyber%202023/Resources/hrpermissions.png)

## Exploitation

The tool we are going to use to exploit this vulnerability is ```Whisker```. Since we have a vulnerable user, we can run the ```add``` command from Whisker to simulate the enrollment of a malicious device, thus updating the ```msDS-KeyCredntialLink```. Our command for this is:

```.\Whisker add /target:vansprinkles``` 

We get the following output:

![Alt text](/Advent%20of%20Cyber%202023/Resources/WhiskerOutput.png)

This points us to use the information we gathered to run Rubeus in order to authenticate the vulnerable user. ```Rubeuus``` is a tool used for direct Kerberos interaction amd exploitation, a ```pass-the-hash``` attack. We will use the certificate generated in output of the previous command to ask for a ```TGT```. A ```TGT``` is a session token that avoids the credntials prompt after user authentication. We can copy and paste the output of our Whisker command to run Rubeus. However, we are provided a breakdown of the command with syntax:

- ```asktgt```: This will make a request to obtain the TGT
- ```/user```: The user we want to impersonate for the TGT
- ```/certificate```: The certificate generated to impersonate the target user
- ```/password```: The password used for decoding the certificate
- ```/domain```: The target domain
- ```/getcredentials```: A flag used to retrieve the NTLM hash
- ```/dc```: The domain controller that will generate the TGT

Our output after running the supplied Rubeus command:

![Alt text](/Advent%20of%20Cyber%202023/Resources/rubeusoutput.png)

We can now execute a ```pass-the-hash``` attack using the ```NTLM``` hash obtained. This attack involves using the encrypted password stored in the DC instead of using the plaintext password.  We will use ```Evil-WinRM``` on our Kali machine for this, which is a tool for remotely managing Windows systems abusing the WindowsRM protocol. 

Our command for this attack:

```evil-winrm -i 10.10.94.96 -u vansprinkles -H 03E805D8A8C5AA435FB48832DAD620E3```

Where: 

- ```-i``` is our victim IP address
- ```-u``` is the user, in our case the administrator
- ```-H``` is the Hash we got from the last step

After running our command, we can see that we are logged in as our target user!

![Alt text](/Advent%20of%20Cyber%202023/Resources/winrm.png)

Lastly, we will navigate to the Administrator account grab the flag from the Desktop:

THM{XMAS_IS_SAFE}

## Conclusion

Today, we used a variety of tools to abuse priveleges on a vulnerable user in order to gain access to the domain. This was a big emphasis to show how simple misconfigurations could lead to a fully compromised system.