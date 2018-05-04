# Binaries-2

## Part 1


__You are given two programs written in C. Your task is to reverse engineer both of them to figure out what they are doing, and find the flags in each of them. The flag is in the format 389R-{flag_text_goes_here}.__


I started with the file called ```onebyone``` and the first thing I did was to run ```xxd onebyone``` to see if I could identify anything malformed or mishapen, I wasn't able to identify any major problems. 

![alt text]()

The second command I used was to see if I could find the flag using ```strings onebyone | grep "FLAG-{"``` but this did not succeed as well.

![alt text]()

My third approach involved using ```radare2``` in order to reverse engineer the executable by using ```radare2 onebyone```. I was then provided with the radare2 prompt, to which I intered ```aaa``` in order to analyze everything, and then ```s main``` to change the address to that of the main function, and then dissasembled using ```pdf``` to get the assembly:

![alt text]()

To enter graph mode I entered ```VV```, and was presented with this graph of the dissassembled executable:

![alt text]()

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


![alt text]()




## Part 2
