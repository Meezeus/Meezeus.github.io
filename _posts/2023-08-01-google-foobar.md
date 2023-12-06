---
layout: post
title: "Google's Foobar Challenge"
subtitle: A series of programming challenges created by Google to find potential new hires
thumbnail-img: /assets/img/google-foobar/foobar.jpg
gh-repo: Meezeus/google-foobar
gh-badge: [code]
---

**Google's foobar challenge** is a series of programming challenges created by
**Google** in order to find potential new hires. To take part, you must be invited
through one of two ways: Googling certain programming-related keywords to
trigger an invite, or receiving an invite from someone who has already taken
part - each participant can invite up to 2 other people depending on their
progress in the challenge. In my case, I was invited by a friend - thanks Mike!

![]({{site.url}}/assets/img/google-foobar/foobar-login.jpg){: .mx-auto.d-block :}

The challenge is split into 5 levels. Each level has a number of challenges that
must be completed in order to progress to the next level:
* **Level 1**: 1 challenge
* **Level 2**: 2 challenges
* **Level 3**: 3 challenges
* **Level 4**: 2 challenges
* **Level 5**: 1 challenge

The difficulty of the challenges increases with the level. The challenges must
be completed within a given time-frame, ranging from a week to a month depending
on the level. To complete a challenge, you must submit Python (or Java) code
that will pass a number of test cases within time constraints. To make things
more interesting, most of the test cases are hidden.

Below I will describe each challenge I received (there are many more challenges,
but each participant is given a random subset for each level) and give a brief
outline of how I solved it.

## Table of Contents
{:.no_toc}

* This will become a table of contents (this text will be scrapped).
{:toc}

## Level 1

### re-id

> There's some unrest in the minion ranks: minions with ID numbers like "1",
"42", and other "good" numbers have been lording it over the poor minions who
are stuck with more boring IDs. To quell the unrest, Commander Lambda has tasked
you with reassigning everyone new, random IDs based on her Completely Foolproof
Scheme.

> She's concatenated the prime numbers in a single long string:
"2357111317192329...". Now every minion must draw a number from a hat. That
number is the starting index in that string of primes, and the minion's new ID
number will be the next five digits in the string. So if a minion draws "3",
their ID number will be "71113".

> Help the Commander assign these IDs by writing a function solution(n) which
takes in the starting index n of Lambda's string of all primes, and returns the
next five digits in the string. Commander Lambda has a lot of minions, so the
value of n will always be between 0 and 10000.

The first - and only - challenge of level 1 was fairly easy. In order to find
the necessary prime numbers, I used an algorithm known as the [Sieve of
Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes). This
algorithm iteratively finds prime numbers up to a given limit. In my case, I
didn't know what the limit would be, so I used a slightly modified version.

~~~ python
def solution(i):
    primes = []
    prime_str = ""
    current_number = 2
    while len(prime_str) < i + 5:
        # Check if current number is a multiple of any found primes.
        prime_factors = [prime for prime in primes if current_number % prime == 0]
        # If not, current number is a new prime.
        if len(prime_factors) == 0:
            primes.append(current_number)
            prime_str += str(current_number)
        current_number += 1    
    return prime_str[i:i+5]
~~~

The algorithm works by checking numbers one-by-one, starting at 2 and going up.
For each number, it checks if it's a multiple of any previously discovered
primes. If this is the case, then the number is not a prime; if this is not the
case, then the number is a prime. The number can then be added to the list of
discovered primes and concatenated to the end of the prime string.

Once the prime string is sufficiently long, the program returns the appropriate
substring and voilà, the first challenge is successfully solved!

