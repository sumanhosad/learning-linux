### [[Implementation X3DH#Implementation X3DH|Implementation of X3DH]]
Below is the same comprehensive guide written in Markdown with all block math expressions enclosed using `$$` (suitable for Obsidian):

---

# Comprehensive Guide: ECDH & X3DH Mathematics

This guide delves deeply into the mathematics behind both **Elliptic Curve Diffie–Hellman (ECDH)** and the **Extended Triple Diffie–Hellman (X3DH)** protocols. It covers the foundational concepts of elliptic curves, the arithmetic involved, efficient algorithms for scalar multiplication, the detailed workings of ECDH, and the multi-step process of X3DH. The goal is to provide both the theoretical background and the practical formulas that underpin these cryptographic protocols.

---

## 1. Elliptic Curves: Basics and Group Structure

### 1.1. The Elliptic Curve Equation

In many cryptographic applications, an elliptic curve over a finite field $$\mathbb{F}_p$$with prime $$p$$) is expressed in the **short Weierstrass form**:

$$
y^2 \equiv x^3 + ax + b \pmod{p}
$$

with the requirement that the discriminant

$$
\Delta = -16(4a^3 + 27b^2) \not\equiv 0 \pmod{p}
$$

ensures that the curve is non-singular (i.e., it has no cusps or self-intersections).

Modern protocols—like those based on Curve25519—often use Montgomery or Edwards curve forms for better performance and security. For instance, Curve25519 is defined by a Montgomery curve:

$$
By^2 = x^3 + Ax^2 + x,
$$

with parameters chosen for efficiency (e.g., $$p = 2^{255} - 19$$, $$A = 486662$$).

### 1.2. The Group Law on Elliptic Curves

The set of points on an elliptic curve, together with a special point at infinity $$\mathcal{O}$$, forms an **abelian group**. The group operation is defined as follows:

#### Point Addition (for $$P \neq Q$$)

Given two points $$P = (x_1, y_1)$$ and $$Q = (x_2, y_2)$$ (with $$Q \neq -P$$), draw the line through them. This line will generally intersect the curve at a third point $$R' = (x_3', y_3')$$. The sum is defined as the reflection of $$R'$$ over the $$x$$-axis:

1. **Calculate the slope:**

   $$
   \lambda = \frac{y_2 - y_1}{x_2 - x_1} \pmod{p}
   $$

   (Division here means multiplication by the modular inverse of $$x_2 - x_1$$ modulo $$p$$.)

2. **Compute the coordinates of the sum $$R = P + Q$$:**

   $$
   x_3 \equiv \lambda^2 - x_1 - x_2 \pmod{p},
   $$
   $$
   y_3 \equiv \lambda (x_1 - x_3) - y_1 \pmod{p}.
   $$

#### Point Doubling (for $$P = Q$$)

When adding a point $$P$$ to itself, you use the tangent at $$P$$:

1. **Calculate the slope:**

   $$
   \lambda = \frac{3x_1^2 + a}{2y_1} \pmod{p}.
   $$

2. **Compute the doubled coordinates:**

   $$
   x_3 \equiv \lambda^2 - 2x_1 \pmod{p},
   $$
   $$
   y_3 \equiv \lambda (x_1 - x_3) - y_1 \pmod{p}.
   $$

#### Identity and Inverses

- **Identity Element:**  
  The point at infinity $$\mathcal{O}$$acts as the identity element.
  
- **Inverse:**  
  The inverse of a point $$P = (x, y)$$ is given by:
  
  $$
  -P = (x, -y)
  $$

---

## 2. Arithmetic and Scalar Multiplication on Elliptic Curves

### 2.1. Finite Field Operations

All arithmetic on an elliptic curve is performed modulo a prime $$p$$:

- **Addition/Subtraction/Multiplication:**  
  Standard arithmetic modulo $$p$$.
  
- **Division:**  
  Involves finding the modular inverse; for example, the inverse of $$x$$ modulo $$p$$ can be computed using Fermat's Little Theorem:
  
  $$
  x^{-1} \equiv x^{p-2} \pmod{p}.
  $$

### 2.2. Scalar Multiplication

The operation $$Q = d \cdot P$$ (where $$d$$ is an integer, typically the private key) is defined as adding the point $$P$$ to itself $$d$$ times. Two popular algorithms are:

#### Double-and-Add

A binary method where:
1. Write $$d$$ in binary.
2. Start with $$Q = \mathcal{O}$$ and iterate over each bit.
3. Double $$Q$$ for every bit; if the bit is 1, add $$P$$.

