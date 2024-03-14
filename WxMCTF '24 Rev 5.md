Writeup by Colin & Phil

## Stage 0 (Starting info)
The file is a Brainf*** program:
```
++[------>+<]>.++[->++<]>+.-[-->+<]>.....---[-->+++<]>-.+[--->++<]>+...+[----->+<]>.++[-->+++<]>.[--->++<]>.+[--->++<]>+.+++.---............+++..-....+.---...+++.---.++[->++<]>+.-[-->+<]>..---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.-...........+..---.++[->++<]>+.-[-->+<]>.....---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.[->++<]>-.-[-->+<]>.....---[-->+++<]>-.+[--->++<]>+..+[----->+<]>.++[-->+++<]>.[--->++<]>.+[--->++<]>+.+++.-..[->++<]>+.-[-->+<]>...---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.--[---->+++<]>..+.---.+++.---...++[->++<]>+.-[-->+<]>.---[-->+++<]>-.+[--->++<]>+...+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.-...+.---.......+++.---.....+++.[->++<]>-.-[-->+<]>.....---[-->+++<]>-.+[--->++<]>+..+[----->+<]>.++[-->+++<]>.[--->++<]>.+[--->++<]>+..+++.-..[->++<]>+.-[-->+<]>...---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.--[---->+++<]>.+.---............+++.-..+.---...++[->++<]>+.-[-->+<]>.---[-->+++<]>-.+[--->++<]>+.....+[----->+<]>.++[-->+++<]>.[--->++<]>.--[---->+++<]>.+.---.++[->++<]>+.-[-->+<]>.....---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.-...[->++<]>+.-[-->+<]>.---[-->+++<]>-.+[--->++<]>+...+[----->+<]>.++[-->+++<]>.[--->++<]>.+[--->++<]>+.+++.-...[->++<]>+.-[-->+<]>.---[-->+++<]>-.+[--->++<]>+.....+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.---.++[->++<]>+.-[-->+<]>.---[-->+++<]>-.+[--->++<]>+....+[----->+<]>.++[-->+++<]>.[--->++<]>.+[--->++<]>+..+++.-.+.-.[->++<]>+.-[-->+<]>...---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.-.[->++<]>+.-[-->+<]>.---[-->+++<]>-.+[--->++<]>+...+[----->+<]>.++[-->+++<]>.[--->++<]>.[->+++++++++<]>.[->++<]>-.-[-->+<]>...---[-->+++<]>-.+[--->++<]>+.+[----->+<]>.++[-->+++<]>.[--->++<]>.--[---->+++<]>..+.
```

Running it gives another one:
```
+[----->+++<]>+.++++++++++++..----.+++.+[-->+<]>.-----------..+[----->+<]>.[----->++<]>+.--[--->+<]>--.+.+++[->+++<]>.---.+++++++.+++++.[----->++<]>++.--[--->+<]>-.++++++++++++.--.+++[->+++++<]>-.+[----->+<]>.---[->+++<]>+.---[->+++++<]>.+[->++++<]>++.-.-[--->+<]>.-[->+++<]>.[--->+<]>--.
```

Running _that_ one gives a Pastebin link:
`https://pastebin.com/pHYjixeu`

## Stage 1
The Pastebin link gives us instructions:

Enter four positive integers a, b, c, and d such that:
 