[Jump to ToC](#table-of-contents)

## Level 2

### hey-i-already-did-that

> Commander Lambda uses an automated algorithm to assign minions randomly to
tasks, in order to keep her minions on their toes. But you've noticed a flaw in
the algorithm - it eventually loops back on itself, so that instead of assigning
new minions as it iterates, it gets stuck in a cycle of values so that the same
minions end up doing the same tasks over and over again. You think proving this
to Commander Lambda will help you make a case for your next promotion.

> You have worked out that the algorithm has the following process: 
1. Start with a random minion ID n, which is a nonnegative integer of length k
   in base b
2. Define x and y as integers of length k.  x has the digits of n in descending
   order, and y has the digits of n in ascending order
3. Define z = x - y.  Add leading zeros to z to maintain length k if necessary
4. Assign n = z to get the next minion ID, and go back to step 2

> For example, given minion ID n = 1211, k = 4, b = 10, then x = 2111, y = 1112
and z = 2111 - 1112 = 0999. Then the next minion ID will be n = 0999 and the
algorithm iterates again: x = 9990, y = 0999 and z = 9990 - 0999 = 8991, and so
on.

> Depending on the values of n, k (derived from n), and b, at some point the
algorithm reaches a cycle, such as by reaching a constant value. For example,
starting with n = 210022, k = 6, b = 3, the algorithm will reach the cycle of
values [210111, 122221, 102212] and it will stay in this cycle no matter how
many times it continues iterating. Starting with n = 1211, the routine will
reach the integer 6174, and since 7641 - 1467 is 6174, it will stay as that
value no matter how many times it iterates.

> Given a minion ID as a string n representing a nonnegative integer of length k
in base b, where 2 <= k <= 9 and 2 <= b <= 10, write a function solution(n, b)
which returns the length of the ending cycle of the algorithm above starting
with n. For instance, in the example above, solution(210022, 3) would return 3,
since iterating on 102212 would return to 210111 when done in base 3. If the
algorithm reaches a constant, such as 0, then the length is 1.

The first challenge of level 2 was also relatively easy, despite the long
challenge description. To solve it, I wrote two functions. The first one
calculates the next value of *n* (the minion ID), using the rules specified. The
only complexity here was converting numbers between bases, but even that was not
too difficult ultimately.

~~~ python
def calculate_next_n(n, b):    
    k = len(n)

    digits = [int(d) for d in n]
    x = sorted(digits, reverse=True)
    y = sorted(digits)

    num_x = 0
    num_y = 0
    for i in range(k):
        num_x += (b**i) * x[k-i-1]
        num_y += (b**i) * y[k-i-1]

    num_z = num_x - num_y
    z = []
    for i in range(k-1, -1, -1):
        quotient = int(num_z / (b**i))
        z.append(str(quotient))
        num_z -= (b**i) * quotient

    new_n = "".join(z)
    return new_n
~~~

The second function is the one that actually solves the problem. This too was
not overly complex. All of the *n*'s (minions IDs) that appear in the sequence
are stored in a list. When a new *n* is generated (using the previous function),
the list is checked to see if that value of *n* has already appeared, indicating
a cycle. The cycle can then be found by taking a slice of the *n* list and it's
length can be determined.

~~~ python
def solution(n, b):
    cycle = False
    n_list = [n]
    while not cycle:
        next_n = calculate_next_n(n, b)
        if next_n in n_list:
            cycle = True
        n_list.append(next_n)
        n = next_n

    cycle_list = n_list[n_list.index(n):]
    cycle_length = len(cycle_list)-1
    return(cycle_length)
~~~

[Jump to ToC](#table-of-contents)

### power-hungry

> Commander Lambda's space station is HUGE. And huge space stations take a LOT
of power. Huge space stations with doomsday devices take even more power. To
help meet the station's power needs, Commander Lambda has installed solar panels
on the station's outer surface. But the station sits in the middle of a quasar
quantum flux field, which wreaks havoc on the solar panels. You and your team of
henchmen has been assigned to repair the solar panels, but you can't take them
all down at once without shutting down the space station (and all those pesky
life support systems!).

> You need to figure out which sets of panels in any given array you can take
offline to repair while still maintaining the maximum amount of power output per
array, and to do THAT, you'll first need to figure out what the maximum output
of each array actually is. Write a function solution(xs) that takes a list of
integers representing the power output levels of each panel in an array, and
returns the maximum product of some non-empty subset of those numbers. So for
example, if an array contained panels with power output levels of [2, -3, 1, 0,
-5], then the maximum product would be found by taking the subset: xs[0] = 2,
xs[1] = -3, xs[4] = -5, giving the product 2*(-3)*(-5) = 30. So
answer([2,-3,1,0,-5]) will be "30".

> Each array of solar panels contains at least 1 and no more than 50 panels, and
each panel will have a power output level whose absolute value is no greater
than 1000 (some panels are malfunctioning so badly that they're draining energy,
but you know a trick with the panels' wave stabilizer that lets you combine two
negative-output panels to produce the positive output of the multiple of their
power values). The final products may be very large, so give the answer as a
string representation of the number.

The second challenge of level 2 proved no more difficult than the first. My
solution starts by checking the length of the power level list: if the list
contains only one element, then the maximum power output will be the power level
of that element, regardless of what it is. Otherwise, the program iterates over
the elements of the list, calculating their combined product. Note that 0's are
ignored, since they would make the product 0 as well if included. The program
also keeps track of the number of negative and positive numbers, and the
smallest negative number found; this will be useful later on.

~~~ python
def solution(xs):
    if len(xs) == 1:
        return str(xs[0])
    
    product = 1
    smallest_negative = -float("inf")
    num_of_negatives = 0
    num_of_positives = 0
    for x in xs:
        if x < 0:
            product *= x
            num_of_negatives += 1
            if x > smallest_negative:
                smallest_negative = x
        elif x > 0:
            product *= x
            num_of_positives += 1

    if (num_of_negatives > 0) and (num_of_negatives % 2 != 0):
        product /= smallest_negative
    
    if num_of_positives == 0 and num_of_negatives < 2:
        product = 0
    
    return str(product)
~~~

Intuitively, we want to include as many numbers as possible if we are trying to
find the maximum product. However, we need to check the number of negative
numbers we have: otherwise we might end up with a negative product. If there is
an odd number of negative numbers, we need to discard one of them. More
specifically, we wish to discard the smallest negative number (i.e. that one
that is closest to 0), as it contributes the least to the size of the product.

There is another case that needs to be considered: what if there are no positive
numbers and less than two negative numbers (if there are 2 or more, it's no
problem since the product of 2 negatives is positive). In other words, what if
we have one negative number and one or more 0's? Since we ignored 0's when
calculating the product before, the current product would be the negative
number. But this is less preferable than having a product of 0, which would have
happened if we had included the 0's in the calculation.

[Jump to ToC](#table-of-contents)

## Level 3

### fuel-injection-perfection

> Commander Lambda has asked for your help to refine the automatic quantum
antimatter fuel injection system for her LAMBCHOP doomsday device. It's a great
chance for you to get a closer look at the LAMBCHOP - and maybe sneak in a bit
of sabotage while you're at it - so you took the job gladly.

> Quantum antimatter fuel comes in small pellets, which is convenient since the
many moving parts of the LAMBCHOP each need to be fed fuel one pellet at a time.
However, minions dump pellets in bulk into the fuel intake. You need to figure
out the most efficient way to sort and shift the pellets down to a single pellet
at a time.

> The fuel control mechanisms have three operations:
1. Add one fuel pellet
2. Remove one fuel pellet
3. Divide the entire group of fuel pellets by 2 (due to the destructive energy
   released when a quantum antimatter pellet is cut in half, the safety controls
   will only allow this to happen if there is an even number of pellets)
 
> Write a function called solution(n) which takes a positive integer as a string
and returns the minimum number of operations needed to transform the number of
pellets to 1. The fuel intake control panel can only display a number up to 309
digits long, so there won't ever be more pellets than you can express in that
many digits.

> For example:\
solution(4) returns 2: 4 -> 2 -> 1\
solution(15) returns 5: 15 -> 16 -> 8 -> 4 -> 2 -> 1

Level 3 is where things started to get interesting. It took me some time and
experimentation before I figured this one out. The key here was to consider the
number of pellets in binary notation. Dividing a binary number by 2 essentially
shifts all the bits to the right, discarding the rightmost bit (which is 0, as
the number needs to be even).

Since the goal is to get to 1 pellet in as least steps as possible, dividing by
2 is intuitively the preferred option. But what if the number is odd - is it
better to add 1 or subtract 1? My first thought was that subtraction is
preferred, since it makes the number smaller and therefore closer to 1. Of
course, it's not that simple; sometimes adding 1 turns out to be more efficient.
This is only the case when the last two bits of the number are both 1. In this
situation, adding 1 will cause a chain reaction where all consecutive rightmost
1 bits become 0 bits. This then allows for multiple (at least 2) consecutive
division operations, which as we know are the best option.

~~~ python
def solution(n):
    n = int(n)
    n_bin = bin(n)[2:]
    counter = 0
    while n != 1:
        if n % 2 == 0:
            n /= 2
        elif n == 3: # edge case where the rule below does not apply
            n -= 1
        elif n_bin[-1] == "1" and n_bin[-2] == "1":
            n += 1
        else:
            n -= 1
        n_bin = bin(n)[2:]
        counter += 1
    return counter
~~~

There is one edge case where the rule above does not hold: when the number is 3,
or 11 in binary. Although this meets the criteria for the rule, adding 1 (and
then dividing twice) is less efficient than subtracting 1 (twice).

[Jump to ToC](#table-of-contents)

### queue-to-do

> You're almost ready to make your move to destroy the LAMBCHOP doomsday device,
but the security checkpoints that guard the underlying systems of the LAMBCHOP
are going to be a problem. You were able to take one down without tripping any
alarms, which is great! Except that as Commander Lambda's assistant, you've
learned that the checkpoints are about to come under automated review, which
means that your sabotage will be discovered and your cover blown - unless you
can trick the automated review system.

> To trick the system, you'll need to write a program to return the same
security checksum that the guards would have after they would have checked all
the workers through. Fortunately, Commander Lambda's desire for efficiency won't
allow for hours-long lines, so the checkpoint guards have found ways to quicken
the pass-through rate. Instead of checking each and every worker coming through,
the guards instead go over everyone in line while noting their security IDs,
then allow the line to fill back up. Once they've done that they go over the
line again, this time leaving off the last worker. They continue doing this,
leaving off one more worker from the line each time but recording the security
IDs of those they do check, until they skip the entire line, at which point they
XOR the IDs of all the workers they noted into a checksum and then take off for
lunch. Fortunately, the workers' orderly nature causes them to always line up in
numerical order without any gaps.

> For example, if the first worker in line has ID 0 and the security checkpoint
line holds three workers, the process would look like this:\
0 1 2 /\
3 4 / 5\
6 / 7 8\
where the guards' XOR (^) checksum is 0^1^2^3^4^6 == 2.

> Likewise, if the first worker has ID 17 and the checkpoint holds four workers,
the process would look like:\
17 18 19 20 /\
21 22 23 / 24\
25 26 / 27 28\
29 / 30 31 32\
which produces the checksum 17^18^19^20^21^22^23^25^26^29 == 14.

> All worker IDs (including the first worker) are between 0 and 2000000000
inclusive, and the checkpoint line will always be at least 1 worker long.

> With this information, write a function solution(start, length) that will
cover for the missing security checkpoint by outputting the same checksum the
guards would normally submit before lunch. You have just enough time to find out
the ID of the first worker to be checked (start) and the length of the line
(length) before the automatic review occurs, so your program must generate the
proper checksum with just those two values.

This seemed like an easy problem at first: all you have to do is take the XOR of
some numbers. Unfortunately, this naive approach didn't work due to the strict
time constraints that this challenge had. I needed to make the XOR more
efficient. I achieved this through the use of two functions, each one employing
a certain "trick" to make things faster.

~~~ python
def smart_xor_range(start, end):
    start_xor = smart_xor(start - 1)
    end_xor = smart_xor(end)
    return start_xor ^ end_xor
~~~

The first function finds the XOR of a range of numbers, where the first number
is *start* and the last number is *end*. However, it doesn't simply XOR the
numbers together. Instead, it uses the following property:

      (0 ^ ... ^ start - 1) ^ (0 ^ ... ^ end)
    = (0 ^ ... ^ start - 1) ^ (0 ^ ... ^ start - 1 ^ start ^ ... ^ end)
    = start ^ ... ^ end

Now you might think that this is even more inefficient: after all it requires
finding the XOR of even more numbers! That would be the case, if not for the
fact that there's a little trick we can use, which brings us to the next
function...

~~~ python
def smart_xor(number):
    remainder = (number + 1) % 4
    if remainder == 0:
        return 0
    elif remainder == 1:
        return number
    elif remainder == 2:
        return 1
    elif remainder == 3:
        return number + 1
    else:
        print("Error @ smart_xor({})!".format(number))
~~~

The second function finds the XOR of all the numbers from 0 to *number*
(inclusive). However, it does this without doing a single XOR operation! Before
I explain how it works, let's lay down some groundwork.

* The XOR operation, when applied to two binary numbers, returns a 0 where the
bits are the same, and a 1 where the bits are different.
* An even number in binary has the last bit equal to 0.
* *2n ^ (2n + 1) = 1*, because all the bits are the same except for the last one.

Therefore, we can pair up numbers in the sequence as follows:

    0 ^ 1 (= 1)
    2 ^ 3 (= 1)
    4 ^ 5 (= 1)
    6 ^ 7 (= 1), and so on.

Each pair of pairs will cancel each other out (*1 ^ 1 = 0*). There can be four
different cases, depending on how many numbers are in the sequence (the number
of numbers is equal to *number + 1*, since we include the 0 at the start):
  1. ***(number + 1) % 4 == 0***.\
  We can successfully pair up all numbers and pair up all pairs. The result is
  0.
  2. ***(number + 1) % 4 == 1***.\
  After pairing up numbers and pairing up all pairs, there is one number left.
  The result is the number.
  3. ***(number + 1) % 4 == 2***.\
  After pairing up all numbers and pairing up pairs, there is one pair left. The
  result is 1.
  4. ***(number + 1) % 4 == 3***.\
  After pairing up numbers and pairing up pairs, there is one pair and one
  number left. The result is *1 ^ number*. However, in this case the number left
  over is always an even number (if it was odd, it would've been paired up) and
  *1 ^ 2n == 2n + 1* (it just flips the last bit from 0 to 1). Therefore the
  result is *number + 1*.

Now all the pieces of the puzzle are in place. We can very quickly find *0 ^ ...
^ number*, which allows us to quickly find *start ^ ... ^ end*. All that's left
to do is to call the functions on each line in the queue and XOR the results
together.

~~~ python
def solution(start, length):
    line_ranges = []
    cut_off = length
    while cut_off != 0:
        line_ranges.append((start, start + cut_off - 1))
        start += length
        cut_off -= 1
        
    checksum = 0
    for start, end in line_ranges:
        checksum ^= smart_xor_range(start, end)    
    return checksum
~~~

[Jump to ToC](#table-of-contents)

### bomb-baby

> You're so close to destroying the LAMBCHOP doomsday device you can taste it!
But in order to do so, you need to deploy special self-replicating bombs
designed for you by the brightest scientists on Bunny Planet. There are two
types: Mach bombs (M) and Facula bombs (F). The bombs, once released into the
LAMBCHOP's inner workings, will automatically deploy to all the strategic points
you've identified and destroy them at the same time.

> But there's a few catches. First, the bombs self-replicate via one of two
distinct processes: Every Mach bomb retrieves a sync unit from a Facula bomb;
for every Mach bomb, a Facula bomb is created; Every Facula bomb spontaneously
creates a Mach bomb.

> For example, if you had 3 Mach bombs and 2 Facula bombs, they could either
produce 3 Mach bombs and 5 Facula bombs, or 5 Mach bombs and 2 Facula bombs. The
replication process can be changed each cycle.

> Second, you need to ensure that you have exactly the right number of Mach and
Facula bombs to destroy the LAMBCHOP device. Too few, and the device might
survive. Too many, and you might overload the mass capacitors and create a
singularity at the heart of the space station - not good!

> And finally, you were only able to smuggle one of each type of bomb - one
Mach, one Facula - aboard the ship when you arrived, so that's all you have to
start with. (Thus it may be impossible to deploy the bombs to destroy the
LAMBCHOP, but that's not going to stop you from trying!)

> You need to know how many replication cycles (generations) it will take to
generate the correct amount of bombs to destroy the LAMBCHOP. Write a function
solution(M, F) where M and F are the number of Mach and Facula bombs needed.
Return the fewest number of generations (as a string) that need to pass before
you'll have the exact number of bombs necessary to destroy the LAMBCHOP, or the
string "impossible" if this can't be done! M and F will be string
representations of positive integers no larger than 10^50. For example, if M =
"2" and F = "1", one generation would need to pass, so the solution would be
"1". However, if M = "2" and F = "4", it would not be possible.

This challenge was the opposite of the previous one: it seemed difficult at
first, but ended up being rather easy. The key was to look at the problem from a
different perspective: working backwards instead of working forwards. This was
something I learnt from doing other coding challenges in the past. Sometimes, it
is much easier to work your way backwards, instead of working your way forwards.

This was one such case. When starting with 1 Mach bomb and 1 Facula bomb, you
have two options available to you at each step (the two replication processes).
It is not clear which one you should choose. However, if you start at the
required number of bombs, M and F, and work backwards, there is only one option
available. One of the numbers will always be bigger than the other (it is
impossible for the numbers to be the same except for right at the start). Since
you cannot have a negative number of bombs, the only option is to subtract the
smaller number from the bigger number. Repeat this process and you will either
end up with 1 of each type of bomb (a success), or one of the bomb types will be
<= 0, which indicates that the desired number of bombs is impossible to achieve.

~~~ python
def solution(x, y):
    x = int(x)
    y = int(y)
    if (x % 2) == 0 and (y % 2) == 0:
       return "impossible"
    if (x == y) and (x != 1) and (y != 1):
       return "impossible"
    
    counter = 0
    while x >= 1 and y >= 1:
        if x == 1 and y == 1:
            return str(counter)
        elif x == 1:
            counter += y - 1
            y = 1
        elif y == 1:
            counter += x - 1
            x = 1
        elif x > y:
            quotient = int(x / y)
            counter += quotient
            x -= y * quotient
        elif y > x:
            quotient = int(y / x)
            counter += quotient
            y -= x * quotient
    return "impossible"
~~~

My solution uses a couple of tricks to speed things up - don't worry, they're
not as complicated as the tricks used in the previous challenge!

First of all, I noticed that it's impossible to have both bomb types be even
numbers. You start with two odd numbers, which add together to make an even
number. An even number + an odd number will result in an odd number. So you can
never end up with two even numbers.

As mentioned previously, the desired number of bombs of each type cannot be the
same (except for the start). For this to be true, one of the bomb types would
need to be 0, which is impossible.

If the number of bombs of one of the types is 1, all that remains to be done is
to continuously subtract 1 from the other bomb type. This will happen until the
other bomb type is also 1. The program does all of this in one go, modifying the
value of the counter and the number of bombs accordingly.

When subtracting one bomb type from the other, it might be possible to do
multiple consecutive subtractions. For example, *(11, 3) --> (8, 3) --> (5,
3) --> (2, 3)*. This is also done in one go, with all values updated
accordingly. Note that this situation is distinct from the one described above,
as the code would not work correctly if one of the bomb types is 1.

[Jump to ToC](#table-of-contents)

## Level 4

### distract-the-trainers

> The time for the mass escape has come, and you need to distract the guards so
that the bunny prisoners can make it out! Unfortunately for you, they're
watching the bunnies closely. Fortunately, this means they haven't realized yet
that the space station is about to explode due to the destruction of the
LAMBCHOP doomsday device. Also fortunately, all that time you spent working as
first a minion and then a henchman means that you know the guards are fond of
bananas. And gambling. And thumb wrestling.

> The guards, being bored, readily accept your suggestion to play the Banana
Games.

> You will set up simultaneous thumb wrestling matches. In each match, two
guards will pair off to thumb wrestle. The guard with fewer bananas will bet all
their bananas, and the other guard will match the bet. The winner will receive
all of the bet bananas. You don't pair off guards with the same number of
bananas (you will see why, shortly). You know enough guard psychology to know
that the one who has more bananas always gets over-confident and loses. Once a
match begins, the pair of guards will continue to thumb wrestle and exchange
bananas, until both of them have the same number of bananas. Once that happens,
both of them will lose interest and go back to guarding the prisoners, and you
don't want THAT to happen!

> For example, if the two guards that were paired started with 3 and 5 bananas,
after the first round of thumb wrestling they will have 6 and 2 (the one with 3
bananas wins and gets 3 bananas from the loser). After the second round, they
will have 4 and 4 (the one with 6 bananas loses 2 bananas). At that point they
stop and get back to guarding.

> How is all this useful to distract the guards? Notice that if the guards had
started with 1 and 4 bananas, then they keep thumb wrestling! 1, 4 -> 2, 3 -> 4,
1 -> 3, 2 -> 1, 4 and so on.

> Now your plan is clear. You must pair up the guards in such a way that the
maximum number of guards go into an infinite thumb wrestling loop!

> Write a function solution(banana_list) which, given a list of positive
integers depicting the amount of bananas the each guard starts with, returns the
fewest possible number of guards that will be left to watch the prisoners.
Element i of the list will be the number of bananas that guard i (counting from
0) starts with.

> The number of guards will be at least 1 and not more than 100, and the number
of bananas each guard starts with will be a positive integer no more than
1073741823 (i.e. 2^30 -1). Some of them stockpile a LOT of bananas.

If level 3 challenges were tricky, level 4 challenges were outright difficult.
Up to now, programming skills and creativity were enough to get by. However,
level 4 required some more complicated computer science knowledge to solve.

The first function I wrote for this challenge simply simulated a wrestling match
between two guards.

~~~ python
def wrestle(banana1, banana2):
    bet = min(banana1, banana2)
    if banana1 == bet:
        banana1 += bet
        banana2 -= bet
    elif banana2 == bet:
        banana2 += bet
        banana1 -= bet
    return banana1, banana2
~~~

Next, I had to determine whether two guards would continue to wrestle
indefinitely. Initially, I used a similar approach to the one I used in
[hey-i-already-did-that](#hey-i-already-did-that). However, this proved to be
too inefficient for this challenge. I could not figure out how to make it
faster, so I had to get some help from the Internet. As it turns out, there was
another trick that could be used here to quickly determine if the wrestling
matches form a cycle.

For each wrestling match, you had to work out the greatest common divisor (GCD)
of the two numbers (i.e. of the number of bananas each guard has). If the GCD
did not change between wrestling matches, then the sequence would not terminate
and the guards would always continue to wrestle. Otherwise, if the number of
bananas each guard has becomes the same, than the wrestling comes to a stop.

~~~ python
def find_gcd(a, b):
    while b != 0:
        a, b = b, a % b
    return a

def infinite_wrestle(banana1, banana2):
    previous_gcd = 0
    new_gcd = find_gcd(banana1, banana2)
    # If the gcd does not change, the sequence will not terminate.
    while (previous_gcd != new_gcd) and (banana1 != banana2):
        banana1, banana2 = wrestle(banana1, banana2)
        previous_gcd = new_gcd
        new_gcd = find_gcd(banana1, banana2)
    return banana1 != banana2
~~~

Now I could determine whether a pair of guards will get stuck wrestling, but I
still had to work out the best way of pairing them up. During my time at
university, I learned about flow graphs and how a surprising number of problems
could be solved by modelling them as a flow graph and finding the maximum flow.
I created a method that constructs a graph where nodes represent guards and an
edge between two nodes symbolises that the two guards will wrestle indefinitely.

~~~ python
def construct_graph(banana_list):
    vertices = []
    edges = []
    for index1 in range(len(banana_list)):
        vertices.append(index1)
        for index2 in range(len(banana_list)):
            if infinite_wrestle(banana_list[index1], banana_list[index2]):
                edges.append((index1, index2))
    return vertices, edges
~~~

However, I quickly realised that what I actually needed to find was a maximum
matching in the graph. I had not learned any such algorithms at university, but
I soon found out about [Edmonds' Blossom
algorithm](https://algorithms.discrete.ma.tum.de/graph-algorithms/matchings-blossom-algorithm/index_en.html).
While I understood roughly what the algorithm did and how it worked, my
understanding wasn't deep enough to correctly implement it myself. Instead, I
found a [Python implementation](https://github.com/yorkyer/edmonds-blossom)
online.

Now I had everything I needed. I used Edmonds' Blossom algorithm to find a
maximum matching in the graph I had constructed. The maximum matching told me
how many guards I could pair up so that they would be distracted wrestling. I
then returned the remaining number of guards.

~~~ python
def solution(banana_list):
    vertices, edges = construct_graph(banana_list)
    if len(edges) == 0:
        return len(banana_list)
    else:
        matching = EdmondsBlossom(vertices, edges).edmonds_blossom()
        return len(banana_list) - len(matching)
~~~

[Jump to ToC](#table-of-contents)

### running-with-bunnies

> You and your rescued bunny prisoners need to get out of this collapsing death
trap of a space station - and fast! Unfortunately, some of the bunnies have been
weakened by their long imprisonment and can't run very fast. Their friends are
trying to help them, but this escape would go a lot faster if you also pitched
in. The defensive bulkhead doors have begun to close, and if you don't make it
through in time, you'll be trapped! You need to grab as many bunnies as you can
and get through the bulkheads before they close.

> The time it takes to move from your starting point to all of the bunnies and
to the bulkhead will be given to you in a square matrix of integers. Each row
will tell you the time it takes to get to the start, first bunny, second bunny,
..., last bunny, and the bulkhead in that order. The order of the rows follows
the same pattern (start, each bunny, bulkhead). The bunnies can jump into your
arms, so picking them up is instantaneous, and arriving at the bulkhead at the
same time as it seals still allows for a successful, if dramatic, escape. (Don't
worry, any bunnies you don't pick up will be able to escape with you since they
no longer have to carry the ones you did pick up.) You can revisit different
spots if you wish, and moving to the bulkhead doesn't mean you have to
immediately leave - you can move to and from the bulkhead to pick up additional
bunnies if time permits.

> In addition to spending time traveling between bunnies, some paths interact
with the space station's security checkpoints and add time back to the clock.
Adding time to the clock will delay the closing of the bulkhead doors, and if
the time goes back up to 0 or a positive number after the doors have already
closed, it triggers the bulkhead to reopen. Therefore, it might be possible to
walk in a circle and keep gaining time: that is, each time a path is traversed,
the same amount of time is used or added.

> Write a function of the form solution(times, time_limit) to calculate the most
bunnies you can pick up and which bunnies they are, while still escaping through
the bulkhead before the doors close for good. If there are multiple sets of
bunnies of the same size, return the set of bunnies with the lowest prisoner IDs
(as indexes) in sorted order. The bunnies are represented as a sorted list by
prisoner ID, with the first bunny being 0. There are at most 5 bunnies, and
time_limit is a non-negative integer that is at most 999.

> For instance, in the case of

>
~~~ 
[
    [0, 2, 2, 2, -1],  # 0 = Start
    [9, 0, 2, 2, -1],  # 1 = Bunny 0
    [9, 3, 0, 2, -1],  # 2 = Bunny 1
    [9, 3, 2, 0, -1],  # 3 = Bunny 2
    [9, 3, 2, 2,  0],  # 4 = Bulkhead
]
~~~

> and a time limit of 1, the five inner array rows designate the starting point,
bunny 0, bunny 1, bunny 2, and the bulkhead door exit respectively. You could
take the path:

>
~~~
Start End Delta Time Status
    -   0     -    1 Bulkhead initially open
    0   4    -1    2
    4   2     2    0
    2   4    -1    1
    4   3     2   -1 Bulkhead closes
    3   4    -1    0 Bulkhead reopens; you and the bunnies exit
~~~

> With this solution, you would pick up bunnies 1 and 2. This is the best
combination for this space station hallway, so the answer is [1, 2].

This challenge also required knowledge of graph algorithms. Luckily, this time
it was ones that I was already familiar with. All the shortest-path algorithms
that I used for this challenge were taken from my own [GitHub
repository](https://github.com/Meezeus/graph-algorithms) of graph algorithms
that I wrote while at uni.

The first function I wrote creates a graph where nodes are locations, edges show
the possibility of travel between locations, and edge weights indicate the time
needed to do so.

~~~ python
def create_graph(times):
    nodes = []
    edges = []
    weights = {}
    for start_index in range(len(times)):
        nodes.append(start_index)
        for end_index in range(len(times[start_index])):
            edges.append((start_index, end_index))
            weights[(start_index, end_index)] = times[start_index][end_index]
    return nodes, edges, weights
~~~

Once the graph is constructed, the program checks for negative cycles using the
Bellman-Ford algorithm. If a negative cycle exists, then all the bunnies can be
successfully rescued as there is essentially an infinite amount of time to do
so. If a negative cycle does not exist, the next step is to figure out the best
path between any two locations (sometimes it might be better to take an indirect
path if it saves time). To do this, the program uses Johnson's algorithm.

~~~ python
def solution(times, times_limit):
    num_of_bunnies = len(times) - 2
    bulkhead_index = len(times) - 1

    nodes, edges, weights = create_graph(times)
    _, start_parents, start_distances = bellman_ford(nodes, edges, weights, 0)
    if not start_parents or not start_distances:
        return [i for i in range(num_of_bunnies)] 
    else:
        _, parents, distances = johnson(nodes, edges, weights)

        ...
~~~

Now I knew the best way to travel around, but I still had to figure out which
bunnies to visit and in what order. I decided to use breadth-first search to
explore the solution space. To help with this, I created a class to represent a
possible solution. A solution keeps track of its location, which bunnies have
not been collected, and the time left. In addition, I created a *move* method
which moves the solution from one location to another using the best possible
route. 

~~~ python
class Solution:

    def __init__(self, num_of_bunnies, times_limit):
        self.location = 0
        self.bunnies_left = [i for i in range(1, num_of_bunnies + 1)] # Indexes of the bunnies in the times array.
        self.time_left = times_limit

    def move(self, new_location, parents, distances):
        path = [self.location] + get_intermediate_locations(self.location, new_location, parents) + [new_location]
        for location in path:
            if location in self.bunnies_left:
                self.bunnies_left.remove(location)
        self.time_left = self.time_left - distances[(self.location, new_location)]
        self.location = new_location
~~~

I needed to know the intermediate locations along the route, because those
locations could be bunnies that needed to be picked up. I wrote another method
to do that.

~~~ python
def get_intermediate_locations(old_location, new_location, parents):
    intermediate_locations = []
    parent = parents[(old_location, new_location)]
    while parent != old_location:
        intermediate_locations.append(parent)
        parent = parents[(old_location, parent)]
    intermediate_locations.reverse()
    return intermediate_locations
~~~

Now I had everything I needed to start my search. I created a queue with a
single, starting solution. I also created variables to keep track of the best
solutions discovered. At each iteration, the program pops a solution off the
queue. First, it checks if the solution is valid. A solution is valid if there
is still enough time to move from the current location to the bulkhead door.
Invalid solutions are discarded.

If the solution is valid, the next step is to check if it is the best solution
found so far. To do this, the program simulates moving to the bulkhead door,
since this might include picking up additional bunnies along the way. It then
compares the number of bunnies left against the best known number. If the
current number is better, it clears the list of best solutions and adds the
current solution to the new list (the best number is also updated). If the
current number is equal, it just adds the solution to the existing list.

The last step is to extend the solution by moving to a previously unseen bunny.
This is done by copying the current solution and using the *move* function on
the copy for each bunny. The copied solutions are then added to the queue.

~~~ python
...
queue = [Solution(num_of_bunnies, times_limit)]
best_solutions = []
best_num_of_bunnies_left = num_of_bunnies

while len(queue) > 0:
    solution = queue.pop(0)

    # Check if the current solution is valid.
    time_left = solution.time_left - distances[(solution.location, bulkhead_index)]
    if time_left >= 0:
        # Check if solution is best found so far.
        bunnies_left = solution.bunnies_left[:]
        intermediate_locations = get_intermediate_locations(solution.location, bulkhead_index, parents)
        for location in intermediate_locations:
            if location in bunnies_left:
                bunnies_left.remove(location)                
        if len(bunnies_left) < best_num_of_bunnies_left:
            best_num_of_bunnies_left = len(bunnies_left)
            best_solutions = []
            best_solutions.append(solution)
        elif len(bunnies_left) == best_num_of_bunnies_left:
            best_solutions.append(solution)

        # Extend the solution by visiting a previously unseen bunny.
        for bunny in solution.bunnies_left:
            copied_solution = copy.deepcopy(solution)
            copied_solution.move(bunny, parents, distances)
            queue.append(copied_solution)
...
~~~

Once the queue is empty, the search is over. Each one of the best solutions
found is completed by moving to the bulkhead door (and possibly collecting
additional bunnies along the way). Remember that the solutions were checked
during the search to ensure that this move is valid. The lists of bunnies
collected by the solutions are then aggregated and sorted. Finally, the solution
with the lowest prisoner IDs is selected and returned.

~~~ python
...
bunnies_collected = []
for best_solution in best_solutions:
    best_solution.move(bulkhead_index, parents, distances)
    bunnies_collected.append([i-1 for i in range(1, bulkhead_index) if i not in best_solution.bunnies_left])
bunnies_collected.sort()
return bunnies_collected[0]
~~~

[Jump to ToC](#table-of-contents)

## Level 5

### expanding nebula

> You've escaped Commander Lambda's exploding space station along with numerous
escape pods full of bunnies. But - oh no! - one of the escape pods has flown
into a nearby nebula, causing you to lose track of it. You start monitoring the
nebula, but unfortunately, just a moment too late to find where the pod went.
However, you do find that the gas of the steadily expanding nebula follows a
simple pattern, meaning that you should be able to determine the previous state
of the gas and narrow down where you might find the pod.

> From the scans of the nebula, you have found that it is very flat and
distributed in distinct patches, so you can model it as a 2D grid. You find that
the current existence of gas in a cell of the grid is determined exactly by its
4 nearby cells, specifically, (1) that cell, (2) the cell below it, (3) the cell
to the right of it, and (4) the cell below and to the right of it. If, in the
current state, exactly 1 of those 4 cells in the 2x2 block has gas, then it will
also have gas in the next state. Otherwise, the cell will be empty in the next
state.

> For example, let's say the previous state of the grid (p) was:

>
~~~
.O..
..O.
...O
O...
~~~

> To see how this grid will change to become the current grid (c) over the next
time step, consider the 2x2 blocks of cells around each cell. Of the 2x2 block
of [p[0][0], p[0][1], p[1][0], p[1][1]], only p[0][1] has gas in it, which means
this 2x2 block would become cell c[0][0] with gas in the next time step:

>
~~~
.O -> O
..
~~~

> Likewise, in the next 2x2 block to the right consisting of [p[0][1], p[0][2],
p[1][1], p[1][2]], two of the containing cells have gas, so in the next state of
the grid, c[0][1] will NOT have gas:

>
~~~
O. -> .
.O
~~~

> Following this pattern to its conclusion, from the previous state p, the
current state of the grid c will be:

>
~~~
O.O
.O.
O.O
~~~

> Note that the resulting output will have 1 fewer row and column, since the
bottom and rightmost cells do not have a cell below and to the right of them,
respectively.

> Write a function solution(g) where g is an array of array of bools saying
whether there is gas in each cell (the current scan of the nebula), and return
an int with the number of possible previous states that could have resulted in
that grid after 1 time step. For instance, if the function were given the
current state c above, it would deduce that the possible previous states were p
(given above) as well as its horizontal and vertical reflections, and would
return 4. The width of the grid will be between 3 and 50 inclusive, and the
height of the grid will be between 3 and 9 inclusive. The answer will always be
less than one billion (10^9).

Level 5 is the level that dealt the killing blow. I was able to create a
solution that produces the right answer and passes *most* of the test cases, but
unfortunately my solution was too inefficient to pass the last two.

First, I created a class to represent a pattern, i.e. a 2x2 grid that will
result in either gas or space in the next time step. I then created a list of
all the possible gas patterns and all the possible space patterns.

~~~ python
class Pattern:

    value_to_string = {True: "O", False: "."}

    def __init__(self, values):
        self.TL = values[0]
        self.TR = values[1]
        self.BL = values[2]
        self.BR = values[3]

GAS_PATTERNS = [Pattern([True, False, False, False]), Pattern([False, True, False, False]), Pattern([False, False, True, False]), Pattern([False, False, False, True])]

SPACE_PATTERNS = [Pattern([False, False, False, False]), Pattern([True, True, True, True]),
                  Pattern([True, True, False, False]), Pattern([False, False, True, True]), Pattern([True, False, True, False]), Pattern([False, True, False, True]), Pattern([True, False, False, True]), Pattern([False, True, True, False]),
                  Pattern([False, True, True, True]), Pattern([True, False, True, True]), Pattern([True, True, False, True]), Pattern([True, True, True, False])]
~~~

Like the previous challenge, I needed a class to represent a solution in the
search space. Initially, a solution kept track of the whole grid. However, due
to the way I was carrying out the search, I realised that I only needed to keep
track of the height of the grid, the previous column and the column that is
currently being 'filled in'. I also needed a method for extending the solution
by adding a pattern to it.

~~~ python
class Solution:

    def __init__(self, height):
        self.height = height
        self.previous_col = []
        self.active_col = []
    
    def extend(self, pattern):
        new_solution = Solution(self.height)
        if len(self.active_col) + 1 == self.height:
            new_solution.previous_col = self.active_col + [pattern]
        elif len(self.active_col) + 1 < self.height:
            new_solution.previous_col = self.previous_col
            new_solution.active_col = self.active_col + [pattern]
        return new_solution
~~~

Next comes the actual search. The way I did this is by sequentially building
partial solutions that could turn into the current grid state. Initially, there
is only one, empty solution.

The program iterates over the cells in the current grid, checking each one to
see whether it's gas or space to determine what type of pattern could have
resulted in that cell. It then tries to extend all the partial solutions from
the previous iteration; each solution is extended by adding each possible
pattern that fits. In order for the pattern to fit, it must comply with the
pattern above and to the left of it (patterns below and to the right will not
have been added yet).

~~~ python
def solution(g):
    height = len(g)
    width = len(g[0])
    solutions = [Solution(height)]
    new_solutions = []
    patterns = []
    top_pattern = None
    left_pattern = None

    for width_index in range(width):
        for height_index in range(height):
            new_solutions = []

            # Determine which patterns to use.
            patterns = GAS_PATTERNS if g[height_index][width_index] else SPACE_PATTERNS

            # Extend the existing solution(s).
            for solution in solutions:
                if height_index < len(solution.previous_col):
                    left_pattern = solution.previous_col[height_index]
                if len(solution.active_col) > 0:
                    top_pattern = solution.active_col[-1]
                for pattern in patterns:
                    if width_index == 0:
                        if height_index == 0:
                            # First column and first row, no need to check left or up.
                            new_solutions.append(solution.extend(pattern))
                        else:
                            # First column, need to check up but not left.
                            if top_pattern.BL == pattern.TL and top_pattern.BR == pattern.TR:
                                new_solutions.append(solution.extend(pattern))
                    else:
                        if height_index == 0:
                            # First row, need to check left but not up.
                            if left_pattern.TR == pattern.TL and left_pattern.BR == pattern.BL:
                                new_solutions.append(solution.extend(pattern))
                        else:
                            # Inner cell, need to check left and up.
                            if left_pattern.TR == pattern.TL and left_pattern.BR == pattern.BL and top_pattern.BL == pattern.TL and top_pattern.BR == pattern.TR:
                                new_solutions.append(solution.extend(pattern))
        
            solutions = new_solutions

    return len(solutions)
~~~

The extended solutions are added to a list of new solutions. These new solutions
are then extended again in the next iteration, and so on. Once the whole grid
has been iterated over, the solutions will be complete. The number of possible
grids that could have resulted in the current grid is simply the number of
solutions at the end.

When I saw that my program was not efficient enough, I tried various things to
speed it up. One such attempt was to change the search from BFS to DFS. I hoped
this would reduce the amount of partial solutions that I would need to store.
Instead of a queue, I now used a stack to store partial solutions. At each
iteration, I popped a solution off the stack and extended it like before. Note
that now I was only extending a single solution at a time, whereas before I was
extending all solutions together. If a solution could not be extended (i.e. if
it was complete), it was discarded and a counter was incremented to keep track
of the number of complete solutions. Once the stack was empty, the counter was
returned. Unfortunately, this version turned out to be even slower than the
original... :(

~~~ python
def solution(g):
    height = len(g)
    width = len(g[0])
    num_of_solutions = 0
    stack = [Solution(height, width)]

    while len(stack) > 0:
        solution = stack.pop()

        # Check if complete.
        if solution.is_complete():
            num_of_solutions += 1
        else:
            # Determine which patterns to use.
            patterns = GAS_PATTERNS if g[solution.height_index][solution.width_index] else SPACE_PATTERNS

            # Extend solution and add to stack.
            for pattern in patterns:
                if solution.compatible(pattern):
                    stack.append(solution.extend(pattern))

    return num_of_solutions
~~~

Another attempt tried to make checking for compatibility between patterns faster
by precomputing all compatible pattern combinations. This way, whenever a
solution is extended, it doesn't need to check for compatibility itself: it just
consults a dictionary, which returns all the patterns that are compatible for
that combination of top pattern, left pattern and gas/space. However, this
version also failed to provide a significant speed increase (on the bright side,
it didn't provide a speed *decrease*).

~~~ python
def solution(g):
    compatible_patterns_gas, compatible_patterns_space = compute_compatible_patterns()
    compatible_patterns = None
    height = len(g)
    width = len(g[0])
    solutions = [Solution(height)]
    new_solutions = []
    top_pattern = None
    left_pattern = None
    patterns = []

    for width_index in range(width):
        for height_index in range(height):
            compatible_patterns = compatible_patterns_gas if g[height_index][width_index] else compatible_patterns_space
            new_solutions = []

            # Extend the existing solution(s).
            for solution in solutions:
                if height_index < len(solution.previous_col):
                    left_pattern = solution.previous_col[height_index]
                else:
                    left_pattern = None
                if len(solution.active_col) > 0:
                    top_pattern = solution.active_col[-1]
                else:
                    top_pattern = None
                    
                patterns = compatible_patterns[top_pattern, left_pattern]
                for pattern in patterns:
                    new_solutions.append(solution.extend(pattern))

    return len(solutions)
~~~

I tried some other things, big and small, but ultimately none of them were
enough. I was out of time and out of ideas. Since I did not pass all the test
cases, I did not successfully complete this level. My foobar journey was over:
so close to the end, and yet so far.

![]({{site.url}}/assets/img/google-foobar/foobar-over.jpg){: .mx-auto.d-block :}

[Jump to ToC](#table-of-contents)

## Conclusion

Even though I wasn't able to successfully complete all the levels, I still very
much enjoyed taking part in this challenge. Not only did I learn new things
(such as Edmonds' Blossom algorithm, interesting properties of XOR, cellular
automata, etc.), but I simply enjoyed challenging myself and problem solving. I
believe that's what programming is all about and it's why I love it so much.

[Jump to ToC](#table-of-contents)
