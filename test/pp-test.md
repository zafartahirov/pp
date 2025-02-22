% PP test file (markdown)
% Christophe Delord

Introduction
============

!mustache(../package.yaml)
```````````````````````````````````````
This document is a test file for `{{name}}` version {{version}} by {{author}}.
Copyright {{copyright}}.
```````````````````````````````````````

It is preprocessed and compared with `pp-test.ref`.

Macros definition
=================

## Definition

----------------------------------- -----------------------------------------------------------------
Definition of `mac1`                !def(mac1)(This is macro `mac1`)
Show macro definition:              !rawdef(mac1)
Show macro value:                   !mac1
----------------------------------- -----------------------------------------------------------------

## Undefinition

----------------------------------- -----------------------------------------------------------------
Undefined macro `mac2`              !undef(mac2)
Show macro definition:              !rawdef(mac2)
Show macro value:                   !mac2
----------------------------------- -----------------------------------------------------------------

## Definition with arguments

----------------------------------- -----------------------------------------------------------------
Definition with arguments:          !define(swap)(!2 !1)
Show macro definition:              !rawdef(swap)
Show macro value:                   !swap(!mac1)(!mac2)
----------------------------------- -----------------------------------------------------------------

## Passing arguments between user macros (see issue #29)

----------------------------------- -----------------------------------------------------------------
A calls B calls C:                  !def(A)(!B(!1))!def(B)(!C(!1))!def(C)(C got !1)!A(42)
same with 3 args:                   !def(A)(!B(!2)(!3)(!1))!def(B)(!C(!2)(!3)(!1))!def(C)(C got !1 !2 !3)!A(42)(43)(44)
----------------------------------- -----------------------------------------------------------------

----------------------------------- -----------------------------------------------------------------
Undefinition:                       !undefine(mac1)!rawdef(mac1)
----------------------------------- -----------------------------------------------------------------

----------------------------------- -----------------------------------------------------------------
Definition test:                    !ifdef(mac1)(mac1 is defined)
Definition test:                    !ifdef(swap)(swap is defined)
Definition test:                    !ifdef(mac1)(mac1 is defined)(mac1 is not defined)
Definition test:                    !ifdef(swap)(swap is defined)(swap is not defined)
Undefinition test:                  !ifndef(mac1)(mac1 is not defined)
Undefinition test:                  !ifndef(swap)(swap is not defined)
Undefinition test:                  !ifndef(mac1)(mac1 is not defined)(mac1 is defined)
Undefinition test:                  !ifndef(swap)(swap is not defined)(swap is defined)
Macro defined:                      !defined(swap) !defined(mac1)
----------------------------------- -------------------------------------------------------------

## Multiple code block arguments

!def(foo) !undef(bar)

!ifdef(foo)
```
foo is defined
```
```
foo is not defined
```

!ifdef(bar)
```
bar is defined
```
```
bar is not defined
```

## Expressions

!def(test)
```
"!1" => !eval{!1} (!if(!1)(true)(false))
```

!test(-1)
!test(1)
!test(2+3)
!test(4-5)
!test(6*7*8)
!test(8/2)
!test(! 0) !test(not 0) !test(! "") !test(not "")
!test(! 3) !test(not 3) !test(! "?") !test(not "?")
!test(3 and 4) !test(3 and 0) !test(0 and 4) !test(0 and 0)
!test(3 && 4) !test(3 && 0) !test(0 && 4) !test(0 && 0)
!test(3 or 4) !test(3 or 0) !test(0 or 4) !test(0 or 0)
!test(3 || 4) !test(3 || 0) !test(0 || 4) !test(0 || 0)
!test(3 xor 4) !test(3 xor 0) !test(0 xor 4) !test(0 xor 0)
!test(3 == 2+1) !test(3 == 2+2) !test(3 == 2-1)
!test(3 /= 2+1) !test(3 /= 2+2) !test(3 /= 2-1)
!test(3 != 2+1) !test(3 != 2+2) !test(3 != 2-1)
!test(3 <= 2+1) !test(3 <= 2+2) !test(3 <= 2-1)
!test(3 < 2+1) !test(3 < 2+2) !test(3 < 2-1)
!test(3 >= 2+1) !test(3 >= 2+2) !test(3 >= 2-1)
!test(3 > 2+1) !test(3 > 2+2) !test(3 > 2-1)
!test(!defined(swap))
!test(!defined(mac1))
!test(!defined(swap) and !defined(mac1))
!test(!defined(swap) or !defined(mac1))
!test(!defined(swap) and not !defined(mac1))