- a must be the smallest number greater than one trillion (1 followed by 12 zeroes) such that `function_1(a)` evaluates to true.
- b must be the smallest number greater than two billion (2 followed by 9 zeroes) such that `function_2(b)` evaluates to true.
- c must be the smallest number greater than ten quadrillion (1 followed by 14 zeroes) such that `function_3(c)` evaluates to true.
- d must be the smallest number greater than one quadrillion (1 followed by 16 zeroes) such that `function_3(d)` evaluates to true.

 and a C++ file:
 ```cpp
 
float _fun(float number) {
	long i;
	float x2, y;
	const float threehalfs = 1.5F;

	x2 = number * 0.5F;
	y = number;
	i = *(long *)&y;                       // evil floating point bit level hacking
	i = 0x5f3759df - (i >> 1);               // wtf?
	y = *(float *)&i;
	y = y * (threehalfs - (x2 * y * y));   // 1st iteration
	//	y  = y * ( threehalfs - ( x2 * y * y ) );   // 2nd iteration, this can be removed

	return y;
}

bool _fun1(const size_t& n) {
    bool flag1 = true;
    for (size_t i = 2; i <= (size_t)(1/_fun(n)); ++i)
        if (!(n%i))
            flag1 = false;
    return flag1;
}

bool _fun2(size_t n) {
    size_t root = (size_t)(1/_fun(n));
    return (root * root == n);
}

/////////////////////////////////////////////////////////////////////////////////////////////////

const inline bool function_1(const size_t& n) {
    if (n<0) return false;
    size_t x = 0;
    for (size_t i=1; x<=n; i++) {
        x += i;
        if (x==n) return true;
    } return false;
}

/////////////////////////////////////////////////////////////////////////////////////////////////

const inline bool function_2(const size_t& n) {
    if (!n) return true;
    size_t a = 0, b = 1, c = 1;
    while (c < n) a = b, b = c, c = a + b;
    return ((c == n | _fun2(5*n*n+4) | _fun2(5*n*n-4)) & _fun1(n));
}

/////////////////////////////////////////////////////////////////////////////////////////////////

const inline bool function_3(const size_t& n) {
    string _ = to_string(n);
    return _fun1(n)&(_==string(_.rbegin(), _.rend()));
}

/////////////////////////////////////////////////////////////////////////////////////////////////

const string cmp_sha_a = "a0c4746b5db5fabb65bb01dc032a974821ee6b4b91c74ba160ab301397718ebe2d3367583a962409dff8ae5f04c283fa48d269a59ca2a250110e6efdc6c8a9da";

const string cmp_sha_b = "c555bd80936fa07d8ce7484fcb30f1664c32502d5d3f14625c1cbf46bf5e0cd11e327db958dbb05e4d9493a28cf0303e915129e33b82f880197f48d42c19d805";

const string cmp_sha_c = "b8abd1927a5f7ea043bfcd676e360a51e579bd40ec979986457a3886da18910c0ce33878195bcc39ad2df38ba47521d0cadb37d3f1c17508ee4c1b4dfbb94f6b";

const string cmp_sha_d = "1f8ab8f2a8425355502b0eb2bac00522546cdcc888ec067072de70f322d18bfeaad275dacfd1dd2322bd6500bcfe55920c672492f26fda2f2cfeabcbfef881ae";

int main(int argc, char** argv) {
    size_t a, b, c, d;
    cout << "Enter a, b, c, and d, all on the same line, seperated by spaces: ";
    cin >> a >> b >> c >> d;
    const bool sha_a_res = (sha512::calculate(to_string(a)) == cmp_sha_a);
    const bool sha_b_res = (sha512::calculate(to_string(b)) == cmp_sha_b);
    const bool sha_c_res = (sha512::calculate(to_string(c)) == cmp_sha_c);
    const bool sha_d_res = (sha512::calculate(to_string(d)) == cmp_sha_d);
    if (sha_a_res & sha_b_res & sha_c_res & sha_d_res) {
        if (function_1(a) & function_2(b) & function_3(c) & function_3(d) & a >= 1000000000000 & b >= 2000000000 & c >= 100000000000000 & d >= 10000000000000000) {
            cout << "Stage 2: https://rentry.co/" << to_string((((a^b^c^d)<<32)%0x100000001b3ULL)<<2) << "\n";
        } else {
            cout << "SHA checks passed but function tests did not! If this happens please open a ticket." << endl;
        }
    } else {
        cout << "Incorrect values entered for a, b, c, and/or d\n";
    }
    return 0;
}
```

Deciphering the functions:

