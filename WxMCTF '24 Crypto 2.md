Writeup by Phil

The RSA problem gives us n, e, and the ciphertext c. We’re also told that the key generator is faulty as well as a list of previous keys.

The obvious thing to try is gcd’ing any 2 of the keys in case one of the primes remains constant during the keygen (which explains why the key generator would be faulty).

```python
from math import gcd
a = 136346… # a, b can be replaced with any 2 of the previous keys
b = 199649…
g = gcd(a, b)
print(g)

# output: 117899…
```

Aha. With this, we can easily find p and q, and thus decrypt the ciphertext.

Or we can go to dcode.fr and let them do it instead.

![A screenshot of dcode.fr showing the ciphertext being decrypted.](/img/crypto2.png)

`wxmctf{CommOn_F@u1t_0R_cOMm0N_f4ctoR?}`
