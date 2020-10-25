<a name=top>
<a href="http://tiny.cc/seng20"><img  width=700
  src="https://raw.githubusercontent.com/txt/se20/master/etc/img/teamBanner.png"></a>
<hr>
<p>
&nbsp;<a href="https://tiny.cc/seng20">home</a> ::
<a href="https://github.com/txt/se20/blob/master/docs/syllabus.md#top">syllabus</a> ::
<a href="https://github.com/txt/se20/blob/master/docs/syllabus.md#timetable">timetable</a> ::
<a href="https://drive.google.com/drive/folders/1ZFn6H8-4kx5uP34bpFgIFonkz9Tw3nYM?usp=sharing">groups</a> ::
<a href="https://moodle-courses2021.wolfware.ncsu.edu/course/view.php?id=3873">moodle</a> ::
<a href="http://seng20.slack.com">chat</a>  ::
<a href="https://github.com/txt/se20/blob/master/LICENSE.md#top">&copy; 2020</a>  
<br>
<hr>

# Abstraction

<img src="../etc/img/abstract.png">
## The Abstraction Test

Which is best?

1. Countries have states. States have cities. Cities have suburbs. Suburbs have streets. Streets have houses.
2. Things, at level `i` have sub-things at level `i+``.

If you are only building one application, the clearly number 1

But number 2, which would need some tools to support it, is more extensible.


## Abstraction in computer science had to be invented.

- Barbara Liskov won at $250K prize for her work on  abstraction in programming languages.
  - [Her award lecture](https://amturing.acm.org/vp/liskov_1108679.cfm)
    is amazing
    - at minute  1:09:00 to 1:11:00 the whole world disappears and her and Guy Steele (\*)debate
      iterator design and the rest of us can just listen

<em>(\*) You don't know who Guy Steele is? Shame on you! [Read it](https://apps.dtic.mil/dtic/tr/fulltext/u2/a030751.pdf) and weep.</em>

Why abstraction? well?

- If you underlying data structures are complex, you want some way to talk to them
  without having to know everything about them
- If you want to run your code on a range of different hardware
  -  you want to write code in  hardware independent manner
     - so code can run all over the place
  - Add an abstraction layer between
    - operating systems below and
    - the application above
    - Once you get "it" going for one OS on one machine
      - It runs wherever that OS runs
    - e g. UNIX
- If computing environments are dynamic, we want to bounce our
computation around the hardware to take full advantage of unused
resources.
 - And when we bounce a "program", we want to bounce with it:
     - all the current
   variable settings, 
     - contents of RAM, 
     - activation records of current
   functions, 
     - return addresses for when those functions return, 
     - network
   connections, 
     - hooks to GUIs and databases, etc etc. 
 - Think of the _layers_ pattern, on steroids
     - Where you grab whole layers and move them to another piece hardware
 - Under the hood, again, this is abstraction.

This tactic has been applied, successfully, dozens of times in the history of computing
All these following use the same idea... abstraction

- Language features that simplify programming complex systems
  - Iterators
  - Error handling
- Operating system shell language tools
  - Pipes
- Hardware independent operating systems
- Languages implemented via virtual machines (LISP, Smalltalk, Java, JavaScript, Erlan,...)
- Languages implemented via transpiles
- "Jump technologies" 
  - Virtual machines
  - Containers
  - Serverless computing

Note that while the applications are different, and occur at very different scales,
the essential idea of _abstraction_ is used  throughout

- Between the programmer and the hardware there are layers of abstraction
- Programmers talk to the abstraction
- Automated tools make abstraction to the hardware

## Kinds of Abstractions

We say abstractions can be sorted by their memory size. The following list is sorted
largest to smallest:

6.  Operating systems
5. Virtual machines
4. Containers
3. Serverless systems
2. Erlang's trick
1. Pipes
1. Iterators and error handlers
   - This last one is not the same as the rest
   - Iterators and error handlers are abstraction tricks for programmers are smaller than
     - Invented by Barbara Liskov
   - While the  rest allow you jump computation around a CPU farm

## Queuing Theory 

Before we start... this is going to seem a digression, but just stay with me here

The point of this little digression is that it is useful to jump around things
between service providers. 
- And the smaller the thing, the faster we can jump it
- And the more little gaps we can jump them into

N tellers/ checkout people. How many lines? One? N?

<img src="https://csbcorrespondent.com/sites/default/files/WSJ%20Teller%20Graphic_050418%20v.4.jpg" width=500>

Answer: see [7 insights into Queue Theory](http://www.treewhimsy.com/TECPB/Articles/SevenInsights.pdf)

[According to the RIOT paper](https://arxiv.org/pdf/1708.08127.pdf)
Computers
execute in highly dynamic environment.

- For example, Schad et al. [11] found that the
runtime of a widely used benchmarks suite can vary by up
to 33% even when run on supposedly identical instances
within the same cloud environment. 
- Not only CPU, but
also bandwidth can be highly variable within the cloud.
  - Schad et al. report that network bandwidth between the
same type of EC2 instances can vary from 410KB/s to
890KB/s. 
- Hence, even after a workflow is planned and
deployed, it is important to monitor instances and repeat
the scheduling process during deployment when necessary.
- If repeated rescheduling are too slow, then there is much waste of computer rescues.

## Error handling

Throw a ball into a stadium, wait for what returns while holding a catchers mitt.
Only catch things you know how to handle

```python
def atom(token: str) -> Atom:
    "Numbers become numbers; every other token is a symbol."
    try: return int(token)
    except ValueError:
        try: return float(token)
        except ValueError:
            return Symbol(token)
