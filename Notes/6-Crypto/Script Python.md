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

**Convert hexadecimal  to decimal**
```python
var="0xc"
print(int(var,16))
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

Another example to decode base64 strings : 
```python
import base64

var = b'anMvbXlmaWxlLnR4dA=='
flag_bytes = base64.b64decode(var)
flag = flag_bytes.decode('utf-8')

print(flag)

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


