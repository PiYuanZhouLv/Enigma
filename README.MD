# Enigma

P.S. I'm sorry for my poor English. if you have advice on translate, please on an issue with tag `translate advice`.

This is the Python version of a machine called Enigma which was widely used in World War II.

> Would you like to use Enigma?
> 
> Now, I provide one for you.
> 
> But you have to assemble it by youself.
> 
> So, please read the usage below carefully!
## How does it work?
The structure of the Enigma is below
```
       +-------+     +------------------+    +-------------+    +----------------+    +----------------+
       | input | ->  | Translate.tran() | -> | Swap.swap() | -> | Wheel.change() | -> | Wheel.change() | ...
       +-------+     +------------------+    +-------------+    +----------------+    +----------------+
                                                                                                          |
                                                                                                +-------------------+
                                                                                                | Reflect.reflect() |
                                                                                                +-------------------+
                                                                                                          |
                                                                                                          V
+--------+     +--------------------+    +-------------+    +------------------+    +------------------+
| output | <-  | Translate.r_tran() | <- | Swap.swap() | <- | Wheel.r_change() | <- | Wheel.r_change() | ...
+--------+     +--------------------+    +-------------+    +------------------+    +------------------+
```

## Usage
There is an example:
```pycon
>>> from enigma import *
>>> send_message("Hello word! Hello pi!你好世界，你好圆周率!", '')
# Message object and its MD5 value have been saved into file message.pickle and message.md5
# Please send this two file above to the receiver.
# ================================================================================
# *Private* Information Below, please keep it a secret and send it to the receiver.
# Swap: 8
# Wheels: [9, 1, 0, 4, 6, 8, 3, 2]
# Reflect: 0
# Start position: (125, 133, 16, 210, 107, 67, 251, 223)
# Code:  (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223))
>>> receive_message('', (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223)))
# ====================================Message=====================================
# Hello word! Hello pi!你好世界，你好圆周率!
```
P.S. File 'message.pickle' and 'message.md5' is also in the folder to test for you.
Before you execute the receive_message(...), please check the MD5 in message.md5 again:
`d6e8b26cfd145565e8894a129589044d`

