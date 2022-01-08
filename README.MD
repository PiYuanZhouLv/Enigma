# Enigma
This is the Python version of a machine called Enigma which was widely used in World War II.

    Would you like to use Enigma?
    Now, I provide one for you.
    But you have to assemble it by youself.
    So, please read the usage below carefully!

## Usage
There is an example:
```
>>>send_message("Hello word! Hello pi!你好世界，你好圆周率!", '')
Message object and its MD5 value have been saved into file message.pickle and message.md5
Please send this two file above to the receiver.
================================================================================
*Private* Information Below, please keep it a secret and send it to the receiver.
Swap: 8
Wheels: [9, 1, 0, 4, 6, 8, 3, 2]
Reflect: 0
Start position: (125, 133, 16, 210, 107, 67, 251, 223)
Code:  (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223))
>>>receive_message('', (8, [9, 1, 0, 4, 6, 8, 3, 2], 0, (125, 133, 16, 210, 107, 67, 251, 223)))
====================================Message=====================================
Hello word! Hello pi!你好世界，你好圆周率!
```
P.S. File message.pickle and message.md5 is also in the folder to test for you.

_TODO_