```


## Iterators
When you code recursive data structures, give programmers
so way to way around that code.

e.g. What does this return?

```python
for x in [10,[[11,12,13],15], 20,30,[40,50,"tim"]]:
   print(x)
```

```python
def items(x):
  if isinstance(x,(str,list,tuple)):
   for y in x:
     for z in items(y):
       yield z
  else:
    yield x
```
Another example (in awk). This example shows that _iterators_ can be implemented in any language,
it you think the right way.

```awk
# application code

while(csv(lst,file))
  doSOmething(lst)
```

Handles numerous details for reading CSV files. For example, the following 
CSV file has blank lines (to be skipped),  white space and comments (that need deleting), 
strings containing numbers that need to be coerced into real numbers,
and record that that split on "," (which need to be combined with the next line).

```
name,   # who
age,    # in year`s
shoesize # posint
#########################

# family1
tim, # what
21,  # a 
10   # nice guy
jane  ,  6,  2

# family2
tim, 21, 10 
jane,6,2
```
The `csv` iterator reader is below. This iterator calls a sub-iterator `getline` that returns 0 on end of line:

```awk
# iterator code

function csv(a,file,     i,j,b4, ok,line,x,y) {
  file  = file ? file : "-"     # [1] read from standard input or file       
  ok = getline < file
  if (ok <0) {                  # [2] complain if missing file
     print "missing "file > "/dev/stderr"; exit 1 }
  if (ok==0) { close(file);return 0 }                                    
  line = b4 $0                         
  gsub(/([ \t]*|#.*$)/, "", line) # [3] kill white space and comments      
  if (!line)       return csv(a,file, line) # [4] skip blanks lines
  if (line ~ /,$/) return csv(a,file, line) # [5] contact incomplete rows with next
  split(line, a, ",")                       # [6] split line into cells on comma
  for(i in a) {
    x=a[i]
    y=a[i]+0
    a[i] = x==y ? y : x                     # [7] coerce number strings, if any
  }
  return 1
}
```

The point being made here is that "abstraction" is a tool you can use, in any language
- You just need to think a little more... abstractly
- Thing "producer" and "consumer" when the consumer does not want to, or need to, understand all the details
  - Which is very useful if I start changing low-level representations
    - e.g. I'm talking to a zip file and not a raw file
    - Then `csv` can change internally while the application code remains the same.

By the way, if we are talking abstractions about consumers and producers then that takes us naturally to...

## Pipes

Doug McIlroy, Bell Labs’ “Computing Techniques
Research Department” 1986
-   “We should have some ways of coupling programs like garden hose.” 
- "Let programmers screw in another segment when it becomes necessary
to massage data in another way.”
- “Expect the output of
 every program to become the input to another, as yet unknown,
 program. Don’t clutter output with extraneous information.”

Pipes changed the whole idea of UNIX:

- Implemented in 1973 when ("in one feverish night",
wrote McIlroy) by  Ken Thompson.
- “It was clear to everyone, practically minutes after the system
came up with pipes working, that it was a wonderful thing. Nobody
would ever go back and give that up if they could.”
- The next
day", McIlroy writes, "saw an unforgettable orgy of one-liners
as everybody joined in the excitement of plumbing." 

Pipes changed how we think about software

- Not applications
- But tools.
  - Compose the things together, if you
had made them so that they actually worked together. 
  - People went back and consciously put into programs the idea that they read from a list of files 
  -  And  if there were no files they read from the standard input so that they could be used in pipelines.

You've probably all used pipes already. Here we download an install shell script  then pipe it to the `sh` command
(so it runs)

```
curl -s http://example.com/install.sh | sh
```

(By the way, that is a security risk. [Use with care!](https://blog.dijit.sh//don-t-pipe-curl-to-bash))

Examples
- `nroff` is a text formatter (old version of latex)
- `lp` is the line printer
- `col` handles escape sequences that sets up text in 2 columns
- `tbl` is an nroff pre-processor that expresses tables in terms of lower-level nroff commands
- `eqn` is an nroff pre-processor that expresses equations in terms of lower-level nroff commands

```bash
nroff files | col | lp

tbl file-1 file-2 . . . | eqn | nroff -ms
````

Think of pipes as `produce`, `translate`, `filter`, `consume`:

```bash
find /usr/bin/ |                #produce 
sed 's:.*/::'  |                #translate: strip directory part
grep -i '^z'   |                #filter   : select items starting with z
xargs -d '\n' aFinalConsumer    #consume 
```

Note that in the above, the `filters` inside the `pipes` could be written in any language at all, by different teams.

Also, operating systems _love_ pipes since they can run each part of the process as a separate process,
maybe even on different machines (\*)

(\*) That said, there is the data bus / network cost of streaming data between parts of the pipe. 

Problems with pipes:

- Assumes a one-way flow, here to there
  - Not ideal for GUI envrionments where users want to call functions in any order
  - Which makes pipes are more an "under the hood" idea
- Also, I've had problems with pipes and concurrency: sending data off on long retrevial arcs (e.g. from another part of the network)
  - But that's just me

## Erlang's Trick

Some languages are designed for concurrency.

Consider the prorgram:

```python
def aa(x):
    def bb(y): return aa(y-1)
    return x if x < 1 else bb(x)
```

Here, we can't run ``bb`` by itself without dragging along everything we know about ``aa`. 

So instead, what if all functions are in the global space and all variables can only access the local space of each function?

Here's a small database language that can derive child info from a database on `father`s and `mother`s.
`X` and `Y` are variabes scoped to within a `clause` (and clauses end with a _fulll stop_).

```prolog
child(X,Y) :- mother(X,Y).  # clause1
child(X,Y) :- father(X,Y).  # clause2

descendent(X,Y) :- child(X,Y).                  # clause3
descendent(X,Y) :- child(X,Z), ancestor(Z,Y).   # clause4

father(tim,don).            # clause
father(janet,don).          # clause6

mother(tim,jane).           # clause7
mother(janet,don).          # clause8
```

Note now that the clauses can all be run seperately on different CPUs since everything we need to know about (eg) `descendent`
is available locally when we call that function.

Now Erlang's _unit of swap_ is really, really small. From
[Erlang](https://www.infoq.com/presentations/erlang-software-for-a-concurrent-world/) 
- Erlang is a concurrent language. 
- By that I mean that threads are part of the programming language, they do not belong to the operating system. 
- That's really what's wrong with programming languages like Java and C++. 
  - It's threads aren't in the programming language, threads are something in the operating system s
    – and they inherit all the problems that they have in the operating system. 
- One of those problems is granularity of the memory management system. 
  - The memory management in the operating system protects whole pages of memory, 
     so the smallest size that a thread can be is the smallest size of a page. 
  - That's actually too big.
  - If you add more memory to your machine – you have the same number of bits that protects the memory 
    so the granularity of the page tables goes up – you end up using say 64kB for a process you know running in a few hundred bytes.

## Containers and Virtual Machines

If iterators is small abstraction
- and OS is large abstraction
- then containers are in the middle

<img src="https://imesh.github.io/images/contvsvm.png">


## Serverless

Servless is an abstraction that is smaller than containers

- Serverless
  - e.g. AWS Lambda (btw, why is it called "lambda"? See below).
  - Why even have an operating system, just start with your current application and only ship the little bits
    needed for the application
  - Good news:
    - Cheaper
    - Smaller memory (so more scalable)
    - Productivity: less to configure, less messing with threading.
      Just explore  the unit of code to the outside world are simple event driven functions.
  - Bad news:
    - Debugging. If you ship a bare bones unit of functionality, you may not get
      the debugging tools needed to understand a drash.
    - Security: more tiny components, less protection from other OS facilites (which
      not execist in your tiny comptuational slice)
    - Vendor lock-in : 
          - But in the serveless world, no OS abstraction between comptuation and support
        environment
        - So not "write once, run everywhre" 
    - Configuration: can't make _any_ assumptions about background utilities from
      "the operating system" cause their ain't one
      - You have to do it all.



## UNIX

If iterators and error handlers are "little abstractions", then operating systems are
"big abstractions".

<img align=right width=500 src="https://upload.wikimedia.org/wikipedia/commons/4/49/Baby_Bells.svg">

- Pre-unix: operating systems were hardware-specific
- The breakup of the Bell System was mandated on January 8, 1982
  - So after laying down all that copper
    - Bell Corporation was not allowed to use it all. 
    - And they actually set up commercial competitors!
- They needed someway for different organizations to work together
  - The ultimate "why can't we all get along"
- Step1: a hardware independent systems language "C"
- Step2: an operating system written in "C"
- Step3: design patterns to allow for interactions
  - Uniform file representation (everything is "ASCII test", no special binary formats for each fie).
  - Use pipes
