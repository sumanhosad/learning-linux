# **📌 X3DH (Extended Triple Diffie-Hellman) - Mathematical Explanation with Examples**  

The **X3DH key agreement protocol** is used to establish a secure communication channel between two users who have never communicated before. It is the foundation of **Signal Protocol** and ensures **forward secrecy** and **post-compromise security**.  

---

## **🔹 Keys Used in X3DH**
Each party (Alice and Bob) has multiple key pairs:

| **Key Type**                            | **Alice (Sender)** | **Bob (Receiver)** |
| --------------------------------------- | ------------------ | ------------------ |
| **Identity Key (IK)** (Long-term)       | (I_A, i_A)         | (I_B, i_B)         |
| **Signed PreKey (SPK)** (Medium-term)   | -                  | (S_B, s_B)         |
| **One-Time PreKey (OTPK)** (Short-term) | -                  | (O_B, o_B)         |
| **Ephemeral Key (EK)** (Temporary)      | (E_A, e_A)         | -                  |

Where:  
- **Lowercase (i_A, s_B, o_B, e_A) are private keys.**  
- **Uppercase (I_A, S_B, O_B, E_A) are public keys.**  
- **PreKeys are pre-generated and uploaded to a server.**

---

## **🔹 Mathematical Functions in X3DH**
### **1️⃣ Elliptic Curve Key Generation**
Each party generates **private-public key pairs** using **Elliptic Curve Diffie-Hellman (ECDH) on Curve25519**:

\[
A = aG, \quad B = bG, \quad S = sG, \quad O = oG, \quad E = eG
\]

Where:
- **G** is a generator point on the elliptic curve.
- **Lowercase letters (a, b, s, o, e) are private keys** (random scalars).
- **Uppercase letters (A, B, S, O, E) are public keys** (points on the elliptic curve).

---

### **2️⃣ X3DH Computes Four ECDH Operations**
Alice and Bob perform **four Diffie-Hellman (DH) operations** to generate a shared secret:

| **DH Operation** | **Formula**                                 | **Computed By**        |
| ---------------- | ------------------------------------------- | ---------------------- |
| **DH1**          | \( K_1 = \text{ECDH}(i_A, S_B) = i_A S_B \) | Alice                  |
| **DH2**          | \( K_2 = \text{ECDH}(e_A, I_B) = e_A I_B \) | Alice                  |
| **DH3**          | \( K_3 = \text{ECDH}(e_A, S_B) = e_A S_B \) | Alice                  |
| **DH4**          | \( K_4 = \text{ECDH}(e_A, O_B) = e_A O_B \) | Alice (if OTPK exists) |

Each **DH computation** follows:

\[
K_i = \text{scalar multiplication of Alice’s private key and Bob’s public key}
\]

---

### **3️⃣ Compute Final Shared Secret Using HKDF**
The derived keys **K1, K2, K3, K4** are **concatenated** and passed into **HKDF (HMAC-based Key Derivation Function)**:

\[
\text{SK} = \text{HKDF}(K_1 || K_2 || K_3 || K_4)
\]

- If Bob does **not use an OTPK**, then \( K_4 \) is ignored.
- **HKDF expands these secrets into encryption keys** used in the **Double Ratchet Algorithm**.

---

## **🔹 Example with Numerical Values**
### **1️⃣ Key Generation**
Let's assume:
- \( i_A = 7 \), \( I_A = 7G \)
- \( i_B = 5 \), \( I_B = 5G \)
- \( s_B = 3 \), \( S_B = 3G \)
- \( o_B = 11 \), \( O_B = 11G \)
- \( e_A = 9 \), \( E_A = 9G \)

---

### **2️⃣ Compute ECDH Shared Secrets**
Using scalar multiplication:

#### **DH1 (Identity Key × Signed PreKey)**
\[
K_1 = i_A S_B = 7 \times (3G) = 21G
\]

#### **DH2 (Ephemeral Key × Identity Key)**
\[
K_2 = e_A I_B = 9 \times (5G) = 45G
\]

#### **DH3 (Ephemeral Key × Signed PreKey)**
\[
K_3 = e_A S_B = 9 \times (3G) = 27G
\]

#### **DH4 (Ephemeral Key × One-Time PreKey)**
\[
K_4 = e_A O_B = 9 \times (11G) = 99G
\]

---

### **3️⃣ Derive the Final Shared Key Using HKDF**
Concatenate \( K_1, K_2, K_3, K_4 \):

\[
K = K_1 || K_2 || K_3 || K_4 = 21G || 45G || 27G || 99G
\]

Pass it through **HKDF (HMAC-SHA256)**:

\[
\text{Session Key} = \text{HKDF}(21G || 45G || 27G || 99G)
\]

---

## **🔹 Summary**
1. **Generates four ECDH shared secrets** using Curve25519.
2. **Concatenates the secrets and runs HKDF** to derive a **final session key**.
3. **Session key is used to encrypt/decrypt messages** in the Double Ratchet Algorithm.

---

## **🔹 Why X3DH is Secure**
- **Forward Secrecy:** Uses ephemeral keys (**e_A**) to ensure past messages remain secure.
- **Post-Compromise Security:** If a private key is compromised, new messages are still protected.
- **Resistant to Man-in-the-Middle Attacks:** Public keys are authenticated via signed prekeys.

---

