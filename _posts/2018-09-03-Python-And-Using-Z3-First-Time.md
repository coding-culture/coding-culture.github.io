---
layout: post
title:  "Z3 and Sudoku, beginning"
date:   2018-09-03
summary: Using Z3 for small Sudoku solve
tags: python Z3
---

# Python and Z3

So whats [Z3?](https://ericpony.github.io/z3py-tutorial/guide-examples.htm) it's a theorem prover built by Microsoft, and it's something I first saw being used by [LiveOverflow](https://www.youtube.com/watch?v=TpdDq56KH1I&t=495s) on Youtube during a CTF and it caught my interest as i'd never seen a Python module like it.

You can feed it equations, or Logic, or Arithmetic, almost anything and it can help you solve it

```python
from z3 import *

def main():
    # new instance of z3's solver
    s = Solver()

    # implement the variables
    a = Int('a')

    # add in the constraints
    s.add(a * a == 9)

    # check model is satisfied
    print(s.check())

    # print the model
    print(s.model())

if __name__ == '__main__':
    main()
```

This produces the output

```python
>> sat
>> [a = 3]
```

As that is the Integer value that is required to the solution a \* a = 9

I wondered how difficult it would be to implement a Z3 Solver over a small "Suko.
If you don't know they are small Sudoku like puzzles but just 3 x 3 with a few extra twists.
They look like this:

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{font-family:Arial, sans-serif;font-size:14px;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:black;}
.tg th{font-family:Arial, sans-serif;font-size:14px;font-weight:normal;padding:10px 5px;border-style:solid;border-width:1px;overflow:hidden;word-break:normal;border-color:black;}
.tg .tg-o79m{background-color:#3166ff;text-align:left;vertical-align:top}
.tg .tg-s7ni{background-color:#f8ff00;text-align:left;vertical-align:top}
.tg .tg-fd62{background-color:#32cb00;text-align:left;vertical-align:top}
</style>
<table class="tg">
  <tr>
    <td class="tg-o79m"></td>
    <td class="tg-o79m"></td>
    <td class="tg-o79m"></td>
  </tr>
  <tr>
    <td class="tg-s7ni"></td>
    <td class="tg-s7ni"></td>
    <td class="tg-o79m"></td>
  </tr>
  <tr>
    <td class="tg-fd62"></td>
    <td class="tg-fd62"></td>
    <td class="tg-fd62"></td>
  </tr>
</table>

It can only use the numbers 1-9 each once, each colour has to total a specific value, and each group of 4 as in each corner must total a specific value. So I found an example Suko, took my basic Z3 and expanded it with extra variables and constraints.

```python
# extra variables
a = Int('a')
b = Int('b')
c = Int('c')
d = Int('d')
e = Int('e')
f = Int('f')
g = Int('g')
h = Int('h')
i = Int('i')
# numbered my square a,b,c top row, d,e,f for middle, g,h,i bottom
```

```python
s.add(a + b + c + f == 20) # blue total 20
s.add(d + e == 8) # yellow total 8
s.add(g + h + i == 17) # green total 17
s.add(a + b + d + e == 14) # corner 4 totals
s.add(b + c + e + f == 23)
s.add(d + e + g + h == 19)
s.add(e + f + h + i == 26)
s.add(And(a > 0, b > 0, c > 0, d > 0, e > 0, f > 0, g > 0, h > 0, i > 0)) # only numbers > 0
s.add(And(a < 10, b < 10, c < 10, d < 10, e < 10, f < 10, g < 10, h < 10, i < 10)) # only numbers < 10
```

I then added a few things to put all the value models into a list, then added the constrains to not allow that model again. Finally write a small is_valid() function to check only the values had been used once, and hey it worked, got a solution.

[i = 6,
f = 5,
b = 2,
a = 4,
d = 1,
g = 3,
h = 8,
c = 9,
e = 7]

It's probably not the best code, or use of Z3 but it's a fun module and next I'm going to try a full size Sudoku. You can find the code [here](https://github.com/coding-culture/coding-culture/blob/master/Python/suko.py) in the repo for all the code I do related to this blog.

Thanks for Reading
