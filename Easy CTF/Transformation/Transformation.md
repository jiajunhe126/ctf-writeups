CTF name: Transformation

Challenge description: I wonder what this really is... enc ''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])

Challenge category: Reverse Engineering

Challenge points: Easy

![Image 1](Description.png)

## Step 1: Step 2: Analysis of the Provided Code

The given code snippet demonstrates how the original flag is converted into an encoded string:

```bash
enc = ''.join([chr((ord(flag[i]) << 8) + ord(flag[i+1])) 
               for i in range(0, len(flag), 2)])
```

Here is the expalnation of the code snippet:

"ord(flag[i]): Converts the character at position i to its ASCII integer value.  

ord(flag[i]) << 8: Shifts the ASCII value left by 8 bits, which is equal to multiplying by 256. This operation places the value in the upper 8 bits of a 16-bit number.

+ ord(flag[i+1]): Adds the ASCII value of the next character, which occupies the lower 8 bits.

chr(...) : Converts this combined 16-bit value into a single Unicode character.

