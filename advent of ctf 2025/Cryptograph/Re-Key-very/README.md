# Write-up of the challenge "Re-Key-very"

This challenge is part of the "Cryptography" category and is woth 134 points.

# Goal of the challenge

There's basically a **gen.py** script that returns specfic **out.txt**, which when we anaylze we realize that the nonce secrecy is predicatable which is very bad in ECDSA.

## Program structure

gen.py:
```python
import hashlib
import fastecdsa.curve
import random
def inv_mod(k, p):
    return pow(k, p - 2, p)

# secp256k1 parameters
curve = fastecdsa.curve.secp256k1
G = curve.G
n = curve.q

def sign(msg, k, d):
    z = int.from_bytes(hashlib.sha256(msg).digest(), 'big')
    R = G * k
    r = R.x % n
    s = (inv_mod(k, n) * (z + r * d)) % n
    return r, s

def verify(msg, signature, Q):
    r, s = signature
    if not (1 <= r < n and 1 <= s < n):
        return False
    z = int.from_bytes(hashlib.sha256(msg).digest(), 'big')
    w = inv_mod(s, n)
    u1 = (z * w) % n
    u2 = (r * w) % n
    R = G * u1 + Q * u2
    return R.x % n == r

key = open('flag.txt', 'rb').read()
d = int.from_bytes(key, 'big')
d = (d % (n - 1)) + 1 
P = G * d
k = random.randint(0, n - 1)

msgs = [
    b'Beware the Krampus Syndicate!',
    b'Santa is watching...',
    b'Good luck getting the key'
]
    
for m in msgs:
    r, s = sign(m, k, d)
    r_bytes = r.to_bytes(32, 'big')
    s_bytes = s.to_bytes(32, 'big')
    print(f'msg: {m}')
    print(f'r  : {r_bytes.hex()}')
    print(f's  : {s_bytes.hex()}')
    assert verify(m, (r, s), P)
    # gonna change nonces!
    k += 1
```

output.txt:
```
msg: b'Beware the Krampus Syndicate!'
r  : a4312e31e6803220d694d1040391e8b7cc25a9b2592245fb586ce90a2b010b63
s  : e54321716f79543591ab4c67e989af3af301e62b3b70354b04e429d57f85aa2e
msg: b'Santa is watching...'
r  : 6c5f7047d21df064b3294de7d117dd1f7ccf5af872d053f12bddd4c6eb9f6192
s  : 1ccf403d4a520bc3822c300516da8b29be93423ab544fb8dbff24ca0e1368367
msg: b'Good luck getting the key'
r  : 2c15aceb49e63e4a2c8357102fbd345ac2cbd1b214c77fba0cd9ffe8d20d2c1e
s  : 1ee49ef3857ad1d9ff3109bfb4a91cb464ab6fdc88ace610ead7e6dee0957d95


```

## Security breach

So the problems that led to this exploit is the use of reusing or predicting ECDSA nonces. ECDSA security completely depends on nonce secrecy so if the nonce secrecy is predicatable then it is **weak** and therefore exploitable. The nonce is not random. In gen.py the same nonce is used and then incremented:

```k, k+1, k+2```
this creates a predictable relationship between signatures.


## Solution
So we just basically first take the leaks and the p from the **out.txt**.
The challenge uses ECDSA, where each signature depends on a secret nonce k. In the provided code, the nonce is not random it starts at some value k and is incremented for each signature **(k, k+1, k+2)**. This creates a predictable relationship between signatures and two signatures with related nonces. Then we can set up two equations and solve for the nonce k. Once k is recovered, the private key d is computed directly from the signature formula.

```python
import hashlib
from fastecdsa.curve import secp256k1

n = secp256k1.q

def inv(x):
    return pow(x, n-2, n)

msgs = [
    b'Beware the Krampus Syndicate!',
    b'Santa is watching...',
]

r = [
    int("a4312e31e6803220d694d1040391e8b7cc25a9b2592245fb586ce90a2b010b63", 16),
    int("6c5f7047d21df064b3294de7d117dd1f7ccf5af872d053f12bddd4c6eb9f6192", 16),
]

s = [
    int("e54321716f79543591ab4c67e989af3af301e62b3b70354b04e429d57f85aa2e", 16),
    int("1ccf403d4a520bc3822c300516da8b29be93423ab544fb8dbff24ca0e1368367", 16),
]

z = [int.from_bytes(hashlib.sha256(m).digest(), "big") for m in msgs]

# Solve for k
num = (z[1] - (r[1] * (-z[0]) * inv(r[0])) ) % n
den = (s[1] - r[1] * s[0] * inv(r[0])) % n
k = num * inv(den) % n

# Recover private key d
d = (s[0]*k - z[0]) * inv(r[0]) % n

print("k =", k)
print("d =", d)

# Convert d to flag
flag = d.to_bytes(32, "big").lstrip(b"\x00")
print("flag =", b"csd{" + flag + b"}")

```

If you liked this writeup you can star my repository.