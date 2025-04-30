---
title: "Implementing strlen() in x86 Assembly"
description: "Detailed walkthrough on implementing strlen() in 32-bit x86 Assembly Language."
date: 2025-04-30 14:20:00 +0000
tags: [Malware Development, 32-bit x86, Reverse Engineering, Assembly Language, C++, Cybersecurity tutorial, Assembly Tutorial]
categories: [Cybersecurity, Malware Analysis, Reverse Engineering]
image: /assets/images/post-30-04-25/banner.jpg
permalink: /posts/implementing-strlen-in-x86-assembly/
---

## Introduction

Welcome to another blog post. As part of my Malware Development learning journey, I am learning Assembly Language with the aim of writing more optimized shellcode for my proof-of-concepts which would give me better chances of bypassing AV/EDR solutions. I am also looking to gain some knowledge on Reverse Engineering, and having a good grasp of assembly language is very vital. Let's dive in!

## What is strlen()?

Let's begin with first understanding the function we are going to implement. `strlen` is a fundamental and frequently used function in the C programming language. It's primary purpose is to determine the length of a null-terminated string.
In C, strings are typically represented as arrays of characters, and they are terminated by a null character (\0). `strlen` counts the number of characters in the string before it encounters this null terminator.
Its prototype is as follows:

```c
size_t strlen(const char *string);
```

- `size_t`: This is an unsigned integer type. It's designed to hold the size of objects in memory.
- `const char *string`: This is the parameter that accepted by the `strlen` function. It's a pointer to a constant character array. The const keyword indicates that the function will not modify the string pointed to by string.

The `strlen` function works by iterating through the characters of the string pointed to by `string`, one by one until it reaches the null terminator (\0). It maintains a counter, which is incremented for each character it encounters before the null terminator. Once the null terminator is found, the function returns the value of this counter. It is important to note that the null terminator itself is not included in the count.

## The Code

Below is the code of how I implemented it. I designed it to mimic the `strlen` function exactly, returning the length in `EAX`. To implement `strlen` I needed to:

- Get access to a pointer to the string
- Initialize  a counter to 0
- Loop through each character in the string
- Compare each character in the string with "\0". If it's not "\0" increment the counter, if it is, exit the loop.
- Return the counter

```asm
;strlen.asm
;x86 implementation of C's strlen() function

section .text
    global _strlen

_strlen:
    push ebp            ;save value of EBP onto the stack
    mov ebp, esp        ;set the stack pointer as the new base pointer
    xor ecx, ecx        ;zero out ECX
    mov esi, [ebp + 8]  ;pointer to the null terminated string

_loop:
    cld                 ;set DF to 0 so the string is processed from
                        ;start to end and not in reverse
    lodsb               ;loads the first byte of the string from ESI
    cmp al, 0           ;compare the loaded byte with 0 (null terminator)
    jne _is_zero        ;jump to label _is_zero is loaded byte is 0
    inc ecx             ;increment ECX if loaded byte is not 0
    jmp _loop           ;repeat till loaded byte is 0

_is_zero:
    pop ebp             ;restore the original value of EBP
    ret                 ;return. Length of string = EAX
```

I have added in the comments to explain exactly what each line does. I hope you find it helpful.

## Testing the Code

To test the asm code, we first need to write a C program `main.c` that calls it:

```c
#include <stdio.h>

// Declare the external strlen function
extern int _strlen(const char *string);

int main() {
    const char *s = "m00ndie";
    size_t len = _strlen(s);
    printf("Length of '%s': %zu\n", s, len); // outputs 7
    return 0;
}
```

Assemble the code using the following command:

```bash
nasm -f elf32 strlen.asm -o strlen_test.o
```

Now we need to compile the C code and link it with the object file we created from your assembly code `strlen_test.o`.

```bash
gcc -m32 main.c strlen_test.o -o test
```

To run the code:

```bash
./test
# Output: Length of 'm00ndie': 7
```

With the code out of the way, let's breakdown how exactly I implemented it.

## Conclusion

Implementing strlen() is more than just a flex, it’s a stepping stone for bigger goals in my Malware Development journey. Taking on this challenge has really built up my muscles in x86. I hope you found this post helpful and you were able to learn a thing a two.

Next, I am going to implement strchr(), another C function in x86 :-). If you’re coding x86, reversing binaries,building malware PoCs, or if you've got tips for x86 shellcode or RE hit me up on X(@_m00ndie).
