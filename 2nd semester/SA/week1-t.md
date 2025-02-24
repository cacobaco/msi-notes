# Week 1 Theory

## Cryptography

### Basics
1. **Types of operations**
    - Substitution
    - Permutation
    - Mathematical operations
2. **Number of keys**
    - Symmetric: 1 key
    - Asymmetric: 2 keys
3. **Processing methods**
    - Block cipher (divides into chunks)
    - Stream cipher (processes character by character or bit by bit)

### Symmetric Algorithms
**Kerckhoffs hypothesis** - Encryption/decryption algorithm details are public except for the key.

**Attack methods:**
- Quality of algorithm -> Cryptanalysis
- Key size -> Brute-force

### (Symmetric) Block Cipher
Encrypts blocks of n bits into n bits.

**Reversible transformation**: Decryption is possible; each plaintext block corresponds to a unique ciphertext block.
**Irreversible transformation**: Decryption may not be possible.

An **ideal block cipher algorithm** would allow all possible reversible transformations, meaning (2^n)! transformations, where n is the block size in bits. In practice, only (2^k)! of (2^n)! are possible, where k is the key size in bits.

#### Ciphers
- **Substitution cipher**: Replaces one element with another (e.g., "aac" → "BBD").
- **Permutation cipher**: Rearranges elements (e.g., "aac" → "CAA").
- **Product cipher**: Combines multiple substitution and permutation operations to enhance security.
- **Feistel cipher**: A structure that divides the plaintext into two halves, processes one half with a function and key, then swaps them iteratively to achieve encryption. Used in DES and other block ciphers.
### Feistel Structure
Uses a repeated structure to achieve encryption. The plaintext is divided into two halves (left and right). In each round:
1. (Substitution) The right half is processed through a function (F) using a subkey.
2. The result is XORed with the left half.
3. (Permutation) The halves are swapped.
 This process is repeated for multiple rounds, at the end a last swap (permutation) is made to simplify decryption, after which the halves are recombined to produce the final ciphertext. This structure allows decryption by applying the same process in reverse, using the subkeys in reverse order.

![[2nd semester/SA/images/w1t-1.png]]

The exact implementation depends on:
- block size: larger results in higher security but in slower execution
- key size: larger improves security but may increase execution time
- number of iterations: larger gives more security but reduces performance
- sub-key generation algorithm
- function F()

**Doesn't need a decryption function. The same encryption function can be used to decrypt it (XOR properties).**

#### Principals for the Design
- **Number of rounds:**
	- should be chosen in a way that the cryptanalytic effort requires greater effort than brute-force key search
- **Function F:**
	- difficult to find out how to undo the substitution, e.g., should be non-linear
	- changing 1 bit = 50% changing every bit

### Data Encryption Standard (DES)
Encrypts 64-bit blocks using a 56-bit key over 16 rounds based on the Feistel structure.

#### DES Structure
1. **Initial Permutation (IP)**: Rearranges the bits of the plaintext.
2. **Feistel Rounds (16 rounds)**:
    - The 64-bit block is divided into two 32-bit halves (L and R).
    - The right half (R) is expanded to 48 bits using an **expansion permutation**.
    - XOR operation is applied between the expanded R and a round-specific 48-bit subkey.
    - The result passes through S-Boxes (Substitution Boxes), reducing it back to 32 bits.
    - A final P-Box permutation is applied.
    - The result is XORed with the left half (L).
    - Left half (L) becomes the previous right half (R).
3. **Final Permutation (FP)**: The final transformation produces the ciphertext.

![[w1t-2.png]]

#### Double DES (2DES)
Encrypts twice using two different keys.
- C=EK2(EK1(P))
- P=DK1(DK2(C))

More resistant to brute-force attacks but vulnerable to the **Meet-in-the-Middle Attack**.

**Meet-in-the-Middle Attack** reduces complexity to 2^(k+1) encryptions and requires 2^k memory.

#### Triple DES (3DES)
- **With Two Keys**: Uses encryption-decryption-encryption (EDE) sequence to improve security.
	- C=EK1(DK2(EK1(P)))
	- P=DK1(EK2(DK1(C)))
- **With Three Keys**: Further increases security with three independent keys:
	- C=EK3(DK2(EK1(P)))
	- P=DK1(EK2(DK3(C)))
- **With Minimal Key**: Uses key derivation from a smaller secret to generate three effective keys.
	- K1=EX1(DX2(EX1(T1)))
	- K2=EX1(DX2(EX1(T2)))
	- K3=EX1(DX2(EX1(T3)))
	- X1, X2 are the keys
	- T1, T2, T3 are constants that do not need to be secret
	- K1, K2, K3 are the keys to use in the previous algorithm.