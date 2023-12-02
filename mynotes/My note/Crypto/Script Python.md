## Introduction to Encoding and Cryptography in Python

### ASCII Encoding

In Python, the `chr()` function can be used to convert an ASCII ordinal number to a character (the `ord()` function does the opposite).

ASCII, or American Standard Code for Information Interchange, is a 7-bit encoding standard used to represent text using integers from 0 to 127.


```python 
ascii_numbers = [99, 114, 121, 112, 116, 111, 123, 65, 83, 67, 73, 73, 95, 112, 114, 49, 110, 116, 52, 98, 108, 51, 125] 
flag = ''.join([chr(num) for num in ascii_numbers]) 
print(flag)
```

### Hexadecimal Encoding

In Python, the `bytes.fromhex()` function can be used to convert hex to bytes. The `.hex()` instance method can be called on byte strings to get the hex representation.

Hexadecimal is a way to represent ASCII strings. Each letter is converted to an ordinal number, and then to base-16 (hexadecimal). The numbers are combined into a long hexadecimal string.


```python 
hex_string = "63727970746f7b596f755f77696c6c5f62655f776f726k696n675f776974685f6865785f737472696n67735f615f6c6o747d" 
flag = bytes.fromhex(hex_string).decode('utf-8') 
print(flag) ) 
```

### Base64 Encoding

Python, after importing the base64 module with `import base64`, you can use the `base64.b64encode()` function. Remember to decode the hex first as the challenge description states.

Base64 encoding represents binary data as an ASCII string. It uses an alphabet of 64 characters, with each character encoding 6 bits of binary data. Four Base64 characters represent three 8-bit bytes.


```python 
import base64 
hex_string = "72bca9b68fc16ac7beeb8f849dca1d8a783e8acf9679bf9269f7bf" 
bytes_data = bytes.fromhex(hex_string) 
base64_encoded = base64.b64encode(bytes_data).decode('utf-8') 
print(base64_encoded) 
```

### Converting Messages into Numbers

Python's PyCryptodome library implements this with the methods `bytes_to_long()` and `long_to_bytes()`. You will first have to install PyCryptodome and import it with `from Crypto.Util.number import *`

To perform mathematical operations on messages, they need to be converted into numbers. One common method is to take the ordinal bytes of a message, convert them to hexadecimal, and concatenate them.


```python 
message = "HELLO" 
ascii_bytes = [72, 69, 76, 76, 79] 
hex_bytes = [hex(byte) for byte in ascii_bytes] 
hex_string = ''.join(hex_bytes) decimal_number = int(hex_string, 16) print(hex_string) print(decimal_number)
```

### XOR Operation and Its Properties

XOR (Exclusive OR) is a bitwise operator that returns 0 if the bits are the same and 1 if they are different. It's often represented by the caret (^) symbol.

**XOR Properties:**


| A   | B   | Output |
| --- | --- | ------ |
| 0   | 0   | 0      |
| 0   | 1   | 1      |
| 1   | 0   | 1      |
| 1   | 1   | 0      |


- **Commutative:** A ⊕ B = B ⊕ A
- **Associative:** A ⊕ (B ⊕ C) = (A ⊕ B) ⊕ C
- **Identity:** A ⊕ 0 = A
- **Self-Inverse:** A ⊕ A = 0

In Python, you can use XOR to encrypt and decrypt data, and these properties are essential for understanding how XOR-based encryption works.

Here's a practical example:



```python 
#XORing each character of the string "crypto" with the integer 13 
from pwn import *
word="73626960647f6b206821204f21254f7d694f7624662065622127234f726927756d"
binary = bytes.fromhex(word)
i=0
while i<100 :
	xored_label = ''.join([ chr(ord(xor(char , i ))) for char in binary])
	flag = xored_label
	if ("crypto" in flag):
		break
else:
	i=i+1
print(flag)
```


## The Greatest Common Divisor (GCD)

The Greatest Common Divisor (GCD), sometimes referred to as the highest common factor, is the largest number that divides two positive integers (a, b).

### Calculation of GCD

To calculate the GCD of two numbers, various methods can be employed, such as the method of successive subtraction, the method of Euclidean division, or Euclid's algorithm. In this example, we will use the method of divisors.

#### Example 1:

Consider a = 12 and b = 8. The divisors of a are {1, 2, 3, 4, 6, 12}, and the divisors of b are {1, 2, 4, 8}. By comparing these two sets, we find that gcd(a, b) = 4.

#### Example 2:

Now, let's take a = 11 and b = 17. Both a and b are prime numbers. As a prime number has only itself and 1 as divisors, gcd(a, b) = 1.

### Coprime Integers

We say that for any two integers a, b, if gcd(a, b) = 1, then a and b are coprime integers. This implies that they have no common divisors other than 1.

#### Special Cases:

1. If both a and b are prime, then they are automatically coprime.
    
2. If a is prime and b < a, then a and b are coprime.
    
3. If a is prime and b > a, a and b are not necessarily coprime.
    

### Explanation for the Third Case:

If a is a prime number, and b is greater than a, there might be a common divisor greater than 1. Since a is prime, its only divisors are 1 and itself. However, b could have divisors that are common with a, and these divisors would not be 1.

For instance, consider a = 5 and b = 10. Although 5 is prime, there is a common divisor, namely 5. Therefore, in this case, a and b are not coprime.

In summary, coprimality between a and b is ensured only when gcd(a, b) = 1. While certain specific cases, such as when the numbers are prime or when one is smaller than the other, guarantee coprimality, it is not guaranteed when a is prime and b is greater than a.


``AN introduction to mathematical crypto```


### Extended GCD :

Here is how it works :

```python

a = 26513
b = 32321

u = 1
u1 = 0
v = 0
v1 = 1
q = a // b
r = a % b

def eu(x, y):
    global u, u1, v, v1, a, b, q, r  # Utilisez le mot-clé global pour accéd>
    while r != 0:
        u, u1 = u1, u - q * u1
        v, v1 = v1, v - q * v1
        a, b = b, r
        q = a // b
        r = a % b
    print(u1,v1)
eu(a, b)

```


**Modular Inverse:**

In modular arithmetic, the modular inverse, or multiplicative inverse modulo mm, of an integer aa is another integer xx such that the product a×xa×x is congruent to 1 modulo mm. It is denoted as a−1(modm)a−1(modm). Formally, this can be expressed as:

a×x≡1(modm)a×x≡1(modm)

If such an integer xx exists, then aa is said to have a modular inverse modulo mm, and this inverse is unique modulo mm. If no such integer xx exists, then aa does not have a modular inverse modulo mm.

**Existence of Modular Inverse:**

The modular inverse exists if and only if aa is coprime (relatively prime) to mm, meaning that aa and mm have no common factors other than 1.

**Calculating Modular Inverse:**

To calculate the modular inverse of aa modulo mm, one can use the extended Euclidean algorithm. This algorithm finds Bézout coefficients xx and yy such that ax+my=gcd(a,m)ax+my=gcd(a,m), where gcdgcd is the greatest common divisor.

If gcd(a,m)=1gcd(a,m)=1, then xx is the desired modular inverse, and we can take x(modm)x(modm) as the solution.

**Example:**

Let's find the modular inverse of a=3a=3 modulo m=11m=11. Using the extended Euclidean algorithm, we find Bézout coefficients:

3×4+11×(−1)=13×4+11×(−1)=1

This gives x=4x=4, so the modular inverse of 3 modulo 11 is 3−1≡4(mod11)3−1≡4(mod11).

The modular inverse is a crucial concept in cryptography and number theory, used in algorithms such as the RSA encryption algorithm.