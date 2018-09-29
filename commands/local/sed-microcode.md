# sed in (pseudo) microcode

by Carlos J. Duarte.

[ This article was posted to the seders mailing list ]

Al, this is a little doc, with some pseudo code, of a sed implementation.

The idea, is when people (that knows sed), has a question or need some fine tunning, to quickly answered themselves.

One particulary example, are the strange behaviors of D, and a\ (well, sort of). 

As a last note remark, I am revieing my scripts packages, and also some documentation on sed (do-it-with-sed, advanced-sed, hints-sed? or sed-fine-tunning). This should be ok on august. 

For now (upto end of july), I will not (probably), dedicate any time to that.

So, there goes the text.

## ADDRESSES

(0) no address, just the command per se

(1) a single address or none, is permitted

(2) a single address, a double address, or none is permitted

any address, if specified, may be a number -- specifies the input line number, or a regular expression -- specifies all pattern spaces, that matches it.

one address, appears per se, before the command two addresses, appears separated per a comma `,`

if an address is omitted, the command will be apllied on all pattern spaces that passes through it

```
y/a/b/                  executes on all ps
3 y/a/b/                executes on ps which last line loaded, was 3
/foo/ y/a/b/            executes on ps that matches /foo/
4,/foo/ y/a/b/          executes from ps which last line loaded was 4,
                        up to, including, ps that matches /foo/
/foo/,5 y/a/b/          as above, but from /foo/ up to 5
```

## COMMANDS MICROCODE

***notes:***
- first, everything begins with a (sic) `goto [end]`, after
  all variables have been zeroed/offed/emptyed

- all commands, have a `pop ps` and a `push ps`, to enforce that
  they really operate on patern space

- the `print ps to stdout` `microcode` below, prints an extra newline
  after the print the `ps` itself

        [end]
        . pop ps
        . if not nflag and line_counter>=1, print ps to stdout
        . print append_buffer to stdout (which might be empty)
        . empty append_buffer
        . set tflag off (see s, t)
        . if come_from_D
        .     set come_from_D off (see D)
        . else
        .     if no more input lines, exit
        .     load next line into ps, increment line_counter
        . push ps
        . goto start

**(2) s/RE/replacement/[gp#w], #: 1-9**

        . pop ps
        . if # not given, or g given, #=1
        . set last_match = begin of ps
        !! do not set tflag off (see t, [end])
        [1]
        . repeat # searches for RE, on ps, from last_match to end
        . if found
        .     tflag on
        .     replace the found match, per `replacement'
        .     if w file, append ps at end of `file'
        .     if p, print ps to stdout
        .     if g, set last_match to end of replacement+1 and goto [1]
        . push ps


**(2) y/list1/list2/**

        . pop ps
        . foreach character c1 on list1
        .     fetch character c2 from list2
        .     replace all occurrences of c1 per c2
        . push ps

**(1)i\\<br/>
text**

        . print text to stdout immediatly


**(1)a\\<br/>
text**

        . collect (i.e append) text to append_buffer (see [end])

**(2)c\\<br/>
text**

        . print text to stdout immediatly
        . pop ps
        . empty ps
        . push ps
        . goto [end]
        !! because ps is empty, the `print ps' at [end] will not
           produce output

**(1)r file**

        . print contents of file, immediatly to stdout

**(2)w file**

        . pop ps
        . append ps at end of file
        . push ps


**(0): label**

        . name next command, as `label'

**(2)b label**

        . goto command named `label'

**(2)t label**

        . if tflag on (see s///)
        .     set tflag off
        .     goto command `label'

**(1)q**

        . pop ps
        . if not nflag, print ps to stdout
        . print append_buffer (might be empty, which cause no output)
        . exit

**(2)p**

        . pop ps
        . print ps to stdout
        . push ps

**(2)l**

        . pop ps
        . copy ps to tmp
        . replace on tmp, some special characters, per their
          conventional equivalents \x
        . print tmp
        . push ps

**(1)=**

        . print line_counter to stdout

**(2)n**

        . if no more lines on input, goto [end]
        . pop ps
        . if not nflag, print ps to stdout
        . load next line into ps, increment line_counter
        . push ps
        !! and continues, does not jump to start, or end

**(2)d**

        . pop ps
        . empty ps
        . push ps
        . goto [end]

**(2)h**

        . pop ps
        . set hold_buffer as ps
        . push ps

**(2)H**

        . pop ps
        . set hold_buffer to hold_buffer+\n+ps
        . push ps

**(2)g**

        . pop ps
        . set ps as hold_buffer
        . push ps

**(2)G**

        . pop ps
        . set ps as ps+\n+hold_buffer
        . push ps

**(2)x**

        . pop ps
        . push hold_buffer
        . set hold_buffer as ps
        !! exchange hold buffer with pattern space

**(2)N**

        . if no more lines on input, goto [end]
        . pop ps
        . load next line into tmp, increment line_counter
        . set ps to ps+\n+tmp
        . push ps
        !! and continues, does not jump to start, or end

**(2)P**

        . pop ps
        . copy ps to tmp
        . print remove from first \n upto end
        . print ps to stdout
        . push tmp

**(2)D**

        . pop ps
        . if ps does not contain a \n
        .     empty ps
        .     push ps
        .     goto [end]
        !! i.e. does the same as `d'
        . remove from beginning of ps, upto, including, first \n
        . push ps
        . set come_from_D on (see [end])
        . goto [end]
        !! do not load next line, if ps contained \n


---
Carlos Duarte, 980712
