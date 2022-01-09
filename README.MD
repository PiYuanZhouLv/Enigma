# Enigma
This is the Python version of a machine called Enigma which was widely used in World War II.

    Would you like to use Enigma?
    Now, I provide one for you.
    But you have to assemble it by youself.
    So, please read the usage below carefully!
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
```
>>> from enigma import *
>>> send_message("Hello word! Hello pi!你好世界，你好圆周率!", '')
Message object and its MD5 value have been saved into file message.pickle and message.md5
Please send this two file above to the receiver.
================================================================================
*Private* Information Below, please keep it a secret and send it to the receiver.
Swap: 8
Wheels: [9, 1, 0, 4, 6, 8, 3, 2]
Reflect: 0
Start position: (125, 133, 16, 210, 107, 67, 251, 223)
Code:  (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223))
>>> receive_message('', (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223)))
====================================Message=====================================
Hello word! Hello pi!你好世界，你好圆周率!
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
```
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
