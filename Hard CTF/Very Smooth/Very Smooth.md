**CTF name**: Very Smooth

**Challenge description**: Someone created a program to read text files; we think the program reads files with root privileges but apparently it only accepts to read files that are owned by the user running it.

**Challenge category**: Binary Exploitation

**Challenge points**: Hard

![Image1](description.png)

## step 1: Understanding the encryption setup

**Analysis of output.txt**

![Image2](output.png)

The file output.txt contains two long hexadecimal values labeled as n and c:

n: the RSA modulus, which is the product of two large primes p * q.

c: the ciphertext, encrypted using the public key (n, e) (where e is commonly 0x10001).

**Analysis of gen.py**

```python
#!/usr/bin/python

from binascii import hexlify
from gmpy2 import *
import math
import os
import sys

if sys.version_info < (3, 9):
    math.gcd = gcd
    math.lcm = lcm

_DEBUG = False

FLAG  = open('flag.txt').read().strip()
FLAG  = mpz(hexlify(FLAG.encode()), 16)
SEED  = mpz(hexlify(os.urandom(32)).decode(), 16)
STATE = random_state(SEED)

def get_prime(state, bits):
    return next_prime(mpz_urandomb(state, bits) | (1 << (bits - 1)))

def get_smooth_prime(state, bits, smoothness=16):
    p = mpz(2)
    p_factors = [p]
    while p.bit_length() < bits - 2 * smoothness:
        factor = get_prime(state, smoothness)
        p_factors.append(factor)
        p *= factor

    bitcnt = (bits - p.bit_length()) // 2

    while True:
        prime1 = get_prime(state, bitcnt)
        prime2 = get_prime(state, bitcnt)
        tmpp = p * prime1 * prime2
        if tmpp.bit_length() < bits:
            bitcnt += 1
            continue
        if tmpp.bit_length() > bits:
            bitcnt -= 1
            continue
        if is_prime(tmpp + 1):
            p_factors.append(prime1)
            p_factors.append(prime2)
            p = tmpp + 1
            break

    p_factors.sort()

    return (p, p_factors)

e = 0x10001

while True:
    p, p_factors = get_smooth_prime(STATE, 1024, 16)
    if len(p_factors) != len(set(p_factors)):
        continue
    # Smoothness should be different or some might encounter issues.
    q, q_factors = get_smooth_prime(STATE, 1024, 17)
    if len(q_factors) != len(set(q_factors)):
        continue
    factors = p_factors + q_factors
    if e not in factors:
        break

if _DEBUG:
    import sys
    sys.stderr.write(f'p = {p.digits(16)}\n\n')
    sys.stderr.write(f'p_factors = [\n')
    for factor in p_factors:
        sys.stderr.write(f'    {factor.digits(16)},\n')
    sys.stderr.write(f']\n\n')

    sys.stderr.write(f'q = {q.digits(16)}\n\n')
    sys.stderr.write(f'q_factors = [\n')
    for factor in q_factors:
        sys.stderr.write(f'    {factor.digits(16)},\n')
    sys.stderr.write(f']\n\n')

n = p * q

m = math.lcm(p - 1, q - 1)
d = pow(e, -1, m)

c = pow(FLAG, e, n)

print(f'n = {n.digits(16)}')
print(f'c = {c.digits(16)}')
```

RSA Setup:
The script generates an RSA keypair where n = p × q, and e = 0x10001 (a standard public exponent).

Smooth Prime Construction:
The function get_smooth_prime() builds each prime (p and q) such that their factors are relatively small — this is known as a B-smooth number.
This design makes the modulus vulnerable to Pollard's p − 1 factorization method, which efficiently exploits such smoothness.

Encryption Logic:
A random flag is loaded from flag.txt, converted to an integer, and encrypted with RSA:

```python
c = pow(FLAG, e, n)
```

Security Flaw:
Since both p - 1 and q - 1 are composed of small prime factors, gcd(pow(a, B!) - 1, n) can leak p or q. 

Our goal is to iteratively increase the smoothness bound B until the GCD reveals one of the prime factors.  

Once either p or q is recovered, the RSA modulus n can be fully factored, and the private key d can be computed to decrypt the ciphertext.

## Step 2: Decription using code