#### Montgomery Ladder

This method is preferred in implementations such as X25519 because it uses a regular pattern of operations (mitigating timing attacks) and computes both the desired scalar multiple and an auxiliary value in a secure, efficient manner.

---

## 3. Elliptic Curve Diffie–Hellman (ECDH)

### 3.1. Key Generation

Both parties agree on a common elliptic curve and a base point $$P$$ of prime order $$n$$. Then:

- **Private Key:**  
  Each party chooses a random integer $$d$$ in the range $$[1, n-1]$$.

- **Public Key:**  
  Compute the public key as:
  
  $$
  Q = d \cdot P.
  $$

The security of ECDH is based on the **Elliptic Curve Discrete Logarithm Problem (ECDLP)**, which makes recovering $$d$$ from $$Q$$ and $$P$$ computationally infeasible.

### 3.2. Computing the Shared Secret

Assume Alice and Bob have:

- **Alice:**  
  Private key $$d_A$$ and public key $$Q_A = d_A \cdot P$$.

- **Bob:**  
  Private key $$d_B$$ and public key $$Q_B = d_B \cdot P$$.

They compute the shared secret as:

$$
S = d_A \cdot Q_B = d_A \cdot (d_B \cdot P) = (d_A \cdot d_B) \cdot P,
$$

and similarly:

$$
S = d_B \cdot Q_A.
$$

Both computations yield the same point $$S$$ on the curve, which is then used (or its $$x$$-coordinate extracted) as the basis for further key derivation (often via a Key Derivation Function, KDF).

---

## 4. Extended Triple Diffie–Hellman (X3DH)

X3DH is an enhanced key agreement protocol used in asynchronous messaging (for example, by the Signal protocol). It builds upon multiple ECDH exchanges using several types of keys.

### 4.1. Key Components in X3DH

Each participant maintains multiple key pairs:

- **Identity Key (IK):**  
  A long-term key pair used for identity.

- **Signed Prekey (SPK):**  
  A medium-term key pair, typically signed by the identity key to verify authenticity.

- **One-Time Prekey (OPK):**  
  An optional, short-lived key pair used only once to provide additional forward secrecy.

- **Ephemeral Key (EK):**  
  A temporary key pair generated for each session.

For example, if Alice wants to initiate a session with Bob:

- **Alice’s Keys:**
  - $$IK_A = (d_{IK_A}, Q_{IK_A})$$ (long-term)
  - $$EK_A = (d_{EK_A}, Q_{EK_A})$$ (ephemeral)

- **Bob’s Keys:**
  - $$IK_B = (d_{IK_B}, Q_{IK_B})$$ (long-term)
  - $$SPK_B = (d_{SPK_B}, Q_{SPK_B})$$ (signed prekey)
  - $$OPK_B = (d_{OPK_B}, Q_{OPK_B})$$ (one-time prekey, if available)

### 4.2. Diffie–Hellman Computations in X3DH

Alice performs several Diffie–Hellman operations to combine multiple sources of entropy and to achieve properties like forward secrecy and authentication. Define the basic DH operation as:

$$
\mathsf{DH}(d, Q) = d \cdot Q.
$$

Then, Alice computes:

4. **DH1 (Identity–Signed Prekey):**

   $$
   S_1 = \mathsf{DH}(d_{IK_A}, Q_{SPK_B}).
   $$

5. **DH2 (Ephemeral–Identity):**

   $$
   S_2 = \mathsf{DH}(d_{EK_A}, Q_{IK_B}).
   $$

6. **DH3 (Ephemeral–Signed Prekey):**

   $$
   S_3 = \mathsf{DH}(d_{EK_A}, Q_{SPK_B}).
   $$

7. **DH4 (Ephemeral–One-Time Prekey, Optional):**

   $$
   S_4 = \mathsf{DH}(d_{EK_A}, Q_{OPK_B}).
   $$

   (If Bob does not supply an OPK or if it’s not used, this step is omitted.)

### 4.3. Combining the Shared Secrets

The outputs $$S_1, S_2, S_3$$ (and $$S_4$$, if computed) are generally points on the curve. In practice, protocols extract or encode the $$x$$-coordinate (or use another standardized encoding) to convert these points into fixed-length bit strings. These bit strings are concatenated:

$$
\text{Input} = \text{Encode}(S_1) \, || \, \text{Encode}(S_2) \, || \, \text{Encode}(S_3) \, || \, \text{Encode}(S_4)
$$

