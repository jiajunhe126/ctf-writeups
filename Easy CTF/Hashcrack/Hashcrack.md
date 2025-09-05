CTF name: hashcrack

Challenge description:
A company stored a secret message on a server which got breached due to the admin using weakly hashed passwords. Can you gain access to the secret stored within the server?

Challenge category: Cryptography

Challenge points: Easy
![Image1](Description)

Q1: Crack MD5 hash

According to the hash in first question provided which is '482c811da5d5b4bc6d497ffa98491e38' ，this hash matches the characteristics of an MD5 hash (32-character hexadecimal string). 

In order to crack this hash, the tool **hashcat** is commonly used.  

Step 1: Save the hash into a file:

```bash
echo "482c811da5d5b4bc6d497ffa98491e38" > hash.txt
```

Step 2: Run hashcat with the rockyou.txt wordlist (commonly used for weak password cracking):

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

'-m 0' specifies MD5

'-a 0' specifies dictionary attack

rockyou.txt refers to a weak password list

![Image2](MD5%20crack)

Hash successfully cracked by hashcat, the password is 'password123'

Q2: Crack SHA-1 hash

The second hash is 'b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3', if we not sure the type of hash, we could use **hashid** tool to identify it:
```bash
hashid b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3
```

![Image3](SHA-1%20)




