**CTF name**: tic-tac

**Challenge description**: Someone created a program to read text files; we think the program reads files with root privileges but apparently it only accepts to read files that are owned by the user running it.

**Challenge category**: Binary Exploitation

**Challenge points**: Hard

![Image1](description.png)

## Step 1: Connect to Instance

Use SSH to log in to the instance provided by picoCTF and enter the challenge environment.

![Image2](1.png)


## Step 2: Prepare local “bait” file and symlink

Since the program likely checks the ownership of the file before reading it, our first goal is to create a file we own. We’ll also prepare a symbolic link that initially points to that file. This setup allows us to later exploit the short gap between the program’s check 
and use by changing what the symlink points to. Create a file you own (owned.txt) as the bait, and make a symlink named victim.txt that points to it. Later we will flip that symlink to point at the real flag.txt.

![Image3](2.png)

## Step 3: Set up the fast toggling script

To exploit the time gap between the program’s check (e.g., checking file owner) and its use (actually opening the file), we need to rapidly switch the symlink victim.txt between two targets: the file we own (owned.txt) and the root-owned file we want to read (flag.txt). A Python script helps us automate this at high speed, increasing the chances of hitting the race condition.We therefore write a small script that repeatedly deletes and recreates the victim.txt symlink, flipping it between owned.txt and flag.txt. This script runs in the background while we invoke the target binary in a loop.

![Image4](3.png)

![Image5](4.png)

Why we need this script?

Because we don’t know exactly when the program performs the check and the open, we have to keep switching back and forth — hoping that the symlink points to owned.txt during the check and to flag.txt during the actual open.

## Step 4: Run the target program in a loop to trigger the race

Since the race condition depends on extremely precise timing, running the program just once is very unlikely to succeed. We need to call the binary in a tight loop, hoping that one of those executions lands during the brief moment when the symlink victim.txt points to flag.txt after the ownership check but before the file is opened.

Run txtreader on victim.txt repeatedly in a loop, and capture its output. If the output contains the flag pattern, stop the loop and extract the flag:

![Image6](5.png)



