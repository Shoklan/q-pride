---
aliases:
- /debugging/programming/python/2020/10/21/Simple-Python-Debugging-Example
categories:
- python
- programming
- debugging
date: '2020-10-21'
description: Some Examples I learned From Debugging My Own Stuff
layout: post
title: Simple Introduction to Python Debugging
toc: false

---

## Inspiration
While working on a script that I'm writing for work, I was having trouble really tracking down where my logic was failing. After looking over the code, everything looked correct but the results were showing me otherwise. So, I opened up python's debugging system and started to dig in.

## What is Debugging?
> In computer programming and software development, debugging is the process of finding and resolving bugs (defects or problems that prevent correct operation) within computer programs, software, or systems.
> cf: https://en.wikipedia.org/wiki/Debugging

There are plenty of tools to do stuff like this but Python comes with its own: pdb — [The Python Debugger](https://docs.python.org/3/library/pdb.html).
To activate it, you'd simply go into your terminal and type:
```
user@station# python3 -m pdb <script-name>
```
This with start the script from the top and immediately stop before running anything:
```
user@station:/scripts# python3 -m pdb keyIntegrityCheck.py
> /scripts/keyIntegrityCheck.py(5)<module>()
-> from pathlib import Path
(Pdb)
```
While this is useful, we need to be able to explore the file. You could keep an editor open on a different monitor and move around but sometimes that's not going to be an option. To print all the lines in the file  you'd use:
```
(Pdb) ll                                                                                    
1     #!/usr/bin/env python3                                        
2     ## Author: Collin Mitchell                                 
3     ## Purpose: To check the integrity of keys without user input.  
4                                                                               
5  -> from pathlib import Path                                                   
6     from itertools import filterfalse                                            
7     import subprocess as sp                                                          
8     import json                                                                     
9     import sys    
# ...
370                 integrityFunc = dd.get(str(filename).split('.')[-1])
371                 if integrityFunc:
372                     consistent = integrityFunc( filename )
373                     if not consistent: print("{} is not consistent.".format(filename))
374                 else:
375                     print("{} is missing a checker; please report {} so it can be added.".format(filename,filename))
(Pdb)
```
You can see the pointer on line five telling us where the current execution point is. We can move the pointer a single line using `n`:
```
(Pdb) n                                                                                                                                                                   [336/1995]
> /scripts/keyIntegrityCheck.py(6)<module>()
-> from itertools import filterfalse                   
(Pdb) ll                                                                                    
  1     #!/usr/bin/env python3                                        
  2     ## Author: Collin Mitchell                                 
  3     ## Purpose: To check the integrity of keys without user input.  
  4                                                                               
  5     from pathlib import Path                                                   
  6  -> from itertools import filterfalse                                            
  7     import subprocess as sp                                                          
  8     import json                                                                     
  9     import sys     
```
You can see the next 11 lines in the console buffer using `l`:
```
(Pdb) l
  1     #!/usr/bin/env python3
  2     ## Author: Collin Mitchell
  3     ## Purpose: To check the integrity of keys without user input.
  4  
  5     from pathlib import Path
  6  -> from itertools import filterfalse
  7     import subprocess as sp
  8     import json
  9     import sys
 10  
 11     # stuff to do with phpserialize:
(Pdb)
```
Not that if you do this and run it again that you wont get the same result:
```
(Pdb) l
 12     import codecs
 13     try:
 14         codecs.lookup_error('surrogateescape')
 15         default_errors = 'surrogateescape'
 16     except LookupError:
 17         default_errors = 'strict'
 18     try:
 19         xrange
 20     except NameError:
 21         xrange = range
 22     try:
(Pdb)
```
... but you will get the **next** 11 lines instead. You can tell it which lines to list centered on a line number using `l 10`:
```
(Pdb) l 10
  5     from pathlib import Path
  6  -> from itertools import filterfalse
  7     import subprocess as sp
  8     import json
  9     import sys
 10  
 11     # stuff to do with phpserialize:
 12     import codecs
 13     try:
 14         codecs.lookup_error('surrogateescape')
 15         default_errors = 'surrogateescape'
(Pdb)
```
Now that we can move around, let's discuss how to actually stop code execution using **breakpoints**. These are locations you set - sometimes with conditions - to stop the code execution and explore the current state. You can set these using 'b':
```
(Pdb) b
(Pdb)
```
Since we don't have any breakpoints set, then it makes sense we don't see any listed. So, now lets set one:
```
(Pdb) b 9
Breakpoint 1 at /scripts/keyIntegrityCheck.py:9
(Pdb)
```
... and continue execution until the breakpoint using `c`:
```
(Pdb) c
> /scripts/keyIntegrityCheck.py(9)<module>()
-> import sys
(Pdb)
```
... and list the active breakpoints again:
```
(Pdb) b
Num Type         Disp Enb   Where
1   breakpoint   keep yes   at /scripts/keyIntegrityCheck.py:9
        breakpoint already hit 1 time
(Pdb)
```
You can clear that breakpoint using the number of the breakpoint as well:
```
(Pdb) b
Num Type         Disp Enb   Where
1   breakpoint   keep yes   at /scripts/keyIntegrityCheck.py:9
        breakpoint already hit 1 time
(Pdb) cl 1
Deleted breakpoint 1 at /scripts/keyIntegrityCheck.py:9
(Pdb) b
(Pdb)
```
Conditional breakpoints I found a bit tricky to get to work correctly because you need to place a comma after the statement:
```
(Pdb) b 371, integrityFunc.__name__ == 'integrityPhp'
Breakpoint 3 at /scripts/keyIntegrityCheck.py:371
(Pdb)
```
... and then you `c` until it triggers:
```
> /scripts/keyIntegrityCheck.py(371)<module>()
-> if integrityFunc:
(Pdb) integrityFunc
<function integrityPhp at 0x7f99291477b8>
(Pdb)
```

## Conclusion
I couldn't find much outside of the official documentation when I needed it so hopefully you find the highlights useful.
There are also tools for whichever IDE you're using so look out for those as well.