- `_fun` is the (in)famous [fast inverse square root](https://en.wikipedia.org/wiki/Fast_inverse_square_root) algorithm
- `_fun1` checks if `n` is prime
- `_fun2` checks if `n` is square
- `function_1` checks if `n` is a triangular number
- `function_2` checks if `n` is prime and any of the following are true:
	- `n` is a Fibonacci number
	- $5n^2 + 4$ is square
	- $5n^2 - 4$ is square
- `function_3` checks if `n` is prime and is also a palindrome.

Finding `a`:
```node
> Math.sqrt(1e12 * 2)
1414213.562373095
> 1414214 * (1414214 + 1) / 2
1000001326005
```

Finding `b`:
```python
>>> import sympy
>>> a, b = 0, 1
>>> while b < 2e9 or not sympy.isprime(b):
...     a, b = b, a + b
...
>>> b
>>> 2971215073
```
 I'm not sure if the last two conditions of `function_2` are even possible. Nevertheless, 2971215073 matches the hash, so I'm not complaining.

For completeness, here's my brute force script for the two conditions:
```c
// Compile with gcc test.c -O3 -lm -o test

#include <stdio.h>
#include <math.h>

int is_square(long long x){
    long long val = sqrt(x);
    return val * val == x;
}

int main(){
    for(long long n = 2e9;; n++){
        if(is_square(5*n*n + 4) || is_square(5*n*n - 4)){
            printf("%lld\n", n);
            break;
        }
    }
}
```

Finding `c` and `d`:
Direct brute force will work, but it's inefficient. Here's my solution:
```python
import sympy

for i in range(1, int(1e8)): # or 1e7 for finding c
    x = i + int(1e8)
    b = str(x).zfill(9)
    s = int(b + b[::-1][1:])
    if sympy.isprime(s):
        print(s)
        break
```

We get

a = 1000001326005
b = 2971215073
c = 100000323000001
d = 10000000500000001

Which leads us to `https://rentry.co/2519405705392`.

## Stage 2

We have a Python file:
```python
# I'm famous for writing readable and well documented code.

_ = lambda __ : __import__('zlib').decompress(__import__('base64').b64decode(__[::-1]));exec((_)(b'=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'))
```

What a mouthful. Replacing the `exec` with `print` gives us... another python program which uses the `_` function. Doing this by hand will take forever.

So I modified the Python script:

```python
_ = lambda __ : __import__('zlib').decompress(__import__('base64').b64decode(__[::-1]))

code = _(b"that big string of bytes")

while True:
    if not code.startswith(b"exec"):
        break
    code = eval(code[4:])

print(code.decode("utf-8"))
```

And we get

```python
import sys
a, b = 0, 1
sys.setrecursionlimit(10**6)
sys.set_int_max_str_digits(0)
M = 1000000007
memo = {}

def chicken(n):
    if n in memo:
        return memo[n]
    elif n <= 1:
        return n
    else:
        memo[n] = (chicken(n - 1) + chicken(n - 2)) % M
        return memo[n]

with open("inp.txt", "r") as rd:
    num = int(rd.readline().strip())

print("rentry.co/"+chicken(num))
```

Obviously, `chicken` gets the `n`th term of the Fibonacci sequence. For those wondering, `inp.txt` contains a huge number:
```python
>>> len(open("inp.txt").read())
10000
```

A trivial solution of any kind is not going to terminate within the lifespan of the universe.
Luckily, we make an observation: we're doing a _modular_ Fibonacci sequence. If we find a length such that the sequence repeats, we can just modulo the big number by that length and run the Fibonacci sequence on that.

A quick Google search directs us to the [Pisano period](https://en.wikipedia.org/wiki/Pisano_period), which is the exact thing we're looking for; it's the period of which the Fibonacci sequence taken modulo `n` repeats.

The Wikipedia page is a very interesting read, but there's one thing in there that's applicable to our situation:
> If $p \equiv \pm 3 \pmod {10},$ ... the Pisano period, which is the order of _r_, is the quotient of $2(p+1)$ by an odd divisor. This quotient is always a multiple of 4.

Here, `p` is the modulus. Our modulus `M` is congruent to -3 mod 10, so this applies!

2(1000000007 + 1) = 2000000016 isn't that big of a number. We can brute force its Pisano period:
```c
#include <stdio.h>

int main(){
    int a = 0, b = 1;
    for(int i = 1;; i++){
        int t = b;
        b = (a + b) % 1000000007;
        a = t;
        if(a == 0 && b == 1) {
            printf("%d\n", i);
            break;
        }
    }
}
```

It outputs `2000000016`. The Fibonacci sequence taken modulo `1000000007` repeats after `2000000016` iterations!

So:
```python
>>> import sys
>>> sys.set_int_max_str_digits(0)
>>> int(open("inp.txt").read()) % 2000000016
1740821687
```

```c
#include <stdio.h>

int main(){
    int a = 0, b = 1;
    for(int i = 0; i < 1740821687; i++){
        int t = b;
        b = (a + b) % 1000000007;
        a = t;
    }
    printf("%d\n", a);
}
```

gives us `723624430`.

## Stage 3

Navigating to `https://rentry.co/723624430`, we get a URL:
```
https%2525252525252525252525253A%2525252525252525252525252F%2525252525252525252525252Fdrive.google.com%2525252525252525252525252Ffile%2525252525252525252525252Fd%2525252525252525252525252F1C8MY_H1msxLwG_dQig0EYKX4T7vhwgQO%2525252525252525252525252Fview%2525252525252525252525253Fusp%2525252525252525252525253Dsharing
```

Decoding it:
```node
> x = "https%25..."
> while(x.includes("%")) x = decodeURIComponent(x);
'https://drive.google.com/file/d/1C8MY_H1msxLwG_dQig0EYKX4T7vhwgQO/view?usp=sharing'
```

We get yet another Python script:
```python
import hashlib

def ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb(n):
  ls = []
  i = 1
  while n != 1:
    i += 1
    while n%i == 0:
      ls.append(i)
      n = n//i
  return ls

IlIllIllIlIl = lambda *args, **kwargs: globals()['\x5f\x5f\x62\x75\x69\x6c\x74\x69\x6e\x73\x5f\x5f'].__dict__['\x70\x72\x69\x6e\x74'](*args, **kwargs)
def lIlllIlllIlIIllllI(IIIIIlIIlIIllIlllI, IlIlIIIlllIIIIlllI):
    if IlIlIIIlllIIIIlllI == 0:
        return IIIIIlIIlIIllIlllI
    return lIlllIlllIlIIllllI(IlIlIIIlllIIIIlllI, IIIIIlIIlIIllIlllI % IlIlIIIlllIIIIlllI)

def a9448de3d95f1fd8dbf485a89f4caa532ec3002e8643fd49612fe68b4a391a4c (O00000O00O00OOOOO ):
  OO00O000O0O0O0000 =0 
  for O00OO00O00OOO0OO0 in range(1 ,O00000O00O00OOOOO +1 ):
    if lIlllIlllIlIIllllI (O00000O00O00OOOOO ,O00OO00O00OOO0OO0 )==1 :
      OO00O000O0O0O0000 +=1 
  return OO00O000O0O0O0000 

n = 303991656187370552532186782413032321693214021716415216613399742509005507946331111390813765388608155157153522967534405677981294458248526809408914703939942640013142585411652084080298452503582315445879592596607073950059427137019447651922916488362622164608938412581525324801263862647823996608338710002709293132391038890616691123551336249155541825018643954124974579574922930584024810200275283323678717488160270593856938058587074959266587325397617925964843728024435066656182376841685027462459968768583539424319191284696295

tot = 0

for i in range(2,n+1):
  if n%i == 0:
    if len(ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb(i))&1 == 1:
      tot += a9448de3d95f1fd8dbf485a89f4caa532ec3002e8643fd49612fe68b4a391a4c(i)

hashed = hashlib.sha256(str(tot).encode()).hexdigest()
IlIllIllIlIl("wxmctf{"+hashed[-32:]+"}")
```

Deobfuscating:

- `ca978112ca1bbdcafac231b39a23dc4da786eff8147c4e72b9807785afee48bb` generates the prime factors of `n`
- `IlIllIllIlIl` is an alias for `print`
- `lIlllIlllIlIIllllI` finds the GCD of two numbers
- `a9448de3d95f1fd8dbf485a89f4caa532ec3002e8643fd49612fe68b4a391a4c` is the totient function (more on that later)

We get
```python
import hashlib

def prime_factors_of(n):
  ls = []
  i = 1
  while n != 1:
    i += 1
    while n%i == 0:
      ls.append(i)
      n = n//i
  return ls

def gcd(a, b):
    if b == 0:
        return a
    return gcd(b, a % b)

def totient(arg):
  tot = 0 
  for i in range(1, arg + 1):
    if gcd(arg, i) == 1:
      tot += 1
  return tot 

# n = 3 * 5 * 7 * 11 * ... * 1229 (200 primes in total)
n = 303991656187370552532186782413032321693214021716415216613399742509005507946331111390813765388608155157153522967534405677981294458248526809408914703939942640013142585411652084080298452503582315445879592596607073950059427137019447651922916488362622164608938412581525324801263862647823996608338710002709293132391038890616691123551336249155541825018643954124974579574922930584024810200275283323678717488160270593856938058587074959266587325397617925964843728024435066656182376841685027462459968768583539424319191284696295

tot = 0

for i in range(2, n+1):
  if n % i == 0:
    if len(prime_factors_of(i)) % 2 == 1:
      tot += totient(i)

hashed = hashlib.sha256(str(tot).encode()).hexdigest()
print("wxmctf{"+hashed[-32:]+"}")
```

Essentially, this computes the sum of the totient functions of the factors of `n` that have an odd number of prime factors.

Now, everything you've seen so far is written by me, Colin. I'm not good at advanced math like this so I asked my friend/teammate Phil to help out with the rest of Stage 3. Say hi!

---

After finishing crypto2 (spoiler: it was very easy), my friend Colin asked me to do rev5. I was given the deobfuscated python code (below) and tasked with figuring out the output of `tot`.

```python
import hashlib

def prime_factors_of(n):
  ls = []
  i = 1
  while n != 1:
	i += 1
	while n%i == 0:
  	ls.append(i)
  	n = n//i
  return ls

def gcd(a, b):
	if b == 0:
    	return a
	return gcd(b, a % b)

def totient(arg):
  tot = 0
  for i in range(1, arg + 1):
	if gcd(arg, i) == 1:
  	tot += 1
  return tot

# n = 3 * 5 * 7 * 11 * ... * 1229 (200 primes in total)
n = 303991656187370552532186782413032321693214021716415216613399742509005507946331111390813765388608155157153522967534405677981294458248526809408914703939942640013142585411652084080298452503582315445879592596607073950059427137019447651922916488362622164608938412581525324801263862647823996608338710002709293132391038890616691123551336249155541825018643954124974579574922930584024810200275283323678717488160270593856938058587074959266587325397617925964843728024435066656182376841685027462459968768583539424319191284696295

tot = 0

for i in range(2, n+1):
  if n % i == 0:
	if len(prime_factors_of(i)) % 2 == 1:
  	tot += totient(i)

hashed = hashlib.sha256(str(tot).encode()).hexdigest()
print("wxmctf{"+hashed[-32:]+"}")
```

[Euler’s totient function](https://en.wikipedia.org/wiki/Euler's_totient_function), denoted with $\phi(n)$, refers to the number of integers from 1 to n that are coprime to n (aka, they share no factors or the gcd is 1). Henceforth, the phrase “totient of n” refers to $\phi(n)$

In the program, `tot` is the sum of the totient of all divisors of n that themselves strictly contain an odd number of prime divisors

So $\phi(3), \phi(5), \phi(3\times5\times7), \phi(3\times7\times11), \phi(3\times5\times7\times11\times13\times19)$ would be included in the sum but
$\phi(3\times5), \phi(1)$ wouldn’t

It is [well known](https://proofwiki.org/wiki/Sum_of_Euler_Phi_Function_over_Divisors) that the sum of the totients of the divisors of any number n is equal to n. 
$\sum_{d|n}^{}\phi(d)=n$

However, strictly looking at divisors with an odd number of prime factors presents a difficult challenge

Hopping around Wolfram and Wikipedia articles related to Euler’s totient function reveals another [useful function](https://en.wikipedia.org/wiki/M%C3%B6bius_function) that seemed related to this problem.

This arithmetic function (Mobius function) $\mu(n)$ returns 1 if n has an even number of prime factors and -1 if n has an odd number of prime factors.

Thus, $\sum_{d|n}^{}\mu(d)\phi(d)$ gives the sum of the totient of all divisors of n with a slight caveat: the totient of divisors with an odd number of prime factors are negative, whereas the totient of divisors with an even number of prime factors are positive.

Searching online for any clues related to the product of the Mobius function and the totient function (because I don’t want to do any of the actually insightful and difficult math myself lmao) reveals [this obscure math stackexchange post](https://math.stackexchange.com/questions/1059175/proving-an-identity-of-the-m%C3%B6bius-function-and-euler-s-totient-function-product)

Although the post asks for a proof of why the summation collapses to 0 for even integers n, the answers provided a generalized solution to our problem for any n. lab bhattacharjee provided a very clever simplification of the summation, reducing the problem from the summation of all divisors of n, a computationally arduous task, to a product of its prime divisors. 
$\prod_{p|n}^{}(\mu(1)\phi(1)+\mu(p)\phi(p))$, where p denotes a prime factor of n

(Expanding the product correctly yields the summation for $\mu(d)\phi(d)$ for all divisors d in n because of combinatorics shenanigans, try it and you’ll see! It helps that both the totient function and the Mobius function are [multiplicative](https://en.wikipedia.org/wiki/Multiplicative_function))

This product, which I now dub as P(n), simplifies to $P(n)=\prod_{p|n}^{}(2-p)$

$\sum_{d|n}^{}\phi(d)-P(n)$ cancels out the totient of all divisors of n with an even number of prime factors, while doubling the totient of all divisors of n with an odd number of prime factors.

Putting everything together, we can get the value of `tot` using this formula:
$\frac{\sum_{d|n}^{}\phi(d)-P(n)}{2}=\frac{n-\prod_{p|n}^{}(2-p)}{2}$

Thus, we can quickly write the algorithm to retrieve the solution to `tot`. 
```python
# a small example in python using n=3*5*7
fn = [3, 5, 7]
n = 3*5*7

# P(n)
prod = 1
for i in fn:
	prod *= (2-i)

tot = (n - prod)//2

print(tot)
```

The rest of the challenge is a matter of encoding our answer for tot to get our flag. (I didn’t do this part 🤪)

---

Alright, I'm back.

Running a modification of the Python code Phil gave me:

```python
def primes(n):
    out = []
    sieve = [True] * (100000)
    for p in range(2, 100000):
        if sieve[p]:
            out.append(p)
            n -= 1
            if n == 0:
                return out
            for i in range(p, 100000, p):
                sieve[i] = False

import math

fm = primes(201)[1:]
n = math.prod(fm)
tot = 0

prod = 1
for i in fm:
    prod *= (2-i)
tot = (n - prod)//2

import hashlib
hashed = hashlib.sha256(str(tot).encode()).hexdigest()
print("wxmctf{"+hashed[-32:]+"}")
```

This outputs `wxmctf{6a1d6a6c4cb0b56c74520e112d1f30c6}`, our flag!