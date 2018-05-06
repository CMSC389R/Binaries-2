# Binaries-2

## Part 1


__You are given two programs written in C. Your task is to reverse engineer both of them to figure out what they are doing, and find the flags in each of them. The flag is in the format 389R-{flag_text_goes_here}.__


I started with the file called ```onebyone``` and the first thing I did was to run ```xxd onebyone``` to see if I could identify anything malformed or mishapen, I wasn't able to identify any major problems. 

![alt text](https://github.com/yreiss1/Binaries-2/blob/master/onebyone-xxd.png)

The second command I used was to see if I could find the flag using ```strings onebyone | grep "FLAG-{"``` but this did not succeed as well.

![alt text](https://github.com/yreiss1/Binaries-2/blob/master/onebyoone-strings.png)

My third approach involved using ```radare2``` in order to reverse engineer the executable by using ```radare2 onebyone```. I was then provided with the radare2 prompt, to which I intered ```aaa``` in order to analyze everything, and then ```s main``` to change the address to that of the main function, and then dissasembled using ```pdf``` to get the assembly:

To enter graph mode I entered ```VV```, and was presented with this graph of the dissassembled executable:

![alt text](https://github.com/yreiss1/Binaries-2/blob/master/onebyone-graph.png)

Running down the graph I concatenated all the characters that was bieng compared and got this string:

```0ec3}-9n8iR{```

Seem like an anagram of the flag, then when I went back to try to figure out what I was missing, I realized the eax register was bieng moved around by some offset each time it was making a comparison, meaning it was comparing the string in a weird order:

```
Char | offset
-------------------
   0 | 7
   e | 10
   c | 9
   3 | 0
   } | 11
   - | 4
   9 | 2
   n | 6
   8 | 1
   i | 8
   R | 3
   { | 5
```

After sorting the characters by the ordering of the offset I recieved the flag: ```389R-{n0ice}```

In order to confirm that it was the correct flag I changed the permissions of file ```onebyone``` by using ```chmod 777 onebyone``` and ran ```./onebyone 389R-{n0ice}```:


![alt text](https://github.com/yreiss1/Binaries-2/blob/master/onebyone-final.png)


In order to understand what ```stackexhange``` is doing I skipped the previous steps that I took with ```onebyone``` where I used ```xxd``` and ```strings``` and went straight to ```radare2```. Using the command ```r2 stackexchange``` I opened the radare2 program, and used ```aa``` to analyse all, and used ```s main``` in order to reach the main code and used ```pdf``` to disassemble it. I then used ```VV``` to enter visual mode, and was given this screen:

![alt text](https://github.com/yreiss1/Binaries-2/blob/master/stackexchange.png) 

By tracing the operations the commands in the screenshot above by hand I was able to understand what the program was doing and decipher the flag!

```
After initializing all the registerss with 0:

register | hex value
--------------------
local_1h | 0
local_2h | 0
local_3h | 0
local_4h | 0
local_5h | 0
local_6h | 0
local_7h | 0
local_8h | 0
local_9h | 0

After the first round of XOR operations:

register | hex value
--------------------
local_1h | 0
local_2h | bd
local_3h | ff
local_4h | 5f
local_5h | 87
local_6h | 6d
local_7h | dd
local_8h | 7d
local_9h | e8

After final round of XOR operations: 

register | hex value | Ascii Character
--------------------------------------
local_1h | 7d        | }
local_2h | 65        | e
local_3h | 79        | y
local_4h | 7b        | {
local_5h | 2d        | -
local_6h | 52        | R
local_7h | 39        | 9
local_8h | 38        | 8
local_9h | 33        | 3
```

I dont know about you, but if you were to read that in descending order that looks like alot like a flag, which I assume it is: ```389R-{ye}```

## Part 2

__You are trying to access a restricted resource on the Briong server, which uses a vulnerable password checker. Luckily, you have someone on the inside that has leaked the source code for the checker. Scan through the code to see if you can break in.__

For part 2, it was easy enough to look over the code in ```outofbounds.c``` to find that the buffer size was 21 and the read size was 22.

![alt text](https://github.com/yreiss1/Binaries-2/blob/master/outofbounds.png)

```
#define BUFFER_SIZE 21
#define READ_SIZE 22
```

Another important detail I noticed while scanning the c code was this:

```
char buffer[BUFFER_SIZE];
unsigned char valid = 0;
```

Meaning that if we overflow the buffer array, we could possibly change```valid``` because they are declared one after the other. And that is exactly what I did... by inserting anything greater than 21 characters, I triggered a buffer overflow that altered the ```valid``` value and provided me with the flag!

```
if (valid)
    {
      printf("Authenticated: %s\n", flag);
      return 0;
    }
```

Flag: ```CMSC389R-{wat_r_u_doing}```

![alt text](https://github.com/yreiss1/Binaries-2/blob/master/Part2.png)
