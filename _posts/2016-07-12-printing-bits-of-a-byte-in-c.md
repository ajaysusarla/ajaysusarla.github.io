---
layout: post
title: Printing bits of a byte in C
date: 2016-07-12 13:37:52.000000000 +10:00
type: post
published: true
status: publish
categories: []
tags:
- c
- linux
- programming
meta:
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '24712996544'
author:
  login: ajaysusarla
  email: ajaysusarla@gmail.com
  display_name: partha
  first_name: ''
  last_name: ''
---
Printing the bit representation of a byte is a very simple and
interesting problem. There are numerous ways this can be done in many
different programming languages out there. In C, one would do
something like this for signed integers:
```
void print_bits(int num)
{
        size_t bits = sizeof(num) * 8;
        int max = 1 << (bits - 1);
        int i;

        for (i = 0; i < bits; i++) {
                printf("%u", (num & max) ? 1 : 0);
                num = num << 1;
        }

        printf("\n");
}
```
What exactly is this function doing?
Most of the code is pretty obvious. The key portions of the code are in lines 3 and 9.
Line 3, calculates the maximum possible positive value for the given
type. In the example above, the given type is an `int`, and on my
machine running _GNU/Linux(x86\_64)_, the number of bits in an `int`
is `32` and its maximum possible positive value is 2147483648.

The binary representation(shown in 4 chunks of 8 bits) of *1* on a 32 bit machine is:
`00000000 00000000 00000000 00000001`

which when left-shifted 31 times(line 3 in the above code),
becomes(notice how the digit *1* has moved from being the LSB to being
the MSB):

`10000000 00000000 00000000 00000000`

whose decimal value is *2147483648*.

Having got the *max* value, we then bitwise-and(*&*) it with the give
number(represented by *num*) and check the result(line 8). The
bitwise-and will yield a number greater than *0*, if there is a *1* in
the MSB and will yield a *0* otherwise. Based on the result of this
operation, we either print a *1* or a *0*.
In each iteration, we left-shift the given number by 1 bit(line 9). We
continue to iterate until we've traversed all bits. 
We can modify the function `print_bits()` to print the output in a
much more readable fashion:
```
#include <stdint.h>
#include <limits.h>

#define numbits(x)  (sizeof(x) * 8)

#define max_signed_value_of_type(t)                             \
        (INTMAX_MAX >> (numbits(intmax_t) - numbits(t)))

void print_bits(int num)
{
        size_t bits = numbits(num);
        int max = max_signed_value_of_type(num) + 1;
        int i;

        for (i = 0; i < bits; i++) {
                if (i && (i % 8) == 0)
                        printf(" ");
                printf("%u", !!(num & max));
                num = num << 1;
        }

        printf("\n");
}
```
We can also implement a function to count the number of *1's* and
*0's* in the binary representation of a given number:

```
#include <stdint.h>
#include <limits.h>

#define numbits(x)  (sizeof(x) * 8)

#define max_signed_value_of_type(t)                             \
        (INTMAX_MAX >> (numbits(intmax_t) - numbits(t)))

void count_bits(unsigned int num)
{
        size_t bits = numbits(num);
        unsigned int max = max_signed_value_of_type(num) + 1;
        int i;
        unsigned int t = num;
        int zeroes = 0, ones = 0;

        for (i = 0; i < bits; i++) {
                if (!!(num & max))
                        ones++;
                else
                        zeroes++;

                num = num << 1;
        }

        printf("%u has %d 0's and %d 1's in its binary representation.\n", t, zeroes, ones);
}
```

The `count_bits()` function above for input number 2 which in binary
is `00000000 00000000 00000000 00000010` we get an output:

`2 has 31 0's and 1 1's in its binary representation.`

This post is a result of a discussion with my nephew who recently took
to programming and I was trying to explain bits and bytes to him.
Although C is probably not the right language when introducing a
high-schooler to programming, I thought it is the language that
expresses bits and bytes well, and also, it is the language that I
know the best.
