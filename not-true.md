# Not TRUe — 400 pts

**Category:** Cryptography  
**Author:** ishaanharry

## Overview

This challenge uses NTRU, a lattice-based cryptosystem. You get the public key and encrypted ciphertext and need to recover the private key to decrypt the flag. The hint flat out tells you its a lattice-based system and asks if theres a lattice attack to compute the private key.

## How NTRU works

NTRU operates on polynomial rings. Two secret polynomials `f` and `g` are used to generate a public key `h`. Encryption takes your message, multiplies the public key by a random polynomial, and adds the message on top. Decryption requires knowing `f` to undo all of that.

The security comes from the fact that recovering `f` and `g` from the public key requires finding short vectors in a lattice which is computationally hard when the parameters are large enough.

## Approach

The challenge uses N=48, p=3, q=509. Those parameters are intentionally small. In a real NTRU implementation N would be something like 743 or higher.

I built the NTRU lattice in SageMath using the public key. The lattice is constructed so that short vectors correspond to the private key polynomials. Ran LLL reduction on it, which is an algorithm that finds relatively short vectors in a lattice. When the dimensions are small enough it recovers the private key directly.

After recovering `f`, I used it to decrypt each ciphertext block:

```python
a = f * ciphertext (mod q)
a = center_lift(a)
m = a (mod p)
# m contains the message bits
```

The encryption script encoded the message as binary in the polynomial coefficients so the last step was converting those bits back to ASCII. Got the flag.

## Notes

Lattice-based crypto is actually the basis for a lot of post-quantum encryption. NIST selected lattice-based schemes for their post-quantum standards because the math is believed to be hard even for quantum computers. This challenge works because N=48 is way too small. The LLL algorithm from 1982 eats it for breakfast. Real implementations use parameters that are orders of magnitude larger.

This was probably the hardest crypto challenge I solved and it took me a while to get the SageMath code working properly. Definitely learned a lot about how lattice reduction actually works under the hood.