## Testing argument scopes (see issue #33)

!def(f)(f take 2 args: !1 and !2 but not !raw(!3))

----------------------------------- -------------------------------------------------------------
Before calling f:                   !1 !2
call with 2 args:                   !f(one)(two)
between two calls:                  !1 !2
call with 1 arg:                    !f(one')
between two calls:                  !1 !2
call with 2 args:                   !f(one'')(two'')
after calling f:                    !1 !2
----------------------------------- -------------------------------------------------------------

!def(g)(!f(!1)(!2))
----------------------------------- -------------------------------------------------------------
Before calling g:                   !1 !2
call with 2 args:                   !g(one)(two)
between two calls:                  !1 !2
call with 1 arg:                    !g(one')
between two calls:                  !1 !2
call with 2 args:                   !g(one'')(two'')
after calling g:                    !1 !2
----------------------------------- -------------------------------------------------------------

!def(g)(!f(!1)(-!2-))
----------------------------------- -------------------------------------------------------------
Before calling g:                   !1 !2
call with 2 args:                   !g(one)(two)
between two calls:                  !1 !2
call with 1 arg:                    !g(one')
between two calls:                  !1 !2
call with 2 args:                   !g(one'')(two'')
after calling g:                    !1 !2
----------------------------------- -------------------------------------------------------------

## Equality / Inequality

!define(one)(1) !define(un)(1) !define(two)(2)

----------------------------------- -----------------------------------------------------------------
Equality test:                      !ifeq(!one)(!un)    (one == un)
Equality test:                      !ifeq(!one)(!two)   (one == two)
Equality test:                      !ifeq(!one)(!un)    (one == un) (one /= un)
Equality test:                      !ifeq(!one)(!two)   (one == two)(one /= two)
Inequality test:                    !ifne(!one)(!un)    (one /= un)
Inequality test:                    !ifne(!one)(!two)   (one /= two)
Inequality test:                    !ifne(!one)(!un)    (one /= un) (one == un)
Inequality test:                    !ifne(!one)(!two)   (one /= two)(one == two)
----------------------------------- -----------------------------------------------------------------

----------------------------------- -----------------------------------------------------------------
Raw text:                           !raw(!swap(a)(b))
Evaluated:                          !swap(a)(b)
----------------------------------- -----------------------------------------------------------------

File inclusion
==============

## Before including `subdir/pp-test.i`

----------------------------------- -----------------------------------------------------------------
File name of the main file:         !main
Directory of the main file:         !root
File name of the current file:      !file
Directory of the current file:      !cwd
----------------------------------- -----------------------------------------------------------------

## Inclusion of `subdir/pp-test.i`

!include(subdir/pp-test.i)

## After including `subdir/pp-test.i`

----------------------------------- -----------------------------------------------------------------
Definitions:                        !answer
----------------------------------- -----------------------------------------------------------------

## Files included without preprocessing

----------------------------------- -----------------------------------------------------------------
Undefinition:                       !undef(answer)
----------------------------------- -----------------------------------------------------------------

!rawinclude(subdir/pp-test.i)

----------------------------------- -----------------------------------------------------------------
No definitions:                     !answer
----------------------------------- -----------------------------------------------------------------

## Files imported

In the document:

!undef(answer)

Before importing: answer = !answer
!import(subdir/pp-test.i)
After import: answer = !answer

And from the command line:

imported_macro: !imported_macro

## Mustache template preprocessor

!def(note)(The template is preprocessed before mustache is called.)

!mustache(subdir/mustache.yaml)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
**Greetings from a YAML file**

{{#names}}
Hi {{name}}!
{{/names}}

!note
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!mustache(subdir/mustache.json)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
**Greetings from a JSON file**

{{#names}}
Hi {{name}}!
{{/names}}

!note
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Comments and quiet definitions
==============================

## Comments

!comment
~~~~~~~~~~~~~~~~~~~~~~~~~
This is a pretty useless comment for fun.
~~~~~~~~~~~~~~~~~~~~~~~~~

!comment(a comment with a title)
~~~~~~~~~~~~~~~~~~~~~~~~~
This is a pretty useless comment for fun.
~~~~~~~~~~~~~~~~~~~~~~~~~

## Quiet definitions

!quiet(Here are some definitions)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

pi is: !def(pi)(3.14)

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

----------------------------------- -----------------------------------------------------------------
Definitions:                        !pi
----------------------------------- -----------------------------------------------------------------

File modification date
======================

!sh
```
touch -d "2010-06-22 12:00:00" /tmp/pp-test-f1
touch -d "2010-06-23 12:00:00" /tmp/pp-test-f2
touch -d "2010-06-21 12:00:00" /tmp/pp-test-f3
```

----------------------------------- -----------------------------------------------------------------
Current file date:                  !ifeq(!mdate)(!exec(LANG=en date +"%A %e %B %Y" -r !file))(mdate = date of !main)(mdate = !mdate /= date of !main)
Specific file date:                 !mdate(/tmp/pp-test-f1 /tmp/pp-test-f2)(/tmp/pp-test-f3)
----------------------------------- -----------------------------------------------------------------

Environment variables
=====================

----------------------------------- -----------------------------------------------------------------
Undefined environment variable:     !env(EMPTYENVVAR)
Environment variable:               !env(TESTENVVAR)
----------------------------------- -----------------------------------------------------------------

OS architecture
===============

----------------------------------- -----------------------------------------------------------------
OS:                                 !os
Architecture:                       !arch
----------------------------------- -----------------------------------------------------------------

Simple arithmetic
=================

----------------------------------- -----------------------------------------------------------------
undefined + 1 =                     !add(x)!x
1 + 1 =                             !add(x)!x
undefined + 3 =                     !add(y)(3)!y
3 + 4 =                             !add(y)(4)!y
----------------------------------- -----------------------------------------------------------------

Simple string manipulation
==========================

----------------------------------- -----------------------------------------------------------------
append(undefined, "foo")            !append(accu)(foo)!accu
append(foo, "bar")                  !append(accu)(bar)!accu
----------------------------------- -----------------------------------------------------------------

Dialect, Output language and output format
==========================================

## Dialect

----------------------------------- -----------------------------------------------------------------
The current dialect is md:          !dialect
All dialects:                       !dialects
Section for a markdown document:    !md(Hello World!)
Section for a rst document:         !rst(Hello World!)
----------------------------------- -----------------------------------------------------------------

## Language

----------------------------------- -----------------------------------------------------------------
The current language is "en":       !lang
All languages:                      !langs
Section in english:                 !en(Hello World!)
Section in french:                  !fr(Bonjour le monde !)
----------------------------------- -----------------------------------------------------------------

## Format

----------------------------------- -----------------------------------------------------------------
The current format is HTML:         !format
All formats:                        !formats
Section for an HTML document:       !html(Hello World!)
Section for a PDF document:         !pdf(Hello World!)
----------------------------------- -----------------------------------------------------------------

External commands and scripts execution
=======================================

!def(mymacro)(mymacro(!1) has been evaluated!)

----------------------------------- ------------------------------------------------------------------------------
Command line (uses sh):             exec(echo "hi é")
`sh` script:                        !sh(echo "hi é")
`bash` script:                      !bash(echo hi à)
`zsh` script:                       !zsh(echo hi à)
`fish` script:                      !fish(echo hi à)
`bat` script:                       !bat(echo hi ç)
`cmd` script:                       !cmd(echo hi ç)
`python` script:                    !python(print("hi"))
`python2` script:                   !python2(print "hi")
`python3` script:                   !python3(print("hi"))
`lua` script:                       !lua(print "hi")
`haskell` script:                   !haskell(main = putStrLn "hi")
`exec` script:                      !exec(!raw(echo "!def(x)(42)!x"))
`rawexec` script:                   !rawexec(!raw(echo "!def(x)(42)!x"))
Raw script output:                  !sh(printf "!%s(%d)" mymacro 42)
Preprocessed script output:         !pp[!sh(printf "!%s(%d)" mymacro 42)]
----------------------------------- ------------------------------------------------------------------------------

A Haskell Stack script:

!stack
~~~~~
{- stack script --resolver !RESOLVER --package base -}
main = putStrLn "hi"
~~~~~

A R script:

```
!Rscript
~~~~~
model = lm(dist~speed, data = cars)
summary(model)
~~~~~
```

Any other script language can be used, provided the shebang header is interpreted, with `sh`and `exec`:

!sh
~~~~~
#!/usr/bin/env python
def hi():
    print("Hello from Python")
hi()
~~~~~

!exec
~~~~~
#!/usr/bin/env lua
function hi() print "Hello from Lua" end
hi()
~~~~~

Diagrams
========

The diagram test does not check the generated images, just the links in the output document.

!dot        (dot-test)         (Test of dot)       ( digraph { dot -> { A B } -> C -> dot } )
!neato      (neato-test)       (Test of neato)     ( digraph { neato -> { A B } -> C -> neato } )
!twopi      (twopi-test)       (Test of twopi)     ( digraph { twopi -> { A B } -> C -> twopi } )
!circo      (circo-test)       (Test of circo)     ( digraph { circo -> { A B } -> C -> circo } )
!fdp        (fdp-test)         (Test of fdp)       ( digraph { fdp -> { A B } -> C -> fdp } )
!patchwork  (patchwork-test)   (Test of patchwork) ( digraph { patchwork -> { A B } -> C } )
!osage      (osage-test)       (Test of osage)     ( digraph { osage -> { A B } -> C -> osage } )

!uml        (uml-test)         (Test of uml)       ( sender -> receiver )

!ditaa      (ditaa-test)       (Test of ditaa)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

    +--------+   +-------+    +-------+
    |        | --+ ditaa +--> |       |
    |  Text  |   +-------+    |diagram|
    |Document|   |!magic!|    |       |
    |     {d}|   |       |    |       |
    +---+----+   +-------+    +-------+
        :                         ^
        |       Lots of work      |
        +-------------------------+

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!blockdiag  (blockdiag-test)    (Test of blockdiag)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A -> B
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!seqdiag    (seqdiag-test)      (Test of seqdiag)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
A -> B [label = "Hello"];
A <- B [label = "Good bye"];
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!actdiag    (actdiag-test)      (Test of actdiag)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
write -> preprocess -> enjoy
lane user {
    label = "User"
    write [label = "Write a document"]
    enjoy [label = "Enjoy the preprocessed document"]
}
lane pp {
    label = "PP"
    preprocess [label = "Preprocess a document"]
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!nwdiag     (nwdiag-test)       (Test of nwdiag)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
network dmz {
    address = "210.x.x.x/24"
    web01 [address = "210.x.x.1"];
    web02 [address = "210.x.x.2"];
}
network internal {
    address = "172.x.x.x/24";
    web01 [address = "172.x.x.1"];
    web02 [address = "172.x.x.2"];
    db01;
    db02;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!rackdiag   (rackdiag-test)     (Test of rackdiag)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
// define height of rack
16U;

// define rack items
1: UPS [2U];
3: DB Server
4: Web Server
5: Web Server
6: Web Server
7: Load Balancer
8: L3 Switch
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!packetdiag (packetdiag-test)   (Test of packetdiag)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
colwidth = 32
node_height = 72

0-15: Source Port
16-31: Destination Port
32-63: Sequence Number
64-95: Acknowledgment Number
96-99: Data Offset
100-105: Reserved
106: URG [rotate = 270]
107: ACK [rotate = 270]
108: PSH [rotate = 270]
109: RST [rotate = 270]
110: SYN [rotate = 270]
111: FIN [rotate = 270]
112-127: Window
128-143: Checksum
144-159: Urgent Pointer
160-191: (Options and Padding)
192-223: data [colheight = 3]
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!asy        (asy-test)         (Test of Asymptote)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
import math;
size(7cm,0);
add(grid(7,3));
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!dot    (size_small    {.img width=50})                        (digraph { small })

!dot    (size_smallt   {.img width=50})    (Small with title)  (digraph { small })

!dot    (size_normal   {.img width=100})                       (digraph { normal })

!dot    (size_normalt  {.img width=100})   (Normal with title) (digraph { normal })

!dot    (size_big      {.img width=150})                       (digraph { big })

!dot    (size_bigt     {.img width=150})   (Big with title)    (digraph { big })

!Rplot(rplot-test)(Test of R plot)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
plot(pressure)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Literate programming
====================

Lets write and test a useful library:

!lit{.stack-work/mylib.h}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
// This is a C library
@functionDeclarations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!lit{.stack-work/mylib.c}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
// This is a C library
@functionImplementations
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The `fib` function computes Fibonacci's numbers:

!lit{@functionDeclarations}{C}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
int fib(int n);
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!lit{@functionImplementations}{C}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
int fib(int n)
{
    return (n < 2) ? 1 : fib(n-1) + fib(n-2);
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The `fact` function computes factorial numbers:

!lit{@functionDeclarations}{int fact(int n);}
!lit{@functionImplementations}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
int fact(int n)
{
    return (n <= 1) ? 1 : n * fact(n-1);
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Some tests of `mylib.c`:

!lit{.stack-work/mylibtest.c}{C}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#include <stdio.h>
#include <stdlib.h>

#include "mylib.h"

int main(int argc, char * argv[])
{
    int i;
    for (i = 1; i < argc; i++)
    {
        int n = atoi(argv[i]);
        printf("fact(%d) = %3d; fib(%d) = %3d\n", n, fact(n), n, fib(n));
    }
    return 0;
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

!exec(gcc .stack-work/mylib.c .stack-work/mylibtest.c -o .stack-work/mylibtest)

!def(test)(mylibtest 0 1 2 3 4 5)
!def(result)(!exec(.stack-work/!test))

`!test` outputs:

~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
!result
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The complete source files are:

`mylib.h`:

!lit{.stack-work/mylib.h}

`mylib.c`:

!lit{.stack-work/mylib.c}

`mylibtest.c`:

!lit{.stack-work/mylibtest.c}

A source file, not necessarily generated with !raw(!lit) can be
formated with !raw(!source or !src):

`mylib.h`:

!src{.stack-work/mylib.h}

And with a different codeblock separator:

15 '~':

!codeblock(15)(~)!src{.stack-work/mylib.h}

20 '`':

!codeblock(20)(`)!src{.stack-work/mylib.h}

25 '~' (default character):

!codeblock(25)!src{.stack-work/mylib.h}

CSV tables
==========

This file with a header:

!lit(/tmp/table1.csv)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Year,Make,Model,Description,Price
1997,Ford,E350,"ac, abs, moon",3000.00
1999,Chevy,"Venture ""Extended Edition""","",4900.00
1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00
1996,Jeep,Grand Cherokee,"MUST SELL!
air, moon roof, loaded",4799.00
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
!flushlit

is rendered by `!raw(!csv(file.csv))` as:

!csv(/tmp/table1.csv)

This file without any header:

!lit(/tmp/table2.csv)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
1997,Ford,E350,"ac, abs, moon",3000.00
1999,Chevy,"Venture ""Extended Edition""","",4900.00
1999,Chevy,"Venture ""Extended Edition, Very Large""",,5000.00
1996,Jeep,Grand Cherokee,"MUST SELL!
air, moon roof, loaded",4799.00
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
!flushlit

is rendered by `!raw(!csv(file.csv)(Year|Make|Model|Description|Price))` as:

!csv(/tmp/table2.csv)(Year|Make|Model|Description|Price)

Parser customization
====================

!def(foo)(foo has been called)
!def(bar)(bar:arg1="!1" - text after macro call:)

## Macro calls

Test the default behaviour:

Inactive: `+` => +foo
Inactive: `=` => =foo
Inactive: `\` => \foo
Active  : `!` => !foo

Set new macro chars: `+=` !macrochars(+=)

Active  : `+` => +foo
Active  : `=` => =foo
Inactive: `\` => \foo
Inactive: `!` => !foo

Restore the default behaviour: `!` +macrochars(!)

Inactive: `+` => +foo
Inactive: `=` => =foo
Inactive: `\` => \foo
Active  : `!` => !foo

## Macro args

Test the default behaviour:

Active  : `()` => !bar(42)
Active  : `{}` => !bar{42}
Active  : `[]` => !bar[42]
Inactive: `<>` => !bar<42>
Inactive: `«»` => !bar«42»

Set new parameter separators: `<>«»` !macroargs( <> «» )

Inactive: `()` => !bar(42)
Inactive: `{}` => !bar{42}
Inactive: `[]` => !bar[42]
Active  : `<>` => !bar<42>
Active  : `«»` => !bar«42»

Restore the default behaviour: `(){}[]` !macroargs«(){}[]»

Active  : `()` => !bar(42)
Active  : `{}` => !bar{42}
Active  : `[]` => !bar[42]
Inactive: `<>` => !bar<42>
Inactive: `«»` => !bar«42»

## Literate macros

Set new literate macro chars: `$` !literatemacrochars($)

!lit(.stack-work/foo)($foo - $foo)
!lit($foo)(I'm foo)

Result: !flushlit

```
!rawinclude(.stack-work/foo)
```

Restore the default behaviour: `@` !literatemacrochars(@)

Builtin macro documentation
===========================

## macros

!macros

## help

!help

User macro documentation
========================

!def(foo)(foo's docstring)(great macro)
!def(useless)(this macro is not documented)
!def(bar)(bar's docstring)(another great macro)
!def(_internal_)(internal docstring)(internal macro not listed)

!usermacros

!userhelp
