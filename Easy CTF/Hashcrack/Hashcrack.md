CTF name: hashcrack

Challenge description:
A company stored a secret message on a server which got breached due to the admin using weakly hashed passwords. Can you gain access to the secret stored within the server?

Challenge category: Cryptography

Challenge points: Easy

![Image1](Description)

## Q1: Crack MD5 hash

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

rockyou.txt refers to a weak password list, this file stores in '/usr/share/wordlists' directory

![Image2](MD5%20crack)

Hash successfully cracked by hashcat, the password is 'password123'

## Q2: Crack SHA-1 hash

The second hash is 'b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3', if we not sure the type of hash, we could use **hashid** tool to identify it:

```bash
hashid b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3
```

![Image3](SHA-1%20hashid)

Based on the output, there are numerous potential results, with SHA-1 being the most common.

Once we have determined the type of hash, repeat the steps from Q1 to crack the hash.

```bash
echo "b7a875fc1ea228b9061041b7cec4bd3c52ab3ce3" > hash2.txt
```

However there is a small change in the second command due to differnt hash type.

```bash
hashcat -m 100 -a 0 hash2.txt /usr/share/wordlists/rockyou.txt
```

In this command, '-m 100' specifies SHA-1 hash crack in hashcat

![Image4](SHA-1%20crack)

We cracked the SHA-1 hash and the password is 'letmein'

## Q3:  Crack SHA-256 hash

Use the same step as Q2 to crack SHA-256 hash. Don't forget to adjust the '-m' parameter in hashcat to the correct mode number.

Identify hash type:

```bash
hashid 916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745
```

![Image5](SHA-256%20hashid)

Crack hash using hashcat:

```bash
echo "916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745" > hash3.txt
```

```bash
echo "916e8c4f79b25028c9e467f1eb8eee6d6bbdff965f9928310ad30a8d88697745" > hash3.txt
```

```bash
hashcat -m 1400 -a 0 hash3.txt /usr/share/wordlists/rockyou.txt
```

Here we use parameter 1400 whcih specifies cracking SHA-256 hash

![Image6](SHA-256%20crack)

The password is 'qwerty098'

After correctly cracking these three hashes, finally get the flag.

![Image7](solution)