### 1. Use function `send_message()` and `receive_message()`
This is the topmost way to call Enigma to work. _(I'm sorry for my poor English, I'll call it like 
"first floor way", "second floor way" below.)_ This floor way base on second floor(ToolsGroup, I'll introduce it later.).
It's biggest advantage is you can call it by a simple way.

Function `send_message(message, path)` has two args, message is the sentences that you want to encrypt and 
path is the position where two message file will be placed. When you call this function, 
it will put two files to the path and output to screen like this:
```
Message object and its MD5 value have been saved into file message.pickle and message.md5
Please send this two file above to the receiver.
================================================================================
*Private* Information Below, please keep it a secret and send it to the receiver.
Swap: 8
Wheels: [9, 1, 0, 4, 6, 8, 3, 2]
Reflect: 0
Start position: (125, 133, 16, 210, 107, 67, 251, 223)
Code:  (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223))
```
Please remember the least line `Code:  (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223))`
because it's the private key to your message.

You may ask me what things are put in two file, let me tell you now. The things in 'message.pickle' is a Message object 
which includes you encrypted message and a ToolsGroup(the second floor). it's named '.pickle' because it was 'pickle' 
by module pickle. Thinking about the security, when the user call unpickle, it will check the md5 between 
'message.pickle' and 'message.md5'.

When message files and private key are received by the receiver, he(she) can call function receive_message().

Function `receive_message(path, info)` also has two args, path is the path where message files are placed, 
info is the private key as a tuple. if everything are right, it will output like this:
```
====================================Message=====================================
Hello word! Hello pi!你好世界，你好圆周率!
```
And the sentences below the "=====Message=====" is the origin sentences.

Security: High
(if one only got message file, he/she needs to test for (10!/2!) * 10 * 10 * 256 * 256 * 256 = 3,044,058,071,040,000 situations, 
if one only got private key, he/she can hardly know the origin message.)

### 2. Use `ToolsGroup`
Here is the example:
```pycon
>>> from enigma import *
>>> e = bytetools.make_up(0, [4, 3, 1], 2)
>>> e.set_position(102, 80, 36)
>>> e.encipher("Hello world! Hello pi!你好世界，你好圆周率!")
b'\x97\xaf93\xa7\xc16\xcd\t.!3\x16\x0e\x95\x04Lwk\xdc\x88#~\xf6\xb6X*(\xec\xad\xbeJ"\xc0\xc3!\xbdk\x84fd\xb1\t\xc1j\xfe\xa8\xb1\x90\x18y\xea\x10'
>>> m = _
>>> e2 = bytetools.make_up(0, [4, 3, 1], 2)
>>> e2.set_position(102, 80, 36)
>>> e2.encipher(m, 'return char')
'Hello world! Hello pi!你好世界，你好圆周率!'
```
First, we should know what's ToolsGroup. ToolsGroup, as its name, it's a set of tools which can be make up to be an Enigma.
In this module, it included two builtin ToolsGroups - `asciitools` and `bytetools`.
About the difference of two group, is the first one just work with ascii chars while the other can work on all Unicode chars.

The most important method is `ToolsGroup.make_up(swap, *wheels, reflect)`.
This function can turn tools to an Enigma.

Note: There are 3 Swaps, 8 Wheels and 4 Reflects in `asciitools`,
6 Swaps, 8 Wheels and 4 Reflects in `bytetools`

To send other a message, you can tell he(she) like this `\x97\xaf93\xa7\xc16\xcd\t.!3\x16\x0e\x95\x04Lwk\xdc\x88#~\xf6\xb6X*(\xec\xad\xbeJ"\xc0\xc3!\xbdk\x84fd\xb1\t\xc1j\xfe\xa8\xb1\x90\x18y\xea\x10 @ (0, [4, 3, 1], 2)`

Security: Median
(if one only got the first part of message, he/she needs to test for 6 * (8!/5!) * 4 * 256 * 256 * 256 = 135,291,469,824 situations(bytetools, 3 wheels) or more, 
if one only got the last part of message, he/she can **never** know the origin message)

### 3. Use `Enigma`
This is the basest way(the third floor way) to call Enigma.
Here is the example(a little long... ...):
```python
# In file
from engima import *
swap = Swap({172: 5, 80: 29, 203: 69, 90: 254, 147: 42, 135: 188, 191: 190, 95: 113, 173: 94, 127: 70, 72: 24,
                  124: 43, 50: 159, 174: 193, 33: 241, 36: 225, 245: 38, 179: 46, 243: 208, 6: 79, 73: 196, 164: 178,
                  75: 184, 121: 125, 248: 211, 7: 19, 242: 132, 63: 13, 71: 34, 31: 52, 11: 163, 228: 212, 170: 157,
                  166: 152, 154: 223, 109: 76, 136: 88, 53: 110, 215: 139, 92: 96, 111: 64, 91: 57, 102: 227,
                  141: 236, 26: 182, 48: 56, 209: 255, 155: 114, 137: 129, 180: 216, 153: 226, 161: 247, 14: 244,
                  158: 30, 221: 103, 86: 18, 186: 104, 160: 74, 149: 77, 222: 84, 162: 251, 128: 177, 122: 197, 4: 89,
                  205: 16, 183: 0, 120: 224, 130: 169, 3: 100, 51: 176, 68: 143, 252: 134, 175: 81, 119: 21, 146: 250,
                  231: 116, 246: 230, 171: 23, 234: 207, 189: 194, 201: 40, 151: 232, 8: 58, 150: 198, 133: 67,
                  105: 28, 148: 131, 10: 217, 229: 210, 44: 253, 112: 239, 59: 15, 82: 118, 106: 32, 49: 218,
                  185: 144, 85: 168, 199: 165, 87: 187, 200: 61})
wheel1 = Wheel(*[84, 1, 91, 215, 15, 184, 247, 227, 78, 97, 21, 32, 238, 63, 40, 195, 19, 157, 132, 139, 66, 103,
                     153, 148, 73, 93, 158, 88, 160, 133, 232, 228, 54, 229, 58, 142, 190, 94, 193, 113, 109, 22, 36,
                     38, 147, 246, 131, 134, 124, 77, 27, 56, 51, 152, 236, 41, 127, 11, 213, 107, 176, 74, 96, 248,
                     144, 178, 224, 130, 202, 102, 233, 163, 254, 135, 204, 217, 199, 46, 185, 211, 168, 243, 146, 2,
                     170, 100, 60, 82, 50, 155, 140, 169, 129, 28, 175, 219, 122, 72, 12, 31, 138, 182, 39, 174, 214,
                     189, 210, 200, 111, 117, 64, 241, 95, 154, 85, 249, 68, 198, 71, 119, 44, 201, 205, 69, 250, 45,
                     35, 180, 137, 25, 26, 10, 208, 151, 33, 42, 164, 3, 186, 197, 251, 242, 150, 105, 62, 70, 4, 61,
                     223, 173, 92, 203, 115, 79, 30, 90, 49, 34, 234, 179, 81, 191, 141, 9, 172, 13, 162, 112, 226,
                     187, 165, 5, 83, 220, 6, 166, 167, 194, 188, 145, 16, 114, 171, 192, 76, 55, 106, 29, 235, 206, 99,
                     67, 65, 37, 244, 183, 125, 118, 121, 7, 47, 216, 231, 43, 222, 123, 221, 104, 161, 86, 156, 57,
                     126, 17, 59, 128, 240, 116, 23, 237, 143, 110, 108, 196, 245, 159, 177, 53, 14, 207, 87, 80, 218,
                     255, 52, 75, 181, 253, 225, 120, 230, 149, 98, 252, 48, 239, 8, 20, 0, 89, 18, 101, 212, 209, 136,
                     24])
wheel2 = Wheel(*[13, 15, 111, 235, 27, 5, 224, 132, 176, 73, 206, 37, 238, 70, 214, 63, 61, 102, 55, 253, 79, 249,
                     24, 157, 33, 131, 82, 74, 231, 194, 11, 90, 106, 200, 126, 9, 17, 164, 109, 39, 154, 122, 244, 168,
                     97, 133, 188, 161, 248, 180, 228, 89, 252, 40, 158, 104, 151, 85, 32, 29, 216, 118, 31, 183, 254,
                     56, 186, 83, 167, 110, 236, 246, 202, 155, 92, 26, 192, 116, 129, 225, 94, 175, 134, 71, 43, 205,
                     88, 67, 77, 1, 4, 36, 187, 12, 179, 49, 251, 103, 197, 210, 182, 80, 119, 127, 46, 115, 242, 178,
                     69, 99, 47, 59, 150, 245, 215, 219, 135, 152, 44, 137, 8, 193, 66, 233, 196, 140, 147, 156, 247,
                     232, 3, 153, 184, 100, 162, 117, 250, 138, 72, 148, 204, 229, 226, 130, 124, 207, 76, 10, 237, 30,
                     149, 174, 93, 243, 60, 230, 41, 50, 20, 84, 217, 208, 163, 14, 75, 223, 57, 0, 51, 125, 121, 201,
                     142, 203, 195, 45, 171, 190, 6, 98, 16, 101, 199, 185, 172, 96, 212, 65, 38, 241, 53, 239, 220,
                     189, 62, 141, 19, 54, 87, 42, 95, 123, 240, 68, 78, 2, 181, 114, 81, 23, 64, 159, 144, 227, 255,
                     52, 105, 7, 234, 58, 120, 35, 177, 112, 160, 136, 48, 221, 107, 25, 113, 170, 34, 213, 139, 166,
                     209, 191, 108, 198, 143, 91, 86, 146, 18, 128, 28, 222, 145, 21, 211, 22, 165, 169, 173, 218])
reflect = Reflect({226: 138, 87: 57, 193: 52, 164: 48, 235: 40, 51: 155, 39: 8, 115: 135, 182: 33, 130: 111, 35: 234,
                   2: 23, 73: 118, 20: 6, 239: 122, 169: 41, 222: 0, 161: 160, 82: 119, 112: 21, 86: 116, 232: 85,
                   180: 252, 200: 104, 158: 80, 100: 171, 149: 61, 221: 62, 241: 202, 14: 243, 29: 230, 189: 210,
                   99: 120, 205: 75, 166: 92, 67: 50, 129: 37, 195: 145, 247: 27, 101: 44, 9: 237, 134: 209, 45: 94,
                   192: 69, 117: 218, 181: 190, 227: 201, 159: 103, 139: 162, 167: 142, 93: 38, 76: 60, 98: 253,
                   22: 25, 24: 125, 246: 15, 96: 240, 198: 132, 19: 191, 223: 137, 78: 178, 31: 10, 11: 126, 77: 74,
                   26: 197, 151: 128, 176: 242, 152: 13, 212: 88, 81: 123, 146: 131, 213: 1, 144: 207, 170: 203,
                   199: 28, 255: 59, 168: 64, 107: 157, 106: 124, 183: 109, 70: 254, 34: 84, 184: 196, 174: 133,
                   110: 250, 156: 249, 18: 105, 163: 79, 65: 90, 194: 5, 211: 153, 208: 206, 177: 150, 238: 236,
                   231: 63, 7: 46, 56: 42, 16: 97, 72: 89, 141: 228, 220: 186, 136: 219, 108: 55, 127: 53, 188: 148,
                   68: 215, 165: 175, 113: 140, 4: 143, 83: 121, 58: 185, 173: 12, 233: 66, 49: 217, 32: 3, 229: 54,
                   147: 214, 30: 91, 154: 114, 71: 245, 179: 36, 251: 102, 204: 244, 95: 225, 47: 224, 187: 172,
                   17: 43, 248: 216})


class Translate:
    @staticmethod
    def tran(letters, *args, **configs):
        if type(letters) == bytes:
            return list(letters)
        b = letters.encode('utf-8')
        return list(b)

    @staticmethod
    def r_tran(letters, *args, **configs):
        b = b''.join([bytes.fromhex(hex(le)[2:].rjust(2, "0")) for le in letters])
        if 'return char' in args \
                or ('return' in configs and configs['return'] == 'char') \
                or 'decrypt' in args \
                or ('type' in configs and configs['type'] == 'decrypt'):
            return b.decode('utf-8')
        return b
    
    
e = Enigma(swap, (wheel1, wheel2), reflect)
# Or Call like this
# e = Enigma()
# e.swap(swap)
# e.wheels(wheel1, wheel2)
# e.reflect(reflect)
e.translate(Translate)
# P.S. the code above equals to 'e = bytetools.make_up(0, [0, 1], 0)'
e.set_position(20, 89)
b = e.encipher("Hello world! Hello pi!你好世界，你好圆周率!")
print(repr(b))
e.set_position(20, 89)
c = e.encipher(b, 'return char')
print('='*80)
print(c)
```
```
# Output here
b'7\xf9\x9f@\xc8LS\x0f\xc5\xa2I\x0e\xc7wPM\x00\xe4\xb5\x82\xd8\x08\xef\x1c.<\x92UN\xd9\xe7\x1aP\xe7\xbd\x13k<\x85\xd9.A\x95,Y\xb49b\xbe|qv\xee'
================================================================================
Hello world! Hello pi!你好世界，你好圆周率!
```
After a very, very long example, it's clear that this way is freest but **cumbersome**.

As the example here, I wouldn't like to explain this long, long codes because ~~I'm lazy~~ it's quiet easy.

Security: Can't compare with others.