A **Key Derivation Function (KDF)**—often HKDF—is then applied to this concatenated string:

$$
K = \mathsf{KDF}(\text{Input}, \text{salt}, \text{info})
$$

This step produces the final symmetric session key. The KDF ensures that the resulting key material is uniformly random and that any weaknesses in a single DH output are mitigated by mixing in additional entropy.

### 4.4. Security Rationale Behind X3DH

- **Asynchronicity:**  
  Bob’s signed prekeys and one-time prekeys are published to a server, enabling Alice to initiate a session even if Bob is offline.

- **Forward Secrecy:**  
  The use of ephemeral keys (and the one-time prekey) means that even if long-term keys are compromised later, past communications remain secure.

- **Authentication:**  
  The signature on Bob’s signed prekey (using his Identity Key) assures Alice that the key originates from Bob, reducing the risk of man-in-the-middle (MITM) attacks.

---

## 5. Key Derivation Functions (KDF) and Additional Security Considerations

### 5.1. Key Derivation

After the DH computations, a KDF (commonly **HKDF**) is used to process the raw shared secret material. HKDF works in two stages:

8. **Extract:**  
   Condense the input keying material into a fixed-length pseudorandom key (PRK) using an HMAC:

   $$
   PRK = \text{HMAC}(\text{salt}, \text{Input})
   $$

9. **Expand:**  
   Expand the PRK into the desired number of bits using:

   $$
   K = \text{HKDF-Expand}(PRK, \text{info}, L)
   $$

   where $$L$$ is the output length and "info" can include context such as domain separation tags.

### 5.2. Side-Channel Resistance

When implementing these protocols, take precautions such as:

- **Constant-Time Operations:**  
  Use constant-time arithmetic to prevent timing attacks.
  
- **Secure Memory Handling:**  
  Zero out sensitive data from memory after use.
  
- **Using Well-Tested Libraries:**  
  Rely on libraries like libsodium, OpenSSL, or Crypto++ to reduce the risk of implementation flaws.

### 5.3. Domain Separation and Parameter Choices

- **Domain Separation:**  
  The “info” parameter in the KDF and other contextual strings should be used to ensure that keys generated for different purposes do not interfere with each other.
  
- **Parameter Choices:**  
  Curves such as Curve25519 are specifically chosen for their strong security properties and efficient, side-channel-resistant implementations.

---

## 6. Summary of Mathematical Formulas

### ECDH

- **Key Generation:**

  $$
  Q = d \cdot P
  $$

- **Shared Secret Computation:**

  $$
  S = d_A \cdot Q_B = d_B \cdot Q_A = (d_A \cdot d_B) \cdot P
  $$

### X3DH

Let:

- $$IK_A = (d_{IK_A}, Q_{IK_A})$$
- $$EK_A = (d_{EK_A}, Q_{EK_A})$$
- $$IK_B = (d_{IK_B}, Q_{IK_B})$$
- $$SPK_B = (d_{SPK_B}, Q_{SPK_B})$$
- $$OPK_B = (d_{OPK_B}, Q_{OPK_B})$$ (optional)

Compute:

- **DH1:**  
  $$
  S_1 = d_{IK_A} \cdot Q_{SPK_B}
  $$

- **DH2:**  
  $$
  S_2 = d_{EK_A} \cdot Q_{IK_B}
  $$

- **DH3:**  
  $$
  S_3 = d_{EK_A} \cdot Q_{SPK_B}
  $$

- **DH4 (if applicable):**  
  $$
  S_4 = d_{EK_A} \cdot Q_{OPK_B}
  $$

- **Final Key:**

  $$
  K = \mathsf{KDF}(\text{Encode}(S_1) \, || \, \text{Encode}(S_2) \, || \, \text{Encode}(S_3) \, || \, \text{Encode}(S_4))
  $$

---

## Conclusion

This comprehensive guide has combined detailed mathematical explanations and practical considerations for both ECDH and X3DH:

- **ECDH** leverages the algebraic structure of elliptic curves and the hardness of the ECDLP to enable secure key exchange.
- **X3DH** builds on multiple ECDH exchanges with different types of keys (Identity, Ephemeral, Signed Prekey, and optionally One-Time Prekey) to support asynchronous communications, provide forward secrecy, and ensure authentication.

Armed with this understanding, you can proceed to implement these protocols in C++ (or another language) using established libraries, while keeping in mind the critical aspects of secure key derivation and side-channel resistance.

If you need further clarification or additional details on any part of this guide, feel free to ask!

---

*Happy coding and secure cryptography!*

##