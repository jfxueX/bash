## 12 Advanced Features of `gawk`

> Write documentation as if whoever reads it is
> a violent psychopath who knows where you live.

&mdash; *Steve English, as quoted by Peter Langston*

This chapter discusses advanced features in `gawk`.
It&rsquo;s a bit of a &ldquo;grab bag&rdquo; of items that are otherwise unrelated
to each other.
First, we look at a command-line option that allows `gawk` to recognize
nondecimal numbers in input data, not just in `awk`
programs.
Then, `gawk`&rsquo;s special features for sorting arrays are presented.
Next, two-way I/O, discussed briefly in earlier parts of this
Web page, is described in full detail, along with the basics
of TCP/IP networking.  Finally, we see how `gawk`
can *profile* an `awk` program, making it possible to tune
it for performance.

Additional advanced features are discussed in separate chapters of their
own:

- [Internationalization](#Internationalization), discusses how to internationalize
your `awk` programs, so that they can speak multiple
national languages.

- [Debugger](#Debugger), describes `gawk`&rsquo;s built-in command-line
debugger for debugging `awk` programs.

- [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic), describes how you can use
`gawk` to perform arbitrary-precision arithmetic.

- [Dynamic Extensions](#Dynamic-Extensions),
discusses the ability to dynamically add new built-in functions to
`gawk`.

&bull; [Nondecimal Data](#Nondecimal-Data):  Allowing nondecimal input data.
&bull; [Array Sorting](#Array-Sorting):  Facilities for controlling array traversal and
                                sorting arrays.
&bull; [Two-way I/O](#Two_002dway-I_002fO):  Two-way communications with another process.
&bull; [TCP/IP Networking](#TCP_002fIP-Networking):  Using `gawk` for network programming.
&bull; [Profiling](#Profiling):  Profiling your `awk` programs.
&bull; [Advanced Features Summary](#Advanced-Features-Summary):  Summary of advanced features.

---

Next: [Array Sorting](#Array-Sorting), Up: [Advanced Features](#Advanced-Features)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 12.1 Allowing Nondecimal Input Data

If you run `gawk` with the --non-decimal-data option,
you can have nondecimal values in your input data:

    $ echo 0123 123 0x123 |
    > gawk --non-decimal-data '{ printf "%d, %d, %d\n", $1, $2, $3 }'
    -| 83, 123, 291
    

For this feature to work, write your program so that
`gawk` treats your data as numeric:

    $ echo 0123 123 0x123 | gawk '{ print $1, $2, $3 }'
    -| 0123 123 0x123
    

The `print` statement treats its expressions as strings.
Although the fields can act as numbers when necessary,
they are still strings, so `print` does not try to treat them
numerically.  You need to add zero to a field to force it to
be treated as a number.  For example:

    $ echo 0123 123 0x123 | gawk --non-decimal-data '
    > { print $1, $2, $3
    >   print $1 + 0, $2 + 0, $3 + 0 }'
    -| 0123 123 0x123
    -| 83 123 291
    

Because it is common to have decimal data with leading zeros, and because
using this facility could lead to surprising results, the default is to leave it
disabled.  If you want it, you must explicitly request it.

> CAUTION:*Use of this option is not recommended.*
> It can break old programs very badly.
> Instead, use the `strtonum()` function to convert your data
> (see [String Functions](#String-Functions)).
> This makes your programs easier to write and easier to read, and
> leads to less surprising results.
> 
> 
> This option may disappear in a future version of `gawk`.

---

Next: [Two-way I/O](#Two_002dway-I_002fO), Previous: [Nondecimal Data](#Nondecimal-Data), Up: [Advanced Features](#Advanced-Features)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 12.2 Controlling Array Traversal and Array Sorting

`gawk` lets you control the order in which a
&lsquo;for (indx in array)&rsquo;
loop traverses an array.

In addition, two built-in functions, `asort()` and `asorti()`,
let you sort arrays based on the array values and indices, respectively.
These two functions also provide control over the sorting criteria used
to order the elements during sorting.

&bull; [Controlling Array Traversal](#Controlling-Array-Traversal):  How to use PROCINFO["sorted_in"].
&bull; [Array Sorting Functions](#Array-Sorting-Functions):  How to use `asort()` and `asorti()`.

---

Next: [Array Sorting Functions](#Array-Sorting-Functions), Up: [Array Sorting](#Array-Sorting)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 12.2.1 Controlling Array Traversal

By default, the order in which a &lsquo;for (indx in array)&rsquo; loop
scans an array is not defined; it is generally based upon
the internal implementation of arrays inside `awk`.

Often, though, it is desirable to be able to loop over the elements
in a particular order that you, the programmer, choose.  `gawk`
lets you do this.

[Controlling Scanning](#Controlling-Scanning) describes how you can assign special,
predefined values to `PROCINFO["sorted_in"]` in order to
control the order in which `gawk` traverses an array
during a `for` loop.

In addition, the value of `PROCINFO["sorted_in"]` can be a
function name.[84](#FOOT84)
This lets you traverse an array based on any custom criterion.
The array elements are ordered according to the return value of this
function.  The comparison function should be defined with at least
four arguments:

    function comp_func(i1, v1, i2, v2)
    {
        compare elements 1 and 2 in some fashionreturn < 0; 0; or > 0
    }
    

Here, `i1` and `i2` are the indices, and `v1` and `v2`
are the corresponding values of the two elements being compared.
Either `v1` or `v2`, or both, can be arrays if the array being
traversed contains subarrays as values.
(See [Arrays of Arrays](#Arrays-of-Arrays) for more information about subarrays.)
The three possible return values are interpreted as follows:

`comp_func(i1, v1, i2, v2) < 0`
Index `i1` comes before index `i2` during loop traversal.

`comp_func(i1, v1, i2, v2) == 0`
Indices `i1` and `i2`
come together, but the relative order with respect to each other is undefined.

`comp_func(i1, v1, i2, v2) > 0`
Index `i1` comes after index `i2` during loop traversal.

Our first comparison function can be used to scan an array in
numerical order of the indices:

    function cmp_num_idx(i1, v1, i2, v2)
    {
         # numerical index comparison, ascending order
         return (i1 - i2)
    }
    

Our second function traverses an array based on the string order of
the element values rather than by indices:

    function cmp_str_val(i1, v1, i2, v2)
    {
        # string value comparison, ascending order
        v1 = v1 ""
        v2 = v2 ""
        if (v1 < v2)
            return -1
        return (v1 != v2)
    }
    

The third
comparison function makes all numbers, and numeric strings without
any leading or trailing spaces, come out first during loop traversal:

    function cmp_num_str_val(i1, v1, i2, v2,   n1, n2)
    {
         # numbers before string value comparison, ascending order
         n1 = v1 + 0
         n2 = v2 + 0
         if (n1 == v1)
             return (n2 == v2) ? (n1 - n2) : -1
         else if (n2 == v2)
             return 1
         return (v1 < v2) ? -1 : (v1 != v2)
    }
    

Here is a main program to demonstrate how `gawk`
behaves using each of the previous functions:

    BEGIN {
        data["one"] = 10
        data["two"] = 20
        data[10] = "one"
        data[100] = 100
        data[20] = "two"
    
        f[1] = "cmp_num_idx"
        f[2] = "cmp_str_val"
        f[3] = "cmp_num_str_val"
        for (i = 1; i <= 3; i++) {
            printf("Sort function: %s\n", f[i])
            PROCINFO["sorted_in"] = f[i]
            for (j in data)
                printf("\tdata[%s] = %s\n", j, data[j])
            print ""
        }
    }
    

Here are the results when the program is run:

    $ gawk -f compdemo.awk
    -| Sort function: cmp_num_idx      Sort by numeric index
    -|     data[two] = 20
    -|     data[one] = 10              Both strings are numerically zero
    -|     data[10] = one
    -|     data[20] = two
    -|     data[100] = 100
    -|
    -| Sort function: cmp_str_val      Sort by element values as strings
    -|     data[one] = 10
    -|     data[100] = 100             String 100 is less than string 20
    -|     data[two] = 20
    -|     data[10] = one
    -|     data[20] = two
    -|
    -| Sort function: cmp_num_str_val  Sort all numeric values before all strings
    -|     data[one] = 10
    -|     data[two] = 20
    -|     data[100] = 100
    -|     data[10] = one
    -|     data[20] = two
    

Consider sorting the entries of a GNU/Linux system password file
according to login name.  The following program sorts records
by a specific field position and can be used for this purpose:

    # passwd-sort.awk --- simple program to sort by field position
    # field position is specified by the global variable POS
    
    function cmp_field(i1, v1, i2, v2)
    {
        # comparison by value, as string, and ascending order
        return v1[POS] < v2[POS] ? -1 : (v1[POS] != v2[POS])
    }
    
    {
        for (i = 1; i <= NF; i++)
            a[NR][i] = $i
    }
    
    

    END {
        PROCINFO["sorted_in"] = "cmp_field"
    

        if (POS < 1 || POS > NF)
            POS = 1
    
        for (i in a) {
            for (j = 1; j <= NF; j++)
                printf("%s%c", a[i][j], j < NF ? ":" : "")
            print ""
        }
    }
    

The first field in each entry of the password file is the user&rsquo;s login name,
and the fields are separated by colons.
Each record defines a subarray,
with each field as an element in the subarray.
Running the program produces the
following output:

    $ gawk -v POS=1 -F: -f sort.awk /etc/passwd
    -| adm:x:3:4:adm:/var/adm:/sbin/nologin
    -| apache:x:48:48:Apache:/var/www:/sbin/nologin
    -| avahi:x:70:70:Avahi daemon:/:/sbin/nologin
    &hellip;
    

The comparison should normally always return the same value when given a
specific pair of array elements as its arguments.  If inconsistent
results are returned, then the order is undefined.  This behavior can be
exploited to introduce random order into otherwise seemingly
ordered data:

    function cmp_randomize(i1, v1, i2, v2)
    {
        # random order (caution: this may never terminate!)
        return (2 - 4 * rand())
    }
    

As already mentioned, the order of the indices is arbitrary if two
elements compare equal.  This is usually not a problem, but letting
the tied elements come out in arbitrary order can be an issue, especially
when comparing item values.  The partial ordering of the equal elements
may change the next time the array is traversed, if other elements are added to or
removed from the array.  One way to resolve ties when comparing elements
with otherwise equal values is to include the indices in the comparison
rules.  Note that doing this may make the loop traversal less efficient,
so consider it only if necessary.  The following comparison functions
force a deterministic order, and are based on the fact that the
(string) indices of two elements are never equal:

    function cmp_numeric(i1, v1, i2, v2)
    {
        # numerical value (and index) comparison, descending order
        return (v1 != v2) ? (v2 - v1) : (i2 - i1)
    }
    
    

    function cmp_string(i1, v1, i2, v2)
    {
        # string value (and index) comparison, descending order
        v1 = v1 i1
        v2 = v2 i2
        return (v1 > v2) ? -1 : (v1 != v2)
    }
    

A custom comparison function can often simplify ordered loop
traversal, and the sky is really the limit when it comes to
designing such a function.

When string comparisons are made during a sort, either for element
values where one or both aren&rsquo;t numbers, or for element indices
handled as strings, the value of `IGNORECASE`
(see [Built-in Variables](#Built_002din-Variables)) controls whether
the comparisons treat corresponding upper- and lowercase letters as
equivalent or distinct.

Another point to keep in mind is that in the case of subarrays,
the element values can themselves be arrays; a production comparison
function should use the `isarray()` function
(see [Type Functions](#Type-Functions))
to check for this, and choose a defined sorting order for subarrays.

All sorting based on `PROCINFO["sorted_in"]`
is disabled in POSIX mode,
because the `PROCINFO` array is not special in that case.

As a side note, sorting the array indices before traversing
the array has been reported to add a 15% to 20% overhead to the
execution time of `awk` programs. For this reason,
sorted array traversal is not the default.

---

Previous: [Controlling Array Traversal](#Controlling-Array-Traversal), Up: [Array Sorting](#Array-Sorting)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 12.2.2 Sorting Array Values and Indices with `gawk`

In most `awk` implementations, sorting an array requires writing
a `sort()` function.  This can be educational for exploring
different sorting algorithms, but usually that&rsquo;s not the point of the program.
`gawk` provides the built-in `asort()` and `asorti()`
functions (see [String Functions](#String-Functions)) for sorting arrays.  For example:

    populate the array data
    n = asort(data)
    for (i = 1; i <= n; i++)
        do something with data[i]
    

After the call to `asort()`, the array `data` is indexed from 1
to some number n, the total number of elements in `data`.
(This count is `asort()`&rsquo;s return value.)
`data[1]` <= `data[2]` <= `data[3]`, and so on.
The default comparison is based on the type of the elements
(see [Typing and Comparison](#Typing-and-Comparison)).
All numeric values come before all string values,
which in turn come before all subarrays.

An important side effect of calling `asort()` is that
*the array&rsquo;s original indices are irrevocably lost*.
As this isn&rsquo;t always desirable, `asort()` accepts a
second argument:

    populate the array source
    n = asort(source, dest)
    for (i = 1; i <= n; i++)
        do something with dest[i]
    

In this case, `gawk` copies the `source` array into the
`dest` array and then sorts `dest`, destroying its indices.
However, the `source` array is not affected.

Often, what&rsquo;s needed is to sort on the values of the *indices*
instead of the values of the elements.  To do that, use the
`asorti()` function.  The interface and behavior are identical to
that of `asort()`, except that the index values are used for sorting
and become the values of the result array:

    { source[$0] = some_func($0) }
    
    END {
        n = asorti(source, dest)
        for (i = 1; i <= n; i++) {
            Work with sorted indices directly:do something with dest[i]
            &hellip;
            Access original array via sorted indices:do something with source[dest[i]]
        }
    }
    

So far, so good. Now it starts to get interesting.  Both `asort()`
and `asorti()` accept a third string argument to control comparison
of array elements.  When we introduced `asort()` and `asorti()`
in [String Functions](#String-Functions), we ignored this third argument; however,
now is the time to describe how this argument affects these two functions.

Basically, the third argument specifies how the array is to be sorted.
There are two possibilities.  As with `PROCINFO["sorted_in"]`,
this argument may be one of the predefined names that `gawk`
provides (see [Controlling Scanning](#Controlling-Scanning)), or it may be the name of a
user-defined function (see [Controlling Array Traversal](#Controlling-Array-Traversal)).

In the latter case, *the function can compare elements in any way
it chooses*, taking into account just the indices, just the values,
or both.  This is extremely powerful.

Once the array is sorted, `asort()` takes the *values* in
their final order and uses them to fill in the result array, whereas
`asorti()` takes the *indices* in their final order and uses
them to fill in the result array.

> NOTE: Copying array indices and elements isn&rsquo;t expensive in terms of memory.
> Internally, `gawk` maintains *reference counts* to data.
> For example, when `asort()` copies the first array to the second one,
> there is only one copy of the original array elements&rsquo; data, even though
> both arrays use the values.

Because `IGNORECASE` affects string comparisons, the value
of `IGNORECASE` also affects sorting for both `asort()` and `asorti()`.
Note also that the locale&rsquo;s sorting order does *not*
come into play; comparisons are based on character values only.[85](#FOOT85)

The following example demonstrates the use of a comparison function with
`asort()`.  The comparison function, `case_fold_compare()`, maps
both values to lowercase in order to compare them ignoring case.

    # case_fold_compare --- compare as strings, ignoring case
    
    function case_fold_compare(i1, v1, i2, v2,    l, r)
    {
        l = tolower(v1)
    

        r = tolower(v2)
    
        if (l < r)
            return -1
        else if (l == r)
            return 0
        else
            return 1
    }
    

And here is the test program for it:

    # Test program
    
    BEGIN {
        Letters = "abcdefghijklmnopqrstuvwxyz" \
                  "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
        split(Letters, data, "")
    
        asort(data, result, "case_fold_compare")
    
        j = length(result)
        for (i = 1; i <= j; i++) {
            printf("%s", result[i])
            if (i % (j/2) == 0)
                printf("\n")
            else
                printf(" ")
        }
    }
    

When run, we get the following:

    $ gawk -f case_fold_compare.awk
    -| A a B b c C D d e E F f g G H h i I J j k K l L M m
    -| n N O o p P Q q r R S s t T u U V v w W X x y Y z Z
    

---

Next: [TCP/IP Networking](#TCP_002fIP-Networking), Previous: [Array Sorting](#Array-Sorting), Up: [Advanced Features](#Advanced-Features)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 12.3 Two-Way Communications with Another Process

It is often useful to be able to
send data to a separate program for
processing and then read the result.  This can always be
done with temporary files:

    # Write the data for processing
    tempfile = ("mydata." PROCINFO["pid"])
    while (not done with data)
        print data | ("subprogram > " tempfile)
    close("subprogram > " tempfile)
    
    # Read the results, remove tempfile when done
    while ((getline newdata < tempfile) > 0)
        process newdata appropriately
    close(tempfile)
    system("rm " tempfile)
    

This works, but not elegantly.  Among other things, it requires that
the program be run in a directory that cannot be shared among users;
for example, /tmp will not do, as another user might happen
to be using a temporary file with the same name.[86](#FOOT86)

However, with `gawk`, it is possible to
open a *two-way* pipe to another process.  The second process is
termed a *coprocess*, as it runs in parallel with `gawk`.
The two-way connection is created using the &lsquo;|&&rsquo; operator
(borrowed from the Korn shell, `ksh`):[87](#FOOT87)

    do {
        print data |& "subprogram"
        "subprogram" |& getline results
    } while (data left to process)
    close("subprogram")
    

The first time an I/O operation is executed using the &lsquo;|&&rsquo;
operator, `gawk` creates a two-way pipeline to a child process
that runs the other program.  Output created with `print`
or `printf` is written to the program&rsquo;s standard input, and
output from the program&rsquo;s standard output can be read by the `gawk`
program using `getline`.
As is the case with processes started by &lsquo;|&rsquo;, the subprogram
can be any program, or pipeline of programs, that can be started by
the shell.

There are some cautionary items to be aware of:

-  As the code inside `gawk` currently stands, the coprocess&rsquo;s
standard error goes to the same place that the parent `gawk`&rsquo;s
standard error goes. It is not possible to read the child&rsquo;s
standard error separately.

- 
I/O buffering may be a problem.  `gawk` automatically
flushes all output down the pipe to the coprocess.
However, if the coprocess does not flush its output,
`gawk` may hang when doing a `getline` in order to read
the coprocess&rsquo;s results.  This could lead to a situation
known as *deadlock*, where each process is waiting for the
other one to do something.

It is possible to close just one end of the two-way pipe to
a coprocess, by supplying a second argument to the `close()`
function of either `"to"` or `"from"`
(see [Close Files And Pipes](#Close-Files-And-Pipes)).
These strings tell `gawk` to close the end of the pipe
that sends data to the coprocess or the end that reads from it,
respectively.

This is particularly necessary in order to use
the system `sort` utility as part of a coprocess;
`sort` must read *all* of its input
data before it can produce any output.
The `sort` program does not receive an end-of-file indication
until `gawk` closes the write end of the pipe.

When you have finished writing data to the `sort`
utility, you can close the `"to"` end of the pipe, and
then start reading sorted data via `getline`.
For example:

    BEGIN {
        command = "LC_ALL=C sort"
        n = split("abcdefghijklmnopqrstuvwxyz", a, "")
    
        for (i = n; i > 0; i--)
            print a[i] |& command
        close(command, "to")
    
        while ((command |& getline line) > 0)
            print "got", line
        close(command)
    }
    

This program writes the letters of the alphabet in reverse order, one
per line, down the two-way pipe to `sort`.  It then closes the
write end of the pipe, so that `sort` receives an end-of-file
indication.  This causes `sort` to sort the data and write the
sorted data back to the `gawk` program.  Once all of the data
has been read, `gawk` terminates the coprocess and exits.

As a side note, the assignment &lsquo;LC_ALL=C&rsquo; in the `sort`
command ensures traditional Unix (ASCII) sorting from `sort`.
This is not strictly necessary here, but it&rsquo;s good to know how to do this.

Be careful when closing the `"from"` end of a two-way pipe; in this
case `gawk` waits for the child process to exit, which may cause
your program to hang.  (Thus, this particular feature is of much less
use in practice than being able to close the `"to"` end.)

> CAUTION: Normally,
> it is a fatal error to write to the `"to"` end of a two-way
> pipe which has been closed, and it is also a fatal error to read
> from the `"from"` end of a two-way pipe that has been closed.
> 
> 
> You may set `PROCINFO["command", "NONFATAL"]` to
> make such operations become nonfatal. If you do so, you then need
> to check `ERRNO` after each `print`, `printf`,
> or `getline`.
> See [Nonfatal](#Nonfatal), for more information.

You may also use pseudo-ttys (ptys) for
two-way communication instead of pipes, if your system supports them.
This is done on a per-command basis, by setting a special element
in the `PROCINFO` array
(see [Auto-set](#Auto_002dset)),
like so:

    command = "sort -nr"           # command, save in convenience variable
    PROCINFO[command, "pty"] = 1   # update PROCINFO
    print &hellip; |& command           # start two-way pipe
    &hellip;
    

If your system does not have ptys, or if all the system&rsquo;s ptys are in use,
`gawk` automatically falls back to using regular pipes.

Using ptys usually avoids the buffer deadlock issues described earlier,
at some loss in performance. This is because the tty driver buffers
and sends data line-by-line.  On systems with the `stdbuf`
(part of the [GNU Coreutils package](https://www.gnu.org/software/coreutils/coreutils.html)), you can use that program instead of ptys.

Note also that ptys are not fully transparent. Certain binary control
codes, such Ctrl-d for end-of-file, are interpreted by the tty
driver and not passed through.

> CAUTION: Finally, coprocesses open up the possibility of *deadlock* between
> `gawk` and the program running in the coprocess. This can occur
> if you send &ldquo;too much&rdquo; data to the coprocess before reading any back;
> each process is blocked writing data with noone available to read what
> they&rsquo;ve already written.  There is no workaround for deadlock; careful
> programming and knowledge of the behavior of the coprocess are required.

---

Next: [Profiling](#Profiling), Previous: [Two-way I/O](#Two_002dway-I_002fO), Up: [Advanced Features](#Advanced-Features)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 12.4 Using `gawk` for Network Programming

> `EMRED`:
> 
>     A host is a host from coast to coast,
> 
>     and nobody talks to a host that&rsquo;s close,
> 
>     unless the host that isn&rsquo;t close
> 
>     is busy, hung, or dead.

&mdash; *Mike O&rsquo;Brien (aka Mr. Protocol)*

In addition to being able to open a two-way pipeline to a coprocess
on the same system
(see [Two-way I/O](#Two_002dway-I_002fO)),
it is possible to make a two-way connection to
another process on another system across an IP network connection.

You can think of this as just a *very long* two-way pipeline to
a coprocess.
The way `gawk` decides that you want to use TCP/IP networking is
by recognizing special file names that begin with one of &lsquo;/inet/&rsquo;,
&lsquo;/inet4/&rsquo;, or &lsquo;/inet6/&rsquo;.

The full syntax of the special file name is
/net-type/protocol/local-port/remote-host/remote-port.
The components are:

net-type
Specifies the kind of Internet connection to make.
Use &lsquo;/inet4/&rsquo; to force IPv4, and
&lsquo;/inet6/&rsquo; to force IPv6.
Plain &lsquo;/inet/&rsquo; (which used to be the only option) uses
the system default, most likely IPv4.

protocol
The protocol to use over IP.  This must be either &lsquo;tcp&rsquo;, or
&lsquo;udp&rsquo;, for a TCP or UDP IP connection,
respectively.  TCP should be used for most applications.

local-port
The local TCP or UDP port number to use.  Use a port number of &lsquo;0&rsquo;
when you want the system to pick a port. This is what you should do
when writing a TCP or UDP client.
You may also use a well-known service name, such as &lsquo;smtp&rsquo;
or &lsquo;http&rsquo;, in which case `gawk` attempts to determine
the predefined port number using the C `getaddrinfo()` function.

remote-host
The IP address or fully qualified domain name of the Internet
host to which you want to connect.

remote-port
The TCP or UDP port number to use on the given remote-host.
Again, use &lsquo;0&rsquo; if you don&rsquo;t care, or else a well-known
service name.

> NOTE: Failure in opening a two-way socket will result in a nonfatal error
> being returned to the calling code. The value of `ERRNO` indicates
> the error (see [Auto-set](#Auto_002dset)).

Consider the following very simple example:

    BEGIN {
        Service = "/inet/tcp/0/localhost/daytime"
        Service |& getline
        print $0
        close(Service)
    }
    

This program reads the current date and time from the local system&rsquo;s
TCP `daytime` server.
It then prints the results and closes the connection.

Because this topic is extensive, the use of `gawk` for
TCP/IP programming is documented separately.
See
[TCP/IP Internetworking with `gawk`](https://www.gnu.org/software/gawk/manual/gawkinet/),
which comes as part of the `gawk` distribution,
for a much more complete introduction and discussion, as well as
extensive examples.

> NOTE:`gawk` can only open direct sockets. There is currently
> no way to access services available over Secure Socket Layer
> (SSL); this includes any web service whose URL starts with &lsquo;https://&rsquo;.

---

Next: [Advanced Features Summary](#Advanced-Features-Summary), Previous: [TCP/IP Networking](#TCP_002fIP-Networking), Up: [Advanced Features](#Advanced-Features)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 12.5 Profiling Your `awk` Programs

You may produce execution traces of your `awk` programs.
This is done by passing the option --profile to `gawk`.
When `gawk` has finished running, it creates a profile of your program in a file
named awkprof.out. Because it is profiling, it also executes up to 45% slower than
`gawk` normally does.

As shown in the following example,
the --profile option can be used to change the name of the file
where `gawk` will write the profile:

    gawk --profile=myprog.prof -f myprog.awk data1 data2
    

In the preceding example, `gawk` places the profile in
myprog.prof instead of in awkprof.out.

Here is a sample session showing a simple `awk` program,
its input data, and the results from running `gawk` with the
--profile option.  First, the `awk` program:

    BEGIN { print "First BEGIN rule" }
    
    END { print "First END rule" }
    
    /foo/ {
        print "matched /foo/, gosh"
        for (i = 1; i <= 3; i++)
            sing()
    }
    
    {
        if (/foo/)
            print "if is true"
        else
            print "else is true"
    }
    
    BEGIN { print "Second BEGIN rule" }
    
    END { print "Second END rule" }
    
    function sing(    dummy)
    {
        print "I gotta be me!"
    }
    

Following is the input data:

    foo
    bar
    baz
    foo
    junk
    

Here is the awkprof.out that results from running the
`gawk` profiler on this program and data (this example also
illustrates that `awk` programmers sometimes get up very early
in the morning to work):

        # gawk profile, created Mon Sep 29 05:16:21 2014
    
        # BEGIN rule(s)
    
        BEGIN {
     1          print "First BEGIN rule"
        }
    
        BEGIN {
     1          print "Second BEGIN rule"
        }
    
        # Rule(s)
    
     5  /foo/ { # 2
     2          print "matched /foo/, gosh"
     6          for (i = 1; i <= 3; i++) {
     6                  sing()
                }
        }
    
     5  {
     5          if (/foo/) { # 2
     2                  print "if is true"
     3          } else {
     3                  print "else is true"
                }
        }
    
        # END rule(s)
    
        END {
     1          print "First END rule"
        }
    
        END {
     1          print "Second END rule"
        }
    
    
        # Functions, listed alphabetically
    
     6  function sing(dummy)
        {
     6          print "I gotta be me!"
        }
    

This example illustrates many of the basic features of profiling output.
They are as follows:

-  The program is printed in the order `BEGIN` rules,
`BEGINFILE` rules,
pattern&ndash;action rules,
`ENDFILE` rules, `END` rules, and functions, listed
alphabetically.
Multiple `BEGIN` and `END` rules retain their
separate identities, as do
multiple `BEGINFILE` and `ENDFILE` rules.

- 
Pattern&ndash;action rules have two counts.
The first count, to the left of the rule, shows how many times
the rule&rsquo;s pattern was *tested*.
The second count, to the right of the rule&rsquo;s opening left brace
in a comment,
shows how many times the rule&rsquo;s action was *executed*.
The difference between the two indicates how many times the rule&rsquo;s
pattern evaluated to false.

-  Similarly,
the count for an `if`-`else` statement shows how many times
the condition was tested.
To the right of the opening left brace for the `if`&rsquo;s body
is a count showing how many times the condition was true.
The count for the `else`
indicates how many times the test failed.

- 
The count for a loop header (such as `for`
or `while`) shows how many times the loop test was executed.
(Because of this, you can&rsquo;t just look at the count on the first
statement in a rule to determine how many times the rule was executed.
If the first statement is a loop, the count is misleading.)

- 
For user-defined functions, the count next to the `function`
keyword indicates how many times the function was called.
The counts next to the statements in the body show how many times
those statements were executed.

- 
The layout uses &ldquo;K&R&rdquo; style with TABs.
Braces are used everywhere, even when
the body of an `if`, `else`, or loop is only a single statement.

- 
Parentheses are used only where needed, as indicated by the structure
of the program and the precedence rules.
For example, &lsquo;(3 + 5) * 4&rsquo; means add three and five, then multiply
the total by four.  However, &lsquo;3 + 5 * 4&rsquo; has no parentheses, and
means &lsquo;3 + (5 * 4)&rsquo;.

-  Parentheses are used around the arguments to `print`
and `printf` only when
the `print` or `printf` statement is followed by a redirection.
Similarly, if
the target of a redirection isn&rsquo;t a scalar, it gets parenthesized.

- `gawk` supplies leading comments in
front of the `BEGIN` and `END` rules,
the `BEGINFILE` and `ENDFILE` rules,
the pattern&ndash;action rules, and the functions.

The profiled version of your program may not look exactly like what you
typed when you wrote it.  This is because `gawk` creates the
profiled version by &ldquo;pretty-printing&rdquo; its internal representation of
the program.  The advantage to this is that `gawk` can produce
a standard representation.
Also, things such as:

    /foo/
    

come out as:

    /foo/   {
        print $0
    }
    

which is correct, but possibly unexpected.

Besides creating profiles when a program has completed,
`gawk` can produce a profile while it is running.
This is useful if your `awk` program goes into an
infinite loop and you want to see what has been executed.
To use this feature, run `gawk` with the --profile
option in the background:

    $ gawk --profile -f myprog &
    [1] 13992
    

The shell prints a job number and process ID number; in this case, 13992.
Use the `kill` command to send the `USR1` signal
to `gawk`:

    $ kill -USR1 13992

As usual, the profiled version of the program is written to
awkprof.out, or to a different file if one was specified with
the --profile option.

Along with the regular profile, as shown earlier, the profile file
includes a trace of any active functions:

    # Function Call Stack:
    
    #   3. baz
    #   2. bar
    #   1. foo
    # -- main --
    

You may send `gawk` the `USR1` signal as many times as you like.
Each time, the profile and function call trace are appended to the output
profile file.

If you use the `HUP` signal instead of the `USR1` signal,
`gawk` produces the profile and the function call trace and then exits.

When `gawk` runs on MS-Windows systems, it uses the
`INT` and `QUIT` signals for producing the profile, and in
the case of the `INT` signal, `gawk` exits.  This is
because these systems don&rsquo;t support the `kill` command, so the
only signals you can deliver to a program are those generated by the
keyboard.  The `INT` signal is generated by the
Ctrl-c or Ctrl-BREAK key, while the
`QUIT` signal is generated by the Ctrl-\ key.

Finally, `gawk` also accepts another option, --pretty-print.
When called this way, `gawk` &ldquo;pretty-prints&rdquo; the program into
awkprof.out, without any execution counts.

> NOTE: Once upon a time, the --pretty-print option would also run
> your program.  This is is no longer the case.

There is a significant difference between the output created when
profiling, and that created when pretty-printing.  Pretty-printed output
preserves the original comments that were in the program, although their
placement may not correspond exactly to their original locations in the
source code.[88](#FOOT88)

However, as a deliberate design decision, profiling output *omits*
the original program&rsquo;s comments. This allows you to focus on the
execution count data and helps you avoid the temptation to use the
profiler for pretty-printing.

Additionally, pretty-printed output does not have the leading indentation
that the profiling output does. This makes it easy to pretty-print your
code once development is completed, and then use the result as the final
version of your program.

Because the internal representation of your program is formatted to
recreate an `awk` program, profiling and pretty-printing
automatically disable `gawk`&rsquo;s default optimizations.

Pretty printing also preserves the original format of numeric
constants; if you used an octal or hexadecimal value in your source
code, it will appear that way in the output.

---

Previous: [Profiling](#Profiling), Up: [Advanced Features](#Advanced-Features)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 12.6 Summary

-  The --non-decimal-data option causes `gawk` to treat
octal- and hexadecimal-looking input data as octal and hexadecimal.
This option should be used with caution or not at all; use of `strtonum()`
is preferable.
Note that this option may disappear in a future version of `gawk`.

-  You can take over complete control of sorting in &lsquo;for (indx in array)&rsquo;
array traversal by setting `PROCINFO["sorted_in"]` to the name of a user-defined
function that does the comparison of array elements based on index and value.

-  Similarly, you can supply the name of a user-defined comparison function as the
third argument to either `asort()` or `asorti()` to control how
those functions sort arrays. Or you may provide one of the predefined control
strings that work for `PROCINFO["sorted_in"]`.

-  You can use the &lsquo;|&&rsquo; operator to create a two-way pipe to a coprocess.
You read from the coprocess with `getline` and write to it with `print`
or `printf`. Use `close()` to close off the coprocess completely, or
optionally, close off one side of the two-way communications.

-  By using special file names with the &lsquo;|&&rsquo; operator, you can open a
TCP/IP (or UDP/IP) connection to remote hosts on the Internet. `gawk`
supports both IPv4 and IPv6.

-  You can generate statement count profiles of your program. This can help you
determine which parts of your program may be taking the most time and let
you tune them more easily.  Sending the `USR1` signal while profiling causes
`gawk` to dump the profile and keep going, including a function call stack.

-  You can also just &ldquo;pretty-print&rdquo; the program.

---

Next: [Debugger](#Debugger), Previous: [Advanced Features](#Advanced-Features), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## 13 Internationalization with `gawk`

Once upon a time, computer makers
wrote software that worked only in English.
Eventually, hardware and software vendors noticed that if their
systems worked in the native languages of non-English-speaking
countries, they were able to sell more systems.
As a result, internationalization and localization
of programs and software systems became a common practice.

For many years, the ability to provide internationalization
was largely restricted to programs written in C and C++.
This chapter describes the underlying library `gawk`
uses for internationalization, as well as how
`gawk` makes internationalization
features available at the `awk` program level.
Having internationalization available at the `awk` level
gives software developers additional flexibility&mdash;they are no
longer forced to write in C or C++ when internationalization is
a requirement.

&bull; [I18N and L10N](#I18N-and-L10N):  Internationalization and Localization.
&bull; [Explaining gettext](#Explaining-gettext):  How GNU `gettext` works.
&bull; [Programmer i18n](#Programmer-i18n):  Features for the programmer.
&bull; [Translator i18n](#Translator-i18n):  Features for the translator.
&bull; [I18N Example](#I18N-Example):  A simple i18n example.
&bull; [Gawk I18N](#Gawk-I18N):  `gawk` is also internationalized.
&bull; [I18N Summary](#I18N-Summary):  Summary of I18N stuff.

---

Next: [Explaining gettext](#Explaining-gettext), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.1 Internationalization and Localization

*Internationalization* means writing (or modifying) a program once,
in such a way that it can use multiple languages without requiring
further source code changes.
*Localization* means providing the data necessary for an
internationalized program to work in a particular language.
Most typically, these terms refer to features such as the language
used for printing error messages, the language used to read
responses, and information related to how numerical and
monetary values are printed and read.

---

Next: [Programmer i18n](#Programmer-i18n), Previous: [I18N and L10N](#I18N-and-L10N), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.2 GNU `gettext`

`gawk` uses GNU `gettext` to provide its internationalization
features.
The facilities in GNU `gettext` focus on messages: strings printed
by a program, either directly or via formatting with `printf` or
`sprintf()`.[89](#FOOT89)

When using GNU `gettext`, each application has its own
*text domain*.  This is a unique name, such as &lsquo;kpilot&rsquo; or &lsquo;gawk&rsquo;,
that identifies the application.
A complete application may have multiple components&mdash;programs written
in C or C++, as well as scripts written in `sh` or `awk`.
All of the components use the same text domain.

To make the discussion concrete, assume we&rsquo;re writing an application
named `guide`.  Internationalization consists of the
following steps, in this order:

1.  The programmer reviews the source for all of `guide`&rsquo;s components
and marks each string that is a candidate for translation.
For example, `"`-F': option required"` is a good candidate for translation.
A table with strings of option names is not (e.g., `gawk`&rsquo;s
--profile option should remain the same, no matter what the local
language).

2. 
The programmer indicates the application&rsquo;s text domain
(`"guide"`) to the `gettext` library,
by calling the `textdomain()` function.

3. 
Messages from the application are extracted from the source code and
collected into a portable object template file (guide.pot),
which lists the strings and their translations.
The translations are initially empty.
The original (usually English) messages serve as the key for
lookup of the translations.

4. 
For each language with a translator, guide.pot
is copied to a portable object file (`.po`)
and translations are created and shipped with the application.
For example, there might be a fr.po for a French translation.

5. 
Each language&rsquo;s .po file is converted into a binary
message object (.gmo) file.
A message object file contains the original messages and their
translations in a binary format that allows fast lookup of translations
at runtime.

6.  When `guide` is built and installed, the binary translation files
are installed in a standard place.

7. 
For testing and development, it is possible to tell `gettext`
to use .gmo files in a different directory than the standard
one by using the `bindtextdomain()` function.

8. 
At runtime, `guide` looks up each string via a call
to `gettext()`.  The returned string is the translated string
if available, or the original string if not.

9.  If necessary, it is possible to access messages from a different
text domain than the one belonging to the application, without
having to switch the application&rsquo;s default text domain back
and forth.

In C (or C++), the string marking and dynamic translation lookup
are accomplished by wrapping each string in a call to `gettext()`:

    printf("%s", gettext("Don't Panic!\n"));
    

The tools that extract messages from source code pull out all
strings enclosed in calls to `gettext()`.

The GNU `gettext` developers, recognizing that typing
&lsquo;gettext(&hellip;)&rsquo; over and over again is both painful and ugly to look
at, use the macro &lsquo;_&rsquo; (an underscore) to make things easier:

    /* In the standard header file: */
    #define _(str) gettext(str)
    
    /* In the program text: */
    printf("%s", _("Don't Panic!\n"));
    

This reduces the typing overhead to just three extra characters per string
and is considerably easier to read as well.

There are locale *categories*
for different types of locale-related information.
The defined locale categories that `gettext` knows about are:

`LC_MESSAGES`
Text messages.  This is the default category for `gettext`
operations, but it is possible to supply a different one explicitly,
if necessary.  (It is almost never necessary to supply a different category.)

`LC_COLLATE`
Text-collation information (i.e., how different characters
and/or groups of characters sort in a given language).

`LC_CTYPE`
Character-type information (alphabetic, digit, upper- or lowercase, and
so on) as well as character encoding.
This information is accessed via the
POSIX character classes in regular expressions,
such as `/[[:alnum:]]/`
(see [Bracket Expressions](#Bracket-Expressions)).

`LC_MONETARY`
Monetary information, such as the currency symbol, and whether the
symbol goes before or after a number.

`LC_NUMERIC`
Numeric information, such as which characters to use for the decimal
point and the thousands separator.[90](#FOOT90)
`LC_TIME`
Time- and date-related information, such as 12- or 24-hour clock, month printed
before or after the day in a date, local month abbreviations, and so on.

`LC_ALL`
All of the above.  (Not too useful in the context of `gettext`.)

> NOTE: As described in [Locales](#Locales), environment variables with the same
> name as the locale categories (`LC_CTYPE`, `LC_ALL`, etc.)
> influence `gawk`&rsquo;s behavior (and that of other utilities).
> 
> 
> Normally, these variables also affect how the `gettext` library
> finds translations.  However, the `LANGUAGE` environment variable
> overrides the `LC_xxx` variables. Many GNU/Linux systems
> may define this variable without your knowledge, causing `gawk`
> to not find the correct translations.  If this happens to you,
> look to see if `LANGUAGE` is defined, and if so, use the shell&rsquo;s
> `unset` command to remove it.

For testing translations of `gawk` itself, you can set
the `GAWK_LOCALE_DIR` environment variable. See the documentation
for the C `bindtextdomain()` function and also see
[Other Environment Variables](#Other-Environment-Variables).

---

Next: [Translator i18n](#Translator-i18n), Previous: [Explaining gettext](#Explaining-gettext), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.3 Internationalizing `awk` Programs

`gawk` provides the following variables for
internationalization:

`TEXTDOMAIN`
This variable indicates the application&rsquo;s text domain.
For compatibility with GNU `gettext`, the default
value is `"messages"`.

`_"your message here"`
String constants marked with a leading underscore
are candidates for translation at runtime.
String constants without a leading underscore are not translated.

`gawk` provides the following functions for
internationalization:

``dcgettext(string` [`,`domain [`,`category]]`)``
Return the translation of string in
text domain domain for locale category category.
The default value for domain is the current value of `TEXTDOMAIN`.
The default value for category is `"LC_MESSAGES"`.

If you supply a value for category, it must be a string equal to
one of the known locale categories described in
the previous section.
You must also supply a text domain.  Use `TEXTDOMAIN` if
you want to use the current domain.

> CAUTION: The order of arguments to the `awk` version
> of the `dcgettext()` function is purposely different from the order for
> the C version.  The `awk` version&rsquo;s order was
> chosen to be simple and to allow for reasonable `awk`-style
> default arguments.

``dcngettext(string1, string2, number` [`,`domain [`,`category]]`)``
Return the plural form used for number of the
translation of string1 and string2 in text domain
domain for locale category category. string1 is the
English singular variant of a message, and string2 is the English plural
variant of the same message.
The default value for domain is the current value of `TEXTDOMAIN`.
The default value for category is `"LC_MESSAGES"`.

The same remarks about argument order as for the `dcgettext()` function apply.

``bindtextdomain(directory` [`,`domain ]`)``
Change the directory in which
`gettext` looks for .gmo files, in case they
will not or cannot be placed in the standard locations
(e.g., during testing).
Return the directory in which domain is &ldquo;bound.&rdquo;

The default domain is the value of `TEXTDOMAIN`.
If directory is the null string (`""`), then
`bindtextdomain()` returns the current binding for the
given domain.

To use these facilities in your `awk` program, follow these steps:

1. 
Set the variable `TEXTDOMAIN` to the text domain of
your program.  This is best done in a `BEGIN` rule
(see [BEGIN/END](#BEGIN_002fEND)),
or it can also be done via the -v command-line
option (see [Options](#Options)):

    BEGIN {
        TEXTDOMAIN = "guide"
        &hellip;
    }
    

2. 
Mark all translatable strings with a leading underscore (&lsquo;_&rsquo;)
character.  It *must* be adjacent to the opening
quote of the string.  For example:

    print _"hello, world"
    x = _"you goofed"
    printf(_"Number of users is %d\n", nusers)
    

3.  If you are creating strings dynamically, you can
still translate them, using the `dcgettext()`
built-in function:[91](#FOOT91)

    if (groggy)
        message = dcgettext("%d customers disturbing me\n", "adminprog")
    else
        message = dcgettext("enjoying %d customers\n", "adminprog")
    printf(message, ncustomers)
    

Here, the call to `dcgettext()` supplies a different
text domain (`"adminprog"`) in which to find the
message, but it uses the default `"LC_MESSAGES"` category.

The previous example only works if `ncustomers` is greater than one.
This example would be better done with `dcngettext()`:

    if (groggy)
        message = dcngettext("%d customer disturbing me\n",
                             "%d customers disturbing me\n",
                             ncustomers, "adminprog")
    else
        message = dcngettext("enjoying %d customer\n",
                             "enjoying %d customers\n",
                             ncustomers, "adminprog")
    printf(message, ncustomers)
    

4. 
During development, you might want to put the .gmo
file in a private directory for testing.  This is done
with the `bindtextdomain()` built-in function:

    BEGIN {
       TEXTDOMAIN = "guide"   # our text domain
       if (Testing) {
           # where to find our files
           bindtextdomain("testdir")
           # joe is in charge of adminprog
           bindtextdomain("../joe/testdir", "adminprog")
       }
       &hellip;
    }
    

See [I18N Example](#I18N-Example)
for an example program showing the steps to create
and use translations from `awk`.

---

Next: [I18N Example](#I18N-Example), Previous: [Programmer i18n](#Programmer-i18n), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.4 Translating `awk` Programs

Once a program&rsquo;s translatable strings have been marked, they must
be extracted to create the initial .pot file.
As part of translation, it is often helpful to rearrange the order
in which arguments to `printf` are output.

`gawk`&rsquo;s --gen-pot command-line option extracts
the messages and is discussed next.
After that, `printf`&rsquo;s ability to
rearrange the order for `printf` arguments at runtime
is covered.

&bull; [String Extraction](#String-Extraction):  Extracting marked strings.
&bull; [Printf Ordering](#Printf-Ordering):  Rearranging `printf` arguments.
&bull; [I18N Portability](#I18N-Portability):  `awk`-level portability issues.

---

Next: [Printf Ordering](#Printf-Ordering), Up: [Translator i18n](#Translator-i18n)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 13.4.1 Extracting Marked Strings

Once your `awk` program is working, and all the strings have
been marked and you&rsquo;ve set (and perhaps bound) the text domain,
it is time to produce translations.
First, use the --gen-pot command-line option to create
the initial .pot file:

    gawk --gen-pot -f guide.awk > guide.pot
    

When run with --gen-pot, `gawk` does not execute your
program.  Instead, it parses it as usual and prints all marked strings
to standard output in the format of a GNU `gettext` Portable Object
file.  Also included in the output are any constant strings that
appear as the first argument to `dcgettext()` or as the first and
second argument to `dcngettext()`.[92](#FOOT92)
You should distribute the generated .pot file with
your `awk` program; translators will eventually use it
to provide you translations that you can also then distribute.
See [I18N Example](#I18N-Example)
for the full list of steps to go through to create and test
translations for `guide`.

---

Next: [I18N Portability](#I18N-Portability), Previous: [String Extraction](#String-Extraction), Up: [Translator i18n](#Translator-i18n)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 13.4.2 Rearranging `printf` Arguments

Format strings for `printf` and `sprintf()`
(see [Printf](#Printf))
present a special problem for translation.
Consider the following:[93](#FOOT93)

    printf(_"String `%s' has %d characters\n",
              string, length(string)))
    

A possible German translation for this might be:

    "%d Zeichen lang ist die Zeichenkette `%s'\n"
    

The problem should be obvious: the order of the format
specifications is different from the original!
Even though `gettext()` can return the translated string
at runtime,
it cannot change the argument order in the call to `printf`.

To solve this problem, `printf` format specifiers may have
an additional optional element, which we call a *positional specifier*.
For example:

    "%2$d Zeichen lang ist die Zeichenkette `%1$s'\n"
    

Here, the positional specifier consists of an integer count, which indicates which
argument to use, and a &lsquo;$&rsquo;. Counts are one-based, and the
format string itself is *not* included.  Thus, in the following
example, &lsquo;string&rsquo; is the first argument and &lsquo;length(string)&rsquo; is the second:

    $ gawk 'BEGIN {
    >     string = "Don\47t Panic"
    >     printf "%2$d characters live in \"%1$s\"\n",
    >                         string, length(string)
    > }'
    -| 11 characters live in "Don't Panic"
    

If present, positional specifiers come first in the format specification,
before the flags, the field width, and/or the precision.

Positional specifiers can be used with the dynamic field width and
precision capability:

    $ gawk 'BEGIN {
    >    printf("%*.*s\n", 10, 20, "hello")
    >    printf("%3$*2$.*1$s\n", 20, 10, "hello")
    > }'
    -|      hello
    -|      hello
    

> NOTE: When using &lsquo;*&rsquo; with a positional specifier, the &lsquo;*&rsquo;
> comes first, then the integer position, and then the &lsquo;$&rsquo;.
> This is somewhat counterintuitive.

`gawk` does not allow you to mix regular format specifiers
and those with positional specifiers in the same string:

    $ gawk 'BEGIN { printf "%d %3$s\n", 1, 2, "hi" }'
    error&rarr; gawk: cmd. line:1: fatal: must use `count$' on all formats or none
    

> NOTE: There are some pathological cases that `gawk` may fail to
> diagnose.  In such cases, the output may not be what you expect.
> It&rsquo;s still a bad idea to try mixing them, even if `gawk`
> doesn&rsquo;t detect it.

Although positional specifiers can be used directly in `awk` programs,
their primary purpose is to help in producing correct translations of
format strings into languages different from the one in which the program
is first written.

---

Previous: [Printf Ordering](#Printf-Ordering), Up: [Translator i18n](#Translator-i18n)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 13.4.3 `awk` Portability Issues

`gawk`&rsquo;s internationalization features were purposely chosen to
have as little impact as possible on the portability of `awk`
programs that use them to other versions of `awk`.
Consider this program:

    BEGIN {
        TEXTDOMAIN = "guide"
        if (Test_Guide)   # set with -v
            bindtextdomain("/test/guide/messages")
        print _"don't panic!"
    }
    

As written, it won&rsquo;t work on other versions of `awk`.
However, it is actually almost portable, requiring very little
change:

- 
Assignments to `TEXTDOMAIN` won&rsquo;t have any effect,
because `TEXTDOMAIN` is not special in other `awk` implementations.

-  Non-GNU versions of `awk` treat marked strings
as the concatenation of a variable named `_` with the string
following it.[94](#FOOT94) Typically, the variable `_` has
the null string (`""`) as its value, leaving the original string constant as
the result.

-  By defining &ldquo;dummy&rdquo; functions to replace `dcgettext()`, `dcngettext()`,
and `bindtextdomain()`, the `awk` program can be made to run, but
all the messages are output in the original language.
For example:

    function bindtextdomain(dir, domain)
    {
        return dir
    }
    
    function dcgettext(string, domain, category)
    {
        return string
    }
    
    function dcngettext(string1, string2, number, domain, category)
    {
        return (number == 1 ? string1 : string2)
    }
    

-  The use of positional specifications in `printf` or
`sprintf()` is *not* portable.
To support `gettext()` at the C level, many systems&rsquo; C versions of
`sprintf()` do support positional specifiers.  But it works only if
enough arguments are supplied in the function call.  Many versions of
`awk` pass `printf` formats and arguments unchanged to the
underlying C library version of `sprintf()`, but only one format and
argument at a time.  What happens if a positional specification is
used is anybody&rsquo;s guess.
However, because the positional specifications are primarily for use in
*translated* format strings, and because non-GNU `awk`s never
retrieve the translated string, this should not be a problem in practice.

---

Next: [Gawk I18N](#Gawk-I18N), Previous: [Translator i18n](#Translator-i18n), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.5 A Simple Internationalization Example

Now let&rsquo;s look at a step-by-step example of how to internationalize and
localize a simple `awk` program, using guide.awk as our
original source:

    BEGIN {
        TEXTDOMAIN = "guide"
        bindtextdomain(".")  # for testing
        print _"Don't Panic"
        print _"The Answer Is", 42
        print "Pardon me, Zaphod who?"
    }
    

Run &lsquo;gawk --gen-pot&rsquo; to create the .pot file:

    $ gawk --gen-pot -f guide.awk > guide.pot

This produces:

    #: guide.awk:4
    msgid "Don't Panic"
    msgstr ""
    
    #: guide.awk:5
    msgid "The Answer Is"
    msgstr ""
    
    

This original portable object template file is saved and reused for each language
into which the application is translated.  The `msgid`
is the original string and the `msgstr` is the translation.

> NOTE: Strings not marked with a leading underscore do not
> appear in the guide.pot file.

Next, the messages must be translated.
Here is a translation to a hypothetical dialect of English,
called &ldquo;Mellow&rdquo;:[95](#FOOT95)

    $ cp guide.pot guide-mellow.poAdd translations to guide-mellow.po &hellip;
    

Following are the translations:

    #: guide.awk:4
    msgid "Don't Panic"
    msgstr "Hey man, relax!"
    
    #: guide.awk:5
    msgid "The Answer Is"
    msgstr "Like, the scoop is"
    
    

The next step is to make the directory to hold the binary message object
file and then to create the guide.mo file.
We pretend that our file is to be used in the `en_US.UTF-8` locale,
because we have to use a locale name known to the C `gettext` routines.
The directory layout shown here is standard for GNU `gettext` on
GNU/Linux systems.  Other versions of `gettext` may use a different
layout:

    $ mkdir en_US.UTF-8 en_US.UTF-8/LC_MESSAGES

The `msgfmt` utility does the conversion from human-readable
.po file to machine-readable .mo file.
By default, `msgfmt` creates a file named messages.
This file must be renamed and placed in the proper directory (using
the -o option) so that `gawk` can find it:

    $ msgfmt guide-mellow.po -o en_US.UTF-8/LC_MESSAGES/guide.mo

Finally, we run the program to test it:

    $ gawk -f guide.awk
    -| Hey man, relax!
    -| Like, the scoop is 42
    -| Pardon me, Zaphod who?
    

If the three replacement functions for `dcgettext()`, `dcngettext()`,
and `bindtextdomain()`
(see [I18N Portability](#I18N-Portability))
are in a file named libintl.awk,
then we can run guide.awk unchanged as follows:

    $ gawk --posix -f guide.awk -f libintl.awk
    -| Don't Panic
    -| The Answer Is 42
    -| Pardon me, Zaphod who?
    

---

Next: [I18N Summary](#I18N-Summary), Previous: [I18N Example](#I18N-Example), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.6 `gawk` Can Speak Your Language

`gawk` itself has been internationalized
using the GNU `gettext` package.
(GNU `gettext` is described in
complete detail in
[GNU `gettext` utilities](https://www.gnu.org/software/gettext/manual/).)
As of this writing, the latest version of GNU `gettext` is
[version 0.19.4](ftp://ftp.gnu.org/gnu/gettext/gettext-0.19.4.tar.gz).

If a translation of `gawk`&rsquo;s messages exists,
then `gawk` produces usage messages, warnings,
and fatal errors in the local language.

---

Previous: [Gawk I18N](#Gawk-I18N), Up: [Internationalization](#Internationalization)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 13.7 Summary

-  Internationalization means writing a program such that it can use multiple
languages without requiring source code changes.  Localization means
providing the data necessary for an internationalized program to work
in a particular language.

- `gawk` uses GNU `gettext` to let you internationalize
and localize `awk` programs.  A program&rsquo;s text domain identifies
the program for grouping all messages and other data together.

-  You mark a program&rsquo;s strings for translation by preceding them with
an underscore. Once that is done, the strings are extracted into a
.pot file.  This file is copied for each language into a .po
file, and the .po files are compiled into .gmo files for
use at runtime.

-  You can use positional specifications with `sprintf()` and
`printf` to rearrange the placement of argument values in formatted
strings and output. This is useful for the translation of format
control strings.

-  The internationalization features have been designed so that they
can be easily worked around in a standard `awk`.

- `gawk` itself has been internationalized and ships with
a number of translations for its messages.

---

Next: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic), Previous: [Internationalization](#Internationalization), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## 14 Debugging `awk` Programs

It would be nice if computer programs worked perfectly the first time they
were run, but in real life, this rarely happens for programs of
any complexity.  Thus, most programming languages have facilities available
for &ldquo;debugging&rdquo; programs, and now `awk` is no exception.

The `gawk` debugger is purposely modeled after
[the GNU Debugger (GDB)](https://www.gnu.org/software/gdb/)
command-line debugger.  If you are familiar with GDB, learning
how to use `gawk` for debugging your program is easy.

&bull; [Debugging](#Debugging):  Introduction to `gawk` debugger.
&bull; [Sample Debugging Session](#Sample-Debugging-Session):  Sample debugging session.
&bull; [List of Debugger Commands](#List-of-Debugger-Commands):  Main debugger commands.
&bull; [Readline Support](#Readline-Support):  Readline support.
&bull; [Limitations](#Limitations):  Limitations and future plans.
&bull; [Debugging Summary](#Debugging-Summary):  Debugging summary.

---

Next: [Sample Debugging Session](#Sample-Debugging-Session), Up: [Debugger](#Debugger)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 14.1 Introduction to the `gawk` Debugger

This section introduces debugging in general and begins
the discussion of debugging in `gawk`.

&bull; [Debugging Concepts](#Debugging-Concepts):  Debugging in General.
&bull; [Debugging Terms](#Debugging-Terms):  Additional Debugging Concepts.
&bull; [Awk Debugging](#Awk-Debugging):  Awk Debugging.

---

Next: [Debugging Terms](#Debugging-Terms), Up: [Debugging](#Debugging)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.1.1 Debugging in General

(If you have used debuggers in other languages, you may want to skip
ahead to [Awk Debugging](#Awk-Debugging).)

Of course, a debugging program cannot remove bugs for you, because it has
no way of knowing what you or your users consider a &ldquo;bug&rdquo; versus a
&ldquo;feature.&rdquo;  (Sometimes, we humans have a hard time with this ourselves.)
In that case, what can you expect from such a tool?  The answer to that
depends on the language being debugged, but in general, you can expect at
least the following:

-  The ability to watch a program execute its instructions one by one,
giving you, the programmer, the opportunity to think about what is happening
on a time scale of seconds, minutes, or hours, rather than the nanosecond
time scale at which the code usually runs.

-  The opportunity to not only passively observe the operation of your
program, but to control it and try different paths of execution, without
having to change your source files.

-  The chance to see the values of data in the program at any point in
execution, and also to change that data on the fly, to see how that
affects what happens afterward.  (This often includes the ability
to look at internal data structures besides the variables you actually
defined in your code.)

-  The ability to obtain additional information about your program&rsquo;s state
or even its internal structure.

All of these tools provide a great amount of help in using your own
skills and understanding of the goals of your program to find where it
is going wrong (or, for that matter, to better comprehend a perfectly
functional program that you or someone else wrote).

---

Next: [Awk Debugging](#Awk-Debugging), Previous: [Debugging Concepts](#Debugging-Concepts), Up: [Debugging](#Debugging)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.1.2 Debugging Concepts

Before diving in to the details, we need to introduce several
important concepts that apply to just about all debuggers.
The following list defines terms used throughout the rest of
this chapter:

*Stack frame*
Programs generally call functions during the course of their execution.
One function can call another, or a function can call itself (recursion).
You can view the chain of called functions (main program calls A, which
calls B, which calls C), as a stack of executing functions: the currently
running function is the topmost one on the stack, and when it finishes
(returns), the next one down then becomes the active function.
Such a stack is termed a *call stack*.

For each function on the call stack, the system maintains a data area
that contains the function&rsquo;s parameters, local variables, and return value,
as well as any other &ldquo;bookkeeping&rdquo; information needed to manage the
call stack.  This data area is termed a *stack frame*.

`gawk` also follows this model, and gives you
access to the call stack and to each stack frame. You can see the
call stack, as well as from where each function on the stack was
invoked. Commands that print the call stack print information about
each stack frame (as detailed later on).

*Breakpoint*
During debugging, you often wish to let the program run until it
reaches a certain point, and then continue execution from there one
statement (or instruction) at a time.  The way to do this is to set
a *breakpoint* within the program.  A breakpoint is where the
execution of the program should break off (stop), so that you can
take over control of the program&rsquo;s execution.  You can add and remove
as many breakpoints as you like.

*Watchpoint*
A watchpoint is similar to a breakpoint.  The difference is that
breakpoints are oriented around the code: stop when a certain point in the
code is reached.  A watchpoint, however, specifies that program execution
should stop when a *data value* is changed.  This is useful, as
sometimes it happens that a variable receives an erroneous value, and it&rsquo;s
hard to track down where this happens just by looking at the code.
By using a watchpoint, you can stop whenever a variable is assigned to,
and usually find the errant code quite quickly.

---

Previous: [Debugging Terms](#Debugging-Terms), Up: [Debugging](#Debugging)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.1.3 `awk` Debugging

Debugging an `awk` program has some specific aspects that are
not shared with programs written in other languages.

First of all, the fact that `awk` programs usually take input
line by line from a file or files and operate on those lines using specific
rules makes it especially useful to organize viewing the execution of
the program in terms of these rules.  As we will see, each `awk`
rule is treated almost like a function call, with its own specific block
of instructions.

In addition, because `awk` is by design a very concise language,
it is easy to lose sight of everything that is going on &ldquo;inside&rdquo;
each line of `awk` code.  The debugger provides the opportunity
to look at the individual primitive instructions carried out
by the higher-level `awk` commands.

---

Next: [List of Debugger Commands](#List-of-Debugger-Commands), Previous: [Debugging](#Debugging), Up: [Debugger](#Debugger)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 14.2 Sample `gawk` Debugging Session

In order to illustrate the use of `gawk` as a debugger, let&rsquo;s look at a sample
debugging session.  We will use the `awk` implementation of the
POSIX `uniq` command described earlier (see [Uniq Program](#Uniq-Program))
as our example.

&bull; [Debugger Invocation](#Debugger-Invocation):  How to Start the Debugger.
&bull; [Finding The Bug](#Finding-The-Bug):  Finding the Bug.

---

Next: [Finding The Bug](#Finding-The-Bug), Up: [Sample Debugging Session](#Sample-Debugging-Session)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.2.1 How to Start the Debugger

Starting the debugger is almost exactly like running `gawk` normally,
except you have to pass an additional option, --debug, or the
corresponding short option, -D.  The file(s) containing the
program and any supporting code are given on the command line as arguments
to one or more -f options. (`gawk` is not designed
to debug command-line programs, only programs contained in files.)
In our case, we invoke the debugger like this:

    $ gawk -D -f getopt.awk -f join.awk -f uniq.awk -1 inputfile

where both getopt.awk and uniq.awk are in `$AWKPATH`.
(Experienced users of GDB or similar debuggers should note that
this syntax is slightly different from what you are used to.
With the `gawk` debugger, you give the arguments for running the program
in the command line to the debugger rather than as part of the `run`
command at the debugger prompt.)
The -1 is an option to uniq.awk.

Instead of immediately running the program on inputfile, as
`gawk` would ordinarily do, the debugger merely loads all
the program source files, compiles them internally, and then gives
us a prompt:

    gawk>
    

from which we can issue commands to the debugger.  At this point, no
code has been executed.

---

Previous: [Debugger Invocation](#Debugger-Invocation), Up: [Sample Debugging Session](#Sample-Debugging-Session)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.2.2 Finding the Bug

Let&rsquo;s say that we are having a problem using (a faulty version of)
uniq.awk in the &ldquo;field-skipping&rdquo; mode, and it doesn&rsquo;t seem to be
catching lines which should be identical when skipping the first field,
such as:

    awk is a wonderful program!
    gawk is a wonderful program!
    

This could happen if we were thinking (C-like) of the fields in a record
as being numbered in a zero-based fashion, so instead of the lines:

    clast = join(alast, fcount+1, n)
    cline = join(aline, fcount+1, m)
    

we wrote:

    clast = join(alast, fcount, n)
    cline = join(aline, fcount, m)
    

The first thing we usually want to do when trying to investigate a
problem like this is to put a breakpoint in the program so that we can
watch it at work and catch what it is doing wrong.  A reasonable spot for
a breakpoint in uniq.awk is at the beginning of the function
`are_equal()`, which compares the current line with the previous one. To set
the breakpoint, use the `b` (breakpoint) command:

    gawk> b are_equal
    -| Breakpoint 1 set at file `awklib/eg/prog/uniq.awk', line 63
    

The debugger tells us the file and line number where the breakpoint is.
Now type &lsquo;r&rsquo; or &lsquo;run&rsquo; and the program runs until it hits
the breakpoint for the first time:

    gawk> r
    -| Starting program:
    -| Stopping in Rule ...
    -| Breakpoint 1, are_equal(n, m, clast, cline, alast, aline)
             at `awklib/eg/prog/uniq.awk':63
    -| 63          if (fcount == 0 && charcount == 0)
    gawk>
    

Now we can look at what&rsquo;s going on inside our program.  First of all,
let&rsquo;s see how we got to where we are.  At the prompt, we type &lsquo;bt&rsquo;
(short for &ldquo;backtrace&rdquo;), and the debugger responds with a
listing of the current stack frames:

    gawk> bt
    -| #0  are_equal(n, m, clast, cline, alast, aline)
             at `awklib/eg/prog/uniq.awk':68
    -| #1  in main() at `awklib/eg/prog/uniq.awk':88
    

This tells us that `are_equal()` was called by the main program at
line 88 of uniq.awk.  (This is not a big surprise, because this
is the only call to `are_equal()` in the program, but in more complex
programs, knowing who called a function and with what parameters can be
the key to finding the source of the problem.)

Now that we&rsquo;re in `are_equal()`, we can start looking at the values
of some variables.  Let&rsquo;s say we type &lsquo;p n&rsquo;
(`p` is short for &ldquo;print&rdquo;).  We would expect to see the value of
`n`, a parameter to `are_equal()`.  Actually, the debugger
gives us:

    gawk> p n
    -| n = untyped variable
    

In this case, `n` is an uninitialized local variable, because the
function was called without arguments (see [Function Calls](#Function-Calls)).

A more useful variable to display might be the current record:

    gawk> p $0
    -| $0 = "gawk is a wonderful program!"
    

This might be a bit puzzling at first, as this is the second line of
our test input.  Let&rsquo;s look at `NR`:

    gawk> p NR
    -| NR = 2
    

So we can see that `are_equal()` was only called for the second record
of the file.  Of course, this is because our program contains a rule for
&lsquo;NR == 1&rsquo;:

    NR == 1 {
        last = $0
        next
    }
    

OK, let&rsquo;s just check that that rule worked correctly:

    gawk> p last
    -| last = "awk is a wonderful program!"
    

Everything we have done so far has verified that the program has worked as
planned, up to and including the call to `are_equal()`, so the problem must
be inside this function.  To investigate further, we must begin
&ldquo;stepping through&rdquo; the lines of `are_equal()`.  We start by typing
&lsquo;n&rsquo; (for &ldquo;next&rdquo;):

    gawk> n
    -| 66          if (fcount > 0) {
    

This tells us that `gawk` is now ready to execute line 66, which
decides whether to give the lines the special &ldquo;field-skipping&rdquo; treatment
indicated by the -1 command-line option.  (Notice that we skipped
from where we were before, at line 63, to here, because the condition
in line 63, &lsquo;if (fcount == 0 && charcount == 0)&rsquo;, was false.)

Continuing to step, we now get to the splitting of the current and
last records:

    gawk> n
    -| 67              n = split(last, alast)
    gawk> n
    -| 68              m = split($0, aline)
    

At this point, we should be curious to see what our records were split
into, so we try to look:

    gawk> p n m alast aline
    -| n = 5
    -| m = untyped variable
    -| alast = array, 5 elements
    -| aline = untyped variable
    

(The `p` command can take more than one argument, similar to
`awk`&rsquo;s `print` statement.)

This is kind of disappointing, though.  All we found out is that there
are five elements in `alast`; `m` and `aline` don&rsquo;t have
values because we are at line 68 but haven&rsquo;t executed it yet.
This information is useful enough (we now know that
none of the words were accidentally left out), but what if we want to see
inside the array?

The first choice would be to use subscripts:

    gawk> p alast[0]
    -| "0" not in array `alast'
    

Oops!

    gawk> p alast[1]
    -| alast["1"] = "awk"
    

This would be kind of slow for a 100-member array, though, so
`gawk` provides a shortcut (reminiscent of another language
not to be mentioned):

    gawk> p @alast
    -| alast["1"] = "awk"
    -| alast["2"] = "is"
    -| alast["3"] = "a"
    -| alast["4"] = "wonderful"
    -| alast["5"] = "program!"
    

It looks like we got this far OK.  Let&rsquo;s take another step
or two:

    gawk> n
    -| 69              clast = join(alast, fcount, n)
    gawk> n
    -| 70              cline = join(aline, fcount, m)
    

Well, here we are at our error (sorry to spoil the suspense).  What we
had in mind was to join the fields starting from the second one to make
the virtual record to compare, and if the first field were numbered zero,
this would work.  Let&rsquo;s look at what we&rsquo;ve got:

    gawk> p cline clast
    -| cline = "gawk is a wonderful program!"
    -| clast = "awk is a wonderful program!"
    

Hey, those look pretty familiar!  They&rsquo;re just our original, unaltered
input records.  A little thinking (the human brain is still the best
debugging tool), and we realize that we were off by one!

We get out of the debugger:

    gawk> q
    -| The program is running. Exit anyway (y/n)? y

Then we get into an editor:

    clast = join(alast, fcount+1, n)
    cline = join(aline, fcount+1, m)
    

and problem solved!

---

Next: [Readline Support](#Readline-Support), Previous: [Sample Debugging Session](#Sample-Debugging-Session), Up: [Debugger](#Debugger)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 14.3 Main Debugger Commands

The `gawk` debugger command set can be divided into the
following categories:

-  Breakpoint control

-  Execution control

-  Viewing and changing data

-  Working with the stack

-  Getting information

-  Miscellaneous

Each of these are discussed in the following subsections.
In the following descriptions, commands that may be abbreviated
show the abbreviation on a second description line.
A debugger command name may also be truncated if that partial
name is unambiguous. The debugger has the built-in capability to
automatically repeat the previous command just by hitting Enter.
This works for the commands `list`, `next`, `nexti`,
`step`, `stepi`, and `continue` executed without any
argument.

&bull; [Breakpoint Control](#Breakpoint-Control):  Control of Breakpoints.
&bull; [Debugger Execution Control](#Debugger-Execution-Control):  Control of Execution.
&bull; [Viewing And Changing Data](#Viewing-And-Changing-Data):  Viewing and Changing Data.
&bull; [Execution Stack](#Execution-Stack):  Dealing with the Stack.
&bull; [Debugger Info](#Debugger-Info):  Obtaining Information about the Program and
                                the Debugger State.
&bull; [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands):  Miscellaneous Commands.

---

Next: [Debugger Execution Control](#Debugger-Execution-Control), Up: [List of Debugger Commands](#List-of-Debugger-Commands)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.3.1 Control of Breakpoints

As we saw earlier, the first thing you probably want to do in a debugging
session is to get your breakpoints set up, because your program
will otherwise just run as if it was not under the debugger.  The commands for
controlling breakpoints are:

`break` [[filename`:`]n | function] [`"expression"`]`b` [[filename`:`]n | function] [`"expression"`]
Without any argument, set a breakpoint at the next instruction
to be executed in the selected stack frame.
Arguments can be one of the following:

n
Set a breakpoint at line number n in the current source file.

filename`:`n
Set a breakpoint at line number n in source file filename.

function
Set a breakpoint at entry to (the first instruction of)
function function.

Each breakpoint is assigned a number that can be used to delete it from
the breakpoint list using the `delete` command.

With a breakpoint, you may also supply a condition.  This is an
`awk` expression (enclosed in double quotes) that the debugger
evaluates whenever the breakpoint is reached. If the condition is true,
then the debugger stops execution and prompts for a command. Otherwise,
it continues executing the program.

`clear` [[filename`:`]n | function]
Without any argument, delete any breakpoint at the next instruction
to be executed in the selected stack frame. If the program stops at
a breakpoint, this deletes that breakpoint so that the program
does not stop at that location again.  Arguments can be one of the following:

n
Delete breakpoint(s) set at line number n in the current source file.

filename`:`n
Delete breakpoint(s) set at line number n in source file filename.

function
Delete breakpoint(s) set at entry to function function.

`condition`n`"expression"`
Add a condition to existing breakpoint or watchpoint n. The
condition is an `awk` expression *enclosed in double quotes*
that the debugger evaluates
whenever the breakpoint or watchpoint is reached. If the condition is true, then
the debugger stops execution and prompts for a command. Otherwise,
the debugger continues executing the program. If the condition expression is
not specified, any existing condition is removed (i.e., the breakpoint or
watchpoint is made unconditional).

`delete` [n1 n2 &hellip;] [n&ndash;m]`d` [n1 n2 &hellip;] [n&ndash;m]
Delete specified breakpoints or a range of breakpoints. Delete
all defined breakpoints if no argument is supplied.

`disable` [n1 n2 &hellip; | n&ndash;m]
Disable specified breakpoints or a range of breakpoints. Without
any argument, disable all breakpoints.

`enable` [`del` | `once`] [n1 n2 &hellip;] [n&ndash;m]`e` [`del` | `once`] [n1 n2 &hellip;] [n&ndash;m]
Enable specified breakpoints or a range of breakpoints. Without
any argument, enable all breakpoints.
Optionally, you can specify how to enable the breakpoints:

`del`
Enable the breakpoints temporarily, then delete each one when
the program stops at it.

`once`
Enable the breakpoints temporarily, then disable each one when
the program stops at it.

`ignore`ncount
Ignore breakpoint number n the next count times it is
hit.

`tbreak` [[filename`:`]n | function]`t` [[filename`:`]n | function]
Set a temporary breakpoint (enabled for only one stop).
The arguments are the same as for `break`.

---

Next: [Viewing And Changing Data](#Viewing-And-Changing-Data), Previous: [Breakpoint Control](#Breakpoint-Control), Up: [List of Debugger Commands](#List-of-Debugger-Commands)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.3.2 Control of Execution

Now that your breakpoints are ready, you can start running the program
and observing its behavior.  There are more commands for controlling
execution of the program than we saw in our earlier example:

`commands` [n]`silent`&hellip;`end`
Set a list of commands to be executed upon stopping at
a breakpoint or watchpoint. n is the breakpoint or watchpoint number.
Without a number, the last one set is used. The actual commands follow,
starting on the next line, and terminated by the `end` command.
If the command `silent` is in the list, the usual messages about
stopping at a breakpoint and the source line are not printed. Any command
in the list that resumes execution (e.g., `continue`) terminates the list
(an implicit `end`), and subsequent commands are ignored.
For example:

    gawk> commands
    > silent
    > printf "A silent breakpoint; i = %d\n", i
    > info locals
    > set i = 10
    > continue
    > end
    gawk>
    

`continue` [count]`c` [count]
Resume program execution. If continued from a breakpoint and count is
specified, ignore the breakpoint at that location the next count times
before stopping.

`finish`
Execute until the selected stack frame returns.
Print the returned value.

`next` [count]`n` [count]
Continue execution to the next source line, stepping over function calls.
The argument count controls how many times to repeat the action, as
in `step`.

`nexti` [count]`ni` [count]
Execute one (or count) instruction(s), stepping over function calls.

`return` [value]
Cancel execution of a function call. If value (either a string or a
number) is specified, it is used as the function&rsquo;s return value. If used in a
frame other than the innermost one (the currently executing function; i.e.,
frame number 0), discard all inner frames in addition to the selected one,
and the caller of that frame becomes the innermost frame.

`run``r`
Start/restart execution of the program. When restarting, the debugger
retains the current breakpoints, watchpoints, command history,
automatic display variables, and debugger options.

`step` [count]`s` [count]
Continue execution until control reaches a different source line in the
current stack frame, stepping inside any function called within
the line.  If the argument count is supplied, steps that many times before
stopping, unless it encounters a breakpoint or watchpoint.

`stepi` [count]`si` [count]
Execute one (or count) instruction(s), stepping inside function calls.
(For illustration of what is meant by an &ldquo;instruction&rdquo; in `gawk`,
see the output shown under `dump` in [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands).)

`until` [[filename`:`]n | function]`u` [[filename`:`]n | function]
Without any argument, continue execution until a line past the current
line in the current stack frame is reached. With an argument,
continue execution until the specified location is reached, or the current
stack frame returns.

---

Next: [Execution Stack](#Execution-Stack), Previous: [Debugger Execution Control](#Debugger-Execution-Control), Up: [List of Debugger Commands](#List-of-Debugger-Commands)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.3.3 Viewing and Changing Data

The commands for viewing and changing variables inside of `gawk` are:

`display` [var | `$`n]
Add variable var (or field `$n`) to the display list.
The value of the variable or field is displayed each time the program stops.
Each variable added to the list is identified by a unique number:

    gawk> display x
    -| 10: x = 1
    

This displays the assigned item number, the variable name, and its current value.
If the display variable refers to a function parameter, it is silently
deleted from the list as soon as the execution reaches a context where
no such variable of the given name exists.
Without argument, `display` displays the current values of
items on the list.

`eval "awk statements"`
Evaluate awk statements in the context of the running program.
You can do anything that an `awk` program would do: assign
values to variables, call functions, and so on.

`eval`param, &hellip;awk statements`end`
This form of `eval` is similar, but it allows you to define
&ldquo;local variables&rdquo; that exist in the context of the
awk statements, instead of using variables or function
parameters defined by the program.

`print`var1[`,`var2 &hellip;]`p`var1[`,`var2 &hellip;]
Print the value of a `gawk` variable or field.
Fields must be referenced by constants:

    gawk> print $3

This prints the third field in the input record (if the specified field does not
exist, it prints &lsquo;Null field&rsquo;). A variable can be an array element, with
the subscripts being constant string values. To print the contents of an array,
prefix the name of the array with the &lsquo;@&rsquo; symbol:

    gawk> print @a

This prints the indices and the corresponding values for all elements in
the array `a`.

`printf`format [`,`arg &hellip;]
Print formatted text. The format may include escape sequences,
such as &lsquo;\n&rsquo;
(see [Escape Sequences](#Escape-Sequences)).
No newline is printed unless one is specified.

`set`var`=`value
Assign a constant (number or string) value to an `awk` variable
or field.
String values must be enclosed between double quotes (`"`&hellip;`"`).

You can also set special `awk` variables, such as `FS`,
`NF`, `NR`, and so on.

`watch`var | `$`n [`"expression"`]`w`var | `$`n [`"expression"`]
Add variable var (or field `$n`) to the watch list.
The debugger then stops whenever
the value of the variable or field changes. Each watched item is assigned a
number that can be used to delete it from the watch list using the
`unwatch` command.

With a watchpoint, you may also supply a condition.  This is an
`awk` expression (enclosed in double quotes) that the debugger
evaluates whenever the watchpoint is reached. If the condition is true,
then the debugger stops execution and prompts for a command. Otherwise,
`gawk` continues executing the program.

`undisplay` [n]
Remove item number n (or all items, if no argument) from the
automatic display list.

`unwatch` [n]
Remove item number n (or all items, if no argument) from the
watch list.

---

Next: [Debugger Info](#Debugger-Info), Previous: [Viewing And Changing Data](#Viewing-And-Changing-Data), Up: [List of Debugger Commands](#List-of-Debugger-Commands)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.3.4 Working with the Stack

Whenever you run a program that contains any function calls,
`gawk` maintains a stack of all of the function calls leading up
to where the program is right now.  You can see how you got to where you are,
and also move around in the stack to see what the state of things was in the
functions that called the one you are in.  The commands for doing this are:

`backtrace` [count]`bt` [count]`where` [count]
Print a backtrace of all function calls (stack frames), or innermost count
frames if count > 0. Print the outermost count frames if
count < 0.  The backtrace displays the name and arguments to each
function, the source file name, and the line number.
The alias `where` for `backtrace` is provided for longtime
GDB users who may be used to that command.

`down` [count]
Move count (default 1) frames down the stack toward the innermost frame.
Then select and print the frame.

`frame` [n]`f` [n]
Select and print stack frame n.  Frame 0 is the currently executing,
or *innermost*, frame (function call); frame 1 is the frame that
called the innermost one. The highest-numbered frame is the one for the
main program.  The printed information consists of the frame number,
function and argument names, source file, and the source line.

`up` [count]
Move count (default 1) frames up the stack toward the outermost frame.
Then select and print the frame.

---

Next: [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands), Previous: [Execution Stack](#Execution-Stack), Up: [List of Debugger Commands](#List-of-Debugger-Commands)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.3.5 Obtaining Information About the Program and the Debugger State

Besides looking at the values of variables, there is often a need to get
other sorts of information about the state of your program and of the
debugging environment itself.  The `gawk` debugger has one command that
provides this information, appropriately called `info`.  `info`
is used with one of a number of arguments that tell it exactly what
you want to know:

`info`what`i`what
The value for what should be one of the following:

`args`
List arguments of the selected frame.

`break`
List all currently set breakpoints.

`display`
List all items in the automatic display list.

`frame`
Give a description of the selected stack frame.

`functions`
List all function definitions including source file names and
line numbers.

`locals`
List local variables of the selected frame.

`source`
Print the name of the current source file. Each time the program stops, the
current source file is the file containing the current instruction.
When the debugger first starts, the current source file is the first file
included via the -f option. The
&lsquo;list filename:lineno&rsquo; command can
be used at any time to change the current source.

`sources`
List all program sources.

`variables`
List all global variables.

`watch`
List all items in the watch list.

Additional commands give you control over the debugger, the ability to
save the debugger&rsquo;s state, and the ability to run debugger commands
from a file.  The commands are:

`option` [name[`=`value]]`o` [name[`=`value]]
Without an argument, display the available debugger options
and their current values. &lsquo;option name&rsquo; shows the current
value of the named option. &lsquo;option name=value&rsquo; assigns
a new value to the named option.
The available options are:

`history_size`
Set the maximum number of lines to keep in the history file
./.gawk_history.  The default is 100.

`listsize`
Specify the number of lines that `list` prints. The default is 15.

`outfile`
Send `gawk` output to a file; debugger output still goes
to standard output. An empty string (`""`) resets output to
standard output.

`prompt`
Change the debugger prompt. The default is &lsquo;gawk> &rsquo;.

`save_history` [`on` | `off`]
Save command history to file ./.gawk_history.
The default is `on`.

`save_options` [`on` | `off`]
Save current options to file ./.gawkrc upon exit.
The default is `on`.
Options are read back into the next session upon startup.

`trace` [`on` | `off`]
Turn instruction tracing on or off. The default is `off`.

`save`filename
Save the commands from the current session to the given file name,
so that they can be replayed using the `source` command.

`source`filename
Run command(s) from a file; an error in any command does not
terminate execution of subsequent commands. Comments (lines starting
with &lsquo;#&rsquo;) are allowed in a command file.
Empty lines are ignored; they do *not*
repeat the last command.
You can&rsquo;t restart the program by having more than one `run`
command in the file. Also, the list of commands may include additional
`source` commands; however, the `gawk` debugger will not source the
same file more than once in order to avoid infinite recursion.

In addition to, or instead of, the `source` command, you can use
the -D file or --debug=file command-line
options to execute commands from a file non-interactively
(see [Options](#Options)).

---

Previous: [Debugger Info](#Debugger-Info), Up: [List of Debugger Commands](#List-of-Debugger-Commands)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 14.3.6 Miscellaneous Commands

There are a few more commands that do not fit into the
previous categories, as follows:

`dump` [filename]
Dump byte code of the program to standard output or to the file
named in filename.  This prints a representation of the internal
instructions that `gawk` executes to implement the `awk`
commands in a program.  This can be very enlightening, as the following
partial dump of Davide Brini&rsquo;s obfuscated code
(see [Signature Program](#Signature-Program)) demonstrates:

    gawk> dump
    -|        # BEGIN
    -|
    -| [  1:0xfcd340] Op_rule           : [in_rule = BEGIN] [source_file = brini.awk]
    

    -| [  1:0xfcc240] Op_push_i         : "~" [MALLOC|STRING|STRCUR]
    -| [  1:0xfcc2a0] Op_push_i         : "~" [MALLOC|STRING|STRCUR]
    -| [  1:0xfcc280] Op_match          :
    -| [  1:0xfcc1e0] Op_store_var      : O
    -| [  1:0xfcc2e0] Op_push_i         : "==" [MALLOC|STRING|STRCUR]
    -| [  1:0xfcc340] Op_push_i         : "==" [MALLOC|STRING|STRCUR]
    -| [  1:0xfcc320] Op_equal          :
    -| [  1:0xfcc200] Op_store_var      : o
    -| [  1:0xfcc380] Op_push           : o
    -| [  1:0xfcc360] Op_plus_i         : 0 [MALLOC|NUMCUR|NUMBER]
    -| [  1:0xfcc220] Op_push_lhs       : o [do_reference = true]
    -| [  1:0xfcc300] Op_assign_plus    :
    -| [   :0xfcc2c0] Op_pop            :
    -| [  1:0xfcc400] Op_push           : O
    -| [  1:0xfcc420] Op_push_i         : "" [MALLOC|STRING|STRCUR]
    -| [   :0xfcc4a0] Op_no_op          :
    -| [  1:0xfcc480] Op_push           : O
    -| [   :0xfcc4c0] Op_concat         : [expr_count = 3] [concat_flag = 0]
    -| [  1:0xfcc3c0] Op_store_var      : x
    -| [  1:0xfcc440] Op_push_lhs       : X [do_reference = true]
    -| [  1:0xfcc3a0] Op_postincrement  :
    -| [  1:0xfcc4e0] Op_push           : x
    -| [  1:0xfcc540] Op_push           : o
    -| [  1:0xfcc500] Op_plus           :
    -| [  1:0xfcc580] Op_push           : o
    -| [  1:0xfcc560] Op_plus           :
    -| [  1:0xfcc460] Op_leq            :
    -| [   :0xfcc5c0] Op_jmp_false      : [target_jmp = 0xfcc5e0]
    -| [  1:0xfcc600] Op_push_i         : "%c" [MALLOC|STRING|STRCUR]
    -| [   :0xfcc660] Op_no_op          :
    -| [  1:0xfcc520] Op_assign_concat  : c
    -| [   :0xfcc620] Op_jmp            : [target_jmp = 0xfcc440]
    &hellip;
    -| [     2:0xfcc5a0] Op_K_printf         : [expr_count = 17] [redir_type = ""]
    -| [      :0xfcc140] Op_no_op            :
    -| [      :0xfcc1c0] Op_atexit           :
    -| [      :0xfcc640] Op_stop             :
    -| [      :0xfcc180] Op_no_op            :
    -| [      :0xfcd150] Op_after_beginfile  :
    

    -| [      :0xfcc160] Op_no_op            :
    -| [      :0xfcc1a0] Op_after_endfile    :
    gawk>
    

`exit`
Exit the debugger.
See the entry for &lsquo;quit&rsquo;, later in this list.

`help``h`
Print a list of all of the `gawk` debugger commands with a short
summary of their usage.  &lsquo;help command&rsquo; prints the information
about the command command.

`list` [`-` | `+` | n | filename`:`n | n&ndash;m | function]`l` [`-` | `+` | n | filename`:`n | n&ndash;m | function]
Print the specified lines (default 15) from the current source file
or the file named filename. The possible arguments to `list`
are as follows:

`-` (Minus)
Print lines before the lines last printed.

`+`
Print lines after the lines last printed.
`list` without any argument does the same thing.

n
Print lines centered around line number n.

n&ndash;m
Print lines from n to m.

filename`:`n
Print lines centered around line number n in
source file filename. This command may change the current source file.

function
Print lines centered around the beginning of the
function function. This command may change the current source file.

`quit``q`
Exit the debugger.  Debugging is great fun, but sometimes we all have
to tend to other obligations in life, and sometimes we find the bug
and are free to go on to the next one!  As we saw earlier, if you are
running a program, the debugger warns you when you type
&lsquo;q&rsquo; or &lsquo;quit&rsquo;, to make sure you really want to quit.

`trace` [`on` | `off`]
Turn on or off continuous printing of the instructions that are about to
be executed, along with the `awk` lines they
implement.  The default is `off`.

It is to be hoped that most of the &ldquo;opcodes&rdquo; in these instructions are
fairly self-explanatory, and using `stepi` and `nexti` while
`trace` is on will make them into familiar friends.

---

Next: [Limitations](#Limitations), Previous: [List of Debugger Commands](#List-of-Debugger-Commands), Up: [Debugger](#Debugger)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 14.4 Readline Support

If `gawk` is compiled with
[the GNU Readline library](http://cnswww.cns.cwru.edu/php/chet/readline/readline.html), you can take advantage of that library&rsquo;s
command completion and history expansion features. The following types
of completion are available:

Command completion
Command names.

Source file name completion
Source file names. Relevant commands are
`break`,
`clear`,
`list`,
`tbreak`,
and
`until`.

Argument completion
Non-numeric arguments to a command.
Relevant commands are `enable` and `info`.

Variable name completion
Global variable names, and function arguments in the current context
if the program is running. Relevant commands are
`display`,
`print`,
`set`,
and
`watch`.

---

Next: [Debugging Summary](#Debugging-Summary), Previous: [Readline Support](#Readline-Support), Up: [Debugger](#Debugger)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 14.5 Limitations

We hope you find the `gawk` debugger useful and enjoyable to work with,
but as with any program, especially in its early releases, it still has
some limitations.  A few that it&rsquo;s worth being aware of are:

-  At this point, the debugger does not give a detailed explanation of
what you did wrong when you type in something it doesn&rsquo;t like. Rather, it just
responds &lsquo;syntax error&rsquo;.  When you do figure out what your mistake was,
though, you&rsquo;ll feel like a real guru.

-  If you perused the dump of opcodes in [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)
(or if you are already familiar with `gawk` internals),
you will realize that much of the internal manipulation of data
in `gawk`, as in many interpreters, is done on a stack.
`Op_push`, `Op_pop`, and the like are the &ldquo;bread and butter&rdquo; of
most `gawk` code.

Unfortunately, as of now, the `gawk`
debugger does not allow you to examine the stack&rsquo;s contents.
That is, the intermediate results of expression evaluation are on the
stack, but cannot be printed.  Rather, only variables that are defined
in the program can be printed.  Of course, a workaround for
this is to use more explicit variables at the debugging stage and then
change back to obscure, perhaps more optimal code later.

-  There is no way to look &ldquo;inside&rdquo; the process of compiling
regular expressions to see if you got it right.  As an `awk`
programmer, you are expected to know the meaning of
`/[^[:alnum:][:blank:]]/`.

-  The `gawk` debugger is designed to be used by running a program (with all its
parameters) on the command line, as described in [Debugger Invocation](#Debugger-Invocation).
There is no way (as of now) to attach or &ldquo;break into&rdquo; a running program.
This seems reasonable for a language that is used mainly for quickly
executing, short programs.

-  The `gawk` debugger only accepts source code supplied with the -f option.

---

Previous: [Limitations](#Limitations), Up: [Debugger](#Debugger)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 14.6 Summary

-  Programs rarely work correctly the first time.  Finding bugs
is called debugging, and a program that helps you find bugs is a
debugger.  `gawk` has a built-in debugger that works very
similarly to the GNU Debugger, GDB.

-  Debuggers let you step through your program one statement at a time,
examine and change variable and array values, and do a number of other
things that let you understand what your program is actually doing (as
opposed to what it is supposed to do).

-  Like most debuggers, the `gawk` debugger works in terms of stack
frames, and lets you set both breakpoints (stop at a point in the code)
and watchpoints (stop when a data value changes).

-  The debugger command set is fairly complete, providing control over
breakpoints, execution, viewing and changing data, working with the stack,
getting information, and other tasks.

-  If the GNU Readline library is available when `gawk` is
compiled, it is used by the debugger to provide command-line history
and editing.

-  Usually, the debugger does not not affect the
program being debugged, but occasionally it can.

---

Next: [Dynamic Extensions](#Dynamic-Extensions), Previous: [Debugger](#Debugger), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## 15 Arithmetic and Arbitrary-Precision Arithmetic with `gawk`

This chapter introduces some basic concepts relating to
how computers do arithmetic and defines some important terms.
It then proceeds to describe floating-point arithmetic,
which is what `awk` uses for all its computations, including a
discussion of arbitrary-precision floating-point arithmetic, which is
a feature available only in `gawk`. It continues on to present
arbitrary-precision integers, and concludes with a description of some
points where `gawk` and the POSIX standard are not quite in
agreement.

> NOTE: Most users of `gawk` can safely skip this chapter.
> But if you want to do scientific calculations with `gawk`,
> this is the place to be.

&bull; [Computer Arithmetic](#Computer-Arithmetic):  A quick intro to computer math.
&bull; [Math Definitions](#Math-Definitions):  Defining terms used.
&bull; [MPFR features](#MPFR-features):  The MPFR features in `gawk`.
&bull; [FP Math Caution](#FP-Math-Caution):  Things to know.
&bull; [Arbitrary Precision Integers](#Arbitrary-Precision-Integers):  Arbitrary Precision Integer Arithmetic with
                                  `gawk`.
&bull; [Checking for MPFR](#Checking-for-MPFR):  How to check if MPFR is available.
&bull; [POSIX Floating Point Problems](#POSIX-Floating-Point-Problems):  Standards Versus Existing Practice.
&bull; [Floating point summary](#Floating-point-summary):  Summary of floating point discussion.

---

Next: [Math Definitions](#Math-Definitions), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.1 A General Description of Computer Arithmetic

Until now, we have worked with data as either numbers or
strings.  Ultimately, however, computers represent everything in terms
of *binary digits*, or *bits*.  A decimal digit can take on any
of 10 values: zero through nine.  A binary digit can take on any of two
values, zero or one.  Using binary, computers (and computer software)
can represent and manipulate numerical and character data.  In general,
the more bits you can use to represent a particular thing, the greater
the range of possible values it can take on.

Modern computers support at least two, and often more, ways to do
arithmetic.  Each kind of arithmetic uses a different representation
(organization of the bits) for the numbers.  The kinds of arithmetic
that interest us are:

Decimal arithmetic
This is the kind of arithmetic you learned in elementary school, using
paper and pencil (and/or a calculator). In theory, numbers can have an
arbitrary number of digits on either side (or both sides) of the decimal
point, and the results of a computation are always exact.

Some modern systems can do decimal arithmetic in hardware, but usually you
need a special software library to provide access to these instructions.
There are also libraries that do decimal arithmetic entirely in software.

Despite the fact that some users expect `gawk` to be performing
decimal arithmetic,[96](#FOOT96) it does not do so.

Integer arithmetic
In school, integer values were referred to as &ldquo;whole&rdquo; numbers&mdash;that
is, numbers without any fractional part, such as 1, 42, or -17.
The advantage to integer numbers is that they represent values exactly.
The disadvantage is that their range is limited.

In computers, integer values come in two flavors: *signed* and
*unsigned*.  Signed values may be negative or positive, whereas
unsigned values are always greater than or equal
to zero.

In computer systems, integer arithmetic is exact, but the possible
range of values is limited.  Integer arithmetic is generally faster than
floating-point arithmetic.

Floating-point arithmetic
Floating-point numbers represent what were called in school &ldquo;real&rdquo;
numbers (i.e., those that have a fractional part, such as 3.1415927).
The advantage to floating-point numbers is that they can represent a
much larger range of values than can integers.  The disadvantage is that
there are numbers that they cannot represent exactly.

Modern systems support floating-point arithmetic in hardware, with a
limited range of values.  There are software libraries that allow
the use of arbitrary-precision floating-point calculations.

POSIX `awk` uses *double-precision* floating-point numbers, which
can hold more digits than *single-precision* floating-point numbers.
`gawk` has facilities for performing arbitrary-precision
floating-point arithmetic, which we describe in more detail shortly.

Computers work with integer and floating-point values of different
ranges. Integer values are usually either 32 or 64 bits in size.
Single-precision floating-point values occupy 32 bits, whereas double-precision
floating-point values occupy 64 bits.
(Quadruple-precision floating point values also exist. They occupy 128 bits,
but such numbers are not available in `awk`.)
Floating-point values are always
signed. The possible ranges of values are shown in [Table 15.1](#table_002dnumeric_002dranges)
and [Table 15.2](#table_002dfloating_002dpoint_002dranges).

RepresentationMinimum valueMaximum value32-bit signed integer-2,147,483,6482,147,483,64732-bit unsigned integer04,294,967,29564-bit signed integer-9,223,372,036,854,775,8089,223,372,036,854,775,80764-bit unsigned integer018,446,744,073,709,551,615

**Table 15.1: **Value ranges for integer representations

RepresentationMinimum positive nonzero valueMinimum finite valueMaximum finite valueSingle-precision floating-point1.175494*10-38-3.402823*10383.402823*1038Double-precision floating-point2.225074*10-308-1.797693*103081.797693*10308Quadruple-precision floating-point3.362103*10-4932-1.189731*1049321.189731*104932

**Table 15.2: **Approximate value ranges for floating-point number representations

---

Next: [MPFR features](#MPFR-features), Previous: [Computer Arithmetic](#Computer-Arithmetic), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.2 Other Stuff to Know

The rest of this chapter uses a number of terms. Here are some
informal definitions that should help you work your way through the material
here:

*Accuracy*
A floating-point calculation&rsquo;s accuracy is how close it comes
to the real (paper and pencil) value.

*Error*
The difference between what the result of a computation &ldquo;should be&rdquo;
and what it actually is.  It is best to minimize error as much
as possible.

*Exponent*
The order of magnitude of a value;
some number of bits in a floating-point value store the exponent.

*Inf*
A special value representing infinity. Operations involving another
number and infinity produce infinity.

*NaN*
&ldquo;Not a number.&rdquo;[97](#FOOT97) A special value that
results from attempting a calculation that has no answer as a real number.
In such a case, programs can either receive a floating-point exception,
or get `NaN` back as the result. The IEEE 754 standard recommends
that systems return `NaN`. Some examples:

`sqrt(-1)`
This makes sense in the range of complex numbers, but not in the
range of real numbers, so the result is `NaN`.

`log(-8)`
-8 is out of the domain of `log()`, so the result is `NaN`.

*Normalized*
How the significand (see later in this list) is usually stored. The
value is adjusted so that the first bit is one, and then that leading
one is assumed instead of physically stored.  This provides one
extra bit of precision.

*Precision*
The number of bits used to represent a floating-point number.
The more bits, the more digits you can represent.
Binary and decimal precisions are related approximately, according to the
formula:

    prec = 3.322 * dps

Here, *prec* denotes the binary precision
(measured in bits) and *dps* (short for decimal places)
is the decimal digits.

*Rounding mode*
How numbers are rounded up or down when necessary.
More details are provided later.

*Significand*
A floating-point value consists of the significand multiplied by 10
to the power of the exponent. For example, in `1.2345e67`,
the significand is `1.2345`.

*Stability*
From [the Wikipedia article on numerical stability](https://en.wikipedia.org/wiki/Numerical_stability):
&ldquo;Calculations that can be proven not to magnify approximation errors
are called *numerically stable*.&rdquo;

See [the Wikipedia article on accuracy and precision](https://en.wikipedia.org/wiki/Accuracy_and_precision) for more information
on some of those terms.

On modern systems, floating-point hardware uses the representation and
operations defined by the IEEE 754 standard.
Three of the standard IEEE 754 types are 32-bit single precision,
64-bit double precision, and 128-bit quadruple precision.
The standard also specifies extended precision formats
to allow greater precisions and larger exponent ranges.
(`awk` uses only the 64-bit double-precision format.)

[Table 15.3](#table_002dieee_002dformats) lists the precision and exponent
field values for the basic IEEE 754 binary formats.

NameTotal bitsPrecisionMinimum exponentMaximum exponentSingle3224-126+127Double6453-1022+1023Quadruple128113-16382+16383

**Table 15.3: **Basic IEEE format values

> NOTE: The precision numbers include the implied leading one that gives them
> one extra bit of significand.

---

Next: [FP Math Caution](#FP-Math-Caution), Previous: [Math Definitions](#Math-Definitions), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.3 Arbitrary-Precision Arithmetic Features in `gawk`

By default, `gawk` uses the double-precision floating-point values
supplied by the hardware of the system it runs on.  However, if it was
compiled to do so, and the -M command-line option is supplied,
`gawk` uses the [GNU MPFR](http://www.mpfr.org) and [GNU MP](https://gmplib.org) (GMP) libraries for
arbitrary-precision arithmetic on numbers.  You can see if MPFR support
is available like so:

    $ gawk --version
    -| GNU Awk 4.1.2, API: 1.1 (GNU MPFR 3.1.0-p3, GNU MP 5.0.2)
    -| Copyright (C) 1989, 1991-2015 Free Software Foundation.
    &hellip;
    

(You may see different version numbers than what&rsquo;s shown here. That&rsquo;s OK;
what&rsquo;s important is to see that GNU MPFR and GNU MP are listed in
the output.)

Additionally, there are a few elements available in the `PROCINFO`
array to provide information about the MPFR and GMP libraries
(see [Auto-set](#Auto_002dset)).

The MPFR library provides precise control over precisions and rounding
modes, and gives correctly rounded, reproducible, platform-independent
results.  With the -M command-line option,
all floating-point arithmetic operators and numeric functions
can yield results to any desired precision level supported by MPFR.

Two predefined variables, `PREC` and `ROUNDMODE`,
provide control over the working precision and the rounding mode.
The precision and the rounding mode are set globally for every operation
to follow.
See [Setting precision](#Setting-precision) and [Setting the rounding mode](#Setting-the-rounding-mode)
for more information.

---

Next: [Arbitrary Precision Integers](#Arbitrary-Precision-Integers), Previous: [MPFR features](#MPFR-features), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.4 Floating-Point Arithmetic: Caveat Emptor!

> Math class is tough!

&mdash; *Teen Talk Barbie, July 1992*

This section provides a high-level overview of the issues
involved when doing lots of floating-point arithmetic.[98](#FOOT98)
The discussion applies to both hardware and arbitrary-precision
floating-point arithmetic.

> CAUTION: The material here is purposely general. If you need to do serious
> computer arithmetic, you should do some research first, and not
> rely just on what we tell you.

&bull; [Inexactness of computations](#Inexactness-of-computations):  Floating point math is not exact.
&bull; [Getting Accuracy](#Getting-Accuracy):  Getting more accuracy takes some work.
&bull; [Try To Round](#Try-To-Round):  Add digits and round.
&bull; [Setting precision](#Setting-precision):  How to set the precision.
&bull; [Setting the rounding mode](#Setting-the-rounding-mode):  How to set the rounding mode.

---

Next: [Getting Accuracy](#Getting-Accuracy), Up: [FP Math Caution](#FP-Math-Caution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.1 Floating-Point Arithmetic Is Not Exact

Binary floating-point representations and arithmetic are inexact.
Simple values like 0.1 cannot be precisely represented using
binary floating-point numbers, and the limited precision of
floating-point numbers means that slight changes in
the order of operations or the precision of intermediate storage
can change the result. To make matters worse, with arbitrary-precision
floating-point arithmetic, you can set the precision before starting a
computation, but then you cannot be sure of the number of significant
decimal places in the final result.

&bull; [Inexact representation](#Inexact-representation):  Numbers are not exactly represented.
&bull; [Comparing FP Values](#Comparing-FP-Values):  How to compare floating point values.
&bull; [Errors accumulate](#Errors-accumulate):  Errors get bigger as they go.

---

Next: [Comparing FP Values](#Comparing-FP-Values), Up: [Inexactness of computations](#Inexactness-of-computations)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.1.1 Many Numbers Cannot Be Represented Exactly

So, before you start to write any code, you should think
about what you really want and what&rsquo;s really happening. Consider the
two numbers in the following example:

    x = 0.875             # 1/2 + 1/4 + 1/8
    y = 0.425
    

Unlike the number in `y`, the number stored in `x`
is exactly representable
in binary because it can be written as a finite sum of one or
more fractions whose denominators are all powers of two.
When `gawk` reads a floating-point number from
program source, it automatically rounds that number to whatever
precision your machine supports. If you try to print the numeric
content of a variable using an output format string of `"%.17g"`,
it may not produce the same number as you assigned to it:

    $ gawk 'BEGIN { x = 0.875; y = 0.425
    >               printf("%0.17g, %0.17g\n", x, y) }'
    -| 0.875, 0.42499999999999999
    

Often the error is so small you do not even notice it, and if you do,
you can always specify how much precision you would like in your output.
Usually this is a format string like `"%.15g"`, which, when
used in the previous example, produces an output identical to the input.

---

Next: [Errors accumulate](#Errors-accumulate), Previous: [Inexact representation](#Inexact-representation), Up: [Inexactness of computations](#Inexactness-of-computations)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.1.2 Be Careful Comparing Values

Because the underlying representation can be a little bit off from the exact value,
comparing floating-point values to see if they are exactly equal is generally a bad idea.
Here is an example where it does not work like you would expect:

    $ gawk 'BEGIN { print (0.1 + 12.2 == 12.3) }'
    -| 0
    

The general wisdom when comparing floating-point values is to see if
they are within some small range of each other (called a *delta*,
or *tolerance*).
You have to decide how small a delta is important to you. Code to do
this looks something like the following:

    delta = 0.00001                 # for example
    difference = abs(a) - abs(b)    # subtract the two values
    if (difference < delta)
        # all ok
    else
        # not ok
    

(We assume that you have a simple absolute value function named
`abs()` defined elsewhere in your program.)

---

Previous: [Comparing FP Values](#Comparing-FP-Values), Up: [Inexactness of computations](#Inexactness-of-computations)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.1.3 Errors Accumulate

The loss of accuracy during a single computation with floating-point
numbers usually isn&rsquo;t enough to worry about. However, if you compute a
value that is the result of a sequence of floating-point operations,
the error can accumulate and greatly affect the computation itself.
Here is an attempt to compute the value of pi using one of its
many series representations:

    BEGIN {
        x = 1.0 / sqrt(3.0)
        n = 6
        for (i = 1; i < 30; i++) {
            n = n * 2.0
            x = (sqrt(x * x + 1) - 1) / x
            printf("%.15f\n", n * x)
        }
    }
    

When run, the early errors propagate through later computations,
causing the loop to terminate prematurely after attempting to divide by zero:

    $ gawk -f pi.awk
    -| 3.215390309173475
    -| 3.159659942097510
    -| 3.146086215131467
    -| 3.142714599645573
    &hellip;
    -| 3.224515243534819
    -| 2.791117213058638
    -| 0.000000000000000
    error&rarr; gawk: pi.awk:6: fatal: division by zero attempted
    

Here is an additional example where the inaccuracies in internal representations
yield an unexpected result:

    $ gawk 'BEGIN {
    >   for (d = 1.1; d <= 1.5; d += 0.1)    # loop five times (?)
    >       i++
    >   print i
    > }'
    -| 4
    

---

Next: [Try To Round](#Try-To-Round), Previous: [Inexactness of computations](#Inexactness-of-computations), Up: [FP Math Caution](#FP-Math-Caution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.2 Getting the Accuracy You Need

Can arbitrary-precision arithmetic give exact results? There are
no easy answers. The standard rules of algebra often do not apply
when using floating-point arithmetic.
Among other things, the distributive and associative laws
do not hold completely, and order of operation may be important
for your computation. Rounding error, cumulative precision loss,
and underflow are often troublesome.

When `gawk` tests the expressions &lsquo;0.1 + 12.2&rsquo; and
&lsquo;12.3&rsquo; for equality using the machine double-precision arithmetic,
it decides that they are not equal!  (See [Comparing FP Values](#Comparing-FP-Values).)
You can get the result you want by increasing the precision; 56 bits in
this case does the job:

    $ gawk -M -v PREC=56 'BEGIN { print (0.1 + 12.2 == 12.3) }'
    -| 1
    

If adding more bits is good, perhaps adding even more bits of
precision is better?
Here is what happens if we use an even larger value of `PREC`:

    $ gawk -M -v PREC=201 'BEGIN { print (0.1 + 12.2 == 12.3) }'
    -| 0
    

This is not a bug in `gawk` or in the MPFR library.
It is easy to forget that the finite number of bits used to store the value
is often just an approximation after proper rounding.
The test for equality succeeds if and only if *all* bits in the two operands
are exactly the same. Because this is not necessarily true after floating-point
computations with a particular precision and effective rounding mode,
a straight test for equality may not work. Instead, compare the
two numbers to see if they are within the desirable delta of each other.

In applications where 15 or fewer decimal places suffice,
hardware double-precision arithmetic can be adequate, and is usually much faster.
But you need to keep in mind that every floating-point operation
can suffer a new rounding error with catastrophic consequences, as illustrated
by our earlier attempt to compute the value of pi.
Extra precision can greatly enhance the stability and the accuracy
of your computation in such cases.

Additionally, you should understand that
repeated addition is not necessarily equivalent to multiplication
in floating-point arithmetic. In the example in
[Errors accumulate](#Errors-accumulate):

    $ gawk 'BEGIN {
    >   for (d = 1.1; d <= 1.5; d += 0.1)    # loop five times (?)
    >       i++
    >   print i
    > }'
    -| 4
    

you may or may not succeed in getting the correct result by choosing
an arbitrarily large value for `PREC`. Reformulation of
the problem at hand is often the correct approach in such situations.

---

Next: [Setting precision](#Setting-precision), Previous: [Getting Accuracy](#Getting-Accuracy), Up: [FP Math Caution](#FP-Math-Caution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.3 Try a Few Extra Bits of Precision and Rounding

Instead of arbitrary-precision floating-point arithmetic,
often all you need is an adjustment of your logic
or a different order for the operations in your calculation.
The stability and the accuracy of the computation of pi
in the earlier example can be enhanced by using the following
simple algebraic transformation:

    (sqrt(x * x + 1) - 1) / x &equiv; x / (sqrt(x * x + 1) + 1)
    

After making this change, the program converges to
pi in under 30 iterations:

    $ gawk -f pi2.awk
    -| 3.215390309173473
    -| 3.159659942097501
    -| 3.146086215131436
    -| 3.142714599645370
    -| 3.141873049979825
    &hellip;
    -| 3.141592653589797
    -| 3.141592653589797
    

---

Next: [Setting the rounding mode](#Setting-the-rounding-mode), Previous: [Try To Round](#Try-To-Round), Up: [FP Math Caution](#FP-Math-Caution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.4 Setting the Precision

`gawk` uses a global working precision; it does not keep track of
the precision or accuracy of individual numbers. Performing an arithmetic
operation or calling a built-in function rounds the result to the current
working precision. The default working precision is 53 bits, which you can
modify using the predefined variable `PREC`. You can also set the
value to one of the predefined case-insensitive strings
shown in [Table 15.4](#table_002dpredefined_002dprecision_002dstrings),
to emulate an IEEE 754 binary format.

`PREC`IEEE 754 binary format`"half"`16-bit half-precision`"single"`Basic 32-bit single precision`"double"`Basic 64-bit double precision`"quad"`Basic 128-bit quadruple precision`"oct"`256-bit octuple precision

**Table 15.4: **Predefined precision strings for `PREC`

The following example illustrates the effects of changing precision
on arithmetic operations:

    $ gawk -M -v PREC=100 'BEGIN { x = 1.0e-400; print x + 0
    >   PREC = "double"; print x + 0 }'
    -| 1e-400
    -| 0
    

> CAUTION: Be wary of floating-point constants! When reading a floating-point
> constant from program source code, `gawk` uses the default
> precision (that of a C `double`), unless overridden by an assignment
> to the special variable `PREC` on the command line, to store it
> internally as an MPFR number.  Changing the precision using `PREC`
> in the program text does *not* change the precision of a constant.
> 
> 
> If you need to represent a floating-point constant at a higher precision
> than the default and cannot use a command-line assignment to `PREC`,
> you should either specify the constant as a string, or as a rational
> number, whenever possible. The following example illustrates the
> differences among various ways to print a floating-point constant:
> 
> 
> 
>     $ gawk -M 'BEGIN { PREC = 113; printf("%0.25f\n", 0.1) }'
>     -| 0.1000000000000000055511151
>     $ gawk -M -v PREC=113 'BEGIN { printf("%0.25f\n", 0.1) }'
>     -| 0.1000000000000000000000000
>     $ gawk -M 'BEGIN { PREC = 113; printf("%0.25f\n", "0.1") }'
>     -| 0.1000000000000000000000000
>     $ gawk -M 'BEGIN { PREC = 113; printf("%0.25f\n", 1/10) }'
>     -| 0.1000000000000000000000000
>     

---

Previous: [Setting precision](#Setting-precision), Up: [FP Math Caution](#FP-Math-Caution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 15.4.5 Setting the Rounding Mode

The `ROUNDMODE` variable provides
program-level control over the rounding mode.
The correspondence between `ROUNDMODE` and the IEEE
rounding modes is shown in [Table 15.5](#table_002dgawk_002drounding_002dmodes).

Rounding modeIEEE name`ROUNDMODE`Round to nearest, ties to even`roundTiesToEven``"N"` or `"n"`Round toward positive infinity`roundTowardPositive``"U"` or `"u"`Round toward negative infinity`roundTowardNegative``"D"` or `"d"`Round toward zero`roundTowardZero``"Z"` or `"z"`Round away from zero`"A"` or `"a"`

**Table 15.5: **`gawk` rounding modes

`ROUNDMODE` has the default value `"N"`, which
selects the IEEE 754 rounding mode `roundTiesToEven`.
In [Table 15.5](#table_002dgawk_002drounding_002dmodes), the value `"A"` selects
rounding away from zero. This is only available if your version of the
MPFR library supports it; otherwise, setting `ROUNDMODE` to `"A"`
has no effect.

The default mode `roundTiesToEven` is the most preferred,
but the least intuitive. This method does the obvious thing for most values,
by rounding them up or down to the nearest digit.
For example, rounding 1.132 to two digits yields 1.13,
and rounding 1.157 yields 1.16.

However, when it comes to rounding a value that is exactly halfway between,
things do not work the way you probably learned in school.
In this case, the number is rounded to the nearest even digit.
So rounding 0.125 to two digits rounds down to 0.12,
but rounding 0.6875 to three digits rounds up to 0.688.
You probably have already encountered this rounding mode when
using `printf` to format floating-point numbers.
For example:

    BEGIN {
        x = -4.5
        for (i = 1; i < 10; i++) {
            x += 1.0
            printf("%4.1f => %2.0f\n", x, x)
        }
    }
    

produces the following output when run on the author&rsquo;s system:[99](#FOOT99)

    -3.5 => -4
    -2.5 => -2
    -1.5 => -2
    -0.5 => 0
     0.5 => 0
     1.5 => 2
     2.5 => 2
     3.5 => 4
     4.5 => 4
    

The theory behind `roundTiesToEven` is that it more or less evenly
distributes upward and downward rounds of exact halves, which might
cause any accumulating round-off error to cancel itself out. This is the
default rounding mode for IEEE 754 computing functions and operators.

Rounding Modes and Conversion

It&rsquo;s important to understand that, along with `CONVFMT` and
`OFMT`, the rounding mode affects how numbers are converted to strings.
For example, consider the following program:

    BEGIN {
        pi = 3.1416
        OFMT = "%.f"        # Print value as integer
        print pi            # ROUNDMODE = "N" by default.
        ROUNDMODE = "U"     # Now change ROUNDMODE
        print pi
    }
    

Running this program produces this output:

    $ gawk -M -f roundmode.awk
    -| 3
    -| 4
    

The other rounding modes are rarely used.  Rounding toward positive infinity
(`roundTowardPositive`) and toward negative infinity
(`roundTowardNegative`) are often used to implement interval
arithmetic, where you adjust the rounding mode to calculate upper and
lower bounds for the range of output. The `roundTowardZero` mode can
be used for converting floating-point numbers to integers.  When rounding
away from zero, the nearest number with magnitude greater than or equal to
the value is selected.

Some numerical analysts will tell you that your choice of rounding
style has tremendous impact on the final outcome, and advise you to
wait until final output for any rounding. Instead, you can often avoid
round-off error problems by setting the precision initially to some
value sufficiently larger than the final desired precision, so that
the accumulation of round-off error does not influence the outcome.
If you suspect that results from your computation are sensitive to
accumulation of round-off error, look for a significant difference in
output when you change the rounding mode to be sure.

---

Next: [Checking for MPFR](#Checking-for-MPFR), Previous: [FP Math Caution](#FP-Math-Caution), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.5 Arbitrary-Precision Integer Arithmetic with `gawk`

When given the -M option,
`gawk` performs all integer arithmetic using GMP arbitrary-precision
integers.  Any number that looks like an integer in a source
or data file is stored as an arbitrary-precision integer.  The size
of the integer is limited only by the available memory.  For example,
the following computes
5432,
the result of which is beyond the
limits of ordinary hardware double-precision floating-point values:

    $ gawk -M 'BEGIN {
    >   x = 5^4^3^2
    >   print "number of digits =", length(x)
    >   print substr(x, 1, 20), "...", substr(x, length(x) - 19, 20)
    > }'
    -| number of digits = 183231
    -| 62060698786608744707 ... 92256259918212890625
    

If instead you were to compute the same value using arbitrary-precision
floating-point values, the precision needed for correct output (using
the formula
&lsquo;prec = 3.322 * dps&rsquo;)
would be 3.322 x 183231,
or 608693.

The result from an arithmetic operation with an integer and a floating-point value
is a floating-point value with a precision equal to the working precision.
The following program calculates the eighth term in
Sylvester&rsquo;s sequence[100](#FOOT100)
using a recurrence:

    $ gawk -M 'BEGIN {
    >   s = 2.0
    >   for (i = 1; i <= 7; i++)
    >       s = s * (s - 1) + 1
    >   print s
    > }'
    -| 113423713055421845118910464
    

The output differs from the actual number, 113,423,713,055,421,844,361,000,443,
because the default precision of 53 bits is not enough to represent the
floating-point results exactly. You can either increase the precision
(100 bits is enough in this case), or replace the floating-point constant
&lsquo;2.0&rsquo; with an integer, to perform all computations using integer
arithmetic to get the correct output.

Sometimes `gawk` must implicitly convert an arbitrary-precision
integer into an arbitrary-precision floating-point value.  This is
primarily because the MPFR library does not always provide the relevant
interface to process arbitrary-precision integers or mixed-mode numbers
as needed by an operation or function.  In such a case, the precision is
set to the minimum value necessary for exact conversion, and the working
precision is not used for this purpose.  If this is not what you need or
want, you can employ a subterfuge and convert the integer to floating
point first, like this:

    gawk -M 'BEGIN { n = 13; print (n + 0.0) % 2.0 }'
    

You can avoid this issue altogether by specifying the number as a floating-point value
to begin with:

    gawk -M 'BEGIN { n = 13.0; print n % 2.0 }'
    

Note that for this particular example, it is likely best
to just use the following:

    gawk -M 'BEGIN { n = 13; print n % 2 }'
    

When dividing two arbitrary precision integers with either
&lsquo;/&rsquo; or &lsquo;%&rsquo;, the result is typically an arbitrary
precision floating point value (unless the denominator evenly
divides into the numerator).

---

Next: [POSIX Floating Point Problems](#POSIX-Floating-Point-Problems), Previous: [Arbitrary Precision Integers](#Arbitrary-Precision-Integers), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.6 How To Check If MPFR Is Available

Occasionally, you might like to be able to check if `gawk`
was invoked with the -M option, enabling arbitrary-precision
arithmetic.  You can do so with the following function, contributed
by Andrew Schorr:

    # adequate_math_precision --- return true if we have enough bits
    
    function adequate_math_precision(n)
    {
        return (1 != (1+(1/(2^(n-1)))))
    }
    

Here is code that invokes the function in order to check
if arbitrary-precision arithmetic is available:

    BEGIN {
        # How many bits of mantissa precision are required
        # for this program to function properly?
        fpbits = 123
    
        # We hope that we were invoked with MPFR enabled. If so, the
        # following statement should configure calculations to our desired
        # precision.
        PREC = fpbits
    
        if (! adequate_math_precision(fpbits)) {
            print("Error: insufficient computation precision available.\n" \
                  "Try again with the -M argument?") > "/dev/stderr"
            # Note: you may need to set a flag here to bail out of END rules
            exit 1
        }
    }
    

Please be aware that `exit` will jump to the `END` rules, if present (see [Exit Statement](#Exit-Statement)).

---

Next: [Floating point summary](#Floating-point-summary), Previous: [Checking for MPFR](#Checking-for-MPFR), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.7 Standards Versus Existing Practice

Historically, `awk` has converted any nonnumeric-looking string
to the numeric value zero, when required.  Furthermore, the original
definition of the language and the original POSIX standards specified that
`awk` only understands decimal numbers (base 10), and not octal
(base 8) or hexadecimal numbers (base 16).

Changes in the language of the
2001 and 2004 POSIX standards can be interpreted to imply that `awk`
should support additional features.  These features are:

-  Interpretation of floating-point data values specified in hexadecimal
notation (e.g., `0xDEADBEEF`). (Note: data values, *not*
source code constants.)

-  Support for the special IEEE 754 floating-point values &ldquo;not a number&rdquo;
(NaN), positive infinity (&ldquo;inf&rdquo;), and negative infinity (&ldquo;-inf&rdquo;).
In particular, the format for these values is as specified by the ISO 1999
C standard, which ignores case and can allow implementation-dependent additional
characters after the &lsquo;nan&rsquo; and allow either &lsquo;inf&rsquo; or &lsquo;infinity&rsquo;.

The first problem is that both of these are clear changes to historical
practice:

-  The `gawk` maintainer feels that supporting hexadecimal
floating-point values, in particular, is ugly, and was never intended by the
original designers to be part of the language.

-  Allowing completely alphabetic strings to have valid numeric
values is also a very severe departure from historical practice.

The second problem is that the `gawk` maintainer feels that this
interpretation of the standard, which required a certain amount of
&ldquo;language lawyering&rdquo; to arrive at in the first place, was not even
intended by the standard developers.  In other words, &ldquo;We see how you
got where you are, but we don&rsquo;t think that that&rsquo;s where you want to be.&rdquo;

Recognizing these issues, but attempting to provide compatibility
with the earlier versions of the standard,
the 2008 POSIX standard added explicit wording to allow, but not require,
that `awk` support hexadecimal floating-point values and
special values for &ldquo;not a number&rdquo; and infinity.

Although the `gawk` maintainer continues to feel that
providing those features is inadvisable,
nevertheless, on systems that support IEEE floating point, it seems
reasonable to provide *some* way to support NaN and infinity values.
The solution implemented in `gawk` is as follows:

-  With the --posix command-line option, `gawk` becomes
&ldquo;hands off.&rdquo; String values are passed directly to the system library&rsquo;s
`strtod()` function, and if it successfully returns a numeric value,
that is what&rsquo;s used.[101](#FOOT101)
By definition, the results are not portable across
different systems.  They are also a little surprising:

    $ echo nanny | gawk --posix '{ print $1 + 0 }'
    -| nan
    $ echo 0xDeadBeef | gawk --posix '{ print $1 + 0 }'
    -| 3735928559
    

-  Without --posix, `gawk` interprets the four string values
&lsquo;+inf&rsquo;,
&lsquo;-inf&rsquo;,
&lsquo;+nan&rsquo;,
and
&lsquo;-nan&rsquo;
specially, producing the corresponding special numeric values.
The leading sign acts a signal to `gawk` (and the user)
that the value is really numeric.  Hexadecimal floating point is
not supported (unless you also use --non-decimal-data,
which is *not* recommended). For example:

    $ echo nanny | gawk '{ print $1 + 0 }'
    -| 0
    $ echo +nan | gawk '{ print $1 + 0 }'
    -| nan
    $ echo 0xDeadBeef | gawk '{ print $1 + 0 }'
    -| 0
    

`gawk` ignores case in the four special values.
Thus, &lsquo;+nan&rsquo; and &lsquo;+NaN&rsquo; are the same.

---

Previous: [POSIX Floating Point Problems](#POSIX-Floating-Point-Problems), Up: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 15.8 Summary

-  Most computer arithmetic is done using either integers or floating-point
values.  Standard `awk` uses double-precision
floating-point values.

-  In the early 1990s Barbie mistakenly said, &ldquo;Math class is tough!&rdquo;
Although math isn&rsquo;t tough, floating-point arithmetic isn&rsquo;t the same
as pencil-and-paper math, and care must be taken:

- - Not all numbers can be represented exactly.

- - Comparing values should use a delta, instead of being done directly
with &lsquo;==&rsquo; and &lsquo;!=&rsquo;.

- - Errors accumulate.

- - Operations are not always truly associative or distributive.

-  Increasing the accuracy can help, but it is not a panacea.

-  Often, increasing the accuracy and then rounding to the desired
number of digits produces reasonable results.

-  Use -M (or --bignum) to enable MPFR
arithmetic. Use `PREC` to set the precision in bits, and
`ROUNDMODE` to set the IEEE 754 rounding mode.

-  With -M, `gawk` performs
arbitrary-precision integer arithmetic using the GMP library.
This is faster and more space-efficient than using MPFR for
the same calculations.

-  There are several areas with respect to floating-point
numbers where `gawk` disagrees with the POSIX standard.
It pays to be aware of them.

-  Overall, there is no need to be unduly suspicious about the results from
floating-point arithmetic. The lesson to remember is that floating-point
arithmetic is always more complex than arithmetic using pencil and
paper. In order to take advantage of the power of floating-point arithmetic,
you need to know its limitations and work within them. For most casual
use of floating-point arithmetic, you will often get the expected result
if you simply round the display of your final results to the correct number
of significant decimal digits.

-  As general advice, avoid presenting numerical data in a manner that
implies better precision than is actually the case.

---

Next: [Language History](#Language-History), Previous: [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## 16 Writing Extensions for `gawk`

It is possible to add new functions written in C or C++ to `gawk` using
dynamically loaded libraries. This facility is available on systems
that support the C `dlopen()` and `dlsym()`
functions.  This chapter describes how to create extensions
using code written in C or C++.

If you don&rsquo;t know anything about C programming, you can safely skip this
chapter, although you may wish to review the documentation on the
extensions that come with `gawk` (see [Extension Samples](#Extension-Samples)),
and the information on the `gawkextlib` project (see [gawkextlib](#gawkextlib)).
The sample extensions are automatically built and installed when
`gawk` is.

> NOTE: When --sandbox is specified, extensions are disabled
> (see [Options](#Options)).

&bull; [Extension Intro](#Extension-Intro):  What is an extension.
&bull; [Plugin License](#Plugin-License):  A note about licensing.
&bull; [Extension Mechanism Outline](#Extension-Mechanism-Outline):  An outline of how it works.
&bull; [Extension API Description](#Extension-API-Description):  A full description of the API.
&bull; [Finding Extensions](#Finding-Extensions):  How `gawk` finds compiled extensions.
&bull; [Extension Example](#Extension-Example):  Example C code for an extension.
&bull; [Extension Samples](#Extension-Samples):  The sample extensions that ship with
                                `gawk`.
&bull; [gawkextlib](#gawkextlib):  The `gawkextlib` project.
&bull; [Extension summary](#Extension-summary):  Extension summary.
&bull; [Extension Exercises](#Extension-Exercises):  Exercises.

---

Next: [Plugin License](#Plugin-License), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.1 Introduction

An *extension* (sometimes called a *plug-in*) is a piece of
external compiled code that `gawk` can load at runtime to
provide additional functionality, over and above the built-in capabilities
described in the rest of this Web page.

Extensions are useful because they allow you (of course) to extend
`gawk`&rsquo;s functionality. For example, they can provide access to
system calls (such as `chdir()` to change directory) and to other
C library routines that could be of use.  As with most software,
&ldquo;the sky is the limit&rdquo;; if you can imagine something that you might
want to do and can write in C or C++, you can write an extension to do it!

Extensions are written in C or C++, using the *application programming
interface* (API) defined for this purpose by the `gawk`
developers.  The rest of this chapter explains
the facilities that the API provides and how to use
them, and presents a small example extension.  In addition, it documents
the sample extensions included in the `gawk` distribution
and describes the `gawkextlib` project.
See [Extension Design](#Extension-Design), for a discussion of the extension mechanism
goals and design.

---

Next: [Extension Mechanism Outline](#Extension-Mechanism-Outline), Previous: [Extension Intro](#Extension-Intro), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.2 Extension Licensing

Every dynamic extension must be distributed under a license that is
compatible with the GNU GPL (see [Copying](#Copying)).

In order for the extension to tell `gawk` that it is
properly licensed, the extension must define the global symbol
`plugin_is_GPL_compatible`.  If this symbol does not exist,
`gawk` emits a fatal error and exits when it tries to load
your extension.

The declared type of the symbol should be `int`.  It does not need
to be in any allocated section, though.  The code merely asserts that
the symbol exists in the global scope.  Something like this is enough:

    int plugin_is_GPL_compatible;
    

---

Next: [Extension API Description](#Extension-API-Description), Previous: [Plugin License](#Plugin-License), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.3 How It Works at a High Level

Communication between
`gawk` and an extension is two-way.  First, when an extension
is loaded, `gawk` passes it a pointer to a `struct` whose fields are
function pointers.
This is shown in [Figure 16.1](#figure_002dload_002dextension).

![Loading the extension](api-figure1.png)

**Figure 16.1: **Loading the extension

The extension can call functions inside `gawk` through these
function pointers, at runtime, without needing (link-time) access
to `gawk`&rsquo;s symbols.  One of these function pointers is to a
function for &ldquo;registering&rdquo; new functions.
This is shown in [Figure 16.2](#figure_002dregister_002dnew_002dfunction).

![Registering a new Function](api-figure2.png)

**Figure 16.2: **Registering a new function

In the other direction, the extension registers its new functions
with `gawk` by passing function pointers to the functions that
provide the new feature (`do_chdir()`, for example).  `gawk`
associates the function pointer with a name and can then call it, using a
defined calling convention.
This is shown in [Figure 16.3](#figure_002dcall_002dnew_002dfunction).

![Calling the new function](api-figure3.png)

**Figure 16.3: **Calling the new function

The `do_xxx()` function, in turn, then uses the function
pointers in the API `struct` to do its work, such as updating
variables or arrays, printing messages, setting `ERRNO`, and so on.

Convenience macros make calling through the function pointers look
like regular function calls so that extension code is quite readable
and understandable.

Although all of this sounds somewhat complicated, the result is that
extension code is quite straightforward to write and to read. You can
see this in the sample extension filefuncs.c (see [Extension Example](#Extension-Example)) and also in the testext.c code for testing the APIs.

Some other bits and pieces:

-  The API provides access to `gawk`&rsquo;s `do_xxx` values,
reflecting command-line options, like `do_lint`, `do_profiling`,
and so on (see [Extension API Variables](#Extension-API-Variables)).
These are informational: an extension cannot affect their values
inside `gawk`.  In addition, attempting to assign to them
produces a compile-time error.

-  The API also provides major and minor version numbers, so that an
extension can check if the `gawk` it is loaded with supports the
facilities it was compiled with.  (Version mismatches &ldquo;shouldn&rsquo;t&rdquo;
happen, but we all know how *that* goes.)
See [Extension Versioning](#Extension-Versioning) for details.

---

Next: [Finding Extensions](#Finding-Extensions), Previous: [Extension Mechanism Outline](#Extension-Mechanism-Outline), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.4 API Description

C or C++ code for an extension must include the header file
gawkapi.h, which declares the functions and defines the data
types used to communicate with `gawk`.
This (rather large) section describes the API in detail.

&bull; [Extension API Functions Introduction](#Extension-API-Functions-Introduction):  Introduction to the API functions.
&bull; [General Data Types](#General-Data-Types):  The data types.
&bull; [Memory Allocation Functions](#Memory-Allocation-Functions):  Functions for allocating memory.
&bull; [Constructor Functions](#Constructor-Functions):  Functions for creating values.
&bull; [Registration Functions](#Registration-Functions):  Functions to register things with
                                         `gawk`.
&bull; [Printing Messages](#Printing-Messages):  Functions for printing messages.
&bull; [Updating `ERRNO`](#Updating-ERRNO):  Functions for updating `ERRNO`.
&bull; [Requesting Values](#Requesting-Values):  How to get a value.
&bull; [Accessing Parameters](#Accessing-Parameters):  Functions for accessing parameters.
&bull; [Symbol Table Access](#Symbol-Table-Access):  Functions for accessing global
                                         variables.
&bull; [Array Manipulation](#Array-Manipulation):  Functions for working with arrays.
&bull; [Redirection API](#Redirection-API):  How to access and manipulate
                                         redirections.
&bull; [Extension API Variables](#Extension-API-Variables):  Variables provided by the API.
&bull; [Extension API Boilerplate](#Extension-API-Boilerplate):  Boilerplate code for using the API.
&bull; [Changes from API V1](#Changes-from-API-V1):  Changes from V1 of the API.

---

Next: [General Data Types](#General-Data-Types), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.1 Introduction

Access to facilities within `gawk` is achieved
by calling through function pointers passed into your extension.

API function pointers are provided for the following kinds of operations:

-  Allocating, reallocating, and releasing memory.

-  Registration functions. You may register:

- - Extension functions

- - Exit callbacks

- - A version string

- - Input parsers

- - Output wrappers

- - Two-way processors

All of these are discussed in detail later in this chapter.

-  Printing fatal, warning, and &ldquo;lint&rdquo; warning messages.

-  Updating `ERRNO`, or unsetting it.

-  Accessing parameters, including converting an undefined parameter into
an array.

-  Symbol table access: retrieving a global variable, creating one,
or changing one.

-  Creating and releasing cached values; this provides an
efficient way to use values for multiple variables and
can be a big performance win.

-  Manipulating arrays:

- - Retrieving, adding, deleting, and modifying elements

- - Getting the count of elements in an array

- - Creating a new array

- - Clearing an array

- - Flattening an array for easy C-style looping over all its indices and elements

-  Accessing and manipulating redirections.

Some points about using the API:

-  The following types, macros, and/or functions are referenced
in gawkapi.h.  For correct use, you must therefore include the
corresponding standard header file *before* including gawkapi.h:

C entityHeader file`EOF``<stdio.h>`Values for `errno``<errno.h>``FILE``<stdio.h>``NULL``<stddef.h>``memcpy()``<string.h>``memset()``<string.h>``size_t``<sys/types.h>``struct stat``<sys/stat.h>`
Due to portability concerns, especially to systems that are not
fully standards-compliant, it is your responsibility
to include the correct files in the correct way. This requirement
is necessary in order to keep gawkapi.h clean, instead of becoming
a portability hodge-podge as can be seen in some parts of
the `gawk` source code.

-  If your extension uses MPFR facilities, and you wish to receive such
values from `gawk` and/or pass such values to it, you must include the
`<mpfr.h>` header before including `<gawkapi.h>`.

-  The gawkapi.h file may be included more than once without ill effect.
Doing so, however, is poor coding practice.

-  Although the API only uses ISO C 90 features, there is an exception; the
&ldquo;constructor&rdquo; functions use the `inline` keyword. If your compiler
does not support this keyword, you should either place
&lsquo;-Dinline=''&rsquo; on your command line or use the GNU Autotools and include a
config.h file in your extensions.

-  All pointers filled in by `gawk` point to memory
managed by `gawk` and should be treated by the extension as
read-only.  Memory for *all* strings passed into `gawk`
from the extension *must* come from calling one of
`gawk_malloc()`, `gawk_calloc()`, or `gawk_realloc()`,
and is managed by `gawk` from then on.

-  The API defines several simple `struct`s that map values as seen
from `awk`.  A value can be a `double`, a string, or an
array (as in multidimensional arrays, or when creating a new array).

String values maintain both pointer and length, because embedded NUL
characters are allowed.

> NOTE: By intent, `gawk` maintains strings using the current multibyte
> encoding (as defined by `LC_xxx` environment variables)
> and not using wide characters.  This matches how `gawk` stores
> strings internally and also how characters are likely to be input into
> and output from files.

> NOTE: String values passed to an extension by `gawk` are always
> NUL-terminated.  Thus it is safe to pass such string values to
> standard library and system routines. However, because `gawk`
> allows embedded NUL characters in string data, before using the data
> as a regular C string, you should check that the length for that string
> passed to the extension matches the return value of `strlen()`
> for it.

-  When retrieving a value (such as a parameter or that of a global variable
or array element), the extension requests a specific type (number, string,
scalar, value cookie, array, or &ldquo;undefined&rdquo;).  When the request is
&ldquo;undefined,&rdquo; the returned value will have the real underlying type.

However, if the request and actual type don&rsquo;t match, the access function
returns &ldquo;false&rdquo; and fills in the type of the actual value that is there,
so that the extension can, e.g., print an error message
(such as &ldquo;scalar passed where array expected&rdquo;).

You may call the API functions by using the function pointers
directly, but the interface is not so pretty. To make extension code look
more like regular code, the gawkapi.h header file defines several
macros that you should use in your code.  This section presents
the macros as if they were functions.

---

Next: [Memory Allocation Functions](#Memory-Allocation-Functions), Previous: [Extension API Functions Introduction](#Extension-API-Functions-Introduction), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.2 General-Purpose Data Types

> I have a true love/hate relationship with unions.

&mdash; *Arnold Robbins*

> That&rsquo;s the thing about unions: the compiler will arrange things so they
> can accommodate both love and hate.

&mdash; *Chet Ramey*

The extension API defines a number of simple types and structures for
general-purpose use. Additional, more specialized, data structures are
introduced in subsequent sections, together with the functions
that use them.

The general-purpose types and structures are as follows:

`typedef void *awk_ext_id_t;`
A value of this type is received from `gawk` when an extension is loaded.
That value must then be passed back to `gawk` as the first parameter of
each API function.

`#define awk_const &hellip;`
This macro expands to &lsquo;const&rsquo; when compiling an extension,
and to nothing when compiling `gawk` itself.  This makes
certain fields in the API data structures unwritable from extension code,
while allowing `gawk` to use them as it needs to.

`typedef enum awk_bool {``    awk_false = 0,``    awk_true``} awk_bool_t;`
A simple Boolean type.

`typedef struct awk_string {``    char *str;      /* data */``    size_t len;     /* length thereof, in chars */``} awk_string_t;`
This represents a mutable string. `gawk`
owns the memory pointed to if it supplied
the value. Otherwise, it takes ownership of the memory pointed to.
*Such memory must come from calling one of the
`gawk_malloc()`, `gawk_calloc()`, or
`gawk_realloc()` functions!*

As mentioned earlier, strings are maintained using the current
multibyte encoding.

`typedef enum {``    AWK_UNDEFINED,``    AWK_NUMBER,``    AWK_STRING,``    AWK_REGEX,``    AWK_STRNUM,``    AWK_ARRAY,``    AWK_SCALAR,         /* opaque access to a variable */``    AWK_VALUE_COOKIE    /* for updating a previously created value */``} awk_valtype_t;`
This `enum` indicates the type of a value.
It is used in the following `struct`.

`typedef struct awk_value {``    awk_valtype_t   val_type;``    union {``        awk_string_t       s;``        awknum_t           n;``        awk_array_t        a;``        awk_scalar_t       scl;``        awk_value_cookie_t vc;``    } u;``} awk_value_t;`
An &ldquo;`awk` value.&rdquo;
The `val_type` member indicates what kind of value the
`union` holds, and each member is of the appropriate type.

`#define str_value      u.s``#define strnum_value   str_value``#define regex_value    str_value``#define num_value      u.n.d``#define num_type       u.n.type``#define num_ptr        u.n.ptr``#define array_cookie   u.a``#define scalar_cookie  u.scl``#define value_cookie   u.vc`
Using these macros makes accessing the fields of the `awk_value_t` more
readable.

`enum AWK_NUMBER_TYPE {``    AWK_NUMBER_TYPE_DOUBLE,``    AWK_NUMBER_TYPE_MPFR,``    AWK_NUMBER_TYPE_MPZ``};`
This `enum` is used in the following structure for defining the
type of numeric value that is being worked with.  It is declared at the
top level of the file so that it works correctly for C++ as well as for C.

`typedef struct awk_number {``    double d;``    enum AWK_NUMBER_TYPE type;``    void *ptr;``} awk_number_t;`
This represents a numeric value.  Internally, `gawk` stores
every number as either a C `double`, a GMP integer, or an MPFR
arbitrary-precision floating-point value.  In order to allow extensions
to also support GMP and MPFR values, numeric values are passed in this
structure.

The double-precision `d` element is always populated
in data received from `gawk`. In addition, by examining the
`type` member, an extension can determine if the `ptr`
member is either a GMP integer (type `mpz_ptr`), or an MPFR
floating-point value (type `mpfr_ptr_t`), and cast it appropriately.

`typedef void *awk_scalar_t;`
Scalars can be represented as an opaque type. These values are obtained
from `gawk` and then passed back into it. This is discussed
in a general fashion in the text following this list, and in more detail in
[Symbol table by cookie](#Symbol-table-by-cookie).

`typedef void *awk_value_cookie_t;`
A &ldquo;value cookie&rdquo; is an opaque type representing a cached value.
This is also discussed in a general fashion in the text following this list,
and in more detail in [Cached values](#Cached-values).

Scalar values in `awk` are numbers, strings, strnums, or typed regexps. The
`awk_value_t` struct represents values.  The `val_type` member
indicates what is in the `union`.

Representing numbers is easy&mdash;the API uses a C `double`.  Strings
require more work. Because `gawk` allows embedded NUL bytes
in string values, a string must be represented as a pair containing a
data pointer and length. This is the `awk_string_t` type.

A strnum (numeric string) value is represented as a string and consists
of user input data that appears to be numeric.
When an extension creates a strnum value, the result is a string flagged
as user input. Subsequent parsing by `gawk` then determines whether it
looks like a number and should be treated as a strnum, or as a regular string.

This is useful in cases where an extension function would like to do something
comparable to the `split()` function which sets the strnum attribute
on the array elements it creates.  For example, an extension that implements
CSV splitting would want to use this feature. This is also useful for a
function that retrieves a data item from a database. The PostgreSQL
`PQgetvalue()` function, for example, returns a string that may be numeric
or textual depending on the contents.

Typed regexp values (see [Strong Regexp Constants](#Strong-Regexp-Constants)) are not of
much use to extension functions.  Extension functions can tell that
they&rsquo;ve received them, and create them for scalar values. Otherwise,
they can examine the text of the regexp through `regex_value.str`
and `regex_value.len`.

Identifiers (i.e., the names of global variables) can be associated
with either scalar values or with arrays.  In addition, `gawk`
provides true arrays of arrays, where any given array element can
itself be an array.  Discussion of arrays is delayed until
[Array Manipulation](#Array-Manipulation).

The various macros listed earlier make it easier to use the elements
of the `union` as if they were fields in a `struct`; this
is a common coding practice in C.  Such code is easier to write and to
read, but it remains *your* responsibility to make sure that
the `val_type` member correctly reflects the type of the value in
the `awk_value_t` struct.

Conceptually, the first three members of the `union` (number, string,
and array) are all that is needed for working with `awk` values.
However, because the API provides routines for accessing and changing
the value of a global scalar variable only by using the variable&rsquo;s name,
there is a performance penalty: `gawk` must find the variable
each time it is accessed and changed.  This turns out to be a real issue,
not just a theoretical one.

Thus, if you know that your extension will spend considerable time
reading and/or changing the value of one or more scalar variables, you
can obtain a *scalar cookie*[102](#FOOT102)
object for that variable, and then use
the cookie for getting the variable&rsquo;s value or for changing the variable&rsquo;s
value.
The `awk_scalar_t` type holds a scalar cookie, and the
`scalar_cookie` macro provides access to the value of that type
in the `awk_value_t` struct.
Given a scalar cookie, `gawk` can directly retrieve or
modify the value, as required, without having to find it first.

The `awk_value_cookie_t` type and `value_cookie` macro are similar.
If you know that you wish to
use the same numeric or string *value* for one or more variables,
you can create the value once, retaining a *value cookie* for it,
and then pass in that value cookie whenever you wish to set the value of a
variable.  This saves storage space within the running `gawk`
process and reduces the time needed to create the value.

---

Next: [Constructor Functions](#Constructor-Functions), Previous: [General Data Types](#General-Data-Types), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.3 Memory Allocation Functions and Convenience Macros

The API provides a number of *memory allocation* functions for
allocating memory that can be passed to `gawk`, as well as a number of
convenience macros.
This subsection presents them all as function prototypes, in
the way that extension code would use them:

`void *gawk_malloc(size_t size);`
Call the correct version of `malloc()` to allocate storage that may
be passed to `gawk`.

`void *gawk_calloc(size_t nmemb, size_t size);`
Call the correct version of `calloc()` to allocate storage that may
be passed to `gawk`.

`void *gawk_realloc(void *ptr, size_t size);`
Call the correct version of `realloc()` to allocate storage that may
be passed to `gawk`.

`void gawk_free(void *ptr);`
Call the correct version of `free()` to release storage that was
allocated with `gawk_malloc()`, `gawk_calloc()`, or `gawk_realloc()`.

The API has to provide these functions because it is possible
for an extension to be compiled and linked against a different
version of the C library than was used for the `gawk`
executable.[103](#FOOT103) If `gawk` were
to use its version of `free()` when the memory came from an
unrelated version of `malloc()`, unexpected behavior would
likely result.

Three convenience macros may be used for allocating storage
from `gawk_malloc()`, `gawk_calloc`, and
`gawk_realloc()`. If the allocation fails, they cause `gawk`
to exit with a fatal error message.  They should be used as if they were
procedure calls that do not return a value:

`#define emalloc(pointer, type, size, message) &hellip;`
The arguments to this macro are as follows:

`pointer`
The pointer variable to point at the allocated storage.

`type`
The type of the pointer variable.  This is used to create a cast for
the call to `gawk_malloc()`.

`size`
The total number of bytes to be allocated.

`message`
A message to be prefixed to the fatal error message. Typically this is the name
of the function using the macro.

For example, you might allocate a string value like so:

    awk_value_t result;
    char *message;
    const char greet[] = "Don't Panic!";
    
    emalloc(message, char *, sizeof(greet), "myfunc");
    strcpy(message, greet);
    make_malloced_string(message, strlen(message), & result);
    

`#define ezalloc(pointer, type, size, message) &hellip;`
This is like `emalloc()`, but it calls `gawk_calloc()`
instead of `gawk_malloc()`.
The arguments are the same as for the `emalloc()` macro, but this
macro guarantees that the memory returned is initialized to zero.

`#define erealloc(pointer, type, size, message) &hellip;`
This is like `emalloc()`, but it calls `gawk_realloc()`
instead of `gawk_malloc()`.
The arguments are the same as for the `emalloc()` macro.

Two additional functions allocate MPFR and GMP objects for use
by extension functions that need to create and then return such
values:

`void *get_mpfr_ptr();`
Allocate and initialize an MPFR object and return a pointer to it.
If the allocation fails, `gawk` exits with a fatal
&ldquo;out of memory&rdquo; error.  If `gawk` was compiled without
MPFR support, calling this function causes a fatal error.

`void *get_mpz_ptr();`
Allocate and initialize a GMP object and return a pointer to it.
If the allocation fails, `gawk` exits with a fatal
&ldquo;out of memory&rdquo; error.  If `gawk` was compiled without
MPFR support, calling this function causes a fatal error.

Both of these functions return &lsquo;void *&rsquo;, since the gawkapi.h
header file should not have dependency upon `<mpfr.h>` (and `<gmp.h>`,
which is included from `<mpfr.h>`).  The actual return values are of
types `mpfr_ptr` and `mpz_ptr` respectively, and you should cast
the return values appropriately before assigning the results to variables
of the correct types.

---

Next: [Registration Functions](#Registration-Functions), Previous: [Memory Allocation Functions](#Memory-Allocation-Functions), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.4 Constructor Functions

The API provides a number of *constructor* functions for creating
string and numeric values, as well as a number of convenience macros.
This subsection presents them all as function prototypes, in
the way that extension code would use them:

`static inline awk_value_t *``make_const_string(const char *string, size_t length, awk_value_t *result);`
This function creates a string value in the `awk_value_t` variable
pointed to by `result`. It expects `string` to be a C string constant
(or other string data), and automatically creates a *copy* of the data
for storage in `result`. It returns `result`.

`static inline awk_value_t *``make_malloced_string(const char *string, size_t length, awk_value_t *result);`
This function creates a string value in the `awk_value_t` variable
pointed to by `result`. It expects `string` to be a &lsquo;char *&rsquo;
value pointing to data previously obtained from `gawk_malloc()`, `gawk_calloc()`, or `gawk_realloc()`. The idea here
is that the data is passed directly to `gawk`, which assumes
responsibility for it. It returns `result`.

`static inline awk_value_t *``make_null_string(awk_value_t *result);`
This specialized function creates a null string (the &ldquo;undefined&rdquo; value)
in the `awk_value_t` variable pointed to by `result`.
It returns `result`.

`static inline awk_value_t *``make_number(double num, awk_value_t *result);`
This function simply creates a numeric value in the `awk_value_t` variable
pointed to by `result`.

`static inline awk_value_t *``make_number_mpz(void *mpz, awk_value_t *result);`
This function creates a GMP number value in `result`.
The `mpz` must be from a call to `get_mpz_ptr()`
(and thus be of real underlying type `mpz_ptr`).
`gawk` takes ownership of this memory.

`static inline awk_value_t *``make_number_mpfr(void *mpfr, awk_value_t *result);`
This function creates an MPFR number value in `result`.
The `mpfr` must be from a call to `get_mpfr_ptr()`.
(and thus be of real underlying type `mpfr_ptr`)
`gawk` takes ownership of this memory.

`static inline awk_value_t *``make_const_user_input(const char *string, size_t length, awk_value_t *result);`
This function is identical to `make_const_string()`, but the string is
flagged as user input that should be treated as a strnum value if the contents
of the string are numeric.

`static inline awk_value_t *``make_malloced_user_input(const char *string, size_t length, awk_value_t *result);`
This function is identical to `make_malloced_string()`, but the string is
flagged as user input that should be treated as a strnum value if the contents
of the string are numeric.

`static inline awk_value_t *``make_const_regex(const char *string, size_t length, awk_value_t *result);`
This function creates a strongly typed regexp value by allocating a copy of the string.
`string` is the regular expression of length `len`.

`static inline awk_value_t *``make_malloced_regex(const char *string, size_t length, awk_value_t *result);`
This function creates a strongly typed regexp value.  `string` is
the regular expression of length `len`.  It expects `string`
to be a &lsquo;char *&rsquo; value pointing to data previously obtained from
`gawk_malloc()`, `gawk_calloc()`, or `gawk_realloc()`.

---

Next: [Printing Messages](#Printing-Messages), Previous: [Constructor Functions](#Constructor-Functions), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5 Registration Functions

This section describes the API functions for
registering parts of your extension with `gawk`.

&bull; [Extension Functions](#Extension-Functions):  Registering extension functions.
&bull; [Exit Callback Functions](#Exit-Callback-Functions):  Registering an exit callback.
&bull; [Extension Version String](#Extension-Version-String):  Registering a version string.
&bull; [Input Parsers](#Input-Parsers):  Registering an input parser.
&bull; [Output Wrappers](#Output-Wrappers):  Registering an output wrapper.
&bull; [Two-way processors](#Two_002dway-processors):  Registering a two-way processor.

---

Next: [Exit Callback Functions](#Exit-Callback-Functions), Up: [Registration Functions](#Registration-Functions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5.1 Registering An Extension Function

Extension functions are described by the following record:

    typedef struct awk_ext_func {
        const char *name;
        awk_value_t *(*const function)(int num_actual_args,
                                       awk_value_t *result,
                                       struct awk_ext_func *finfo);
        const size_t max_expected_args;
        const size_t min_required_args;
        awk_bool_t suppress_lint;
        void *data;        /* opaque pointer to any extra state */
    } awk_ext_func_t;
    

The fields are:

`const char *name;`
The name of the new function.
`awk`-level code calls the function by this name.
This is a regular C string.

Function names must obey the rules for `awk`
identifiers. That is, they must begin with either an English letter
or an underscore, which may be followed by any number of
letters, digits, and underscores.
Letter case in function names is significant.

`awk_value_t *(*const function)(int num_actual_args,``                              awk_value_t *result,``                              struct awk_ext_func *finfo);`
This is a pointer to the C function that provides the extension&rsquo;s
functionality.
The function must fill in `*result` with either a number,
a string, or a regexp.
`gawk` takes ownership of any string memory.
As mentioned earlier, string memory *must* come from one of
`gawk_malloc()`, `gawk_calloc()`, or `gawk_realloc()`.

The `num_actual_args` argument tells the C function how many
actual parameters were passed from the calling `awk` code.

The `finfo` parameter is a pointer to the `awk_ext_func_t` for
this function. The called function may access data within it as desired, or not.

The function must return the value of `result`.
This is for the convenience of the calling code inside `gawk`.

`const size_t max_expected_args;`
This is the maximum number of arguments the function expects to receive.
If called with more arguments than this, and if lint checking has
been enabled, then `gawk` prints a warning message.  For more
information, see the entry for `suppress_lint`, later in this list.

`const size_t min_required_args;`
This is the minimum number of arguments the function expects to receive.
If called with fewer arguments, `gawk` prints a fatal error
message and exits.

`awk_bool_t suppress_lint;`
This flag tells `gawk` not to print a lint message if lint
checking has been enabled and if more arguments were supplied in the call
than expected.  An extension function can tell if `gawk` already
printed at least one such message by checking if &lsquo;num_actual_args >
finfo->max_expected_args&rsquo;.  If so, and the function does not want more
lint messages to be printed, it should set `finfo->suppress_lint`
to `awk_true`.

`void *data;`
This is an opaque pointer to any data that an extension function may
wish to have available when called.  Passing the `awk_ext_func_t`
structure to the extension function, and having this pointer available
in it enable writing a single C or C++ function that implements multiple
`awk`-level extension functions.

Once you have a record representing your extension function, you register
it with `gawk` using this API function:

`awk_bool_t add_ext_func(const char *name_space, awk_ext_func_t *func);`
This function returns true upon success, false otherwise.
The `name_space` parameter is currently not used; you should pass in an
empty string (`""`).  The `func` pointer is the address of a
`struct` representing your function, as just described.

`gawk` does not modify what `func` points to, but the
extension function itself receives this pointer and can modify what it
points to, thus it is purposely not declared to be `const`.

The combination of `min_required_args`, `max_expected_args`,
and `suppress_lint` may be confusing. Here is how you should
set things up.

Any number of arguments is valid
Set `min_required_args` and `max_expected_args` to zero and
set `suppress_lint` to `awk_true`.

A minimum number of arguments is required, no limit on maximum number of arguments
Set `min_required_args` to the minimum required. Set
`max_expected_args` to zero and
set `suppress_lint` to `awk_true`.

A minimum number of arguments is required, a maximum number is expected
Set `min_required_args` to the minimum required. Set
`max_expected_args` to the maximum expected.
Set `suppress_lint` to `awk_false`.

A minimum number of arguments is required, and no more than a maximum is allowed
Set `min_required_args` to the minimum required. Set
`max_expected_args` to the maximum expected.
Set `suppress_lint` to `awk_false`.
In your extension function, check that `num_actual_args` does not
exceed `f->max_expected_args`. If it does, issue a fatal error message.

---

Next: [Extension Version String](#Extension-Version-String), Previous: [Extension Functions](#Extension-Functions), Up: [Registration Functions](#Registration-Functions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5.2 Registering An Exit Callback Function

An *exit callback* function is a function that
`gawk` calls before it exits.
Such functions are useful if you have general &ldquo;cleanup&rdquo; tasks
that should be performed in your extension (such as closing database
connections or other resource deallocations).
You can register such
a function with `gawk` using the following function:

`void awk_atexit(void (*funcp)(void *data, int exit_status),``                void *arg0);`
The parameters are:

`funcp`
A pointer to the function to be called before `gawk` exits. The `data`
parameter will be the original value of `arg0`.
The `exit_status` parameter is the exit status value that
`gawk` intends to pass to the `exit()` system call.

`arg0`
A pointer to private data that `gawk` saves in order to pass to
the function pointed to by `funcp`.

Exit callback functions are called in last-in, first-out (LIFO)
order&mdash;that is, in the reverse order in which they are registered with
`gawk`.

---

Next: [Input Parsers](#Input-Parsers), Previous: [Exit Callback Functions](#Exit-Callback-Functions), Up: [Registration Functions](#Registration-Functions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5.3 Registering An Extension Version String

You can register a version string that indicates the name and
version of your extension with `gawk`, as follows:

`void register_ext_version(const char *version);`
Register the string pointed to by `version` with `gawk`.
Note that `gawk` does *not* copy the `version` string, so
it should not be changed.

`gawk` prints all registered extension version strings when it
is invoked with the --version option.

---

Next: [Output Wrappers](#Output-Wrappers), Previous: [Extension Version String](#Extension-Version-String), Up: [Registration Functions](#Registration-Functions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5.4 Customized Input Parsers

By default, `gawk` reads text files as its input. It uses the value
of `RS` to find the end of the record, and then uses `FS`
(or `FIELDWIDTHS` or `FPAT`) to split it into fields (see [Reading Files](#Reading-Files)).
Additionally, it sets the value of `RT` (see [Built-in Variables](#Built_002din-Variables)).

If you want, you can provide your own custom input parser.  An input
parser&rsquo;s job is to return a record to the `gawk` record-processing
code, along with indicators for the value and length of the data to be
used for `RT`, if any.

To provide an input parser, you must first provide two functions
(where XXX is a prefix name for your extension):

`awk_bool_t XXX_can_take_file(const awk_input_buf_t *iobuf);`
This function examines the information available in `iobuf`
(which we discuss shortly).  Based on the information there, it
decides if the input parser should be used for this file.
If so, it should return true. Otherwise, it should return false.
It should not change any state (variable values, etc.) within `gawk`.

`awk_bool_t XXX_take_control_of(awk_input_buf_t *iobuf);`
When `gawk` decides to hand control of the file over to the
input parser, it calls this function.  This function in turn must fill
in certain fields in the `awk_input_buf_t` structure and ensure
that certain conditions are true.  It should then return true. If an
error of some kind occurs, it should not fill in any fields and should
return false; then `gawk` will not use the input parser.
The details are presented shortly.

Your extension should package these functions inside an
`awk_input_parser_t`, which looks like this:

    typedef struct awk_input_parser {
        const char *name;   /* name of parser */
        awk_bool_t (*can_take_file)(const awk_input_buf_t *iobuf);
        awk_bool_t (*take_control_of)(awk_input_buf_t *iobuf);
        awk_const struct awk_input_parser *awk_const next;   /* for gawk */
    } awk_input_parser_t;
    

The fields are:

`const char *name;`
The name of the input parser. This is a regular C string.

`awk_bool_t (*can_take_file)(const awk_input_buf_t *iobuf);`
A pointer to your `XXX_can_take_file()` function.

`awk_bool_t (*take_control_of)(awk_input_buf_t *iobuf);`
A pointer to your `XXX_take_control_of()` function.

`awk_const struct input_parser *awk_const next;`
This is for use by `gawk`;
therefore it is marked `awk_const` so that the extension cannot
modify it.

The steps are as follows:

1.  Create a `static awk_input_parser_t` variable and initialize it
appropriately.

2.  When your extension is loaded, register your input parser with
`gawk` using the `register_input_parser()` API function
(described next).

An `awk_input_buf_t` looks like this:

    typedef struct awk_input {
        const char *name;       /* filename */
        int fd;                 /* file descriptor */
    #define INVALID_HANDLE (-1)
        void *opaque;           /* private data for input parsers */
        int (*get_record)(char **out, struct awk_input *iobuf,
                          int *errcode, char **rt_start, size_t *rt_len,
                          const awk_fieldwidth_info_t **field_width);
        ssize_t (*read_func)();
        void (*close_func)(struct awk_input *iobuf);
        struct stat sbuf;       /* stat buf */
    } awk_input_buf_t;
    

The fields can be divided into two categories: those for use (initially,
at least) by `XXX_can_take_file()`, and those for use by
`XXX_take_control_of()`.  The first group of fields and their uses
are as follows:

`const char *name;`
The name of the file.

`int fd;`
A file descriptor for the file.  If `gawk` was able to
open the file, then `fd` will *not* be equal to
`INVALID_HANDLE`. Otherwise, it will.

`struct stat sbuf;`
If the file descriptor is valid, then `gawk` will have filled
in this structure via a call to the `fstat()` system call.

The `XXX_can_take_file()` function should examine these
fields and decide if the input parser should be used for the file.
The decision can be made based upon `gawk` state (the value
of a variable defined previously by the extension and set by
`awk` code), the name of the
file, whether or not the file descriptor is valid, the information
in the `struct stat`, or any combination of these factors.

Once `XXX_can_take_file()` has returned true, and
`gawk` has decided to use your input parser, it calls
`XXX_take_control_of()`.  That function then fills
either the `get_record` field or the `read_func` field in
the `awk_input_buf_t`.  It must also ensure that `fd` is *not*
set to `INVALID_HANDLE`.  The following list describes the fields that
may be filled by `XXX_take_control_of()`:

`void *opaque;`
This is used to hold any state information needed by the input parser
for this file.  It is &ldquo;opaque&rdquo; to `gawk`.  The input parser
is not required to use this pointer.

`int (*get_record)(char **out,``                  struct awk_input *iobuf,``                  int *errcode,``                  char **rt_start,``                  size_t *rt_len,``                  const awk_fieldwidth_info_t **field_width);`
This function pointer should point to a function that creates the input
records.  Said function is the core of the input parser.  Its behavior
is described in the text following this list.

`ssize_t (*read_func)();`
This function pointer should point to a function that has the
same behavior as the standard POSIX `read()` system call.
It is an alternative to the `get_record` pointer.  Its behavior
is also described in the text following this list.

`void (*close_func)(struct awk_input *iobuf);`
This function pointer should point to a function that does
the &ldquo;teardown.&rdquo; It should release any resources allocated by
`XXX_take_control_of()`.  It may also close the file. If it
does so, it should set the `fd` field to `INVALID_HANDLE`.

If `fd` is still not `INVALID_HANDLE` after the call to this
function, `gawk` calls the regular `close()` system call.

Having a &ldquo;teardown&rdquo; function is optional. If your input parser does
not need it, do not set this field.  Then, `gawk` calls the
regular `close()` system call on the file descriptor, so it should
be valid.

The `XXX_get_record()` function does the work of creating
input records.  The parameters are as follows:

`char **out`
This is a pointer to a `char *` variable that is set to point
to the record.  `gawk` makes its own copy of the data, so
the extension must manage this storage.

`struct awk_input *iobuf`
This is the `awk_input_buf_t` for the file.  The fields should be
used for reading data (`fd`) and for managing private state
(`opaque`), if any.

`int *errcode`
If an error occurs, `*errcode` should be set to an appropriate
code from `<errno.h>`.

`char **rt_start``size_t *rt_len`
If the concept of a &ldquo;record terminator&rdquo; makes sense, then
`*rt_start` should be set to point to the data to be used for
`RT`, and `*rt_len` should be set to the length of the
data. Otherwise, `*rt_len` should be set to zero.
`gawk` makes its own copy of this data, so the
extension must manage this storage.

`const awk_fieldwidth_info_t **field_width`
If `field_width` is not `NULL`, then `*field_width` will be initialized
to `NULL`, and the function may set it to point to a structure
supplying field width information to override the default
field parsing mechanism. Note that this structure will not
be copied by `gawk`; it must persist at least until the next call
to `get_record` or `close_func`. Note also that `field_width` is
`NULL` when `getline` is assigning the results to a variable, thus
field parsing is not needed. If the parser does set `*field_width`,
then `gawk` uses this layout to parse the input record,
and the `PROCINFO["FS"]` value will be `"API"` while this record
is active in `$0`.
The `awk_fieldwidth_info_t` data structure
is described below.

The return value is the length of the buffer pointed to by
`*out`, or `EOF` if end-of-file was reached or an
error occurred.

It is guaranteed that `errcode` is a valid pointer, so there is no
need to test for a `NULL` value.  `gawk` sets `*errcode`
to zero, so there is no need to set it unless an error occurs.

If an error does occur, the function should return `EOF` and set
`*errcode` to a value greater than zero.  In that case, if `*errcode`
does not equal zero, `gawk` automatically updates
the `ERRNO` variable based on the value of `*errcode`.
(In general, setting &lsquo;*errcode = errno&rsquo; should do the right thing.)

As an alternative to supplying a function that returns an input record,
you may instead supply a function that simply reads bytes, and let
`gawk` parse the data into records.  If you do so, the data
should be returned in the multibyte encoding of the current locale.
Such a function should follow the same behavior as the `read()`
system call, and you fill in the `read_func` pointer with its
address in the `awk_input_buf_t` structure.

By default, `gawk` sets the `read_func` pointer to
point to the `read()` system call. So your extension need not
set this field explicitly.

> NOTE: You must choose one method or the other: either a function that
> returns a record, or one that returns raw data.  In particular,
> if you supply a function to get a record, `gawk` will
> call it, and will never call the raw read function.

`gawk` ships with a sample extension that reads directories,
returning records for each entry in a directory (see [Extension Sample Readdir](#Extension-Sample-Readdir)).  You may wish to use that code as a guide for writing
your own input parser.

When writing an input parser, you should think about (and document)
how it is expected to interact with `awk` code.  You may want
it to always be called, and to take effect as appropriate (as the
`readdir` extension does).  Or you may want it to take effect
based upon the value of an `awk` variable, as the XML extension
from the `gawkextlib` project does (see [gawkextlib](#gawkextlib)).
In the latter case, code in a `BEGINFILE` rule
can look at `FILENAME` and `ERRNO` to decide whether or
not to activate an input parser (see [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)).

You register your input parser with the following function:

`void register_input_parser(awk_input_parser_t *input_parser);`
Register the input parser pointed to by `input_parser` with
`gawk`.

If you would like to override the default field parsing mechanism for a given
record, then you must populate an `awk_fieldwidth_info_t` structure,
which looks like this:

    typedef struct {
            awk_bool_t     use_chars; /* false ==> use bytes */
            size_t         nf;        /* number of fields in record (NF) */
            struct awk_field_info {
                    size_t skip;      /* amount to skip before field starts */
                    size_t len;       /* length of field */
            } fields[1];              /* actual dimension should be nf */
    } awk_fieldwidth_info_t;
    

The fields are:

`awk_bool_t use_chars;`
Set this to `awk_true` if the field lengths are specified in terms
of potentially multi-byte characters, and set it to `awk_false` if
the lengths are in terms of bytes.
Performance will be better if the values are supplied in
terms of bytes. 

`size_t nf;`
Set this to the number of fields in the input record, i.e. `NF`.

`struct awk_field_info fields[nf];`
This is a variable-length array whose actual dimension should be `nf`.
For each field, the `skip` element should be set to the number
of characters or bytes, as controlled by the `use_chars` flag,
to skip before the start of this field. The `len` element provides
the length of the field. The values in `fields[0]` provide the information
for `$1`, and so on through the `fields[nf-1]` element containing the information for `$NF`.

A convenience macro `awk_fieldwidth_info_size(numfields)` is provided to
calculate the appropriate size of a variable-length 
`awk_fieldwidth_info_t` structure containing `numfields` fields. This can
be used as an argument to `malloc()` or in a union to allocate space
statically. Please refer to the `readdir_test` sample extension for an
example.

---

Next: [Two-way processors](#Two_002dway-processors), Previous: [Input Parsers](#Input-Parsers), Up: [Registration Functions](#Registration-Functions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5.5 Customized Output Wrappers

An *output wrapper* is the mirror image of an input parser.
It allows an extension to take over the output to a file opened
with the &lsquo;>&rsquo; or &lsquo;>>&rsquo; I/O redirection operators (see [Redirection](#Redirection)).

The output wrapper is very similar to the input parser structure:

    typedef struct awk_output_wrapper {
        const char *name;   /* name of the wrapper */
        awk_bool_t (*can_take_file)(const awk_output_buf_t *outbuf);
        awk_bool_t (*take_control_of)(awk_output_buf_t *outbuf);
        awk_const struct awk_output_wrapper *awk_const next;  /* for gawk */
    } awk_output_wrapper_t;
    

The members are as follows:

`const char *name;`
This is the name of the output wrapper.

`awk_bool_t (*can_take_file)(const awk_output_buf_t *outbuf);`
This points to a function that examines the information in
the `awk_output_buf_t` structure pointed to by `outbuf`.
It should return true if the output wrapper wants to take over the
file, and false otherwise.  It should not change any state (variable
values, etc.) within `gawk`.

`awk_bool_t (*take_control_of)(awk_output_buf_t *outbuf);`
The function pointed to by this field is called when `gawk`
decides to let the output wrapper take control of the file. It should
fill in appropriate members of the `awk_output_buf_t` structure,
as described next, and return true if successful, false otherwise.

`awk_const struct output_wrapper *awk_const next;`
This is for use by `gawk`;
therefore it is marked `awk_const` so that the extension cannot
modify it.

The `awk_output_buf_t` structure looks like this:

    typedef struct awk_output_buf {
        const char *name;   /* name of output file */
        const char *mode;   /* mode argument to fopen */
        FILE *fp;           /* stdio file pointer */
        awk_bool_t redirected;  /* true if a wrapper is active */
        void *opaque;       /* for use by output wrapper */
        size_t (*gawk_fwrite)(const void *buf, size_t size, size_t count,
                    FILE *fp, void *opaque);
        int (*gawk_fflush)(FILE *fp, void *opaque);
        int (*gawk_ferror)(FILE *fp, void *opaque);
        int (*gawk_fclose)(FILE *fp, void *opaque);
    } awk_output_buf_t;
    

Here too, your extension will define `XXX_can_take_file()`
and `XXX_take_control_of()` functions that examine and update
data members in the `awk_output_buf_t`.
The data members are as follows:

`const char *name;`
The name of the output file.

`const char *mode;`
The mode string (as would be used in the second argument to `fopen()`)
with which the file was opened.

`FILE *fp;`
The `FILE` pointer from `<stdio.h>`. `gawk` opens the file
before attempting to find an output wrapper.

`awk_bool_t redirected;`
This field must be set to true by the `XXX_take_control_of()` function.

`void *opaque;`
This pointer is opaque to `gawk`. The extension should use it to store
a pointer to any private data associated with the file.

`size_t (*gawk_fwrite)(const void *buf, size_t size, size_t count,``                      FILE *fp, void *opaque);``int (*gawk_fflush)(FILE *fp, void *opaque);``int (*gawk_ferror)(FILE *fp, void *opaque);``int (*gawk_fclose)(FILE *fp, void *opaque);`
These pointers should be set to point to functions that perform
the equivalent function as the `<stdio.h>` functions do, if appropriate.
`gawk` uses these function pointers for all output.
`gawk` initializes the pointers to point to internal &ldquo;pass-through&rdquo;
functions that just call the regular `<stdio.h>` functions, so an
extension only needs to redefine those functions that are appropriate for
what it does.

The `XXX_can_take_file()` function should make a decision based
upon the `name` and `mode` fields, and any additional state
(such as `awk` variable values) that is appropriate.

When `gawk` calls `XXX_take_control_of()`, that function should fill
in the other fields as appropriate, except for `fp`, which it should just
use normally.

You register your output wrapper with the following function:

`void register_output_wrapper(awk_output_wrapper_t *output_wrapper);`
Register the output wrapper pointed to by `output_wrapper` with
`gawk`.

---

Previous: [Output Wrappers](#Output-Wrappers), Up: [Registration Functions](#Registration-Functions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.5.6 Customized Two-way Processors

A *two-way processor* combines an input parser and an output wrapper for
two-way I/O with the &lsquo;|&&rsquo; operator (see [Redirection](#Redirection)).  It makes identical
use of the `awk_input_parser_t` and `awk_output_buf_t` structures
as described earlier.

A two-way processor is represented by the following structure:

    typedef struct awk_two_way_processor {
        const char *name;   /* name of the two-way processor */
        awk_bool_t (*can_take_two_way)(const char *name);
        awk_bool_t (*take_control_of)(const char *name,
                                      awk_input_buf_t *inbuf,
                                      awk_output_buf_t *outbuf);
        awk_const struct awk_two_way_processor *awk_const next;  /* for gawk */
    } awk_two_way_processor_t;
    

The fields are as follows:

`const char *name;`
The name of the two-way processor.

`awk_bool_t (*can_take_two_way)(const char *name);`
The function pointed to by this field should return true if it wants to take over two-way I/O for this file name.
It should not change any state (variable
values, etc.) within `gawk`.

`awk_bool_t (*take_control_of)(const char *name,``                              awk_input_buf_t *inbuf,``                              awk_output_buf_t *outbuf);`
The function pointed to by this field should fill in the `awk_input_buf_t` and
`awk_output_buf_t` structures pointed to by `inbuf` and
`outbuf`, respectively.  These structures were described earlier.

`awk_const struct two_way_processor *awk_const next;`
This is for use by `gawk`;
therefore it is marked `awk_const` so that the extension cannot
modify it.

As with the input parser and output processor, you provide
&ldquo;yes I can take this&rdquo; and &ldquo;take over for this&rdquo; functions,
`XXX_can_take_two_way()` and `XXX_take_control_of()`.

You register your two-way processor with the following function:

`void register_two_way_processor(awk_two_way_processor_t *two_way_processor);`
Register the two-way processor pointed to by `two_way_processor` with
`gawk`.

---

Next: [Updating `ERRNO`](#Updating-ERRNO), Previous: [Registration Functions](#Registration-Functions), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.6 Printing Messages

You can print different kinds of warning messages from your
extension, as described here.  Note that for these functions,
you must pass in the extension ID received from `gawk`
when the extension was loaded:[104](#FOOT104)
`void fatal(awk_ext_id_t id, const char *format, ...);`
Print a message and then cause `gawk` to exit immediately.

`void nonfatal(awk_ext_id_t id, const char *format, ...);`
Print a nonfatal error message.

`void warning(awk_ext_id_t id, const char *format, ...);`
Print a warning message.

`void lintwarn(awk_ext_id_t id, const char *format, ...);`
Print a &ldquo;lint warning.&rdquo;  Normally this is the same as printing a
warning message, but if `gawk` was invoked with &lsquo;--lint=fatal&rsquo;,
then lint warnings become fatal error messages.

All of these functions are otherwise like the C `printf()`
family of functions, where the `format` parameter is a string
with literal characters and formatting codes intermixed.

---

Next: [Requesting Values](#Requesting-Values), Previous: [Printing Messages](#Printing-Messages), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.7 Updating `ERRNO`

The following functions allow you to update the `ERRNO`
variable:

`void update_ERRNO_int(int errno_val);`
Set `ERRNO` to the string equivalent of the error code
in `errno_val`. The value should be one of the defined
error codes in `<errno.h>`, and `gawk` turns it
into a (possibly translated) string using the C `strerror()` function.

`void update_ERRNO_string(const char *string);`
Set `ERRNO` directly to the string value of `ERRNO`.
`gawk` makes a copy of the value of `string`.

`void unset_ERRNO(void);`
Unset `ERRNO`.

---

Next: [Accessing Parameters](#Accessing-Parameters), Previous: [Updating `ERRNO`](#Updating-ERRNO), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.8 Requesting Values

All of the functions that return values from `gawk`
work in the same way. You pass in an `awk_valtype_t` value
to indicate what kind of value you expect.  If the actual value
matches what you requested, the function returns true and fills
in the `awk_value_t` result.
Otherwise, the function returns false, and the `val_type`
member indicates the type of the actual value.  You may then
print an error message or reissue the request for the actual
value type, as appropriate.  This behavior is summarized in
[Table 16.1](#table_002dvalue_002dtypes_002dreturned).

Type of Actual ValueStringStrnumNumberRegexArrayUndefinedStringStringStringStringStringfalsefalseStrnumfalseStrnumStrnumfalsefalsefalseNumberNumberNumberNumberfalsefalsefalseTypeRegexfalsefalsefalseRegexfalsefalseRequestedArrayfalsefalsefalsefalseArrayfalseScalarScalarScalarScalarScalarfalsefalseUndefinedStringStrnumNumberRegexArrayUndefinedValue cookiefalsefalsefalsefalsefalsefalse

**Table 16.1: **API value types returned

---

Next: [Symbol Table Access](#Symbol-Table-Access), Previous: [Requesting Values](#Requesting-Values), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.9 Accessing and Updating Parameters

Two functions give you access to the arguments (parameters)
passed to your extension function. They are:

`awk_bool_t get_argument(size_t count,``                        awk_valtype_t wanted,``                        awk_value_t *result);`
Fill in the `awk_value_t` structure pointed to by `result`
with the `count`th argument.  Return true if the actual
type matches `wanted`, and false otherwise.  In the latter
case, `result->val_type` indicates the actual type
(see [Table 16.1](#table_002dvalue_002dtypes_002dreturned)).  Counts are zero-based&mdash;the first
argument is numbered zero, the second one, and so on. `wanted`
indicates the type of value expected.

`awk_bool_t set_argument(size_t count, awk_array_t array);`
Convert a parameter that was undefined into an array; this provides
call by reference for arrays.  Return false if `count` is too big,
or if the argument&rsquo;s type is not undefined.  See [Array Manipulation](#Array-Manipulation)
for more information on creating arrays.

---

Next: [Array Manipulation](#Array-Manipulation), Previous: [Accessing Parameters](#Accessing-Parameters), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.10 Symbol Table Access

Two sets of routines provide access to global variables, and one set
allows you to create and release cached values.

&bull; [Symbol table by name](#Symbol-table-by-name):  Accessing variables by name.
&bull; [Symbol table by cookie](#Symbol-table-by-cookie):  Accessing variables by &ldquo;cookie&rdquo;.
&bull; [Cached values](#Cached-values):  Creating and using cached values.

---

Next: [Symbol table by cookie](#Symbol-table-by-cookie), Up: [Symbol Table Access](#Symbol-Table-Access)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.10.1 Variable Access and Update by Name

The following routines provide the ability to access and update
global `awk`-level variables by name.  In compiler terminology,
identifiers of different kinds are termed *symbols*, thus the &ldquo;sym&rdquo;
in the routines&rsquo; names.  The data structure that stores information
about symbols is termed a *symbol table*.
The functions are as follows:

`awk_bool_t sym_lookup(const char *name,``                      awk_valtype_t wanted,``                      awk_value_t *result);`
Fill in the `awk_value_t` structure pointed to by `result`
with the value of the variable named by the string `name`, which is
a regular C string.  `wanted` indicates the type of value expected.
Return true if the actual type matches `wanted`, and false otherwise.
In the latter case, `result->val_type` indicates the actual type
(see [Table 16.1](#table_002dvalue_002dtypes_002dreturned)).

`awk_bool_t sym_update(const char *name, awk_value_t *value);`
Update the variable named by the string `name`, which is a regular
C string.  The variable is added to `gawk`&rsquo;s symbol table
if it is not there.  Return true if everything worked, and false otherwise.

Changing types (scalar to array or vice versa) of an existing variable
is *not* allowed, nor may this routine be used to update an array.
This routine cannot be used to update any of the predefined
variables (such as `ARGC` or `NF`).

An extension can look up the value of `gawk`&rsquo;s special variables.
However, with the exception of the `PROCINFO` array, an extension
cannot change any of those variables.

---

Next: [Cached values](#Cached-values), Previous: [Symbol table by name](#Symbol-table-by-name), Up: [Symbol Table Access](#Symbol-Table-Access)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.10.2 Variable Access and Update by Cookie

A *scalar cookie* is an opaque handle that provides access
to a global variable or array. It is an optimization that
avoids looking up variables in `gawk`&rsquo;s symbol table every time
access is needed. This was discussed earlier, in [General Data Types](#General-Data-Types).

The following functions let you work with scalar cookies:

`awk_bool_t sym_lookup_scalar(awk_scalar_t cookie,``                             awk_valtype_t wanted,``                             awk_value_t *result);`
Retrieve the current value of a scalar cookie.
Once you have obtained a scalar cookie using `sym_lookup()`, you can
use this function to get its value more efficiently.
Return false if the value cannot be retrieved.

`awk_bool_t sym_update_scalar(awk_scalar_t cookie, awk_value_t *value);`
Update the value associated with a scalar cookie.  Return false if
the new value is not of type `AWK_STRING`, `AWK_STRNUM`, `AWK_REGEX`, or `AWK_NUMBER`.
Here too, the predefined variables may not be updated.

It is not obvious at first glance how to work with scalar cookies or
what their raison d&rsquo;&ecirc;tre really is.  In theory, the `sym_lookup()`
and `sym_update()` routines are all you really need to work with
variables.  For example, you might have code that looks up the value of
a variable, evaluates a condition, and then possibly changes the value
of the variable based on the result of that evaluation, like so:

    /*  do_magic --- do something really great */
    
    static awk_value_t *
    do_magic(int nargs, awk_value_t *result)
    {
        awk_value_t value;
    
        if (   sym_lookup("MAGIC_VAR", AWK_NUMBER, & value)
            && some_condition(value.num_value)) {
                value.num_value += 42;
                sym_update("MAGIC_VAR", & value);
        }
    
        return make_number(0.0, result);
    }
    

This code looks (and is) simple and straightforward. So what&rsquo;s the problem?

Well, consider what happens if `awk`-level code associated
with your extension calls the `magic()` function (implemented in
C by `do_magic()`), once per record, while processing hundreds
of thousands or millions of records.  The `MAGIC_VAR` variable is
looked up in the symbol table once or twice per function call!

The symbol table lookup is really pure overhead; it is considerably
more efficient to get a cookie that represents the variable, and use
that to get the variable&rsquo;s value and update it as needed.[105](#FOOT105)

Thus, the way to use cookies is as follows.  First, install
your extension&rsquo;s variable in `gawk`&rsquo;s symbol table using
`sym_update()`, as usual. Then get a scalar cookie for the variable
using `sym_lookup()`:

    static awk_scalar_t magic_var_cookie;    /* cookie for MAGIC_VAR */
    
    static void
    my_extension_init()
    {
        awk_value_t value;
    

    
    
        /* install initial value */
        sym_update("MAGIC_VAR", make_number(42.0, & value));
    
        /* get the cookie */
        sym_lookup("MAGIC_VAR", AWK_SCALAR, & value);
    
        /* save the cookie */
        magic_var_cookie = value.scalar_cookie;
        &hellip;
    }
    

Next, use the routines in this section for retrieving and updating
the value through the cookie.  Thus, `do_magic()` now becomes
something like this:

    /*  do_magic --- do something really great */
    
    static awk_value_t *
    do_magic(int nargs, awk_value_t *result)
    {
        awk_value_t value;
    
        if (   sym_lookup_scalar(magic_var_cookie, AWK_NUMBER, & value)
            && some_condition(value.num_value)) {
                value.num_value += 42;
                sym_update_scalar(magic_var_cookie, & value);
        }
        &hellip;
    
        return make_number(0.0, result);
    }
    

> NOTE: The previous code omitted error checking for
> presentation purposes.  Your extension code should be more robust
> and carefully check the return values from the API functions.

---

Previous: [Symbol table by cookie](#Symbol-table-by-cookie), Up: [Symbol Table Access](#Symbol-Table-Access)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.10.3 Creating and Using Cached Values

The routines in this section allow you to create and release
cached values.  Like scalar cookies, in theory, cached values
are not necessary. You can create numbers and strings using
the functions in [Constructor Functions](#Constructor-Functions). You can then
assign those values to variables using `sym_update()`
or `sym_update_scalar()`, as you like.

However, you can understand the point of cached values if you remember that
*every* string value&rsquo;s storage *must* come from `gawk_malloc()`,
`gawk_calloc()`, or `gawk_realloc()`.
If you have 20 variables, all of which have the same string value, you
must create 20 identical copies of the string.[106](#FOOT106)

It is clearly more efficient, if possible, to create a value once, and
then tell `gawk` to reuse the value for multiple variables. That
is what the routines in this section let you do.  The functions are as follows:

`awk_bool_t create_value(awk_value_t *value, awk_value_cookie_t *result);`
Create a cached string or numeric value from `value` for
efficient later assignment.  Only values of type `AWK_NUMBER`, `AWK_REGEX`, `AWK_STRNUM`,
and `AWK_STRING` are allowed.  Any other type is rejected.
`AWK_UNDEFINED` could be allowed, but doing so would result in
inferior performance.

`awk_bool_t release_value(awk_value_cookie_t vc);`
Release the memory associated with a value cookie obtained
from `create_value()`.

You use value cookies in a fashion similar to the way you use scalar cookies.
In the extension initialization routine, you create the value cookie:

    static awk_value_cookie_t answer_cookie;  /* static value cookie */
    
    static void
    my_extension_init()
    {
        awk_value_t value;
        char *long_string;
        size_t long_string_len;
    
        /* code from earlier */
        &hellip;
        /* &hellip; fill in long_string and long_string_len &hellip; */
        make_malloced_string(long_string, long_string_len, & value);
        create_value(& value, & answer_cookie);    /* create cookie */
        &hellip;
    }
    

Once the value is created, you can use it as the value of any number
of variables:

    static awk_value_t *
    do_magic(int nargs, awk_value_t *result)
    {
        awk_value_t new_value;
    
        &hellip;    /* as earlier */
    
        value.val_type = AWK_VALUE_COOKIE;
        value.value_cookie = answer_cookie;
        sym_update("VAR1", & value);
        sym_update("VAR2", & value);
        &hellip;
        sym_update("VAR100", & value);
        &hellip;
    }
    

Using value cookies in this way saves considerable storage, as all of
`VAR1` through `VAR100` share the same value.

You might be wondering, &ldquo;Is this sharing problematic?
What happens if `awk` code assigns a new value to `VAR1`;
are all the others changed too?&rdquo;

That&rsquo;s a great question. The answer is that no, it&rsquo;s not a problem.
Internally, `gawk` uses *reference-counted strings*. This means
that many variables can share the same string value, and `gawk`
keeps track of the usage.  When a variable&rsquo;s value changes, `gawk`
simply decrements the reference count on the old value and updates
the variable to use the new value.

Finally, as part of your cleanup action (see [Exit Callback Functions](#Exit-Callback-Functions))
you should release any cached values that you created, using
`release_value()`.

---

Next: [Redirection API](#Redirection-API), Previous: [Symbol Table Access](#Symbol-Table-Access), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.11 Array Manipulation

The primary data structure[107](#FOOT107) in `awk`
is the associative array (see [Arrays](#Arrays)).
Extensions need to be able to manipulate `awk` arrays.
The API provides a number of data structures for working with arrays,
functions for working with individual elements, and functions for
working with arrays as a whole. This includes the ability to
&ldquo;flatten&rdquo; an array so that it is easy for C code to traverse
every element in an array.  The array data structures integrate
nicely with the data structures for values to make it easy to
both work with and create true arrays of arrays (see [General Data Types](#General-Data-Types)).

&bull; [Array Data Types](#Array-Data-Types):  Data types for working with arrays.
&bull; [Array Functions](#Array-Functions):  Functions for working with arrays.
&bull; [Flattening Arrays](#Flattening-Arrays):  How to flatten arrays.
&bull; [Creating Arrays](#Creating-Arrays):  How to create and populate arrays.

---

Next: [Array Functions](#Array-Functions), Up: [Array Manipulation](#Array-Manipulation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.11.1 Array Data Types

The data types associated with arrays are as follows:

`typedef void *awk_array_t;`
If you request the value of an array variable, you get back an
`awk_array_t` value. This value is opaque[108](#FOOT108) to the extension; it uniquely identifies the array but can
only be used by passing it into API functions or receiving it from API
functions. This is very similar to way &lsquo;FILE *&rsquo; values are used
with the `<stdio.h>` library routines.

`typedef struct awk_element {``    /* convenience linked list pointer, not used by gawk */``    struct awk_element *next;``    enum {``        AWK_ELEMENT_DEFAULT = 0,  /* set by gawk */``        AWK_ELEMENT_DELETE = 1    /* set by extension */``    } flags;``    awk_value_t    index;``    awk_value_t    value;``} awk_element_t;`
The `awk_element_t` is a &ldquo;flattened&rdquo;
array element. `awk` produces an array of these
inside the `awk_flat_array_t` (see the next item).
Individual elements may be marked for deletion. New elements must be added
individually, one at a time, using the separate API for that purpose.
The fields are as follows:

`struct awk_element *next;`
This pointer is for the convenience of extension writers.  It allows
an extension to create a linked list of new elements that can then be
added to an array in a loop that traverses the list.

`enum { &hellip; } flags;`
A set of flag values that convey information between the extension
and `gawk`.  Currently there is only one: `AWK_ELEMENT_DELETE`.
Setting it causes `gawk` to delete the
element from the original array upon release of the flattened array.

`index``value`
The index and value of the element, respectively.
*All* memory pointed to by `index` and `value` belongs to `gawk`.

`typedef struct awk_flat_array {``    awk_const void *awk_const opaque1;    /* for use by gawk */``    awk_const void *awk_const opaque2;    /* for use by gawk */``    awk_const size_t count;     /* how many elements */``    awk_element_t elements[1];  /* will be extended */``} awk_flat_array_t;`
This is a flattened array. When an extension gets one of these
from `gawk`, the `elements` array is of actual
size `count`.
The `opaque1` and `opaque2` pointers are for use by `gawk`;
therefore they are marked `awk_const` so that the extension cannot
modify them.

---

Next: [Flattening Arrays](#Flattening-Arrays), Previous: [Array Data Types](#Array-Data-Types), Up: [Array Manipulation](#Array-Manipulation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.11.2 Array Functions

The following functions relate to individual array elements:

`awk_bool_t get_element_count(awk_array_t a_cookie, size_t *count);`
For the array represented by `a_cookie`, place in `*count`
the number of elements it contains. A subarray counts as a single element.
Return false if there is an error.

`awk_bool_t get_array_element(awk_array_t a_cookie,``                             const awk_value_t *const index,``                             awk_valtype_t wanted,``                             awk_value_t *result);`
For the array represented by `a_cookie`, return in `*result`
the value of the element whose index is `index`.
`wanted` specifies the type of value you wish to retrieve.
Return false if `wanted` does not match the actual type or if
`index` is not in the array (see [Table 16.1](#table_002dvalue_002dtypes_002dreturned)).

The value for `index` can be numeric, in which case `gawk`
converts it to a string. Using nonintegral values is possible, but
requires that you understand how such values are converted to strings
(see [Conversion](#Conversion)); thus, using integral values is safest.

As with *all* strings passed into `gawk` from an extension,
the string value of `index` must come from `gawk_malloc()`,
`gawk_calloc()`, or `gawk_realloc()`, and
`gawk` releases the storage.

`awk_bool_t set_array_element(awk_array_t a_cookie,``                             const awk_value_t *const index,``                             const awk_value_t *const value);`
In the array represented by `a_cookie`, create or modify
the element whose index is given by `index`.
The `ARGV` and `ENVIRON` arrays may not be changed,
although the `PROCINFO` array can be.

`awk_bool_t set_array_element_by_elem(awk_array_t a_cookie,``                                     awk_element_t element);`
Like `set_array_element()`, but take the `index` and `value`
from `element`. This is a convenience macro.

`awk_bool_t del_array_element(awk_array_t a_cookie,``                             const awk_value_t* const index);`
Remove the element with the given index from the array
represented by `a_cookie`.
Return true if the element was removed, or false if the element did
not exist in the array.

The following functions relate to arrays as a whole:

`awk_array_t create_array(void);`
Create a new array to which elements may be added.
See [Creating Arrays](#Creating-Arrays) for a discussion of how to
create a new array and add elements to it.

`awk_bool_t clear_array(awk_array_t a_cookie);`
Clear the array represented by `a_cookie`.
Return false if there was some kind of problem, true otherwise.
The array remains an array, but after calling this function, it
has no elements. This is equivalent to using the `delete`
statement (see [Delete](#Delete)).

`awk_bool_t flatten_array_typed(awk_array_t a_cookie,``                               awk_flat_array_t **data,``                               awk_valtype_t index_type,``                               awk_valtype_t value_type);`
For the array represented by `a_cookie`, create an `awk_flat_array_t`
structure and fill it in with indices and values of the requested types.
Set the pointer whose address is passed as `data`
to point to this structure.
Return true upon success, or false otherwise.
See [Flattening Arrays](#Flattening-Arrays),
for a discussion of how to
flatten an array and work with it.

`awk_bool_t flatten_array(awk_array_t a_cookie, awk_flat_array_t **data);`
For the array represented by `a_cookie`, create an `awk_flat_array_t`
structure and fill it in with `AWK_STRING` indices and
`AWK_UNDEFINED` values.
This is superseded by `flatten_array_typed()`.
It is provided as a macro, and remains for convenience and for source code
compatibility with the previous version of the API.

`awk_bool_t release_flattened_array(awk_array_t a_cookie,``                                   awk_flat_array_t *data);`
When done with a flattened array, release the storage using this function.
You must pass in both the original array cookie and the address of
the created `awk_flat_array_t` structure.
The function returns true upon success, false otherwise.

---

Next: [Creating Arrays](#Creating-Arrays), Previous: [Array Functions](#Array-Functions), Up: [Array Manipulation](#Array-Manipulation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.11.3 Working With All The Elements of an Array

To *flatten* an array is to create a structure that
represents the full array in a fashion that makes it easy
for C code to traverse the entire array.  Some of the code
in extension/testext.c does this, and also serves
as a nice example showing how to use the APIs.

We walk through that part of the code one step at a time.
First, the `gawk` script that drives the test extension:

    @load "testext"
    BEGIN {
        n = split("blacky rusty sophie raincloud lucky", pets)
        printf("pets has %d elements\n", length(pets))
        ret = dump_array_and_delete("pets", "3")
        printf("dump_array_and_delete(pets) returned %d\n", ret)
        if ("3" in pets)
            printf("dump_array_and_delete() did NOT remove index \"3\"!\n")
        else
            printf("dump_array_and_delete() did remove index \"3\"!\n")
        print ""
    }
    

This code creates an array with `split()` (see [String Functions](#String-Functions))
and then calls `dump_array_and_delete()`. That function looks up
the array whose name is passed as the first argument, and
deletes the element at the index passed in the second argument.
The `awk` code then prints the return value and checks if the element
was indeed deleted.  Here is the C code that implements
`dump_array_and_delete()`. It has been edited slightly for
presentation.

The first part declares variables, sets up the default
return value in `result`, and checks that the function
was called with the correct number of arguments:

    static awk_value_t *
    dump_array_and_delete(int nargs, awk_value_t *result)
    {
        awk_value_t value, value2, value3;
        awk_flat_array_t *flat_array;
        size_t count;
        char *name;
        int i;
    
        assert(result != NULL);
        make_number(0.0, result);
    
        if (nargs != 2) {
            printf("dump_array_and_delete: nargs not right "
                   "(%d should be 2)\n", nargs);
            goto out;
        }
    

The function then proceeds in steps, as follows. First, retrieve
the name of the array, passed as the first argument, followed by
the array itself. If either operation fails, print an
error message and return:

        /* get argument named array as flat array and print it */
        if (get_argument(0, AWK_STRING, & value)) {
            name = value.str_value.str;
            if (sym_lookup(name, AWK_ARRAY, & value2))
                printf("dump_array_and_delete: sym_lookup of %s passed\n",
                       name);
            else {
                printf("dump_array_and_delete: sym_lookup of %s failed\n",
                       name);
                goto out;
            }
        } else {
            printf("dump_array_and_delete: get_argument(0) failed\n");
            goto out;
        }
    

For testing purposes and to make sure that the C code sees
the same number of elements as the `awk` code,
the second step is to get the count of elements in the array
and print it:

        if (! get_element_count(value2.array_cookie, & count)) {
            printf("dump_array_and_delete: get_element_count failed\n");
            goto out;
        }
    
        printf("dump_array_and_delete: incoming size is %lu\n",
               (unsigned long) count);
    

The third step is to actually flatten the array, and then
to double-check that the count in the `awk_flat_array_t`
is the same as the count just retrieved:

        if (! flatten_array_typed(value2.array_cookie, & flat_array,
                                  AWK_STRING, AWK_UNDEFINED)) {
            printf("dump_array_and_delete: could not flatten array\n");
            goto out;
        }
    
        if (flat_array->count != count) {
            printf("dump_array_and_delete: flat_array->count (%lu)"
                   " != count (%lu)\n",
                    (unsigned long) flat_array->count,
                    (unsigned long) count);
            goto out;
        }
    

The fourth step is to retrieve the index of the element
to be deleted, which was passed as the second argument.
Remember that argument counts passed to `get_argument()`
are zero-based, and thus the second argument is numbered one:

        if (! get_argument(1, AWK_STRING, & value3)) {
            printf("dump_array_and_delete: get_argument(1) failed\n");
            goto out;
        }
    

The fifth step is where the &ldquo;real work&rdquo; is done. The function
loops over every element in the array, printing the index and
element values. In addition, upon finding the element with the
index that is supposed to be deleted, the function sets the
`AWK_ELEMENT_DELETE` bit in the `flags` field
of the element.  When the array is released, `gawk`
traverses the flattened array, and deletes any elements that
have this flag bit set:

        for (i = 0; i < flat_array->count; i++) {
            printf("\t%s[\"%.*s\"] = %s\n",
                name,
                (int) flat_array->elements[i].index.str_value.len,
                flat_array->elements[i].index.str_value.str,
                valrep2str(& flat_array->elements[i].value));
    
            if (strcmp(value3.str_value.str,
                       flat_array->elements[i].index.str_value.str) == 0) {
                flat_array->elements[i].flags |= AWK_ELEMENT_DELETE;
                printf("dump_array_and_delete: marking element \"%s\" "
                       "for deletion\n",
                    flat_array->elements[i].index.str_value.str);
            }
        }
    

The sixth step is to release the flattened array. This tells
`gawk` that the extension is no longer using the array,
and that it should delete any elements marked for deletion.
`gawk` also frees any storage that was allocated,
so you should not use the pointer (`flat_array` in this
code) once you have called `release_flattened_array()`:

        if (! release_flattened_array(value2.array_cookie, flat_array)) {
            printf("dump_array_and_delete: could not release flattened array\n");
            goto out;
        }
    

Finally, because everything was successful, the function sets the
return value to success, and returns:

        make_number(1.0, result);
    out:
        return result;
    }
    

Here is the output from running this part of the test:

    pets has 5 elements
    dump_array_and_delete: sym_lookup of pets passed
    dump_array_and_delete: incoming size is 5
            pets["1"] = "blacky"
            pets["2"] = "rusty"
            pets["3"] = "sophie"
    dump_array_and_delete: marking element "3" for deletion
            pets["4"] = "raincloud"
            pets["5"] = "lucky"
    dump_array_and_delete(pets) returned 1
    dump_array_and_delete() did remove index "3"!
    

---

Previous: [Flattening Arrays](#Flattening-Arrays), Up: [Array Manipulation](#Array-Manipulation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.11.4 How To Create and Populate Arrays

Besides working with arrays created by `awk` code, you can
create arrays and populate them as you see fit, and then `awk`
code can access them and manipulate them.

There are two important points about creating arrays from extension code:

-  You must install a new array into `gawk`&rsquo;s symbol
table immediately upon creating it.  Once you have done so,
you can then populate the array.

Similarly, if installing a new array as a subarray of an existing array,
you must add the new array to its parent before adding any elements to it.

Thus, the correct way to build an array is to work &ldquo;top down.&rdquo;  Create
the array, and immediately install it in `gawk`&rsquo;s symbol table
using `sym_update()`, or install it as an element in a previously
existing array using `set_array_element()`.  We show example code shortly.

-  Due to `gawk` internals, after using `sym_update()` to install an array
into `gawk`, you have to retrieve the array cookie from the value
passed in to `sym_update()` before doing anything else with it, like so:

    awk_value_t val;
    awk_array_t new_array;
    
    new_array = create_array();
    val.val_type = AWK_ARRAY;
    val.array_cookie = new_array;
    
    /* install array in the symbol table */
    sym_update("array", & val);
    
    new_array = val.array_cookie;    /* YOU MUST DO THIS */
    

If installing an array as a subarray, you must also retrieve the value
of the array cookie after the call to `set_element()`.

The following C code is a simple test extension to create an array
with two regular elements and with a subarray. The leading `#include`
directives and boilerplate variable declarations
(see [Extension API Boilerplate](#Extension-API-Boilerplate))
are omitted for brevity.
The first step is to create a new array and then install it
in the symbol table:

    /* create_new_array --- create a named array */
    
    static void
    create_new_array()
    {
        awk_array_t a_cookie;
        awk_array_t subarray;
        awk_value_t index, value;
    
        a_cookie = create_array();
        value.val_type = AWK_ARRAY;
        value.array_cookie = a_cookie;
    
        if (! sym_update("new_array", & value))
            printf("create_new_array: sym_update(\"new_array\") failed!\n");
        a_cookie = value.array_cookie;
    

Note how `a_cookie` is reset from the `array_cookie` field in
the `value` structure.

The second step is to install two regular values into `new_array`:

        (void) make_const_string("hello", 5, & index);
        (void) make_const_string("world", 5, & value);
        if (! set_array_element(a_cookie, & index, & value)) {
            printf("fill_in_array: set_array_element failed\n");
            return;
        }
    
        (void) make_const_string("answer", 6, & index);
        (void) make_number(42.0, & value);
        if (! set_array_element(a_cookie, & index, & value)) {
            printf("fill_in_array: set_array_element failed\n");
            return;
        }
    

The third step is to create the subarray and install it:

        (void) make_const_string("subarray", 8, & index);
        subarray = create_array();
        value.val_type = AWK_ARRAY;
        value.array_cookie = subarray;
        if (! set_array_element(a_cookie, & index, & value)) {
            printf("fill_in_array: set_array_element failed\n");
            return;
        }
        subarray = value.array_cookie;
    

The final step is to populate the subarray with its own element:

        (void) make_const_string("foo", 3, & index);
        (void) make_const_string("bar", 3, & value);
        if (! set_array_element(subarray, & index, & value)) {
            printf("fill_in_array: set_array_element failed\n");
            return;
        }
    }
    

Here is a sample script that loads the extension
and then dumps the array:

    @load "subarray"
    
    function dumparray(name, array,     i)
    {
        for (i in array)
            if (isarray(array[i]))
                dumparray(name "[\"" i "\"]", array[i])
            else
                printf("%s[\"%s\"] = %s\n", name, i, array[i])
    }
    
    BEGIN {
        dumparray("new_array", new_array);
    }
    

Here is the result of running the script:

    $ AWKLIBPATH=$PWD gawk -f subarray.awk
    -| new_array["subarray"]["foo"] = bar
    -| new_array["hello"] = world
    -| new_array["answer"] = 42
    

(See [Finding Extensions](#Finding-Extensions) for more information on the
`AWKLIBPATH` environment variable.)

---

Next: [Extension API Variables](#Extension-API-Variables), Previous: [Array Manipulation](#Array-Manipulation), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.12 Accessing and Manipulating Redirections

The following function allows extensions to access and manipulate redirections.

`awk_bool_t get_file(const char *name,``                    size_t name_len,``                    const char *filetype,``                    int fd,``                    const awk_input_buf_t **ibufp,``                    const awk_output_buf_t **obufp);`
Look up file `name` in `gawk`&rsquo;s internal redirection table.
If `name` is `NULL` or `name_len` is zero, return
data for the currently open input file corresponding to `FILENAME`.
(This does not access the `filetype` argument, so that may be undefined).  
If the file is not already open, attempt to open it.
The `filetype` argument must be zero-terminated and should be one of:

`">"`
A file opened for output.

`">>"`
A file opened for append.

`"<"`
A file opened for input.

`"|>"`
A pipe opened for output.

`"|<"`
A pipe opened for input.

`"|&"`
A two-way coprocess.

On error, return `awk_false`.  Otherwise, return
`awk_true`, and return additional information about the redirection
in the `ibufp` and `obufp` pointers.

For input redirections, the `*ibufp` value should be non-`NULL`,
and `*obufp` should be `NULL`.  For output redirections,
the `*obufp` value should be non-`NULL`, and `*ibufp`
should be `NULL`.  For two-way coprocesses, both values should
be non-`NULL`.

In the usual case, the extension is interested in `(*ibufp)->fd`
and/or `fileno((*obufp)->fp)`.  If the file is not already
open, and the `fd` argument is nonnegative, `gawk`
will use that file descriptor instead of opening the file in the
usual way.  If `fd` is nonnegative, but the file exists already,
`gawk` ignores `fd` and returns the existing file.  It is
the caller&rsquo;s responsibility to notice that neither the `fd` in
the returned `awk_input_buf_t` nor the `fd` in the returned
`awk_output_buf_t` matches the requested value.

Note that supplying a file descriptor is currently *not* supported
for pipes.  However, supplying a file descriptor should work for input,
output, append, and two-way (coprocess) sockets.  If `filetype`
is two-way, `gawk` assumes that it is a socket!  Note that in
the two-way case, the input and output file descriptors may differ.
To check for success, you must check whether either matches.

It is anticipated that this API function will be used to implement I/O
multiplexing and a socket library.

---

Next: [Extension API Boilerplate](#Extension-API-Boilerplate), Previous: [Redirection API](#Redirection-API), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.13 API Variables

The API provides two sets of variables.  The first provides information
about the version of the API (both with which the extension was compiled,
and with which `gawk` was compiled).  The second provides
information about how `gawk` was invoked.

&bull; [Extension Versioning](#Extension-Versioning):  API Version information.
&bull; [Extension GMP/MPFR Versioning](#Extension-GMP_002fMPFR-Versioning):  Version information about GMP and MPFR.
&bull; [Extension API Informational Variables](#Extension-API-Informational-Variables):  Variables providing information about
                                  `gawk`&rsquo;s invocation.

---

Next: [Extension GMP/MPFR Versioning](#Extension-GMP_002fMPFR-Versioning), Up: [Extension API Variables](#Extension-API-Variables)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.13.1 API Version Constants and Variables

The API provides both a &ldquo;major&rdquo; and a &ldquo;minor&rdquo; version number.
The API versions are available at compile time as C preprocessor defines
to support conditional compilation, and as enum constants to facilitate
debugging:

API VersionC Preprocessor Defineenum constantMajor`gawk_api_major_version``GAWK_API_MAJOR_VERSION`Minor`gawk_api_minor_version``GAWK_API_MINOR_VERSION`

**Table 16.2: **gawk API version constants

The minor version increases when new functions are added to the API. Such
new functions are always added to the end of the API `struct`.

The major version increases (and the minor version is reset to zero) if any
of the data types change size or member order, or if any of the existing
functions change signature.

It could happen that an extension may be compiled against one version
of the API but loaded by a version of `gawk` using a different
version. For this reason, the major and minor API versions of the
running `gawk` are included in the API `struct` as read-only
constant integers:

`api->major_version`
The major version of the running `gawk`.

`api->minor_version`
The minor version of the running `gawk`.

It is up to the extension to decide if there are API incompatibilities.
Typically, a check like this is enough:

    if (   api->major_version != GAWK_API_MAJOR_VERSION
        || api->minor_version < GAWK_API_MINOR_VERSION) {
            fprintf(stderr, "foo_extension: version mismatch with gawk!\n");
            fprintf(stderr, "\tmy version (%d, %d), gawk version (%d, %d)\n",
                    GAWK_API_MAJOR_VERSION, GAWK_API_MINOR_VERSION,
                    api->major_version, api->minor_version);
            exit(1);
    }
    

Such code is included in the boilerplate `dl_load_func()` macro
provided in gawkapi.h (discussed in
[Extension API Boilerplate](#Extension-API-Boilerplate)).

---

Next: [Extension API Informational Variables](#Extension-API-Informational-Variables), Previous: [Extension Versioning](#Extension-Versioning), Up: [Extension API Variables](#Extension-API-Variables)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.13.2 GMP and MPFR Version Information

The API also includes information about the versions of GMP and MPFR
with which the running `gawk` was compiled (if any).
They are included in the API `struct` as read-only
constant integers:

`api->gmp_major_version`
The major version of the GMP library used to compile `gawk`.

`api->gmp_minor_version`
The minor version of the GMP library used to compile `gawk`.

`api->mpfr_major_version`
The major version of the MPFR library used to compile `gawk`.

`api->mpfr_minor_version`
The minor version of the MPFR library used to compile `gawk`.

These fields are set to zero if `gawk` was compiled without
MPFR support.

You can check if the versions of MPFR and GMP that you are using match those
of `gawk` with the following macro:

`check_mpfr_version(extension)`
The `extension` is the extension id passed to all the other macros
and functions defined in gawkapi.h.  If you have not included
the `<mpfr.h>` header file, then this macro will be defined to do nothing.

If you have included that file, then this macro compares the MPFR
and GMP major and minor versions against those of the library you are
compiling against.  If your libraries are newer than `gawk`&rsquo;s, it
produces a fatal error message.

The `dl_load_func()` macro (see [Extension API Boilerplate](#Extension-API-Boilerplate))
calls `check_mpfr_version()`.

---

Previous: [Extension GMP/MPFR Versioning](#Extension-GMP_002fMPFR-Versioning), Up: [Extension API Variables](#Extension-API-Variables)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.13.3 Informational Variables

The API provides access to several variables that describe
whether the corresponding command-line options were enabled when
`gawk` was invoked.  The variables are:

`do_debug`
This variable is true if `gawk` was invoked with --debug option.

`do_lint`
This variable is true if `gawk` was invoked with --lint option.

`do_mpfr`
This variable is true if `gawk` was invoked with --bignum option.

`do_profile`
This variable is true if `gawk` was invoked with --profile option.

`do_sandbox`
This variable is true if `gawk` was invoked with --sandbox option.

`do_traditional`
This variable is true if `gawk` was invoked with --traditional option.

The value of `do_lint` can change if `awk` code
modifies the `LINT` predefined variable (see [Built-in Variables](#Built_002din-Variables)).
The others should not change during execution.

---

Next: [Changes from API V1](#Changes-from-API-V1), Previous: [Extension API Variables](#Extension-API-Variables), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.14 Boilerplate Code

As mentioned earlier (see [Extension Mechanism Outline](#Extension-Mechanism-Outline)), the function
definitions as presented are really macros. To use these macros, your
extension must provide a small amount of boilerplate code (variables and
functions) toward the top of your source file, using predefined names
as described here.  The boilerplate needed is also provided in comments
in the gawkapi.h header file:

    /* Boilerplate code: */
    int plugin_is_GPL_compatible;
    
    static gawk_api_t *const api;
    

    static awk_ext_id_t ext_id;
    static const char *ext_version = NULL; /* or &hellip; = "some string" */
    
    static awk_ext_func_t func_table[] = {
        { "name", do_name, 1, 0, awk_false, NULL },
        /* &hellip; */
    };
    
    /* EITHER: */
    
    static awk_bool_t (*init_func)(void) = NULL;
    
    /* OR: */
    
    static awk_bool_t
    init_my_extension(void)
    {
        &hellip;
    }
    
    static awk_bool_t (*init_func)(void) = init_my_extension;
    
    dl_load_func(func_table, some_name, "name_space_in_quotes")
    

These variables and functions are as follows:

`int plugin_is_GPL_compatible;`
This asserts that the extension is compatible with
the GNU GPL (see [Copying](#Copying)).
If your extension does not have this, `gawk`
will not load it (see [Plugin License](#Plugin-License)).

`static gawk_api_t *const api;`
This global `static` variable should be set to point to
the `gawk_api_t` pointer that `gawk` passes to your
`dl_load()` function.  This variable is used by all of the macros.

`static awk_ext_id_t ext_id;`
This global static variable should be set to the `awk_ext_id_t`
value that `gawk` passes to your `dl_load()` function.
This variable is used by all of the macros.

`static const char *ext_version = NULL; /* or &hellip; = "some string" */`
This global `static` variable should be set either
to `NULL`, or to point to a string giving the name and version of
your extension.

`static awk_ext_func_t func_table[] = { &hellip; };`
This is an array of one or more `awk_ext_func_t` structures,
as described earlier (see [Extension Functions](#Extension-Functions)).
It can then be looped over for multiple calls to
`add_ext_func()`.

`static awk_bool_t (*init_func)(void) = NULL;``                   OR``static awk_bool_t init_my_extension(void) { &hellip; }``static awk_bool_t (*init_func)(void) = init_my_extension;`
If you need to do some initialization work, you should define a
function that does it (creates variables, opens files, etc.)
and then define the `init_func` pointer to point to your
function.
The function should return `awk_false` upon failure, or `awk_true`
if everything goes well.

If you don&rsquo;t need to do any initialization, define the pointer and
initialize it to `NULL`.

`dl_load_func(func_table, some_name, "name_space_in_quotes")`
This macro expands to a `dl_load()` function that performs
all the necessary initializations.

The point of all the variables and arrays is to let the
`dl_load()` function (from the `dl_load_func()`
macro) do all the standard work. It does the following:

1.  Check the API versions. If the extension major version does not match
`gawk`&rsquo;s, or if the extension minor version is greater than
`gawk`&rsquo;s, it prints a fatal error message and exits.

2.  Check the MPFR and GMP versions. If there is a mismatch, it prints
a fatal error message and exits.

3.  Load the functions defined in `func_table`.
If any of them fails to load, it prints a warning message but
continues on.

4.  If the `init_func` pointer is not `NULL`, call the
function it points to. If it returns `awk_false`, print a
warning message.

5.  If `ext_version` is not `NULL`, register
the version string with `gawk`.

---

Previous: [Extension API Boilerplate](#Extension-API-Boilerplate), Up: [Extension API Description](#Extension-API-Description)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.4.15 Changes From Version 1 of the API

The current API is *not* binary compatible with version 1 of the API.
You will have to recompile your extensions in order to use them with
the current version of `gawk`.

Fortunately, at the possible expense of some compile-time warnings, the API remains
source-code&ndash;compatible with the previous API. The major differences are
the additional members in the `awk_ext_func_t` structure, and the
addition of the third argument to the C implementation function
(see [Extension Functions](#Extension-Functions)).

Here is a list of individual features that changed from version 1 to
version 2 of the API:

-  Numeric values can now have MPFR/MPZ variants
(see [General Data Types](#General-Data-Types)).

-  There are new string types: `AWK_REGEX` and `AWK_STRNUM`
(see [General Data Types](#General-Data-Types)).

-  The `ezalloc()` macro is new
(see [Memory Allocation Functions](#Memory-Allocation-Functions)).

-  The `awk_ext_func_t` structure changed. Instead of
`num_expected_args`, it now has `max_expected` and
`min_required`
(see [Extension Functions](#Extension-Functions)).

-  For `get_record()`, an input parser can now specify field widths
(see [Input Parsers](#Input-Parsers)).

-  Extensions can now produce nonfatal error messages
(see [Printing Messages](#Printing-Messages)).

-  When flattening an array, you can now specify the index and value types
(see [Array Functions](#Array-Functions)).

-  The `get_file()` API is new
(see [Redirection API](#Redirection-API)).

---

Next: [Extension Example](#Extension-Example), Previous: [Extension API Description](#Extension-API-Description), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.5 How `gawk` Finds Extensions

Compiled extensions have to be installed in a directory where
`gawk` can find them.  If `gawk` is configured and
built in the default fashion, the directory in which to find
extensions is /usr/local/lib/gawk.  You can also specify a search
path with a list of directories to search for compiled extensions.
See [AWKLIBPATH Variable](#AWKLIBPATH-Variable) for more information.

---

Next: [Extension Samples](#Extension-Samples), Previous: [Finding Extensions](#Finding-Extensions), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.6 Example: Some File Functions

> No matter where you go, there you are.

&mdash; *Buckaroo Banzai*

Two useful functions that are not in `awk` are `chdir()` (so
that an `awk` program can change its directory) and `stat()`
(so that an `awk` program can gather information about a file).
In order to illustrate the API in action, this section implements
these functions for `gawk` in an extension.

&bull; [Internal File Description](#Internal-File-Description):  What the new functions will do.
&bull; [Internal File Ops](#Internal-File-Ops):  The code for internal file operations.
&bull; [Using Internal File Ops](#Using-Internal-File-Ops):  How to use an external extension.

---

Next: [Internal File Ops](#Internal-File-Ops), Up: [Extension Example](#Extension-Example)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.6.1 Using `chdir()` and `stat()`

This section shows how to use the new functions at
the `awk` level once they&rsquo;ve been integrated into the
running `gawk` interpreter.  Using `chdir()` is very
straightforward. It takes one argument, the new directory to change to:

    @load "filefuncs"
    &hellip;
    newdir = "/home/arnold/funstuff"
    ret = chdir(newdir)
    if (ret < 0) {
        printf("could not change to %s: %s\n", newdir, ERRNO) > "/dev/stderr"
        exit 1
    }
    &hellip;
    

The return value is negative if the `chdir()` failed, and
`ERRNO` (see [Built-in Variables](#Built_002din-Variables)) is set to a string indicating
the error.

Using `stat()` is a bit more complicated.  The C `stat()`
function fills in a structure that has a fair amount of information.
The right way to model this in `awk` is to fill in an associative
array with the appropriate information:

    file = "/home/arnold/.profile"
    ret = stat(file, fdata)
    if (ret < 0) {
        printf("could not stat %s: %s\n",
                 file, ERRNO) > "/dev/stderr"
        exit 1
    }
    printf("size of %s is %d bytes\n", file, fdata["size"])
    

The `stat()` function always clears the data array, even if
the `stat()` fails.  It fills in the following elements:

`"name"`
The name of the file that was `stat()`ed.

`"dev"``"ino"`
The file&rsquo;s device and inode numbers, respectively.

`"mode"`
The file&rsquo;s mode, as a numeric value. This includes both the file&rsquo;s
type and its permissions.

`"nlink"`
The number of hard links (directory entries) the file has.

`"uid"``"gid"`
The numeric user and group ID numbers of the file&rsquo;s owner.

`"size"`
The size in bytes of the file.

`"blocks"`
The number of disk blocks the file actually occupies. This may not
be a function of the file&rsquo;s size if the file has holes.

`"atime"``"mtime"``"ctime"`
The file&rsquo;s last access, modification, and inode update times,
respectively.  These are numeric timestamps, suitable for formatting
with `strftime()`
(see [Time Functions](#Time-Functions)).

`"pmode"`
The file&rsquo;s &ldquo;printable mode.&rdquo;  This is a string representation of
the file&rsquo;s type and permissions, such as is produced by
&lsquo;ls -l&rsquo;&mdash;for example, `"drwxr-xr-x"`.

`"type"`
A printable string representation of the file&rsquo;s type.  The value
is one of the following:

`"blockdev"``"chardev"`
The file is a block or character device (&ldquo;special file&rdquo;).

`"directory"`
The file is a directory.

`"fifo"`
The file is a named pipe (also known as a FIFO).

`"file"`
The file is just a regular file.

`"socket"`
The file is an `AF_UNIX` (&ldquo;Unix domain&rdquo;) socket in the
filesystem.

`"symlink"`
The file is a symbolic link.

`"devbsize"`
The size of a block for the element indexed by `"blocks"`.
This information is derived from either the `DEV_BSIZE`
constant defined in `<sys/param.h>` on most systems,
or the `S_BLKSIZE` constant in `<sys/stat.h>` on BSD systems.
For some other systems, *a priori* knowledge is used to provide
a value. Where no value can be determined, it defaults to 512.

Several additional elements may be present, depending upon the operating
system and the type of the file.  You can test for them in your `awk`
program by using the `in` operator
(see [Reference to Elements](#Reference-to-Elements)):

`"blksize"`
The preferred block size for I/O to the file. This field is not
present on all POSIX-like systems in the C `stat` structure.

`"linkval"`
If the file is a symbolic link, this element is the name of the
file the link points to (i.e., the value of the link).

`"rdev"``"major"``"minor"`
If the file is a block or character device file, then these values
represent the numeric device number and the major and minor components
of that number, respectively.

---

Next: [Using Internal File Ops](#Using-Internal-File-Ops), Previous: [Internal File Description](#Internal-File-Description), Up: [Extension Example](#Extension-Example)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.6.2 C Code for `chdir()` and `stat()`

Here is the C code for these extensions.[109](#FOOT109)

The file includes a number of standard header files, and then includes
the gawkapi.h header file, which provides the API definitions.
Those are followed by the necessary variable declarations
to make use of the API macros and boilerplate code
(see [Extension API Boilerplate](#Extension-API-Boilerplate)):

    #ifdef HAVE_CONFIG_H
    #include <config.h>
    #endif
    
    #include <stdio.h>
    #include <assert.h>
    #include <errno.h>
    #include <stdlib.h>
    #include <string.h>
    #include <unistd.h>
    
    #include <sys/types.h>
    #include <sys/stat.h>
    
    #include "gawkapi.h"
    
    #include "gettext.h"
    #define _(msgid)  gettext(msgid)
    #define N_(msgid) msgid
    
    #include "gawkfts.h"
    #include "stack.h"
    
    static const gawk_api_t *api;    /* for convenience macros to work */
    static awk_ext_id_t ext_id;
    static awk_bool_t init_filefuncs(void);
    static awk_bool_t (*init_func)(void) = init_filefuncs;
    static const char *ext_version = "filefuncs extension: version 1.0";
    
    int plugin_is_GPL_compatible;
    

By convention, for an `awk` function `foo()`, the C function
that implements it is called `do_foo()`.  The function should have
two arguments. The first is an `int`, usually called `nargs`,
that represents the number of actual arguments for the function.
The second is a pointer to an `awk_value_t` structure, usually named
`result`:

    /*  do_chdir --- provide dynamically loaded chdir() function for gawk */
    
    static awk_value_t *
    do_chdir(int nargs, awk_value_t *result, struct awk_ext_func *unused)
    

    {
        awk_value_t newdir;
        int ret = -1;
    
        assert(result != NULL);
    

The `newdir`
variable represents the new directory to change to, which is retrieved
with `get_argument()`.  Note that the first argument is
numbered zero.

If the argument is retrieved successfully, the function calls the
`chdir()` system call. Otherwise, if the `chdir()` fails,
it updates `ERRNO`:

        if (get_argument(0, AWK_STRING, & newdir)) {
            ret = chdir(newdir.str_value.str);
            if (ret < 0)
                update_ERRNO_int(errno);
        }
    

Finally, the function returns the return value to the `awk` level:

        return make_number(ret, result);
    }
    

The `stat()` extension is more involved.  First comes a function
that turns a numeric mode into a printable representation
(e.g., octal `0644` becomes &lsquo;-rw-r--r--&rsquo;). This is omitted here for brevity:

    /* format_mode --- turn a stat mode field into something readable */
    
    static char *
    format_mode(unsigned long fmode)
    {
        &hellip;
    }
    

Next comes a function for reading symbolic links, which is also
omitted here for brevity:

    /* read_symlink --- read a symbolic link into an allocated buffer.
       &hellip; */
    
    static char *
    read_symlink(const char *fname, size_t bufsize, ssize_t *linksize)
    {
        &hellip;
    }
    

Two helper functions simplify entering values in the
array that will contain the result of the `stat()`:

    /* array_set --- set an array element */
    
    static void
    array_set(awk_array_t array, const char *sub, awk_value_t *value)
    {
        awk_value_t index;
    
        set_array_element(array,
                          make_const_string(sub, strlen(sub), & index),
                          value);
    
    }
    
    /* array_set_numeric --- set an array element with a number */
    
    static void
    array_set_numeric(awk_array_t array, const char *sub, double num)
    {
        awk_value_t tmp;
    
        array_set(array, sub, make_number(num, & tmp));
    }
    

The following function does most of the work to fill in
the `awk_array_t` result array with values obtained
from a valid `struct stat`. This work is done in a separate function
to support the `stat()` function for `gawk` and also
to support the `fts()` extension, which is included in
the same file but whose code is not shown here
(see [Extension Sample File Functions](#Extension-Sample-File-Functions)).

The first part of the function is variable declarations,
including a table to map file types to strings:

    /* fill_stat_array --- do the work to fill an array with stat info */
    
    static int
    fill_stat_array(const char *name, awk_array_t array, struct stat *sbuf)
    {
        char *pmode;    /* printable mode */
        const char *type = "unknown";
        awk_value_t tmp;
        static struct ftype_map {
            unsigned int mask;
            const char *type;
        } ftype_map[] = {
            { S_IFREG, "file" },
            { S_IFBLK, "blockdev" },
            { S_IFCHR, "chardev" },
            { S_IFDIR, "directory" },
    #ifdef S_IFSOCK
            { S_IFSOCK, "socket" },
    #endif
    #ifdef S_IFIFO
            { S_IFIFO, "fifo" },
    #endif
    #ifdef S_IFLNK
            { S_IFLNK, "symlink" },
    #endif
    #ifdef S_IFDOOR /* Solaris weirdness */
            { S_IFDOOR, "door" },
    #endif
        };
        int j, k;
    

The destination array is cleared, and then code fills in
various elements based on values in the `struct stat`:

        /* empty out the array */
        clear_array(array);
    
        /* fill in the array */
        array_set(array, "name", make_const_string(name, strlen(name),
                                                   & tmp));
        array_set_numeric(array, "dev", sbuf->st_dev);
        array_set_numeric(array, "ino", sbuf->st_ino);
        array_set_numeric(array, "mode", sbuf->st_mode);
        array_set_numeric(array, "nlink", sbuf->st_nlink);
        array_set_numeric(array, "uid", sbuf->st_uid);
        array_set_numeric(array, "gid", sbuf->st_gid);
        array_set_numeric(array, "size", sbuf->st_size);
        array_set_numeric(array, "blocks", sbuf->st_blocks);
        array_set_numeric(array, "atime", sbuf->st_atime);
        array_set_numeric(array, "mtime", sbuf->st_mtime);
        array_set_numeric(array, "ctime", sbuf->st_ctime);
    
        /* for block and character devices, add rdev,
           major and minor numbers */
        if (S_ISBLK(sbuf->st_mode) || S_ISCHR(sbuf->st_mode)) {
            array_set_numeric(array, "rdev", sbuf->st_rdev);
            array_set_numeric(array, "major", major(sbuf->st_rdev));
            array_set_numeric(array, "minor", minor(sbuf->st_rdev));
        }
    

The latter part of the function makes selective additions
to the destination array, depending upon the availability of
certain members and/or the type of the file. It then returns zero,
for success:

    #ifdef HAVE_STRUCT_STAT_ST_BLKSIZE
        array_set_numeric(array, "blksize", sbuf->st_blksize);
    #endif
    

    
    
        pmode = format_mode(sbuf->st_mode);
        array_set(array, "pmode", make_const_string(pmode, strlen(pmode),
                                                    & tmp));
    
        /* for symbolic links, add a linkval field */
        if (S_ISLNK(sbuf->st_mode)) {
            char *buf;
            ssize_t linksize;
    
            if ((buf = read_symlink(name, sbuf->st_size,
                        & linksize)) != NULL)
                array_set(array, "linkval",
                          make_malloced_string(buf, linksize, & tmp));
            else
                warning(ext_id, _("stat: unable to read symbolic link `%s'"),
                        name);
        }
    
        /* add a type field */
        type = "unknown";   /* shouldn't happen */
        for (j = 0, k = sizeof(ftype_map)/sizeof(ftype_map[0]); j < k; j++) {
            if ((sbuf->st_mode & S_IFMT) == ftype_map[j].mask) {
                type = ftype_map[j].type;
                break;
            }
        }
    
        array_set(array, "type", make_const_string(type, strlen(type), & tmp));
    
        return 0;
    }
    

The third argument to `stat()` was not discussed previously. This
argument is optional. If present, it causes `do_stat()` to use
the `stat()` system call instead of the `lstat()` system
call.  This is done by using a function pointer: `statfunc`.
`statfunc` is initialized to point to `lstat()` (instead
of `stat()`) to get the file information, in case the file is a
symbolic link. However, if the third argument is included, `statfunc`
is set to point to `stat()`, instead.

Here is the `do_stat()` function, which starts with
variable declarations and argument checking:

    /* do_stat --- provide a stat() function for gawk */
    
    static awk_value_t *
    do_stat(int nargs, awk_value_t *result, struct awk_ext_func *unused)
    {
        awk_value_t file_param, array_param;
        char *name;
        awk_array_t array;
        int ret;
        struct stat sbuf;
        /* default is lstat() */
        int (*statfunc)(const char *path, struct stat *sbuf) = lstat;
    
        assert(result != NULL);
    

Then comes the actual work. First, the function gets the arguments.
Next, it gets the information for the file.  If the called function
(`lstat()` or `stat()`) returns an error, the code sets
`ERRNO` and returns:

        /* file is first arg, array to hold results is second */
        if (   ! get_argument(0, AWK_STRING, & file_param)
            || ! get_argument(1, AWK_ARRAY, & array_param)) {
            warning(ext_id, _("stat: bad parameters"));
            return make_number(-1, result);
        }
    
        if (nargs == 3) {
            statfunc = stat;
        }
    
        name = file_param.str_value.str;
        array = array_param.array_cookie;
    
        /* always empty out the array */
        clear_array(array);
    
        /* stat the file; if error, set ERRNO and return */
        ret = statfunc(name, & sbuf);
    

        if (ret < 0) {
            update_ERRNO_int(errno);
            return make_number(ret, result);
        }
    

The tedious work is done by `fill_stat_array()`, shown
earlier.  When done, the function returns the result from `fill_stat_array()`:

        ret = fill_stat_array(name, array, & sbuf);
    
        return make_number(ret, result);
    }
    

Finally, it&rsquo;s necessary to provide the &ldquo;glue&rdquo; that loads the
new function(s) into `gawk`.

The `filefuncs` extension also provides an `fts()`
function, which we omit here
(see [Extension Sample File Functions](#Extension-Sample-File-Functions)).
For its sake, there is an initialization
function:

    /* init_filefuncs --- initialization routine */
    
    static awk_bool_t
    init_filefuncs(void)
    {
        &hellip;
    }
    

We are almost done. We need an array of `awk_ext_func_t`
structures for loading each function into `gawk`:

    static awk_ext_func_t func_table[] = {
        { "chdir", do_chdir, 1, 1, awk_false, NULL },
        { "stat",  do_stat, 3, 2, awk_false, NULL },
        &hellip;
    };
    

Each extension must have a routine named `dl_load()` to load
everything that needs to be loaded.  It is simplest to use the
`dl_load_func()` macro in `gawkapi.h`:

    /* define the dl_load() function using the boilerplate macro */
    
    dl_load_func(func_table, filefuncs, "")
    

And that&rsquo;s it!

---

Previous: [Internal File Ops](#Internal-File-Ops), Up: [Extension Example](#Extension-Example)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.6.3 Integrating the Extensions

Now that the code is written, it must be possible to add it at
runtime to the running `gawk` interpreter.  First, the
code must be compiled.  Assuming that the functions are in
a file named filefuncs.c, and idir is the location
of the gawkapi.h header file,
the following steps[110](#FOOT110) create a GNU/Linux shared library:

    $ gcc -fPIC -shared -DHAVE_CONFIG_H -c -O -g -Iidir filefuncs.c
    $ gcc -o filefuncs.so -shared filefuncs.o

Once the library exists, it is loaded by using the `@load` keyword:

    # file testff.awk
    @load "filefuncs"
    
    BEGIN {
        "pwd" | getline curdir  # save current directory
        close("pwd")
    
        chdir("/tmp")
        system("pwd")   # test it
        chdir(curdir)   # go back
    
        print "Info for testff.awk"
        ret = stat("testff.awk", data)
        print "ret =", ret
        for (i in data)
            printf "data[\"%s\"] = %s\n", i, data[i]
        print "testff.awk modified:",
            strftime("%m %d %Y %H:%M:%S", data["mtime"])
    
        print "\nInfo for JUNK"
        ret = stat("JUNK", data)
        print "ret =", ret
        for (i in data)
            printf "data[\"%s\"] = %s\n", i, data[i]
        print "JUNK modified:", strftime("%m %d %Y %H:%M:%S", data["mtime"])
    }
    

The `AWKLIBPATH` environment variable tells
`gawk` where to find extensions (see [Finding Extensions](#Finding-Extensions)).
We set it to the current directory and run the program:

    $ AWKLIBPATH=$PWD gawk -f testff.awk
    -| /tmp
    -| Info for testff.awk
    -| ret = 0
    -| data["blksize"] = 4096
    -| data["devbsize"] = 512
    -| data["mtime"] = 1412004710
    -| data["mode"] = 33204
    -| data["type"] = file
    -| data["dev"] = 2053
    -| data["gid"] = 1000
    -| data["ino"] = 10358899
    -| data["ctime"] = 1412004710
    -| data["blocks"] = 8
    -| data["nlink"] = 1
    -| data["name"] = testff.awk
    -| data["atime"] = 1412004716
    -| data["pmode"] = -rw-rw-r--
    -| data["size"] = 666
    -| data["uid"] = 1000
    -| testff.awk modified: 09 29 2014 18:31:50
    -|
    -| Info for JUNK
    -| ret = -1
    -| JUNK modified: 01 01 1970 02:00:00
    

---

Next: [gawkextlib](#gawkextlib), Previous: [Extension Example](#Extension-Example), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.7 The Sample Extensions in the `gawk` Distribution

This section provides a brief overview of the sample extensions
that come in the `gawk` distribution. Some of them are intended
for production use (e.g., the `filefuncs`, `readdir`, and
`inplace` extensions).  Others mainly provide example code that
shows how to use the extension API.

&bull; [Extension Sample File Functions](#Extension-Sample-File-Functions):  The file functions sample.
&bull; [Extension Sample Fnmatch](#Extension-Sample-Fnmatch):  An interface to `fnmatch()`.
&bull; [Extension Sample Fork](#Extension-Sample-Fork):  An interface to `fork()` and other
                                      process functions.
&bull; [Extension Sample Inplace](#Extension-Sample-Inplace):  Enabling in-place file editing.
&bull; [Extension Sample Ord](#Extension-Sample-Ord):  Character to value to character
                                      conversions.
&bull; [Extension Sample Readdir](#Extension-Sample-Readdir):  An interface to `readdir()`.
&bull; [Extension Sample Revout](#Extension-Sample-Revout):  Reversing output sample output wrapper.
&bull; [Extension Sample Rev2way](#Extension-Sample-Rev2way):  Reversing data sample two-way processor.
&bull; [Extension Sample Read write array](#Extension-Sample-Read-write-array):  Serializing an array to a file.
&bull; [Extension Sample Readfile](#Extension-Sample-Readfile):  Reading an entire file into a string.
&bull; [Extension Sample Time](#Extension-Sample-Time):  An interface to `gettimeofday()`
                                      and `sleep()`.
&bull; [Extension Sample API Tests](#Extension-Sample-API-Tests):  Tests for the API.

---

Next: [Extension Sample Fnmatch](#Extension-Sample-Fnmatch), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.1 File-Related Functions

The `filefuncs` extension provides three different functions, as follows.
The usage is:

`@load "filefuncs"`
This is how you load the extension.

`result = chdir("/some/directory")`
The `chdir()` function is a direct hook to the `chdir()`
system call to change the current directory.  It returns zero
upon success or a value less than zero upon error.
In the latter case, it updates `ERRNO`.

`result = stat("/some/path", statdata` [`, follow`]`)`
The `stat()` function provides a hook into the
`stat()` system call.
It returns zero upon success or a value less than zero upon error.
In the latter case, it updates `ERRNO`.

By default, it uses the `lstat()` system call.  However, if passed
a third argument, it uses `stat()` instead.

In all cases, it clears the `statdata` array.
When the call is successful, `stat()` fills the `statdata`
array with information retrieved from the filesystem, as follows:

SubscriptField in `struct stat`File type`"name"`The file nameAll`"dev"``st_dev`All`"ino"``st_ino`All`"mode"``st_mode`All`"nlink"``st_nlink`All`"uid"``st_uid`All`"gid"``st_gid`All`"size"``st_size`All`"atime"``st_atime`All`"mtime"``st_mtime`All`"ctime"``st_ctime`All`"rdev"``st_rdev`Device files`"major"``st_major`Device files`"minor"``st_minor`Device files`"blksize"``st_blksize`All`"pmode"`A human-readable version of the mode value, like that printed by
`ls` (for example, `"-rwxr-xr-x"`)All`"linkval"`The value of the symbolic linkSymbolic links`"type"`The type of the file as a string&mdash;one of
`"file"`,
`"blockdev"`,
`"chardev"`,
`"directory"`,
`"socket"`,
`"fifo"`,
`"symlink"`,
`"door"`,
or
`"unknown"`
(not all systems support all file types)All`flags = or(FTS_PHYSICAL, ...)``result = fts(pathlist, flags, filedata)`
Walk the file trees provided in `pathlist` and fill in the
`filedata` array, as described next.  `flags` is the bitwise
OR of several predefined values, also described in a moment.
Return zero if there were no errors, otherwise return -1.

The `fts()` function provides a hook to the C library `fts()`
routines for traversing file hierarchies.  Instead of returning data
about one file at a time in a stream, it fills in a multidimensional
array with data about each file and directory encountered in the requested
hierarchies.

The arguments are as follows:

`pathlist`
An array of file names.  The element values are used; the index values are ignored.

`flags`
This should be the bitwise OR of one or more of the following
predefined constant flag values.  At least one of
`FTS_LOGICAL` or `FTS_PHYSICAL` must be provided; otherwise
`fts()` returns an error value and sets `ERRNO`.
The flags are:

`FTS_LOGICAL`
Do a &ldquo;logical&rdquo; file traversal, where the information returned for
a symbolic link refers to the linked-to file, and not to the symbolic
link itself.  This flag is mutually exclusive with `FTS_PHYSICAL`.

`FTS_PHYSICAL`
Do a &ldquo;physical&rdquo; file traversal, where the information returned for a
symbolic link refers to the symbolic link itself.  This flag is mutually
exclusive with `FTS_LOGICAL`.

`FTS_NOCHDIR`
As a performance optimization, the C library `fts()` routines
change directory as they traverse a file hierarchy.  This flag disables
that optimization.

`FTS_COMFOLLOW`
Immediately follow a symbolic link named in `pathlist`,
whether or not `FTS_LOGICAL` is set.

`FTS_SEEDOT`
By default, the C library `fts()` routines do not return entries for
. (dot) and .. (dot-dot).  This option causes entries for
dot-dot to also be included.  (The extension always includes an entry
for dot; more on this in a moment.)

`FTS_XDEV`
During a traversal, do not cross onto a different mounted filesystem.

`filedata`
The `filedata` array holds the results.
`fts()` first clears it.  Then it creates
an element in `filedata` for every element in `pathlist`.
The index is the name of the directory or file given in `pathlist`.
The element for this index is itself an array.  There are two cases:

*The path is a file*
In this case, the array contains two or three elements:

`"path"`
The full path to this file, starting from the &ldquo;root&rdquo; that was given
in the `pathlist` array.

`"stat"`
This element is itself an array, containing the same information as provided
by the `stat()` function described earlier for its
`statdata` argument.  The element may not be present if
the `stat()` system call for the file failed.

`"error"`
If some kind of error was encountered, the array will also
contain an element named `"error"`, which is a string describing the error.

*The path is a directory*
In this case, the array contains one element for each entry in the
directory.  If an entry is a file, that element is the same as for files, just
described.  If the entry is a directory, that element is (recursively)
an array describing the subdirectory.  If `FTS_SEEDOT` was provided
in the flags, then there will also be an element named `".."`.  This
element will be an array containing the data as provided by `stat()`.

In addition, there will be an element whose index is `"."`.
This element is an array containing the same two or three elements as
for a file: `"path"`, `"stat"`, and `"error"`.

The `fts()` function returns zero if there were no errors.
Otherwise, it returns -1.

> NOTE: The `fts()` extension does not exactly mimic the
> interface of the C library `fts()` routines, choosing instead to
> provide an interface that is based on associative arrays, which is
> more comfortable to use from an `awk` program.  This includes the
> lack of a comparison function, because `gawk` already provides
> powerful array sorting facilities.  Although an `fts_read()`-like
> interface could have been provided, this felt less natural than simply
> creating a multidimensional array to represent the file hierarchy and
> its information.

See test/fts.awk in the `gawk` distribution for an example
use of the `fts()` extension function.

---

Next: [Extension Sample Fork](#Extension-Sample-Fork), Previous: [Extension Sample File Functions](#Extension-Sample-File-Functions), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.2 Interface to `fnmatch()`

This extension provides an interface to the C library
`fnmatch()` function.  The usage is:

`@load "fnmatch"`
This is how you load the extension.

`result = fnmatch(pattern, string, flags)`
The return value is zero on success, `FNM_NOMATCH`
if the string did not match the pattern, or
a different nonzero value if an error occurred.

In addition to the `fnmatch()` function, the `fnmatch` extension
adds one constant (`FNM_NOMATCH`), and an array of flag values
named `FNM`.

The arguments to `fnmatch()` are:

`pattern`
The file name wildcard to match

`string`
The file name string

`flag`
Either zero, or the bitwise OR of one or more of the
flags in the `FNM` array

The flags are as follows:

Array elementCorresponding flag defined by `fnmatch()``FNM["CASEFOLD"]``FNM_CASEFOLD``FNM["FILE_NAME"]``FNM_FILE_NAME``FNM["LEADING_DIR"]``FNM_LEADING_DIR``FNM["NOESCAPE"]``FNM_NOESCAPE``FNM["PATHNAME"]``FNM_PATHNAME``FNM["PERIOD"]``FNM_PERIOD`
Here is an example:

    @load "fnmatch"
    &hellip;
    flags = or(FNM["PERIOD"], FNM["NOESCAPE"])
    if (fnmatch("*.a", "foo.c", flags) == FNM_NOMATCH)
        print "no match"
    

---

Next: [Extension Sample Inplace](#Extension-Sample-Inplace), Previous: [Extension Sample Fnmatch](#Extension-Sample-Fnmatch), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.3 Interface to `fork()`, `wait()`, and `waitpid()`

The `fork` extension adds three functions, as follows:

`@load "fork"`
This is how you load the extension.

`pid = fork()`
This function creates a new process. The return value is zero in the
child and the process ID number of the child in the parent, or -1
upon error. In the latter case, `ERRNO` indicates the problem.
In the child, `PROCINFO["pid"]` and `PROCINFO["ppid"]` are
updated to reflect the correct values.

`ret = waitpid(pid)`
This function takes a numeric argument, which is the process ID to
wait for. The return value is that of the
`waitpid()` system call.

`ret = wait()`
This function waits for the first child to die.
The return value is that of the
`wait()` system call.

There is no corresponding `exec()` function.

Here is an example:

    @load "fork"
    &hellip;
    if ((pid = fork()) == 0)
        print "hello from the child"
    else
        print "hello from the parent"
    

---

Next: [Extension Sample Ord](#Extension-Sample-Ord), Previous: [Extension Sample Fork](#Extension-Sample-Fork), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.4 Enabling In-Place File Editing

The `inplace` extension emulates GNU `sed`&rsquo;s -i option,
which performs &ldquo;in-place&rdquo; editing of each input file.
It uses the bundled inplace.awk include file to invoke the extension
properly:

    # inplace --- load and invoke the inplace extension.
    
    @load "inplace"
    
    # Please set INPLACE_SUFFIX to make a backup copy.  For example, you may
    # want to set INPLACE_SUFFIX to .bak on the command line or in a BEGIN rule.
    
    # By default, each filename on the command line will be edited inplace.
    # But you can selectively disable this by adding an inplace=0 argument
    # prior to files that you do not want to process this way.  You can then
    # reenable it later on the commandline by putting inplace=1 before files
    # that you wish to be subject to inplace editing.
    
    # N.B. We call inplace_end() in the BEGINFILE and END rules so that any
    # actions in an ENDFILE rule will be redirected as expected.
    

    BEGIN {
        inplace = 1         # enabled by default
    }
    

    BEGINFILE {
        if (_inplace_filename != "")
            inplace_end(_inplace_filename, INPLACE_SUFFIX)
        if (inplace)
            inplace_begin(_inplace_filename = FILENAME, INPLACE_SUFFIX)
        else
            _inplace_filename = ""
    }
    

    END {
        if (_inplace_filename != "")
            inplace_end(_inplace_filename, INPLACE_SUFFIX)
    }
    

For each regular file that is processed, the extension redirects
standard output to a temporary file configured to have the same owner
and permissions as the original.  After the file has been processed,
the extension restores standard output to its original destination.
If `INPLACE_SUFFIX` is not an empty string, the original file is
linked to a backup file name created by appending that suffix.  Finally,
the temporary file is renamed to the original file name.

Note that the use of this feature can be controlled by placing &lsquo;inplace=0&rsquo;
on the command-line prior to listing files that should not be processed this
way.  You can reenable inplace editing by adding an &lsquo;inplace=1&rsquo; argument
prior to files that should be subject to inplace editing.

The `_inplace_filename` variable serves to keep track of the
current filename so as to not invoke `inplace_end()` before
processing the first file.

If any error occurs, the extension issues a fatal error to terminate
processing immediately without damaging the original file.

Here are some simple examples:

    $ gawk -i inplace '{ gsub(/foo/, "bar") }; { print }' file1 file2 file3

To keep a backup copy of the original files, try this:

    $ gawk -i inplace -v INPLACE_SUFFIX=.bak '{ gsub(/foo/, "bar") }
    > { print }' file1 file2 file3

Please note that, while the extension does attempt to preserve ownership and permissions, it makes no attempt to copy the ACLs from the original file.

If the program dies prematurely, as might happen if an unhandled signal is received, a temporary file may be left behind.

---

Next: [Extension Sample Readdir](#Extension-Sample-Readdir), Previous: [Extension Sample Inplace](#Extension-Sample-Inplace), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.5 Character and Numeric values: `ord()` and `chr()`

The `ordchr` extension adds two functions, named
`ord()` and `chr()`, as follows:

`@load "ordchr"`
This is how you load the extension.

`number = ord(string)`
Return the numeric value of the first character in `string`.

`char = chr(number)`
Return a string whose first character is that represented by `number`.

These functions are inspired by the Pascal language functions
of the same name.  Here is an example:

    @load "ordchr"
    &hellip;
    printf("The numeric value of 'A' is %d\n", ord("A"))
    printf("The string value of 65 is %s\n", chr(65))
    

---

Next: [Extension Sample Revout](#Extension-Sample-Revout), Previous: [Extension Sample Ord](#Extension-Sample-Ord), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.6 Reading Directories

The `readdir` extension adds an input parser for directories.
The usage is as follows:

    @load "readdir"
    

When this extension is in use, instead of skipping directories named
on the command line (or with `getline`),
they are read, with each entry returned as a record.

The record consists of three fields. The first two are the inode number and the
file name, separated by a forward slash character.
On systems where the directory entry contains the file type, the record
has a third field (also separated by a slash), which is a single letter
indicating the type of the file. The letters and their corresponding file
types are shown in [Table 16.3](#table_002dreaddir_002dfile_002dtypes).

LetterFile type`b`Block device`c`Character device`d`Directory`f`Regular file`l`Symbolic link`p`Named pipe (FIFO)`s`Socket`u`Anything else (unknown)

**Table 16.3: **File types returned by the `readdir` extension

On systems without the file type information, the third field is always
&lsquo;u&rsquo;.

> NOTE: On GNU/Linux systems, there are filesystems that don&rsquo;t support the
> `d_type` entry (see the readdir(3) manual page), and so the file
> type is always &lsquo;u&rsquo;.  You can use the `filefuncs` extension to call
> `stat()` in order to get correct type information.

Here is an example:

    @load "readdir"
    &hellip;
    BEGIN { FS = "/" }
    { print "file name is", $2 }
    

---

Next: [Extension Sample Rev2way](#Extension-Sample-Rev2way), Previous: [Extension Sample Readdir](#Extension-Sample-Readdir), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.7 Reversing Output

The `revoutput` extension adds a simple output wrapper that reverses
the characters in each output line.  Its main purpose is to show how to
write an output wrapper, although it may be mildly amusing for the unwary.
Here is an example:

    @load "revoutput"
    
    BEGIN {
        REVOUT = 1
        print "don't panic" > "/dev/stdout"
    }
    

The output from this program is &lsquo;cinap t'nod&rsquo;.

---

Next: [Extension Sample Read write array](#Extension-Sample-Read-write-array), Previous: [Extension Sample Revout](#Extension-Sample-Revout), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.8 Two-Way I/O Example

The `revtwoway` extension adds a simple two-way processor that
reverses the characters in each line sent to it for reading back by
the `awk` program.  Its main purpose is to show how to write
a two-way processor, although it may also be mildly amusing.
The following example shows how to use it:

    @load "revtwoway"
    
    BEGIN {
        cmd = "/magic/mirror"
        print "don't panic" |& cmd
        cmd |& getline result
        print result
        close(cmd)
    }
    

The output from this program
also is:
&lsquo;cinap t'nod&rsquo;.

---

Next: [Extension Sample Readfile](#Extension-Sample-Readfile), Previous: [Extension Sample Rev2way](#Extension-Sample-Rev2way), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.9 Dumping and Restoring an Array

The `rwarray` extension adds two functions,
named `writea()` and `reada()`, as follows:

`@load "rwarray"`
This is how you load the extension.

`ret = writea(file, array)`
This function takes a string argument, which is the name of the file
to which to dump the array, and the array itself as the second argument.
`writea()` understands arrays of arrays.  It returns one on
success, or zero upon failure.

`ret = reada(file, array)`
`reada()` is the inverse of `writea()`;
it reads the file named as its first argument, filling in
the array named as the second argument. It clears the array first.
Here too, the return value is one on success, or zero upon failure.

The array created by `reada()` is identical to that written by
`writea()` in the sense that the contents are the same. However,
due to implementation issues, the array traversal order of the re-created
array is likely to be different from that of the original array.  As array
traversal order in `awk` is by default undefined, this is (technically)
not a problem.  If you need to guarantee a particular traversal
order, use the array sorting features in `gawk` to do so
(see [Array Sorting](#Array-Sorting)).

The file contains binary data.  All integral values are written in network
byte order.  However, double-precision floating-point values are written
as native binary data.  Thus, arrays containing only string data can
theoretically be dumped on systems with one byte order and restored on
systems with a different one, but this has not been tried.

Here is an example:

    @load "rwarray"
    &hellip;
    ret = writea("arraydump.bin", array)
    &hellip;
    ret = reada("arraydump.bin", array)
    

---

Next: [Extension Sample Time](#Extension-Sample-Time), Previous: [Extension Sample Read write array](#Extension-Sample-Read-write-array), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.10 Reading an Entire File

The `readfile` extension adds a single function
named `readfile()`, and an input parser:

`@load "readfile"`
This is how you load the extension.

`result = readfile("/some/path")`
The argument is the name of the file to read.  The return value is a
string containing the entire contents of the requested file.  Upon error,
the function returns the empty string and sets `ERRNO`.

`BEGIN { PROCINFO["readfile"] = 1 }`
In addition, the extension adds an input parser that is activated if
`PROCINFO["readfile"]` exists.
When activated, each input file is returned in its entirety as `$0`.
`RT` is set to the null string.

Here is an example:

    @load "readfile"
    &hellip;
    contents = readfile("/path/to/file");
    if (contents == "" && ERRNO != "") {
        print("problem reading file", ERRNO) > "/dev/stderr"
        ...
    }
    

---

Next: [Extension Sample API Tests](#Extension-Sample-API-Tests), Previous: [Extension Sample Readfile](#Extension-Sample-Readfile), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.11 Extension Time Functions

The `time` extension adds two functions, named `gettimeofday()`
and `sleep()`, as follows:

`@load "time"`
This is how you load the extension.

`the_time = gettimeofday()`
Return the time in seconds that has elapsed since 1970-01-01 UTC as a
floating-point value.  If the time is unavailable on this platform, return
-1 and set `ERRNO`.  The returned time should have sub-second
precision, but the actual precision may vary based on the platform.
If the standard C `gettimeofday()` system call is available on this
platform, then it simply returns the value.  Otherwise, if on MS-Windows,
it tries to use `GetSystemTimeAsFileTime()`.

`result = sleep(seconds)`
Attempt to sleep for seconds seconds.  If seconds is negative,
or the attempt to sleep fails, return -1 and set `ERRNO`.
Otherwise, return zero after sleeping for the indicated amount of time.
Note that seconds may be a floating-point (nonintegral) value.
Implementation details: depending on platform availability, this function
tries to use `nanosleep()` or `select()` to implement the delay.

---

Previous: [Extension Sample Time](#Extension-Sample-Time), Up: [Extension Samples](#Extension-Samples)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### 16.7.12 API Tests

The `testext` extension exercises parts of the extension API that
are not tested by the other samples.  The extension/testext.c
file contains both the C code for the extension and `awk`
test code inside C comments that run the tests. The testing framework
extracts the `awk` code and runs the tests.  See the source file
for more information.

---

Next: [Extension summary](#Extension-summary), Previous: [Extension Samples](#Extension-Samples), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.8 The `gawkextlib` Project

The [`gawkextlib`](https://sourceforge.net/projects/gawkextlib/)
project provides a number of `gawk` extensions, including one for
processing XML files.  This is the evolution of the original `xgawk`
(XML `gawk`) project.

There are a number of extensions. Some of the more interesting ones are:

- `abort` extension. It allows you to exit immediately from your
`awk` program without running the `END` rules.

- `json` extension.
This serializes a multidimensional array into a JSON string, and
can deserialize a JSON string into a `gawk` array.
This extension is interesting since it is written in C++ instead of C.

-  MPFR library extension.
This provides access to a number of MPFR functions that `gawk`&rsquo;s
native MPFR support does not.

-  Select extension. It provides functionality based on the
`select()` system call.

-  XML parser extension, using the [Expat](https://expat.sourceforge.net)
XML parsing library

You can check out the code for the `gawkextlib` project
using the [Git](https://git-scm.com) distributed source
code control system.  The command is as follows:

    git clone git://git.code.sf.net/p/gawkextlib/code gawkextlib-code
    

You will need to have the [RapidJson](http://www.rapidjson.org)
JSON parser library installed in order to build and use the `json` extension.

You will need to have the [Expat](https://expat.sourceforge.net)
XML parser library installed in order to build and use the XML extension.

In addition, you must have the GNU Autotools installed
([Autoconf](https://www.gnu.org/software/autoconf),
[Automake](https://www.gnu.org/software/automake),
[Libtool](https://www.gnu.org/software/libtool),
and
[GNU `gettext`](https://www.gnu.org/software/gettext)).

The simple recipe for building and testing `gawkextlib` is as follows.
First, build and install `gawk`:

    cd .../path/to/gawk/code
    ./configure --prefix=/tmp/newgawk     Install in /tmp/newgawk for now
    make && make check                    Build and check that all is OK
    make install                          Install gawk

Next, go to [https://sourceforge.net/projects/gawkextlib/files](https://sourceforge.net/projects/gawkextlib/files) to
download `gawkextlib` and any extensions that you would like to build.
The README file at that site explains how to build the code.  If you
installed `gawk` in a non-standard location, you will need to
specify `./configure --with-gawk=/path/to/gawk` to find it.
You may need to use the `sudo` utility
to install both `gawk` and `gawkextlib`, depending upon
how your system works.

If you write an extension that you wish to share with other
`gawk` users, consider doing so through the
`gawkextlib` project.
See the project&rsquo;s website for more information.

---

Next: [Extension Exercises](#Extension-Exercises), Previous: [gawkextlib](#gawkextlib), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.9 Summary

-  You can write extensions (sometimes called plug-ins) for `gawk`
in C or C++ using the application programming interface (API) defined
by the `gawk` developers.

-  Extensions must have a license compatible with the GNU General Public
License (GPL), and they must assert that fact by declaring a variable
named `plugin_is_GPL_compatible`.

-  Communication between `gawk` and an extension is two-way.
`gawk` passes a `struct` to the extension that contains
various data fields and function pointers.  The extension can then call
into `gawk` via the supplied function pointers to accomplish
certain tasks.

-  One of these tasks is to &ldquo;register&rdquo; the name and implementation of
new `awk`-level functions with `gawk`.  The implementation
takes the form of a C function pointer with a defined signature.
By convention, implementation functions are named `do_XXXX()`
for some `awk`-level function `XXXX()`.

-  The API is defined in a header file named gawkapi.h. You must include
a number of standard header files *before* including it in your source file.

-  API function pointers are provided for the following kinds of operations:

-  Allocating, reallocating, and releasing memory

-  Registration functions (you may register
extension functions,
exit callbacks,
a version string,
input parsers,
output wrappers,
and two-way processors)

-  Printing fatal, nonfatal, warning, and &ldquo;lint&rdquo; warning messages

-  Updating `ERRNO`, or unsetting it

-  Accessing parameters, including converting an undefined parameter into
an array

-  Symbol table access (retrieving a global variable, creating one,
or changing one)

-  Creating and releasing cached values; this provides an
efficient way to use values for multiple variables and
can be a big performance win

-  Manipulating arrays
(retrieving, adding, deleting, and modifying elements;
getting the count of elements in an array;
creating a new array;
clearing an array;
and
flattening an array for easy C-style looping over all its indices and elements)

-  The API defines a number of standard data types for representing
`awk` values, array elements, and arrays.

-  The API provides convenience functions for constructing values.
It also provides memory management functions to ensure compatibility
between memory allocated by `gawk` and memory allocated by an
extension.

- *All* memory passed from `gawk` to an extension must be
treated as read-only by the extension.

- *All* memory passed from an extension to `gawk` must come from
the API&rsquo;s memory allocation functions. `gawk` takes responsibility for
the memory and releases it when appropriate.

-  The API provides information about the running version of `gawk` so
that an extension can make sure it is compatible with the `gawk`
that loaded it.

-  It is easiest to start a new extension by copying the boilerplate code
described in this chapter.  Macros in the gawkapi.h header
file make this easier to do.

-  The `gawk` distribution includes a number of small but useful
sample extensions. The `gawkextlib` project includes several more
(larger) extensions.  If you wish to write an extension and contribute it
to the community of `gawk` users, the `gawkextlib` project
is the place to do so.

---

Previous: [Extension summary](#Extension-summary), Up: [Dynamic Extensions](#Dynamic-Extensions)   [[Contents](#SEC_Contents)][[Index](#Index)]

### 16.10 Exercises

1.  Add functions to implement system calls such as `chown()`,
`chmod()`, and `umask()` to the file operations extension
presented in [Internal File Ops](#Internal-File-Ops).

2.  Write an input parser that prints a prompt if the input is
a from a &ldquo;terminal&rdquo; device.  You can use the `isatty()`
function to tell if the input file is a terminal. (Hint: this function
is usually expensive to call; try to call it just once.)
The content of the prompt should come from a variable settable
by `awk`-level code.
You can write the prompt to standard error. However,
for best results, open a new file descriptor (or file pointer)
on /dev/tty and print the prompt there, in case standard
error has been redirected.

Why is standard error a better
choice than standard output for writing the prompt?
Which reading mechanism should you replace, the one to get
a record, or the one to read raw bytes?

3.  (Hard.)
How would you provide namespaces in `gawk`, so that the
names of functions in different extensions don&rsquo;t conflict with each other?
If you come up with a really good scheme, contact the `gawk`
maintainer to tell him about it.

4.  Write a wrapper script that provides an interface similar to
&lsquo;sed -i&rsquo; for the &ldquo;inplace&rdquo; extension presented in
[Extension Sample Inplace](#Extension-Sample-Inplace).

---

Next: [Installation](#Installation), Previous: [Dynamic Extensions](#Dynamic-Extensions), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

# Part IV:
Appendices

---

## Appendix A The Evolution of the `awk` Language

This Web page describes the GNU implementation of `awk`,
which follows the POSIX specification.  Many longtime `awk`
users learned `awk` programming with the original `awk`
implementation in Version 7 Unix.  (This implementation was the basis for
`awk` in Berkeley Unix, through 4.3-Reno.  Subsequent versions
of Berkeley Unix, and, for a while, some systems derived from 4.4BSD-Lite, used various
versions of `gawk` for their `awk`.)  This chapter
briefly describes the evolution of the `awk` language, with
cross-references to other parts of the Web page where you can
find more information.

&bull; [V7/SVR3.1](#V7_002fSVR3_002e1):  The major changes between V7 and System V
                                Release 3.1.
&bull; [SVR4](#SVR4):  Minor changes between System V Releases 3.1
                                and 4.
&bull; [POSIX](#POSIX):  New features from the POSIX standard.
&bull; [BTL](#BTL):  New features from Brian Kernighan&rsquo;s version of
                                `awk`.
&bull; [POSIX/GNU](#POSIX_002fGNU):  The extensions in `gawk` not in POSIX
                                `awk`.
&bull; [Feature History](#Feature-History):  The history of the features in `gawk`.
&bull; [Common Extensions](#Common-Extensions):  Common Extensions Summary.
&bull; [Ranges and Locales](#Ranges-and-Locales):  How locales used to affect regexp ranges.
&bull; [Contributors](#Contributors):  The major contributors to `gawk`.
&bull; [History summary](#History-summary):  History summary.

---

Next: [SVR4](#SVR4), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.1 Major Changes Between V7 and SVR3.1

The `awk` language evolved considerably between the release of
Version 7 Unix (1978) and the new version that was first made generally available in
System V Release 3.1 (1987).  This section summarizes the changes, with
cross-references to further details:

-  The requirement for &lsquo;;&rsquo; to separate rules on a line
(see [Statements/Lines](#Statements_002fLines))

-  User-defined functions and the `return` statement
(see [User-defined](#User_002ddefined))

-  The `delete` statement (see [Delete](#Delete))

-  The `do`-`while` statement
(see [Do Statement](#Do-Statement))

-  The built-in functions `atan2()`, `cos()`, `sin()`, `rand()`, and
`srand()` (see [Numeric Functions](#Numeric-Functions))

-  The built-in functions `gsub()`, `sub()`, and `match()`
(see [String Functions](#String-Functions))

-  The built-in functions `close()` and `system()`
(see [I/O Functions](#I_002fO-Functions))

-  The `ARGC`, `ARGV`, `FNR`, `RLENGTH`, `RSTART`,
and `SUBSEP` predefined variables (see [Built-in Variables](#Built_002din-Variables))

-  Assignable `$0` (see [Changing Fields](#Changing-Fields))

-  The conditional expression using the ternary operator &lsquo;?:&rsquo;
(see [Conditional Exp](#Conditional-Exp))

-  The expression &lsquo;indx in array&rsquo; outside of `for`
statements (see [Reference to Elements](#Reference-to-Elements))

-  The exponentiation operator &lsquo;^&rsquo;
(see [Arithmetic Ops](#Arithmetic-Ops)) and its assignment operator
form &lsquo;^=&rsquo; (see [Assignment Ops](#Assignment-Ops))

-  C-compatible operator precedence, which breaks some old `awk`
programs (see [Precedence](#Precedence))

-  Regexps as the value of `FS`
(see [Field Separators](#Field-Separators)) and as the
third argument to the `split()` function
(see [String Functions](#String-Functions)), rather than using only the first character
of `FS`
-  Dynamic regexps as operands of the &lsquo;~&rsquo; and &lsquo;!~&rsquo; operators
(see [Computed Regexps](#Computed-Regexps))

-  The escape sequences &lsquo;\b&rsquo;, &lsquo;\f&rsquo;, and &lsquo;\r&rsquo;
(see [Escape Sequences](#Escape-Sequences))

-  Redirection of input for the `getline` function
(see [Getline](#Getline))

-  Multiple `BEGIN` and `END` rules
(see [BEGIN/END](#BEGIN_002fEND))

-  Multidimensional arrays
(see [Multidimensional](#Multidimensional))

---

Next: [POSIX](#POSIX), Previous: [V7/SVR3.1](#V7_002fSVR3_002e1), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.2 Changes Between SVR3.1 and SVR4

The System V Release 4 (1989) version of Unix `awk` added these features
(some of which originated in `gawk`):

-  The `ENVIRON` array (see [Built-in Variables](#Built_002din-Variables))

-  Multiple -f options on the command line
(see [Options](#Options))

-  The -v option for assigning variables before program execution begins
(see [Options](#Options))

-  The -- signal for terminating command-line options

-  The &lsquo;\a&rsquo;, &lsquo;\v&rsquo;, and &lsquo;\x&rsquo; escape sequences
(see [Escape Sequences](#Escape-Sequences))

-  A defined return value for the `srand()` built-in function
(see [Numeric Functions](#Numeric-Functions))

-  The `toupper()` and `tolower()` built-in string functions
for case translation
(see [String Functions](#String-Functions))

-  A cleaner specification for the &lsquo;%c&rsquo; format-control letter in the
`printf` function
(see [Control Letters](#Control-Letters))

-  The ability to dynamically pass the field width and precision (`"%*.*d"`)
in the argument list of `printf` and `sprintf()`
(see [Control Letters](#Control-Letters))

-  The use of regexp constants, such as `/foo/`, as expressions, where
they are equivalent to using the matching operator, as in &lsquo;$0 ~ /foo/&rsquo;
(see [Using Constant Regexps](#Using-Constant-Regexps))

-  Processing of escape sequences inside command-line variable assignments
(see [Assignment Options](#Assignment-Options))

---

Next: [BTL](#BTL), Previous: [SVR4](#SVR4), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.3 Changes Between SVR4 and POSIX `awk`

The POSIX Command Language and Utilities standard for `awk` (1992)
introduced the following changes into the language:

-  The use of -W for implementation-specific options
(see [Options](#Options))

-  The use of `CONVFMT` for controlling the conversion of numbers
to strings (see [Conversion](#Conversion))

-  The concept of a numeric string and tighter comparison rules to go
with it (see [Typing and Comparison](#Typing-and-Comparison))

-  The use of predefined variables as function parameter names is forbidden
(see [Definition Syntax](#Definition-Syntax))

-  More complete documentation of many of the previously undocumented
features of the language

In 2012, a number of extensions that had been commonly available for
many years were finally added to POSIX. They are:

-  The `fflush()` built-in function for flushing buffered output
(see [I/O Functions](#I_002fO-Functions))

-  The `nextfile` statement
(see [Nextfile Statement](#Nextfile-Statement))

-  The ability to delete all of an array at once with &lsquo;delete array&rsquo;
(see [Delete](#Delete))

See [Common Extensions](#Common-Extensions) for a list of common extensions
not permitted by the POSIX standard.

The 2008 POSIX standard can be found online at
[http://pubs.opengroup.org/onlinepubs/9699919799/](http://pubs.opengroup.org/onlinepubs/9699919799/).

---

Next: [POSIX/GNU](#POSIX_002fGNU), Previous: [POSIX](#POSIX), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.4 Extensions in Brian Kernighan&rsquo;s `awk`

Brian Kernighan
has made his version available via his home page
(see [Other Versions](#Other-Versions)).

This section describes common extensions that
originally appeared in his version of `awk`:

-  The &lsquo;**&rsquo; and &lsquo;**=&rsquo; operators
(see [Arithmetic Ops](#Arithmetic-Ops)
and
[Assignment Ops](#Assignment-Ops))

-  The use of `func` as an abbreviation for `function`
(see [Definition Syntax](#Definition-Syntax))

-  The `fflush()` built-in function for flushing buffered output
(see [I/O Functions](#I_002fO-Functions))

See [Common Extensions](#Common-Extensions) for a full list of the extensions
available in his `awk`.

---

Next: [Feature History](#Feature-History), Previous: [BTL](#BTL), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.5 Extensions in `gawk` Not in POSIX `awk`

The GNU implementation, `gawk`, adds a large number of features.
They can all be disabled with either the --traditional or
--posix options
(see [Options](#Options)).

A number of features have come and gone over the years. This section
summarizes the additional features over POSIX `awk` that are
in the current version of `gawk`.

-  Additional predefined variables:

- - The
`ARGIND`,
`BINMODE`,
`ERRNO`,
`FIELDWIDTHS`,
`FPAT`,
`IGNORECASE`,
`LINT`,
`PROCINFO`,
`RT`,
and
`TEXTDOMAIN`
variables
(see [Built-in Variables](#Built_002din-Variables))

-  Special files in I/O redirections:

- - The /dev/stdin, /dev/stdout, /dev/stderr, and
/dev/fd/N special file names
(see [Special Files](#Special-Files))

- - The /inet, /inet4, and /inet6 special files for
TCP/IP networking using &lsquo;|&&rsquo; to specify which version of the
IP protocol to use
(see [TCP/IP Networking](#TCP_002fIP-Networking))

-  Changes and/or additions to the language:

- - The &lsquo;\x&rsquo; escape sequence
(see [Escape Sequences](#Escape-Sequences))

- - Full support for both POSIX and GNU regexps
(see [Regexp](#Regexp))

- - The ability for `FS` and for the third
argument to `split()` to be null strings
(see [Single Character Fields](#Single-Character-Fields))

- - The ability for `RS` to be a regexp
(see [Records](#Records))

- - The ability to use octal and hexadecimal constants in `awk`
program source code
(see [Nondecimal-numbers](#Nondecimal_002dnumbers))

- - The &lsquo;|&&rsquo; operator for two-way I/O to a coprocess
(see [Two-way I/O](#Two_002dway-I_002fO))

- - Indirect function calls
(see [Indirect Calls](#Indirect-Calls))

- - Directories on the command line produce a warning and are skipped
(see [Command-line directories](#Command_002dline-directories))

- - Output with `print` and `printf` need not be fatal
(see [Nonfatal](#Nonfatal))

-  New keywords:

- - The `BEGINFILE` and `ENDFILE` special patterns
(see [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE))

- - The `switch` statement
(see [Switch Statement](#Switch-Statement))

-  Changes to standard `awk` functions:

- - The optional second argument to `close()` that allows closing one end
of a two-way pipe to a coprocess
(see [Two-way I/O](#Two_002dway-I_002fO))

- - POSIX compliance for `gsub()` and `sub()` with --posix
- - The `length()` function accepts an array argument
and returns the number of elements in the array
(see [String Functions](#String-Functions))

- - The optional third argument to the `match()` function
for capturing text-matching subexpressions within a regexp
(see [String Functions](#String-Functions))

- - Positional specifiers in `printf` formats for
making translations easier
(see [Printf Ordering](#Printf-Ordering))

- - The `split()` function&rsquo;s additional optional fourth
argument, which is an array to hold the text of the field separators
(see [String Functions](#String-Functions))

-  Additional functions only in `gawk`:

- - The `gensub()`, `patsplit()`, and `strtonum()` functions
for more powerful text manipulation
(see [String Functions](#String-Functions))

- - The `asort()` and `asorti()` functions for sorting arrays
(see [Array Sorting](#Array-Sorting))

- - The `mktime()`, `systime()`, and `strftime()`
functions for working with timestamps
(see [Time Functions](#Time-Functions))

- - The
`and()`,
`compl()`,
`lshift()`,
`or()`,
`rshift()`,
and
`xor()`
functions for bit manipulation
(see [Bitwise Functions](#Bitwise-Functions))

- - The `isarray()` function to check if a variable is an array or not
(see [Type Functions](#Type-Functions))

- - The `bindtextdomain()`, `dcgettext()`, and `dcngettext()`
functions for internationalization
(see [Programmer i18n](#Programmer-i18n))

-  Changes and/or additions in the command-line options:

- - The `AWKPATH` environment variable for specifying a path search for
the -f command-line option
(see [Options](#Options))

- - The `AWKLIBPATH` environment variable for specifying a path search for
the -l command-line option
(see [Options](#Options))

- - The
-b,
-c,
-C,
-d,
-D,
-e,
-E,
-g,
-h,
-i,
-l,
-L,
-M,
-n,
-N,
-o,
-O,
-p,
-P,
-r,
-s,
-S,
-t,
and
-V
short options. Also, the
ability to use GNU-style long-named options that start with --,
and the
--assign,
--bignum,
--characters-as-bytes,
--copyright,
--debug,
--dump-variables,
--exec,
--field-separator,
--file,
--gen-pot,
--help,
--include,
--lint,
--lint-old,
--load,
--non-decimal-data,
--optimize,
--no-optimize,
--posix,
--pretty-print,
--profile,
--re-interval,
--sandbox,
--source,
--traditional,
--use-lc-numeric,
and
--version
long options
(see [Options](#Options)).

-  Support for the following obsolete systems was removed from the code
and the documentation for `gawk` version 4.0:

- - Amiga

- - Atari

- - BeOS

- - Cray

- - MIPS RiscOS

- - MS-DOS with the Microsoft Compiler

- - MS-Windows with the Microsoft Compiler

- - NeXT

- - SunOS 3.x, Sun 386 (Road Runner)

- - Tandem (non-POSIX)

- - Prestandard VAX C compiler for VAX/VMS

- - GCC for VAX and Alpha has not been tested for a while.

-  Support for the following obsolete system was removed from the code
for `gawk` version 4.1:

- - Ultrix

-  Support for the following systems was removed from the code
for `gawk` version 4.2:

- - MirBSD

- - GNU/Linux on Alpha

---

Next: [Common Extensions](#Common-Extensions), Previous: [POSIX/GNU](#POSIX_002fGNU), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.6 History of `gawk` Features

This section describes the features in `gawk`
over and above those in POSIX `awk`,
in the order they were added to `gawk`.

Version 2.10 of `gawk` introduced the following features:

-  The `AWKPATH` environment variable for specifying a path search for
the -f command-line option
(see [Options](#Options)).

-  The `IGNORECASE` variable and its effects
(see [Case-sensitivity](#Case_002dsensitivity)).

-  The /dev/stdin, /dev/stdout, /dev/stderr and
/dev/fd/N special file names
(see [Special Files](#Special-Files)).

Version 2.13 of `gawk` introduced the following features:

-  The `FIELDWIDTHS` variable and its effects
(see [Constant Size](#Constant-Size)).

-  The `systime()` and `strftime()` built-in functions for obtaining
and printing timestamps
(see [Time Functions](#Time-Functions)).

-  Additional command-line options
(see [Options](#Options)):

- - The -W lint option to provide error and portability checking
for both the source code and at runtime.

- - The -W compat option to turn off the GNU extensions.

- - The -W posix option for full POSIX compliance.

Version 2.14 of `gawk` introduced the following feature:

-  The `next file` statement for skipping to the next data file
(see [Nextfile Statement](#Nextfile-Statement)).

Version 2.15 of `gawk` introduced the following features:

-  New variables (see [Built-in Variables](#Built_002din-Variables)):

- - `ARGIND`, which tracks the movement of `FILENAME`
through `ARGV`.

- - `ERRNO`, which contains the system error message when
`getline` returns -1 or `close()` fails.

-  The /dev/pid, /dev/ppid, /dev/pgrpid, and
/dev/user special file names. These have since been removed.

-  The ability to delete all of an array at once with &lsquo;delete array&rsquo;
(see [Delete](#Delete)).

-  Command-line option changes
(see [Options](#Options)):

- - The ability to use GNU-style long-named options that start with --.

- - The --source option for mixing command-line and library-file
source code.

Version 3.0 of `gawk` introduced the following features:

-  New or changed variables:

- - `IGNORECASE` changed, now applying to string comparison as well
as regexp operations
(see [Case-sensitivity](#Case_002dsensitivity)).

- - `RT`, which contains the input text that matched `RS`
(see [Records](#Records)).

-  Full support for both POSIX and GNU regexps
(see [Regexp](#Regexp)).

-  The `gensub()` function for more powerful text manipulation
(see [String Functions](#String-Functions)).

-  The `strftime()` function acquired a default time format,
allowing it to be called with no arguments
(see [Time Functions](#Time-Functions)).

-  The ability for `FS` and for the third
argument to `split()` to be null strings
(see [Single Character Fields](#Single-Character-Fields)).

-  The ability for `RS` to be a regexp
(see [Records](#Records)).

-  The `next file` statement became `nextfile`
(see [Nextfile Statement](#Nextfile-Statement)).

-  The `fflush()` function from
BWK `awk`
(then at Bell Laboratories;
see [I/O Functions](#I_002fO-Functions)).

-  New command-line options:

- - The --lint-old option to
warn about constructs that are not available in
the original Version 7 Unix version of `awk`
(see [V7/SVR3.1](#V7_002fSVR3_002e1)).

- - The -m option from BWK `awk`.  (Brian was
still at Bell Laboratories at the time.)  This was later removed from
both his `awk` and from `gawk`.

- - The --re-interval option to provide interval expressions in regexps
(see [Regexp Operators](#Regexp-Operators)).

- - The --traditional option was added as a better name for
--compat (see [Options](#Options)).

-  The use of GNU Autoconf to control the configuration process
(see [Quick Installation](#Quick-Installation)).

-  Amiga support.
This has since been removed.

Version 3.1 of `gawk` introduced the following features:

-  New variables
(see [Built-in Variables](#Built_002din-Variables)):

- - `BINMODE`, for non-POSIX systems,
which allows binary I/O for input and/or output files
(see [PC Using](#PC-Using)).

- - `LINT`, which dynamically controls lint warnings.

- - `PROCINFO`, an array for providing process-related information.

- - `TEXTDOMAIN`, for setting an application&rsquo;s internationalization text domain
(see [Internationalization](#Internationalization)).

-  The ability to use octal and hexadecimal constants in `awk`
program source code
(see [Nondecimal-numbers](#Nondecimal_002dnumbers)).

-  The &lsquo;|&&rsquo; operator for two-way I/O to a coprocess
(see [Two-way I/O](#Two_002dway-I_002fO)).

-  The /inet special files for TCP/IP networking using &lsquo;|&&rsquo;
(see [TCP/IP Networking](#TCP_002fIP-Networking)).

-  The optional second argument to `close()` that allows closing one end
of a two-way pipe to a coprocess
(see [Two-way I/O](#Two_002dway-I_002fO)).

-  The optional third argument to the `match()` function
for capturing text-matching subexpressions within a regexp
(see [String Functions](#String-Functions)).

-  Positional specifiers in `printf` formats for
making translations easier
(see [Printf Ordering](#Printf-Ordering)).

-  A number of new built-in functions:

- - The `asort()` and `asorti()` functions for sorting arrays
(see [Array Sorting](#Array-Sorting)).

- - The `bindtextdomain()`, `dcgettext()` and `dcngettext()` functions
for internationalization
(see [Programmer i18n](#Programmer-i18n)).

- - The `extension()` function and the ability to add
new built-in functions dynamically
(see [Dynamic Extensions](#Dynamic-Extensions)).

- - The `mktime()` function for creating timestamps
(see [Time Functions](#Time-Functions)).

- - The `and()`, `or()`, `xor()`, `compl()`,
`lshift()`, `rshift()`, and `strtonum()` functions
(see [Bitwise Functions](#Bitwise-Functions)).

- 
The support for &lsquo;next file&rsquo; as two words was removed completely
(see [Nextfile Statement](#Nextfile-Statement)).

-  Additional command-line options
(see [Options](#Options)):

- - The --dump-variables option to print a list of all global variables.

- - The --exec option, for use in CGI scripts.

- - The --gen-po command-line option and the use of a leading
underscore to mark strings that should be translated
(see [String Extraction](#String-Extraction)).

- - The --non-decimal-data option to allow non-decimal
input data
(see [Nondecimal Data](#Nondecimal-Data)).

- - The --profile option and `pgawk`, the
profiling version of `gawk`, for producing execution
profiles of `awk` programs
(see [Profiling](#Profiling)).

- - The --use-lc-numeric option to force `gawk`
to use the locale&rsquo;s decimal point for parsing input data
(see [Conversion](#Conversion)).

-  The use of GNU Automake to help in standardizing the configuration process
(see [Quick Installation](#Quick-Installation)).

-  The use of GNU `gettext` for `gawk`&rsquo;s own message output
(see [Gawk I18N](#Gawk-I18N)).

-  BeOS support. This was later removed.

-  Tandem support. This was later removed.

-  The Atari port became officially unsupported and was
later removed entirely.

-  The source code changed to use ISO C standard-style function definitions.

-  POSIX compliance for `sub()` and `gsub()`
(see [Gory Details](#Gory-Details)).

-  The `length()` function was extended to accept an array argument
and return the number of elements in the array
(see [String Functions](#String-Functions)).

-  The `strftime()` function acquired a third argument to
enable printing times as UTC
(see [Time Functions](#Time-Functions)).

Version 4.0 of `gawk` introduced the following features:

-  Variable additions:

- - `FPAT`, which allows you to specify a regexp that matches
the fields, instead of matching the field separator
(see [Splitting By Content](#Splitting-By-Content)).

- - If `PROCINFO["sorted_in"]` exists, &lsquo;for(iggy in foo)&rsquo; loops sort the
indices before looping over them.  The value of this element
provides control over how the indices are sorted before the loop
traversal starts
(see [Controlling Scanning](#Controlling-Scanning)).

- - `PROCINFO["strftime"]`, which holds
the default format for `strftime()`
(see [Time Functions](#Time-Functions)).

-  The special files /dev/pid, /dev/ppid, /dev/pgrpid
and /dev/user were removed.

-  Support for IPv6 was added via the /inet6 special file.
/inet4 forces IPv4 and /inet chooses the system
default, which is probably IPv4
(see [TCP/IP Networking](#TCP_002fIP-Networking)).

-  The use of &lsquo;\s&rsquo; and &lsquo;\S&rsquo; escape sequences in regular expressions
(see [GNU Regexp Operators](#GNU-Regexp-Operators)).

-  Interval expressions became part of default regular expressions
(see [Regexp Operators](#Regexp-Operators)).

-  POSIX character classes work even with --traditional
(see [Regexp Operators](#Regexp-Operators)).

- `break` and `continue` became invalid outside a loop,
even with --traditional
(see [Break Statement](#Break-Statement), and also see
[Continue Statement](#Continue-Statement)).

- `fflush()`, `nextfile`, and &lsquo;delete array&rsquo;
are allowed if --posix or --traditional, since they
are all now part of POSIX.

-  An optional third argument to
`asort()` and `asorti()`, specifying how to sort
(see [String Functions](#String-Functions)).

-  The behavior of `fflush()` changed to match BWK `awk`
and for POSIX; now both &lsquo;fflush()&rsquo; and &lsquo;fflush("")&rsquo;
flush all open output redirections
(see [I/O Functions](#I_002fO-Functions)).

-  The `isarray()`
function which distinguishes if an item is an array
or not, to make it possible to traverse arrays of arrays
(see [Type Functions](#Type-Functions)).

-  The `patsplit()`
function which gives the same capability as `FPAT`, for splitting
(see [String Functions](#String-Functions)).

-  An optional fourth argument to the `split()` function,
which is an array to hold the values of the separators
(see [String Functions](#String-Functions)).

-  Arrays of arrays
(see [Arrays of Arrays](#Arrays-of-Arrays)).

-  The `BEGINFILE` and `ENDFILE` special patterns
(see [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)).

-  Indirect function calls
(see [Indirect Calls](#Indirect-Calls)).

- `switch` / `case` are enabled by default
(see [Switch Statement](#Switch-Statement)).

-  Command-line option changes
(see [Options](#Options)):

- - The -b and --characters-as-bytes options
which prevent `gawk` from treating input as a multibyte string.

- - The redundant --compat, --copyleft, and --usage
long options were removed.

- - The --gen-po option was finally renamed to the correct --gen-pot.

- - The --sandbox option which disables certain features.

- - All long options acquired corresponding short options, for use in &lsquo;#!&rsquo; scripts.

-  Directories named on the command line now produce a warning, not a fatal
error, unless --posix or --traditional are used
(see [Command-line directories](#Command_002dline-directories)).

-  The `gawk` internals were rewritten, bringing the `dgawk`
debugger and possibly improved performance
(see [Debugger](#Debugger)).

-  Per the GNU Coding Standards, dynamic extensions must now define
a global symbol indicating that they are GPL-compatible
(see [Plugin License](#Plugin-License)).

-  In POSIX mode, string comparisons use `strcoll()` / `wcscoll()`
(see [POSIX String Comparison](#POSIX-String-Comparison)).

-  The option for raw sockets was removed, since it was never implemented
(see [TCP/IP Networking](#TCP_002fIP-Networking)).

-  Ranges of the form &lsquo;[d-h]&rsquo; are treated as if they were in the
C locale, no matter what kind of regexp is being used, and even if
--posix
(see [Ranges and Locales](#Ranges-and-Locales)).

-  Support was removed for the following systems:

- - Atari

- - Amiga

- - BeOS

- - Cray

- - MIPS RiscOS

- - MS-DOS with the Microsoft Compiler

- - MS-Windows with the Microsoft Compiler

- - NeXT

- - SunOS 3.x, Sun 386 (Road Runner)

- - Tandem (non-POSIX)

- - Prestandard VAX C compiler for VAX/VMS

Version 4.1 of `gawk` introduced the following features:

-  Three new arrays:
`SYMTAB`, `FUNCTAB`, and `PROCINFO["identifiers"]`
(see [Auto-set](#Auto_002dset)).

-  The three executables `gawk`, `pgawk`, and `dgawk`, were merged into
one, named just `gawk`.  As a result the command-line options changed.

-  Command-line option changes
(see [Options](#Options)):

- - The -D option invokes the debugger.

- - The -i and --include options
load `awk` library files.

- - The -l and --load options load compiled dynamic extensions.

- - The -M and --bignum options enable MPFR.

- - The -o option only does pretty-printing.

- - The -p option is used for profiling.

- - The -R option was removed.

-  Support for high precision arithmetic with MPFR
(see [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)).

-  The `and()`, `or()` and `xor()` functions
changed to allow any number of arguments,
with a minimum of two
(see [Bitwise Functions](#Bitwise-Functions)).

-  The dynamic extension interface was completely redone
(see [Dynamic Extensions](#Dynamic-Extensions)).

-  Redirected `getline` became allowed inside
`BEGINFILE` and `ENDFILE`
(see [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)).

-  The `where` command was added to the debugger
(see [Execution Stack](#Execution-Stack)).

-  Support for Ultrix was removed.

Version 4.2 of `gawk` introduced the following changes:

-  Changes to `ENVIRON` are reflected into `gawk`&rsquo;s
environment and that of programs that it runs.
See [Auto-set](#Auto_002dset).

- `FIELDWIDTHS` was enhanced to allow skipping characters
before assigning a value to a field
(see [Splitting By Content](#Splitting-By-Content)).

-  The `PROCINFO["argv"]` array.
See [Auto-set](#Auto_002dset).

-  The maximum number of hexadecimal digits in &lsquo;\x&rsquo; escapes
is now two.
See [Escape Sequences](#Escape-Sequences).

-  Strongly typed regexp constants of the form &lsquo;@/&hellip;/&rsquo;
(see [Strong Regexp Constants](#Strong-Regexp-Constants)).

-  The bitwise functions changed, making negative arguments into
a fatal error (see [Bitwise Functions](#Bitwise-Functions)).

-  The `mktime()` function now accepts an optional
second argument
(see [Time Functions](#Time-Functions)).

-  The `typeof()` function (see [Type Functions](#Type-Functions)).

-  Optimizations are enabled by default. Use -s /
--no-optimize to disable optimizations.

-  For many years, POSIX specified that default field splitting
only allowed spaces and tabs to separate fields, and this was
how `gawk` behaved with --posix. As of 2013,
the standard restored historical behavior, and now default
field splitting with --posix also allows newlines to
separate fields.

-  Nonfatal output with `print` and `printf`.
See [Nonfatal](#Nonfatal).

-  Retryable I/O via `PROCINFO[input-file, "RETRY"]`;
(see [Retrying Input](#Retrying-Input)).

-  Changes to the pretty-printer (see [Profiling](#Profiling)):

- - The --pretty-print option no longer runs the `awk`
program too.

- - Comments in the source program are preserved and placed into the
output file.

- - Explicit parentheses for expressions
in the input are preserved in the generated output.

-  Improvements to the extension API
(see [Dynamic Extensions](#Dynamic-Extensions)):

- - The `get_file()` function to access open redirections.

- - The `nonfatal()` function for generating nonfatal error messages.

- - Support for GMP and MPFR values.

- - Input parsers can now override the default field parsing mechanism
by specifying explicit locations.

-  Shell startup files are supplied with the distribution and
installed by &lsquo;make install&rsquo; (see [Shell Startup Files](#Shell-Startup-Files)).

-  The `igawk` program and its manual page are no longer
installed when `gawk` is built.
See [Igawk Program](#Igawk-Program).

-  Support for MirBSD was removed.

-  Support for GNU/Linux on Alpha was removed.

---

Next: [Ranges and Locales](#Ranges-and-Locales), Previous: [Feature History](#Feature-History), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.7 Common Extensions Summary

The following table summarizes the common extensions supported
by `gawk`, Brian Kernighan&rsquo;s `awk`, and `mawk`,
the three most widely used freely available versions of `awk`
(see [Other Versions](#Other-Versions)).

FeatureBWK `awk``mawk``gawk`Now standard&lsquo;\x&rsquo; escape sequenceXXX`FS` as null stringXXX/dev/stdin special fileXXX/dev/stdout special fileXXX/dev/stderr special fileXXX`delete` without subscriptXXXX`fflush()` functionXXXX`length()` of an arrayXXX`nextfile` statementXXXX`**` and `**=` operatorsXX`func` keywordXX`BINMODE` variableXX`RS` as regexpXXTime-related functionsXX
---

Next: [Contributors](#Contributors), Previous: [Common Extensions](#Common-Extensions), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.8 Regexp Ranges and Locales: A Long Sad Story

This section describes the confusing history of ranges within
regular expressions and their interactions with locales, and how this
affected different versions of `gawk`.

The original Unix tools that worked with regular expressions defined
character ranges (such as &lsquo;[a-z]&rsquo;) to match any character between
the first character in the range and the last character in the range,
inclusive.  Ordering was based on the numeric value of each character
in the machine&rsquo;s native character set.  Thus, on ASCII-based systems,
&lsquo;[a-z]&rsquo; matched all the lowercase letters, and only the lowercase
letters, as the numeric values for the letters from &lsquo;a&rsquo; through
&lsquo;z&rsquo; were contiguous.  (On an EBCDIC system, the range &lsquo;[a-z]&rsquo;
includes additional nonalphabetic characters as well.)

Almost all introductory Unix literature explained range expressions
as working in this fashion, and in particular, would teach that the
&ldquo;correct&rdquo; way to match lowercase letters was with &lsquo;[a-z]&rsquo;, and
that &lsquo;[A-Z]&rsquo; was the &ldquo;correct&rdquo; way to match uppercase letters.
And indeed, this was true.[111](#FOOT111)

The 1992 POSIX standard introduced the idea of locales (see [Locales](#Locales)).
Because many locales include other letters besides the plain 26
letters of the English alphabet, the POSIX standard added
character classes (see [Bracket Expressions](#Bracket-Expressions)) as a way to match
different kinds of characters besides the traditional ones in the ASCII
character set.

However, the standard *changed* the interpretation of range expressions.
In the `"C"` and `"POSIX"` locales, a range expression like
&lsquo;[a-dx-z]&rsquo; is still equivalent to &lsquo;[abcdxyz]&rsquo;, as in ASCII.
But outside those locales, the ordering was defined to be based on
*collation order*.

What does that mean?
In many locales, &lsquo;A&rsquo; and &lsquo;a&rsquo; are both less than &lsquo;B&rsquo;.
In other words, these locales sort characters in dictionary order,
and &lsquo;[a-dx-z]&rsquo; is typically not equivalent to &lsquo;[abcdxyz]&rsquo;;
instead, it might be equivalent to &lsquo;[ABCXYabcdxyz]&rsquo;, for example.

This point needs to be emphasized: much literature teaches that you should
use &lsquo;[a-z]&rsquo; to match a lowercase character.  But on systems with
non-ASCII locales, this also matches all of the uppercase characters
except &lsquo;A&rsquo; or &lsquo;Z&rsquo;!  This was a continuous cause of confusion, even well
into the twenty-first century.

To demonstrate these issues, the following example uses the `sub()`
function, which does text replacement (see [String Functions](#String-Functions)).  Here,
the intent is to remove trailing uppercase characters:

    $ echo something1234abc | gawk-3.1.8 '{ sub("[A-Z]*$", ""); print }'
    -| something1234a
    

This output is unexpected, as the &lsquo;bc&rsquo; at the end of
&lsquo;something1234abc&rsquo; should not normally match &lsquo;[A-Z]*&rsquo;.
This result is due to the locale setting (and thus you may not see
it on your system).

Similar considerations apply to other ranges.  For example, &lsquo;["-/]&rsquo;
is perfectly valid in ASCII, but is not valid in many Unicode locales,
such as `en_US.UTF-8`.

Early versions of `gawk` used regexp matching code that was not
locale-aware, so ranges had their traditional interpretation.

When `gawk` switched to using locale-aware regexp matchers,
the problems began; especially as both GNU/Linux and commercial Unix
vendors started implementing non-ASCII locales, *and making them
the default*.  Perhaps the most frequently asked question became something
like, &ldquo;Why does &lsquo;[A-Z]&rsquo; match lowercase letters?!?&rdquo;

This situation existed for close to 10 years, if not more, and
the `gawk` maintainer grew weary of trying to explain that
`gawk` was being nicely standards-compliant, and that the issue
was in the user&rsquo;s locale.  During the development of version 4.0,
he modified `gawk` to always treat ranges in the original,
pre-POSIX fashion, unless --posix was used (see [Options](#Options)).[112](#FOOT112)

Fortunately, shortly before the final release of `gawk` 4.0,
the maintainer learned that the 2008 standard had changed the
definition of ranges, such that outside the `"C"` and `"POSIX"`
locales, the meaning of range expressions was *undefined*.[113](#FOOT113)

By using this lovely technical term, the standard gives license
to implementers to implement ranges in whatever way they choose.
The `gawk` maintainer chose to apply the pre-POSIX meaning
both with the default regexp matching and when --traditional or
--posix are used.
In all cases `gawk` remains POSIX-compliant.

---

Next: [History summary](#History-summary), Previous: [Ranges and Locales](#Ranges-and-Locales), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.9 Major Contributors to `gawk`

> Always give credit where credit is due.

&mdash; *Anonymous*

This section names the major contributors to `gawk`
and/or this Web page, in approximate chronological order:

- 
Dr. Alfred V. Aho,
Dr. Peter J. Weinberger, and
Dr. Brian W. Kernighan, all of Bell Laboratories,
designed and implemented Unix `awk`,
from which `gawk` gets the majority of its feature set.

- 
Paul Rubin
did the initial design and implementation in 1986, and wrote
the first draft (around 40 pages) of this Web page.

- 
Jay Fenlason
finished the initial implementation.

- 
Diane Close
revised the first draft of this Web page, bringing it
to around 90 pages.

- 
Richard Stallman
helped finish the implementation and the initial draft of this
Web page.
He is also the founder of the FSF and the GNU Project.

- 
John Woods
contributed parts of the code (mostly fixes) in
the initial version of `gawk`.

- 
In 1988,
David Trueman
took over primary maintenance of `gawk`,
making it compatible with &ldquo;new&rdquo; `awk`, and
greatly improving its performance.

- 
Conrad Kwok,
Scott Garfinkle,
and
Kent Williams
did the initial ports to MS-DOS with various versions of MSC.

- 
Pat Rankin
provided the VMS port and its documentation.

- 
Hal Peterson
provided help in porting `gawk` to Cray systems.
(This is no longer supported.)

- 
Kai Uwe Rommel
provided the initial port to OS/2 and its documentation.

- 
Michal Jaegermann
provided the port to Atari systems and its documentation.
(This port is no longer supported.)
He continues to provide portability checking,
and has done a lot of work to make sure `gawk`
works on non-32-bit systems.

- 
Fred Fish
provided the port to Amiga systems and its documentation.
(With Fred&rsquo;s sad passing, this is no longer supported.)

- 
Scott Deifik
formerly maintained the MS-DOS port using DJGPP.

- 
Eli Zaretskii
currently maintains the MS-Windows port using MinGW.

- 
Juan Grigera
provided a port to Windows32 systems.
(This is no longer supported.)

- 
For many years,
Dr. Darrel Hankerson
acted as coordinator for the various ports to different PC platforms
and created binary distributions for various PC operating systems.
He was also instrumental in keeping the documentation up to date for
the various PC platforms.

- 
Christos Zoulas
provided the `extension()`
built-in function for dynamically adding new functions.
(This was obsoleted at `gawk` 4.1.)

- 
J&uuml;rgen Kahrs
contributed the initial version of the TCP/IP networking
code and documentation, and motivated the inclusion of the &lsquo;|&&rsquo; operator.

- 
Stephen Davies
provided the initial port to Tandem systems and its documentation.
(However, this is no longer supported.)
He was also instrumental in the initial work to integrate the
byte-code internals into the `gawk` code base.
Additionally, he did most of the work enabling the pretty-printer
to preserve and output comments.

- 
Matthew Woehlke
provided improvements for Tandem&rsquo;s POSIX-compliant systems.

- 
Martin Brown
provided the port to BeOS and its documentation.
(This is no longer supported.)

- 
Arno Peters
did the initial work to convert `gawk` to use
GNU Automake and GNU `gettext`.

- 
Alan J. Broder
provided the initial version of the `asort()` function
as well as the code for the optional third argument to the
`match()` function.

- 
Andreas Buening
updated the `gawk` port for OS/2.

- 
Isamu Hasegawa,
of IBM in Japan, contributed support for multibyte characters.

- 
Michael Benzinger contributed the initial code for `switch` statements.

- 
Patrick T.J. McPhee contributed the code for dynamic loading in Windows32
environments.
(This is no longer supported.)

- 
Anders Wallin helped keep the VMS port going for several years.

- 
Assaf Gordon contributed the code to implement the
--sandbox option.

- 
John Haque made the following contributions:

- - The modifications to convert `gawk`
into a byte-code interpreter, including the debugger

- - The addition of true arrays of arrays

- - The additional modifications for support of arbitrary-precision arithmetic

- - The initial text of
[Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)
- - The work to merge the three versions of `gawk`
into one, for the 4.1 release

- - Improved array internals for arrays indexed by integers

- - The improved array sorting features were also driven by John, together
with Pat Rankin

- 
Panos Papadopoulos contributed the original text for [Include Files](#Include-Files).

- 
Efraim Yawitz contributed the original text for [Debugger](#Debugger).

- 
The development of the extension API first released with
`gawk` 4.1 was driven primarily by
Arnold Robbins and Andrew Schorr, with notable contributions from
the rest of the development team.

- 
John Malmberg contributed significant improvements to the
OpenVMS port and the related documentation.

- 
Antonio Giovanni Colombo rewrote a number of examples in the early
chapters that were severely dated, for which I am incredibly grateful.
He also provided and maintains the Italian translation.

- 
Marco Curreli, together with Antonio Colombo, translated this
Web page into Italian.  It is included in the `gawk`
distribution.

- 
Juan Manuel Guerrero took over maintenance of the DJGPP port.

- 
Arnold Robbins
has been working on `gawk` since 1988, at first
helping David Trueman, and as the primary maintainer since around 1994.

---

Previous: [Contributors](#Contributors), Up: [Language History](#Language-History)   [[Contents](#SEC_Contents)][[Index](#Index)]

### A.10 Summary

-  The `awk` language has evolved over time. The first release
was with V7 Unix, circa 1978.  In 1987, for System V Release 3.1,
major additions, including user-defined functions, were made to the language.
Additional changes were made for System V Release 4, in 1989.
Since then, further minor changes have happened under the auspices of the
POSIX standard.

-  Brian Kernighan&rsquo;s `awk` provides a small number of extensions
that are implemented in common with other versions of `awk`.

- `gawk` provides a large number of extensions over POSIX `awk`.
They can be disabled with either the --traditional or --posix
options.

-  The interaction of POSIX locales and regexp matching in `gawk` has been confusing over
the years. Today, `gawk` implements Rational Range Interpretation, where
ranges of the form &lsquo;[a-z]&rsquo; match *only* the characters numerically between
&lsquo;a&rsquo; through &lsquo;z&rsquo; in the machine&rsquo;s native character set.  Usually this is ASCII,
but it can be EBCDIC on IBM S/390 systems.

-  Many people have contributed to `gawk` development over the years.
We hope that the list provided in this chapter is complete and gives
the appropriate credit where credit is due.

---

Next: [Notes](#Notes), Previous: [Language History](#Language-History), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## Appendix B Installing `gawk`

This appendix provides instructions for installing `gawk` on the
various platforms that are supported by the developers.  The primary
developer supports GNU/Linux (and Unix), whereas the other ports are
contributed.
See [Bugs](#Bugs)
for the email addresses of the people who maintain
the respective ports.

&bull; [Gawk Distribution](#Gawk-Distribution):  What is in the `gawk` distribution.
&bull; [Unix Installation](#Unix-Installation):  Installing `gawk` under various
                                versions of Unix.
&bull; [Non-Unix Installation](#Non_002dUnix-Installation):  Installation on Other Operating Systems.
&bull; [Bugs](#Bugs):  Reporting Problems and Bugs.
&bull; [Other Versions](#Other-Versions):  Other freely available `awk`
                                implementations.
&bull; [Installation summary](#Installation-summary):  Summary of installation.

---

Next: [Unix Installation](#Unix-Installation), Up: [Installation](#Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

### B.1 The `gawk` Distribution

This section describes how to get the `gawk`
distribution, how to extract it, and then what is in the various files and
subdirectories.

&bull; [Getting](#Getting):  How to get the distribution.
&bull; [Extracting](#Extracting):  How to extract the distribution.
&bull; [Distribution contents](#Distribution-contents):  What is in the distribution.

---

Next: [Extracting](#Extracting), Up: [Gawk Distribution](#Gawk-Distribution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.1.1 Getting the `gawk` Distribution

There are two ways to get GNU software:

-  Copy it from someone else who already has it.

- 
Retrieve `gawk`
from the Internet host
`ftp.gnu.org`, in the directory /gnu/gawk.
Both anonymous `ftp` and `http` access are supported.
If you have the `wget` program, you can use a command like
the following:

    wget https://ftp.gnu.org/gnu/gawk/gawk-4.2.1.tar.gz
    

The GNU software archive is mirrored around the world.
The up-to-date list of mirror sites is available from
[the main FSF website](https://www.gnu.org/order/ftp.html).
Try to use one of the mirrors; they
will be less busy, and you can usually find one closer to your site.

You may also retrieve the `gawk` source code from the official
Git repository; for more information see [Accessing The Source](#Accessing-The-Source).

---

Next: [Distribution contents](#Distribution-contents), Previous: [Getting](#Getting), Up: [Gawk Distribution](#Gawk-Distribution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.1.2 Extracting the Distribution

`gawk` is distributed as several `tar` files compressed with
different compression programs: `gzip`, `bzip2`,
and `xz`. For simplicity, the rest of these instructions assume
you are using the one compressed with the GNU Gzip program (`gzip`).

Once you have the distribution (e.g.,
gawk-4.2.1.tar.gz),
use `gzip` to expand the
file and then use `tar` to extract it.  You can use the following
pipeline to produce the `gawk` distribution:

    gzip -d -c gawk-4.2.1.tar.gz | tar -xvpf -
    

On a system with GNU `tar`, you can let `tar`
do the decompression for you:

    tar -xvpzf gawk-4.2.1.tar.gz
    

Extracting the archive
creates a directory named gawk-4.2.1
in the current directory.

The distribution file name is of the form
gawk-V.R.P.tar.gz.
The V represents the major version of `gawk`,
the R represents the current release of version V, and
the P represents a *patch level*, meaning that minor bugs have
been fixed in the release.  The current patch level is 1,
but when retrieving distributions, you should get the version with the highest
version, release, and patch level.  (Note, however, that patch levels greater than
or equal to 70 denote &ldquo;beta&rdquo; or nonproduction software; you might not want
to retrieve such a version unless you don&rsquo;t mind experimenting.)
If you are not on a Unix or GNU/Linux system, you need to make other arrangements
for getting and extracting the `gawk` distribution.  You should consult
a local expert.

---

Previous: [Extracting](#Extracting), Up: [Gawk Distribution](#Gawk-Distribution)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.1.3 Contents of the `gawk` Distribution

The `gawk` distribution has a number of C source files,
documentation files,
subdirectories, and files related to the configuration process
(see [Unix Installation](#Unix-Installation)),
as well as several subdirectories related to different non-Unix
operating systems:

Various &lsquo;.c&rsquo;, &lsquo;.y&rsquo;, and &lsquo;.h&rsquo; files
These files contain the actual `gawk` source code.

support/*
C header and source files for routines that `gawk`
uses, but that are not part of its core functionality.
For example, argument parsing, regular expression matching,
and random number generating routines are all kept here.

ABOUT-NLS
A file containing information about GNU `gettext` and translations.

AUTHORS
A file with some information about the authorship of `gawk`.
It exists only to satisfy the pedants at the Free Software Foundation.

READMEREADME_d/README.*
Descriptive files: README for `gawk` under Unix and the
rest for the various hardware and software combinations.

INSTALL
A file providing an overview of the configuration and installation process.

ChangeLog
A detailed list of source code changes as bugs are fixed or improvements made.

ChangeLog.0
An older list of source code changes.

NEWS
A list of changes to `gawk` since the last release or patch.

NEWS.0
An older list of changes to `gawk`.

COPYING
The GNU General Public License.

POSIX.STD
A description of behaviors in the POSIX standard for `awk` that
are left undefined, or where `gawk` may not comply fully, as well
as a list of things that the POSIX standard should describe but does not.

doc/awkforai.txt
Pointers to the original draft of
a short article describing why `gawk` is a good language for
artificial intelligence (AI) programming.

doc/bc_notes
A brief description of `gawk`&rsquo;s &ldquo;byte code&rdquo; internals.

doc/README.carddoc/ad.blockdoc/awkcard.indoc/cardfontsdoc/colorsdoc/macrosdoc/no.colorsdoc/setter.outline
The `troff` source for a five-color `awk` reference card.
A modern version of `troff` such as GNU `troff` (`groff`) is
needed to produce the color version. See the file README.card
for instructions if you have an older `troff`.

doc/gawk.1
The `troff` source for a manual page describing `gawk`.
This is distributed for the convenience of Unix users.

doc/gawktexi.indoc/sidebar.awk
The Texinfo source file for this Web page.
It should be processed by doc/sidebar.awk
before processing with `texi2dvi` or `texi2pdf`
to produce a printed document, and
with `makeinfo` to produce an Info or HTML file.
The Makefile takes care of this processing and produces
printable output via `texi2dvi` or `texi2pdf`.

doc/gawk.texi
The file produced after processing gawktexi.in
with sidebar.awk.

doc/gawk.info
The generated Info file for this Web page.

doc/gawkinet.texi
The Texinfo source file for
TCP/IP Internetworking with `gawk`.
It should be processed with TeX
(via `texi2dvi` or `texi2pdf`)
to produce a printed document and
with `makeinfo` to produce an Info or HTML file.

doc/gawkinet.info
The generated Info file for
TCP/IP Internetworking with `gawk`.

doc/igawk.1
The `troff` source for a manual page describing the `igawk`
program presented in
[Igawk Program](#Igawk-Program).
(Since `gawk` can do its own `@include` processing,
neither `igawk` nor igawk.1 are installed.)

doc/it/*
Files for the Italian translation of this Web page, produced and
contributed by Antonio Colombo and Marco Curreli.

doc/Makefile.in
The input file used during the configuration process to generate the
actual Makefile for creating the documentation.

Makefile.am*/Makefile.am
Files used by the GNU Automake software for generating
the Makefile.in files used by Autoconf and
`configure`.

Makefile.inaclocal.m4bisonfix.awkconfig.guessconfigh.inconfigure.acconfigurecustom.hdepcompinstall-shmissing_d/*mkinstalldirsm4/*
These files and subdirectories are used when configuring and compiling
`gawk` for various Unix systems.  Most of them are explained
in [Unix Installation](#Unix-Installation). The rest are there to support the main
infrastructure.

po/*
The po library contains message translations.

awklib/extract.awkawklib/Makefile.amawklib/Makefile.inawklib/eg/*
The awklib directory contains a copy of extract.awk
(see [Extract Program](#Extract-Program)),
which can be used to extract the sample programs from the Texinfo
source file for this Web page. It also contains a Makefile.in file, which
`configure` uses to generate a Makefile.
Makefile.am is used by GNU Automake to create Makefile.in.
The library functions from
[Library Functions](#Library-Functions),
are included as ready-to-use files in the `gawk` distribution.
They are installed as part of the installation process.
The rest of the programs in this Web page are available in appropriate
subdirectories of awklib/eg.

extension/*
The source code, manual pages, and infrastructure files for
the sample extensions included with `gawk`.
See [Dynamic Extensions](#Dynamic-Extensions), for more information.

extras/*
Additional non-essential files.  Currently, this directory contains some shell
startup files to be installed in /etc/profile.d to aid in manipulating
the `AWKPATH` and `AWKLIBPATH` environment variables.
See [Shell Startup Files](#Shell-Startup-Files), for more information.

posix/*
Files needed for building `gawk` on POSIX-compliant systems.

pc/*
Files needed for building `gawk` under MS-Windows
(see [PC Installation](#PC-Installation) for details).

vms/*
Files needed for building `gawk` under Vax/VMS and OpenVMS
(see [VMS Installation](#VMS-Installation) for details).

test/*
A test suite for
`gawk`.  You can use &lsquo;make check&rsquo; from the top-level `gawk`
directory to run your version of `gawk` against the test suite.
If `gawk` successfully passes &lsquo;make check&rsquo;, then you can
be confident of a successful port.

---

Next: [Non-Unix Installation](#Non_002dUnix-Installation), Previous: [Gawk Distribution](#Gawk-Distribution), Up: [Installation](#Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

### B.2 Compiling and Installing `gawk` on Unix-Like Systems

Usually, you can compile and install `gawk` by typing only two
commands.  However, if you use an unusual system, you may need
to configure `gawk` for your system yourself.

&bull; [Quick Installation](#Quick-Installation):  Compiling `gawk` under Unix.
&bull; [Shell Startup Files](#Shell-Startup-Files):  Shell convenience functions.
&bull; [Additional Configuration Options](#Additional-Configuration-Options):  Other compile-time options.
&bull; [Configuration Philosophy](#Configuration-Philosophy):  How it&rsquo;s all supposed to work.

---

Next: [Shell Startup Files](#Shell-Startup-Files), Up: [Unix Installation](#Unix-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.2.1 Compiling `gawk` for Unix-Like Systems

The normal installation steps should work on all modern commercial
Unix-derived systems, GNU/Linux, BSD-based systems, and the Cygwin
environment for MS-Windows.

After you have extracted the `gawk` distribution, `cd`
to gawk-4.2.1.  As with most GNU
software, you configure `gawk` for your system by running the
`configure` program.  This program is a Bourne shell script that
is generated automatically using GNU Autoconf.
(The Autoconf software is
described fully in
Autoconf&mdash;Generating Automatic Configuration Scripts,
which can be found online at
[the Free Software Foundation&rsquo;s website](https://www.gnu.org/software/autoconf/manual/index.html).)

To configure `gawk`, simply run `configure`:

    sh ./configure
    

This produces a Makefile and config.h tailored to your system.
The config.h file describes various facts about your system.
You might want to edit the Makefile to
change the `CFLAGS` variable, which controls
the command-line options that are passed to the C compiler (such as
optimization levels or compiling for debugging).

Alternatively, you can add your own values for most `make`
variables on the command line, such as `CC` and `CFLAGS`, when
running `configure`:

    CC=cc CFLAGS=-g sh ./configure
    

See the file INSTALL in the `gawk` distribution for
all the details.

After you have run `configure` and possibly edited the Makefile,
type:

    make
    

Shortly thereafter, you should have an executable version of `gawk`.
That&rsquo;s all there is to it!
To verify that `gawk` is working properly,
run &lsquo;make check&rsquo;.  All of the tests should succeed.
If these steps do not work, or if any of the tests fail,
check the files in the README_d directory to see if you&rsquo;ve
found a known problem.  If the failure is not described there,
send in a bug report (see [Bugs](#Bugs)).

Of course, once you&rsquo;ve built `gawk`, it is likely that you will
wish to install it.  To do so, you need to run the command &lsquo;make
install&rsquo;, as a user with the appropriate permissions.  How to do this
varies by system, but on many systems you can use the `sudo`
command to do so.  The command then becomes &lsquo;sudo make install&rsquo;. It
is likely that you will be asked for your password, and you will have
to have been set up previously as a user who is allowed to run the
`sudo` command.

---

Next: [Additional Configuration Options](#Additional-Configuration-Options), Previous: [Quick Installation](#Quick-Installation), Up: [Unix Installation](#Unix-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.2.2 Shell Startup Files

The distribution contains shell startup files gawk.sh and
gawk.csh, containing functions to aid in manipulating
the `AWKPATH` and `AWKLIBPATH` environment variables.
On a Fedora GNU/Linux system, these files should be installed in /etc/profile.d;
on other platforms, the appropriate location may be different.

`gawkpath_default`
Reset the `AWKPATH` environment variable to its default value.

`gawkpath_prepend`
Add the argument to the front of the `AWKPATH` environment variable.

`gawkpath_append`
Add the argument to the end of the `AWKPATH` environment variable.

`gawklibpath_default`
Reset the `AWKLIBPATH` environment variable to its default value.

`gawklibpath_prepend`
Add the argument to the front of the `AWKLIBPATH` environment variable.

`gawklibpath_append`
Add the argument to the end of the `AWKLIBPATH` environment variable.

---

Next: [Configuration Philosophy](#Configuration-Philosophy), Previous: [Shell Startup Files](#Shell-Startup-Files), Up: [Unix Installation](#Unix-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.2.3 Additional Configuration Options

There are several additional options you may use on the `configure`
command line when compiling `gawk` from scratch, including:

`--disable-extensions`
Disable configuring and building the sample extensions in the
extension directory. This is useful for cross-compiling.
The default action is to dynamically check if the extensions
can be configured and compiled.

`--disable-lint`
Disable all lint checking within `gawk`.  The
--lint and --lint-old options
(see [Options](#Options))
are accepted, but silently do nothing.
Similarly, setting the `LINT` variable
(see [User-modified](#User_002dmodified))
has no effect on the running `awk` program.

When used with the GNU Compiler Collection&rsquo;s (GCC&rsquo;s)
automatic dead-code-elimination, this option
cuts almost 23K bytes off the size of the `gawk`
executable on GNU/Linux x86_64 systems.  Results on other systems and
with other compilers are likely to vary.
Using this option may bring you some slight performance improvement.

> CAUTION: Using this option will cause some of the tests in the test suite
> to fail.  This option may be removed at a later date.

`--disable-mpfr`
Skip checking for the MPFR and GMP libraries. This is useful
mainly for the developers, to make sure nothing breaks if
MPFR support is not available.

`--disable-nls`
Disable all message-translation facilities.
This is usually not desirable, but it may bring you some slight performance
improvement.

`--enable-versioned-extension-dir`
Use a versioned directory for extensions.  The directory name will
include the major and minor API versions in it. This makes it possible
to keep extensions for different API versions on the same system
without their conflicting with one another.

`--with-whiny-user-strftime`
Force use of the included version of the C `strftime()`
function for deficient systems.

Use the command &lsquo;./configure --help&rsquo; to see the full list of
options supplied by `configure`.

---

Previous: [Additional Configuration Options](#Additional-Configuration-Options), Up: [Unix Installation](#Unix-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.2.4 The Configuration Process

This section is of interest only if you know something about using the
C language and Unix-like operating systems.

The source code for `gawk` generally attempts to adhere to formal
standards wherever possible.  This means that `gawk` uses library
routines that are specified by the ISO C standard and by the POSIX
operating system interface standard.
The `gawk` source code requires using an ISO C compiler (the 1990
standard).

Many Unix systems do not support all of either the ISO or the
POSIX standards.  The missing_d subdirectory in the `gawk`
distribution contains replacement versions of those functions that are
most likely to be missing.

The config.h file that `configure` creates contains
definitions that describe features of the particular operating system
where you are attempting to compile `gawk`.  The three things
described by this file are: what header files are available, so that
they can be correctly included, what (supposedly) standard functions
are actually available in your C libraries, and various miscellaneous
facts about your operating system.  For example, there may not be an
`st_blksize` element in the `stat` structure.  In this case,
&lsquo;HAVE_STRUCT_STAT_ST_BLKSIZE&rsquo; is undefined.

It is possible for your C compiler to lie to `configure`. It may
do so by not exiting with an error when a library function is not
available.  To get around this, edit the custom.h file.
Use an &lsquo;#ifdef&rsquo; that is appropriate for your system, and either
`#define` any constants that `configure` should have defined but
didn&rsquo;t, or `#undef` any constants that `configure` defined and
should not have.  The custom.h file is automatically included by
the config.h file.

It is also possible that the `configure` program generated by
Autoconf will not work on your system in some other fashion.
If you do have a problem, the configure.ac file is the input for
Autoconf.  You may be able to change this file and generate a
new version of `configure` that works on your system
(see [Bugs](#Bugs)
for information on how to report problems in configuring `gawk`).
The same mechanism may be used to send in updates to configure.ac
and/or custom.h.

---

Next: [Bugs](#Bugs), Previous: [Unix Installation](#Unix-Installation), Up: [Installation](#Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

### B.3 Installation on Other Operating Systems

This section describes how to install `gawk` on
various non-Unix systems.

&bull; [PC Installation](#PC-Installation):  Installing and Compiling `gawk` on
                                Microsoft Windows.
&bull; [VMS Installation](#VMS-Installation):  Installing `gawk` on VMS.

---

Next: [VMS Installation](#VMS-Installation), Up: [Non-Unix Installation](#Non_002dUnix-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.1 Installation on MS-Windows

This section covers installation and usage of `gawk`
on Intel architecture machines running any version of MS-Windows.
In this section, the term &ldquo;Windows32&rdquo;
refers to any of Microsoft Windows 95/98/ME/NT/2000/XP/Vista/7/8/10.

See also the README_d/README.pc file in the distribution.

&bull; [PC Binary Installation](#PC-Binary-Installation):  Installing a prepared distribution.
&bull; [PC Compiling](#PC-Compiling):  Compiling `gawk` for Windows32.
&bull; [PC Using](#PC-Using):  Running `gawk` on Windows32.
&bull; [Cygwin](#Cygwin):  Building and running `gawk` for
                                Cygwin.
&bull; [MSYS](#MSYS):  Using `gawk` In The MSYS Environment.

---

Next: [PC Compiling](#PC-Compiling), Up: [PC Installation](#PC-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.1.1 Installing a Prepared Distribution for MS-Windows Systems

The only supported binary distribution for MS-Windows systems
is that provided by Eli Zaretskii&rsquo;s [&ldquo;ezwinports&rdquo;](https://sourceforge.net/projects/ezwinports/) project.  Install the compiled `gawk` from there.

---

Next: [PC Using](#PC-Using), Previous: [PC Binary Installation](#PC-Binary-Installation), Up: [PC Installation](#PC-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.1.2 Compiling `gawk` for PC Operating Systems

`gawk` can be compiled for Windows32 using MinGW (Windows32).
The file README_d/README.pc in the `gawk` distribution
contains additional notes, and pc/Makefile contains important
information on compilation options.

To build `gawk` for Windows32, copy the files in
the pc directory (*except* for ChangeLog) to the
directory with the rest of the `gawk` sources, then invoke
`make` with the appropriate target name as an argument to
build `gawk`.  The Makefile copied from the pc
directory contains a configuration section with comments and may need
to be edited in order to work with your `make` utility.

The Makefile supports a number of targets for building various
MS-DOS and Windows32 versions.  A list of targets is printed if the
`make` command is given without a target.  As an example,
to build a native MS-Windows binary of `gawk` using the MinGW tools,
type &lsquo;make mingw32&rsquo;.

---

Next: [Cygwin](#Cygwin), Previous: [PC Compiling](#PC-Compiling), Up: [PC Installation](#PC-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.1.3 Using `gawk` on PC Operating Systems

Information in this section applies to the MinGW and
DJGPP ports of `gawk`. See [Cygwin](#Cygwin) for information
about the Cygwin port.

Under MS-Windows, the MinGW environment supports
both the &lsquo;|&&rsquo; operator and TCP/IP networking
(see [TCP/IP Networking](#TCP_002fIP-Networking)).
The DJGPP environment does not support &lsquo;|&&rsquo;.

The MS-Windows version of `gawk` searches for
program files as described in [AWKPATH Variable](#AWKPATH-Variable).  However,
semicolons (rather than colons) separate elements in the `AWKPATH`
variable.  If `AWKPATH` is not set or is empty, then the default
search path is &lsquo;.;c:/lib/awk;c:/gnu/lib/awk&rsquo;.

Under MS-Windows,
`gawk` (and many other text programs) silently
translates end-of-line &lsquo;\r\n&rsquo; to &lsquo;\n&rsquo; on input and &lsquo;\n&rsquo;
to &lsquo;\r\n&rsquo; on output.  A special `BINMODE` variable (c.e.)
allows control over these translations and is interpreted as follows:

-  If `BINMODE` is `"r"` or one,
then
binary mode is set on read (i.e., no translations on reads).

-  If `BINMODE` is `"w"` or two,
then
binary mode is set on write (i.e., no translations on writes).

-  If `BINMODE` is `"rw"` or `"wr"` or three,
binary mode is set for both read and write.

- `BINMODE=non-null-string` is
the same as &lsquo;BINMODE=3&rsquo; (i.e., no translations on
reads or writes).  However, `gawk` issues a warning
message if the string is not one of `"rw"` or `"wr"`.

The modes for standard input and standard output are set one time
only (after the
command line is read, but before processing any of the `awk` program).
Setting `BINMODE` for standard input or
standard output is accomplished by using an
appropriate &lsquo;-v BINMODE=N&rsquo; option on the command line.
`BINMODE` is set at the time a file or pipe is opened and cannot be
changed midstream.

The name `BINMODE` was chosen to match `mawk`
(see [Other Versions](#Other-Versions)).
`mawk` and `gawk` handle `BINMODE` similarly; however,
`mawk` adds a &lsquo;-W BINMODE=N&rsquo; option and an environment
variable that can set `BINMODE`, `RS`, and `ORS`.  The
files binmode[1-3].awk (under gnu/lib/awk in some of the
prepared binary distributions) have been chosen to match `mawk`&rsquo;s &lsquo;-W
BINMODE=N&rsquo; option.  These can be changed or discarded; in particular,
the setting of `RS` giving the fewest &ldquo;surprises&rdquo; is open to debate.
`mawk` uses &lsquo;RS = "\r\n"&rsquo; if binary mode is set on read, which is
appropriate for files with the MS-DOS-style end-of-line.

To illustrate, the following examples set binary mode on writes for standard
output and other files, and set `ORS` as the &ldquo;usual&rdquo; MS-DOS-style
end-of-line:

    gawk -v BINMODE=2 -v ORS="\r\n" &hellip;
    

or:

    gawk -v BINMODE=w -f binmode2.awk &hellip;
    

These give the same result as the &lsquo;-W BINMODE=2&rsquo; option in
`mawk`.
The following changes the record separator to `"\r\n"` and sets binary
mode on reads, but does not affect the mode on standard input:

    gawk -v RS="\r\n" -e "BEGIN { BINMODE = 1 }" &hellip;
    

or:

    gawk -f binmode1.awk &hellip;
    

With proper quoting, in the first example the setting of `RS` can be
moved into the `BEGIN` rule.

---

Next: [MSYS](#MSYS), Previous: [PC Using](#PC-Using), Up: [PC Installation](#PC-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.1.4 Using `gawk` In The Cygwin Environment

`gawk` can be built and used &ldquo;out of the box&rdquo; under MS-Windows
if you are using the [Cygwin environment](http://www.cygwin.com).
This environment provides an excellent simulation of GNU/Linux, using
Bash, GCC, GNU Make,
and other GNU programs.  Compilation and installation for Cygwin is the
same as for a Unix system:

    tar -xvpzf gawk-4.2.1.tar.gz
    cd gawk-4.2.1
    ./configure
    make && make check
    

When compared to GNU/Linux on the same system, the &lsquo;configure&rsquo;
step on Cygwin takes considerably longer.  However, it does finish,
and then the &lsquo;make&rsquo; proceeds as usual.

Recent versions of Cygwin open all files in binary mode. This means
that you should use &lsquo;RS = "\r?\n"&rsquo; in order to be able to
handle standard MS-Windows text files with carriage-return plus
line-feed line endings.

The Cygwin environment supports
both the &lsquo;|&&rsquo; operator and TCP/IP networking
(see [TCP/IP Networking](#TCP_002fIP-Networking)).

---

Previous: [Cygwin](#Cygwin), Up: [PC Installation](#PC-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.1.5 Using `gawk` In The MSYS Environment

In the MSYS environment under MS-Windows, `gawk` automatically
uses binary mode for reading and writing files.  Thus, there is no
need to use the `BINMODE` variable.

This can cause problems with other Unix-like components that have
been ported to MS-Windows that expect `gawk` to do automatic
translation of `"\r\n"`, because it won&rsquo;t.

---

Previous: [PC Installation](#PC-Installation), Up: [Non-Unix Installation](#Non_002dUnix-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2 Compiling and Installing `gawk` on Vax/VMS and OpenVMS

This subsection describes how to compile and install `gawk` under VMS.
The older designation &ldquo;VMS&rdquo; is used throughout to refer to OpenVMS.

&bull; [VMS Compilation](#VMS-Compilation):  How to compile `gawk` under VMS.
&bull; [VMS Dynamic Extensions](#VMS-Dynamic-Extensions):  Compiling `gawk` dynamic extensions on
                                VMS.
&bull; [VMS Installation Details](#VMS-Installation-Details):  How to install `gawk` under VMS.
&bull; [VMS Running](#VMS-Running):  How to run `gawk` under VMS.
&bull; [VMS GNV](#VMS-GNV):  The VMS GNV Project.
&bull; [VMS Old Gawk](#VMS-Old-Gawk):  An old version comes with some VMS systems.

---

Next: [VMS Dynamic Extensions](#VMS-Dynamic-Extensions), Up: [VMS Installation](#VMS-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2.1 Compiling `gawk` on VMS

To compile `gawk` under VMS, there is a `DCL` command procedure
that issues all the necessary `CC` and `LINK` commands. There is
also a Makefile for use with the `MMS` and `MMK` utilities.
From the source directory, use either:

    $ @[.vms]vmsbuild.com

or:

    $ MMS/DESCRIPTION=[.vms]descrip.mms gawk

or:

    $ MMK/DESCRIPTION=[.vms]descrip.mms gawk

`MMK` is an open source, free, near-clone of `MMS` and
can better handle ODS-5 volumes with upper- and lowercase file names.
`MMK` is available from [https://github.com/endlesssoftware/mmk](https://github.com/endlesssoftware/mmk).

With ODS-5 volumes and extended parsing enabled, the case of the target
parameter may need to be exact.

`gawk` has been tested under VAX/VMS 7.3 and Alpha/VMS 7.3-1
using Compaq C V6.4, and under Alpha/VMS 7.3, Alpha/VMS 7.3-2, and IA64/VMS 8.3.
The most recent builds used HP C V7.3 on Alpha VMS 8.3 and both
Alpha and IA64 VMS 8.4 used HP C 7.3.[114](#FOOT114)

See [VMS GNV](#VMS-GNV) for information on building
`gawk` as a PCSI kit that is compatible with the GNV product.

---

Next: [VMS Installation Details](#VMS-Installation-Details), Previous: [VMS Compilation](#VMS-Compilation), Up: [VMS Installation](#VMS-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2.2 Compiling `gawk` Dynamic Extensions on VMS

The extensions that have been ported to VMS can be built using one of
the following commands:

    $ MMS/DESCRIPTION=[.vms]descrip.mms extensions

or:

    $ MMK/DESCRIPTION=[.vms]descrip.mms extensions

`gawk` uses `AWKLIBPATH` as either an environment variable
or a logical name to find the dynamic extensions.

Dynamic extensions need to be compiled with the same compiler options for
floating-point, pointer size, and symbol name handling as were used
to compile `gawk` itself.
Alpha and Itanium should use IEEE floating point.  The pointer size is 32 bits,
and the symbol name handling should be exact case with CRC shortening for
symbols longer than 32 bits.

For Alpha and Itanium:

    /name=(as_is,short)
    /float=ieee/ieee_mode=denorm_results
    

For VAX:

    /name=(as_is,short)
    

Compile-time macros need to be defined before the first VMS-supplied
header file is included, as follows:

    #if (__CRTL_VER >= 70200000) && !defined (__VAX)
    #define _LARGEFILE 1
    #endif
    
    #ifndef __VAX
    #ifdef __CRTL_VER
    #if __CRTL_VER >= 80200000
    #define _USE_STD_STAT 1
    #endif
    #endif
    #endif
    

If you are writing your own extensions to run on VMS, you must supply these
definitions yourself. The config.h file created when building `gawk`
on VMS does this for you; if instead you use that file or a similar one, then you
must remember to include it before any VMS-supplied header files.

---

Next: [VMS Running](#VMS-Running), Previous: [VMS Dynamic Extensions](#VMS-Dynamic-Extensions), Up: [VMS Installation](#VMS-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2.3 Installing `gawk` on VMS

To use `gawk`, all you need is a &ldquo;foreign&rdquo; command, which is a
`DCL` symbol whose value begins with a dollar sign. For example:

    $ GAWK :== $disk1:[gnubin]gawk

Substitute the actual location of `gawk.exe` for
&lsquo;$disk1:[gnubin]&rsquo;. The symbol should be placed in the
login.com of any user who wants to run `gawk`,
so that it is defined every time the user logs on.
Alternatively, the symbol may be placed in the system-wide
sylogin.com procedure, which allows all users
to run `gawk`.

If your `gawk` was installed by a PCSI kit into the
GNV$GNU: directory tree, the program will be known as
GNV$GNU:[bin]gnv$gawk.exe and the help file will be
GNV$GNU:[vms_help]gawk.hlp.

The PCSI kit also installs a GNV$GNU:[vms_bin]gawk_verb.cld file
that can be used to add `gawk` and `awk` as DCL commands.

For just the current process you can use:

    $ set command gnv$gnu:[vms_bin]gawk_verb.cld

Or the system manager can use GNV$GNU:[vms_bin]gawk_verb.cld to
add the `gawk` and `awk` to the system-wide &lsquo;DCLTABLES&rsquo;.

The DCL syntax is documented in the gawk.hlp file.

Optionally, the gawk.hlp entry can be loaded into a VMS help library:

    $ LIBRARY/HELP sys$help:helplib [.vms]gawk.hlp

(You may want to substitute a site-specific help library rather than
the standard VMS library &lsquo;HELPLIB&rsquo;.)  After loading the help text,
the command:

    $ HELP GAWK

provides information about both the `gawk` implementation and the
`awk` programming language.

The logical name &lsquo;AWK_LIBRARY&rsquo; can designate a default location
for `awk` program files.  For the -f option, if the specified
file name has no device or directory path information in it, `gawk`
looks in the current directory first, then in the directory specified
by the translation of &lsquo;AWK_LIBRARY&rsquo; if the file is not found.
If, after searching in both directories, the file still is not found,
`gawk` appends the suffix &lsquo;.awk&rsquo; to the file name and retries
the file search.  If &lsquo;AWK_LIBRARY&rsquo; has no definition, a default value
of &lsquo;SYS$LIBRARY:&rsquo; is used for it.

---

Next: [VMS GNV](#VMS-GNV), Previous: [VMS Installation Details](#VMS-Installation-Details), Up: [VMS Installation](#VMS-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2.4 Running `gawk` on VMS

Command-line parsing and quoting conventions are significantly different
on VMS, so examples in this Web page or from other sources often need minor
changes.  They *are* minor though, and all `awk` programs
should run correctly.

Here are a couple of trivial tests:

    $ gawk -- "BEGIN {print ""Hello, World!""}"
    $ gawk -"W" version
    ! could also be -"W version" or "-W version"
    

Note that uppercase and mixed-case text must be quoted.

The VMS port of `gawk` includes a `DCL`-style interface in addition
to the original shell-style interface (see the help entry for details).
One side effect of dual command-line parsing is that if there is only a
single parameter (as in the quoted string program), the command
becomes ambiguous.  To work around this, the normally optional --
flag is required to force Unix-style parsing rather than `DCL` parsing.
If any other dash-type options (or multiple parameters such as data files to
process) are present, there is no ambiguity and -- can be omitted.

The `exit` value is a Unix-style value and is encoded into a VMS exit
status value when the program exits.

The VMS severity bits will be set based on the `exit` value.
A failure is indicated by 1, and VMS sets the `ERROR` status.
A fatal error is indicated by 2, and VMS sets the `FATAL` status.
All other values will have the `SUCCESS` status.  The exit value is
encoded to comply with VMS coding standards and will have the
`C_FACILITY_NO` of `0x350000` with the constant `0xA000`
added to the number shifted over by 3 bits to make room for the severity codes.

To extract the actual `gawk` exit code from the VMS status, use:

    unix_status = (vms_status .and. %x7f8) / 8
    

A C program that uses `exec()` to call `gawk` will get the original
Unix-style exit value.

Older versions of `gawk` for VMS treated a Unix exit code 0 as 1,
a failure as 2, a fatal error as 4, and passed all the other numbers through.
This violated the VMS exit status coding requirements.

VAX/VMS floating point uses unbiased rounding. See [Round Function](#Round-Function).

VMS reports time values in GMT unless one of the `SYS$TIMEZONE_RULE`
or `TZ` logical names is set.  Older versions of VMS, such as VAX/VMS
7.3, do not set these logical names.

The default search path, when looking for `awk` program files specified
by the -f option, is `"SYS$DISK:[],AWK_LIBRARY:"`.  The logical
name `AWKPATH` can be used to override this default.  The format
of `AWKPATH` is a comma-separated list of directory specifications.
When defining it, the value should be quoted so that it retains a single
translation and not a multitranslation `RMS` searchlist.

This restriction also applies to running `gawk` under GNV,
as redirection is always to a DCL command.

If you are redirecting data to a VMS command or utility, the current
implementation requires that setting up a VMS foreign command that runs
a command file before invoking `gawk`.
(This restriction may be removed in a future release of `gawk` on VMS.)

Without this command file, the input data will also appear prepended
to the output data.

This also allows simulating POSIX commands that are not found on VMS or the
use of GNV utilities.

The example below is for `gawk` redirecting data to the VMS
`sort` command.

    $ sort = "@device:[dir]vms_gawk_sort.com"
    

The command file needs to be of the format in the example below.

The first line inhibits the passed input data from also showing up in the
output.  It must be in the format in the example.

The next line creates a foreign command that overrides the outer foreign
command which prevents an infinite recursion of command files.

The next to the last command redirects `sys$input` to be
`sys$command`, in order to pick up the data that is being redirected
to the command.

The last line runs the actual command.  It must be the last command as the data
redirected from `gawk` will be read when the command file ends.

    $!'f$verify(0,0)'
    $ sort := sort
    $ define/user sys$input sys$command:
    $ sort sys$input: sys$output:
    

---

Next: [VMS Old Gawk](#VMS-Old-Gawk), Previous: [VMS Running](#VMS-Running), Up: [VMS Installation](#VMS-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2.5 The VMS GNV Project

The VMS GNV package provides a build environment similar to POSIX with ports
of a collection of open source tools.  The `gawk` found in the GNV
base kit is an older port.  Currently, the GNV project is being reorganized
to supply individual PCSI packages for each component.
See [https://sourceforge.net/p/gnv/wiki/InstallingGNVPackages/](https://sourceforge.net/p/gnv/wiki/InstallingGNVPackages/).

The normal build procedure for `gawk` produces a program that
is suitable for use with GNV.

The file vms/gawk_build_steps.txt in the distribution documents
the procedure for building a VMS PCSI kit that is compatible with GNV.

---

Previous: [VMS GNV](#VMS-GNV), Up: [VMS Installation](#VMS-Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.3.2.6 Some VMS Systems Have An Old Version of `gawk`

Some versions of VMS have an old version of `gawk`.  To access it,
define a symbol, as follows:

    $ gawk :== $sys$common:[syshlp.examples.tcpip.snmp]gawk.exe

This is apparently version 2.15.6, which is extremely old. We
recommend compiling and using the current version.

---

Next: [Other Versions](#Other-Versions), Previous: [Non-Unix Installation](#Non_002dUnix-Installation), Up: [Installation](#Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

### B.4 Reporting Problems and Bugs

> There is nothing more dangerous than a bored archaeologist.

&mdash; *Douglas Adams, The Hitchhiker&rsquo;s Guide to the Galaxy*

If you have problems with `gawk` or think that you have found a bug,
report it to the developers; we cannot promise to do anything,
but we might well want to fix it.

&bull; [Bug address](#Bug-address):  Where to send reports to.
&bull; [Usenet](#Usenet):  Where not to send reports to.
&bull; [Maintainers](#Maintainers):  Maintainers of non-*nix ports.

---

Next: [Usenet](#Usenet), Up: [Bugs](#Bugs)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.4.1 Submitting Bug Reports

Before reporting a bug, make sure you have really found a genuine bug.
First, verify that you have the latest version of `gawk`.
Many bugs (usually subtle ones) are fixed at each release, and if yours
is out of date, the problem may already have been solved.

Second, please see if setting the environment variable `LC_ALL`
to `LC_ALL=C` causes things to behave as you expect. If so, it&rsquo;s
a locale issue, and may or may not really be a bug.

Third, carefully reread the documentation and see if it says you can do
what you&rsquo;re trying to do.  If it&rsquo;s not clear whether you should be able
to do something or not, report that too; it&rsquo;s a bug in the documentation!

Finally, before reporting a bug or trying to fix it yourself, try to isolate it
to the smallest possible `awk` program and input data file that
reproduce the problem.  Then send us the program and data file,
some idea of what kind of Unix system you&rsquo;re using,
the compiler you used to compile `gawk`, and the exact results
`gawk` gave you.  Also say what you expected to occur; this helps
us decide whether the problem is really in the documentation.

Make sure to include the version number of `gawk` you are using.
You can get this information with the command &lsquo;gawk --version&rsquo;.

Once you have a precise problem description, send email to
&ldquo;bug-gawk at gnu dot org&rdquo;.

The `gawk` maintainers subscribe to this address, and
thus they will receive your bug report.
Although you can send mail to the maintainers directly,
the bug reporting address is preferred because the
email list is archived at the GNU Project.
*All email must be in English. This is the only language
understood in common by all the maintainers.*
In addition, please be sure to send all mail in *plain text*,
not (or not exclusively) in HTML.

> NOTE: Many distributions of GNU/Linux and the various BSD-based operating systems
> have their own bug reporting systems.  If you report a bug using your distribution&rsquo;s
> bug reporting system, you should also send a copy to
> &ldquo;bug-gawk at gnu dot org&rdquo;.
> 
> 
> This is for two reasons.  First, although some distributions forward
> bug reports &ldquo;upstream&rdquo; to the GNU mailing list, many don&rsquo;t, so there is a good
> chance that the `gawk`  maintainers won&rsquo;t even see the bug report!  Second,
> mail to the GNU list is archived, and having everything at the GNU Project
> keeps things self-contained and not dependent on other organizations.

Non-bug suggestions are always welcome as well.  If you have questions
about things that are unclear in the documentation or are just obscure
features, ask on the bug list; we will try to help you out if we can.

---

Next: [Maintainers](#Maintainers), Previous: [Bug address](#Bug-address), Up: [Bugs](#Bugs)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.4.2 Please Don&rsquo;t Post Bug Reports to USENET

> I gave up on Usenet a couple of years ago and haven&rsquo;t really looked back.
> It&rsquo;s like sports talk radio&mdash;you feel smarter for not having read it.

&mdash; *Chet Ramey*

Please do *not* try to report bugs in `gawk` by posting to the
Usenet/Internet newsgroup `comp.lang.awk`.  Although some of the
`gawk` developers occasionally read this news group, the primary
`gawk` maintainer no longer does.  Thus it&rsquo;s virtually guaranteed
that he will *not* see your posting.  The steps described here are
the only officially recognized way for reporting bugs.  Really.

---

Previous: [Usenet](#Usenet), Up: [Bugs](#Bugs)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### B.4.3 Reporting Problems with Non-Unix Ports

If you find bugs in one of the non-Unix ports of `gawk`,
send an email to the bug list, with a copy to the
person who maintains that port.  The maintainers are named in the following list,
as well as in the README file in the `gawk` distribution.
Information in the README file should be considered authoritative
if it conflicts with this Web page.

The people maintaining the various `gawk` ports are:

Unix and POSIX systemsArnold Robbins, &ldquo;arnold at skeeve dot com&rdquo;MS-DOS with DJGPPJuan Manuel Guerrero, &ldquo;juan dot guerrero at gmx dot de&rdquo;MS-Windows with MinGWEli Zaretskii, &ldquo;eliz at gnu dot org&rdquo;OS/2Andreas Buening, &ldquo;andreas dot buening at nexgo dot de&rdquo;VMSJohn Malmberg, &ldquo;wb8tyw at qsl.net&rdquo;z/OS (OS/390)Daniel Richard G. &ldquo;skunk at iSKUNK.ORG&rdquo;Dave Pitts (Maintainer Emeritus), &ldquo;dpitts at cozx dot com&rdquo;
If your bug is also reproducible under Unix, send a copy of your
report to the &ldquo;bug-gawk at gnu dot org&rdquo; email list as well.

---

Next: [Installation summary](#Installation-summary), Previous: [Bugs](#Bugs), Up: [Installation](#Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

### B.5 Other Freely Available `awk` Implementations

> It&rsquo;s kind of fun to put comments like this in your awk code:
> 
>       `// Do C++ comments work? answer: yes! of course`

&mdash; *Michael Brennan*

There are a number of other freely available `awk` implementations.
This section briefly describes where to get them:

Unix `awk`
Brian Kernighan, one of the original designers of Unix `awk`,
has made his implementation of
`awk` freely available.
You can retrieve this version via
[his home page](http://www.cs.princeton.edu/~bwk).
It is available in several archive formats:

Shell archive
[http://www.cs.princeton.edu/~bwk/btl.mirror/awk.shar](http://www.cs.princeton.edu/~bwk/btl.mirror/awk.shar)
Compressed `tar` file
[http://www.cs.princeton.edu/~bwk/btl.mirror/awk.tar.gz](http://www.cs.princeton.edu/~bwk/btl.mirror/awk.tar.gz)
Zip file
[http://www.cs.princeton.edu/~bwk/btl.mirror/awk.zip](http://www.cs.princeton.edu/~bwk/btl.mirror/awk.zip)

You can also retrieve it from GitHub:

    git clone git://github.com/onetrueawk/awk bwkawk
    

This command creates a copy of the [Git](https://git-scm.com)
repository in a directory named bwkawk.  If you leave that argument
off the `git` command line, the repository copy is created in a
directory named awk.

This version requires an ISO C (1990 standard) compiler; the C compiler
from GCC (the GNU Compiler Collection) works quite nicely.

See [Common Extensions](#Common-Extensions)
for a list of extensions in this `awk` that are not in POSIX `awk`.

As a side note, Dan Bornstein has created a Git repository tracking
all the versions of BWK `awk` that he could find. It&rsquo;s
available at [git://github.com/danfuzz/one-true-awk](git://github.com/danfuzz/one-true-awk).

`mawk`
Michael Brennan wrote an independent implementation of `awk`,
called `mawk`.  It is available under the
GPL (see [Copying](#Copying)),
just as `gawk` is.

The original distribution site for the `mawk` source code
no longer has it.  A copy is available at
[http://www.skeeve.com/gawk/mawk1.3.3.tar.gz](http://www.skeeve.com/gawk/mawk1.3.3.tar.gz).

In 2009, Thomas Dickey took on `mawk` maintenance.
Basic information is available on
[the project&rsquo;s web page](http://www.invisible-island.net/mawk).
The download URL is
[http://invisible-island.net/datafiles/release/mawk.tar.gz](http://invisible-island.net/datafiles/release/mawk.tar.gz).

Once you have it,
`gunzip` may be used to decompress this file. Installation
is similar to `gawk`&rsquo;s
(see [Unix Installation](#Unix-Installation)).

See [Common Extensions](#Common-Extensions)
for a list of extensions in `mawk` that are not in POSIX `awk`.

`mawk` 2.0
In 2016, Michael Brennan resumed `mawk` development.
His development snapshots are available via Git from the project&rsquo;s
[GitHub page](https://github.com/mikebrennan000/mawk-2).

`awka`
Written by Andrew Sumner,
`awka` translates `awk` programs into C, compiles them,
and links them with a library of functions that provide the core
`awk` functionality.
It also has a number of extensions.

The `awk` translator is released under the GPL, and the library
is under the LGPL.

To get `awka`, go to [https://sourceforge.net/projects/awka](https://sourceforge.net/projects/awka).

The project seems to be frozen; no new code changes have been made
since approximately 2001.

`pawk`
Nelson H.F. Beebe at the University of Utah has modified
BWK `awk` to provide timing and profiling information.
It is different from `gawk` with the --profile option
(see [Profiling](#Profiling))
in that it uses CPU-based profiling, not line-count
profiling.  You may find it at either
[ftp://ftp.math.utah.edu/pub/pawk/pawk-20030606.tar.gz](ftp://ftp.math.utah.edu/pub/pawk/pawk-20030606.tar.gz)
or
[http://www.math.utah.edu/pub/pawk/pawk-20030606.tar.gz](http://www.math.utah.edu/pub/pawk/pawk-20030606.tar.gz).

BusyBox `awk`
BusyBox is a GPL-licensed program providing small versions of many
applications within a single executable. It is aimed at embedded systems.
It includes a full implementation of POSIX `awk`.  When building
it, be careful not to do &lsquo;make install&rsquo; as it will overwrite
copies of other applications in your /usr/local/bin.  For more
information, see the [project&rsquo;s home page](https://busybox.net).

The OpenSolaris POSIX `awk`
The versions of `awk` in /usr/xpg4/bin and
/usr/xpg6/bin on Solaris are more or less POSIX-compliant.
They are based on the `awk` from Mortice Kern Systems for PCs.
We were able to make this code compile and work under GNU/Linux
with 1&ndash;2 hours of work.  Making it more generally portable (using
GNU Autoconf and/or Automake) would take more work, and this
has not been done, at least to our knowledge.

The source code used to be available from the OpenSolaris website.
However, that project was ended and the website shut down.  Fortunately, the
[Illumos project](https://wiki.illumos.org/display/illumos/illumos+Home)
makes this implementation available.  You can view the files one at a time from
[https://github.com/joyent/illumos-joyent/blob/master/usr/src/cmd/awk_xpg4](https://github.com/joyent/illumos-joyent/blob/master/usr/src/cmd/awk_xpg4).

`jawk`
This is an interpreter for `awk` written in Java. It claims
to be a full interpreter, although because it uses Java facilities
for I/O and for regexp matching, the language it supports is different
from POSIX `awk`.  More information is available on the
[project&rsquo;s home page](https://jawk.sourceforge.net).

Libmawk
This is an embeddable `awk` interpreter derived from
`mawk`. For more information, see
[http://repo.hu/projects/libmawk/](http://repo.hu/projects/libmawk/).

`pawk`
This is a Python module that claims to bring `awk`-like
features to Python. See [https://github.com/alecthomas/pawk](https://github.com/alecthomas/pawk)
for more information. (This is not related to Nelson Beebe&rsquo;s
modified version of BWK `awk`, described earlier.)

QSE `awk`
This is an embeddable `awk` interpreter. For more information,
see [https://code.google.com/p/qse/](https://code.google.com/p/qse/). 
`QTawk`
This is an independent implementation of `awk` distributed
under the GPL. It has a large number of extensions over standard
`awk` and may not be 100% syntactically compatible with it.
See [http://www.quiktrim.org/QTawk.html](http://www.quiktrim.org/QTawk.html) for more information,
including the manual. The download link there is out of date; see
[http://www.quiktrim.org/#AdditionalResources](http://www.quiktrim.org/#AdditionalResources) for the latest
download link.

The project may also be frozen; no new code changes have been made
since approximately 2014.

Other versions
See also the &ldquo;Versions and implementations&rdquo; section of the
[Wikipedia article](https://en.wikipedia.org/wiki/Awk_language#Versions_and_implementations) on `awk` for information on additional versions.

---

Previous: [Other Versions](#Other-Versions), Up: [Installation](#Installation)   [[Contents](#SEC_Contents)][[Index](#Index)]

### B.6 Summary

-  The `gawk` distribution is available from the GNU Project&rsquo;s main
distribution site, `ftp.gnu.org`.  The canonical build recipe is:

    wget https://ftp.gnu.org/gnu/gawk/gawk-4.2.1.tar.gz
    tar -xvpzf gawk-4.2.1.tar.gz
    cd gawk-4.2.1
    ./configure && make && make check
    

> NOTE: Because of the &lsquo;https://&rsquo; URL, you may have to supply the
> --no-check-certificate option to `wget` to download
> the file.

- `gawk` may be built on non-POSIX systems as well. The currently
supported systems are MS-Windows using
MSYS, DJGPP, MinGW, and Cygwin,
and both Vax/VMS and OpenVMS.
Instructions for each system are included in this appendix.

-  Bug reports should be sent via email to [bug-gawk@gnu.org](mailto:bug-gawk@gnu.org).
Bug reports should be in English and should include the version of `gawk`,
how it was compiled, and a short program and data file that demonstrate
the problem.

-  There are a number of other freely available `awk`
implementations.  Many are POSIX-compliant; others are less so.

---

Next: [Basic Concepts](#Basic-Concepts), Previous: [Installation](#Installation), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## Appendix C Implementation Notes

This appendix contains information mainly of interest to implementers and
maintainers of `gawk`.  Everything in it applies specifically to
`gawk` and not to other implementations.

&bull; [Compatibility Mode](#Compatibility-Mode):  How to disable certain `gawk`
                                extensions.
&bull; [Additions](#Additions):  Making Additions To `gawk`.
&bull; [Future Extensions](#Future-Extensions):  New features that may be implemented one day.
&bull; [Implementation Limitations](#Implementation-Limitations):  Some limitations of the implementation.
&bull; [Extension Design](#Extension-Design):  Design notes about the extension API.
&bull; [Old Extension Mechanism](#Old-Extension-Mechanism):  Some compatibility for old extensions.
&bull; [Notes summary](#Notes-summary):  Summary of implementation notes.

---

Next: [Additions](#Additions), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.1 Downward Compatibility and Debugging

See [POSIX/GNU](#POSIX_002fGNU),
for a summary of the GNU extensions to the `awk` language and program.
All of these features can be turned off by invoking `gawk` with the
--traditional option or with the --posix option.

If `gawk` is compiled for debugging with &lsquo;-DDEBUG&rsquo;, then there
is one more option available on the command line:

`-Y``--parsedebug`
Print out the parse stack information as the program is being parsed.

This option is intended only for serious `gawk` developers
and not for the casual user.  It probably has not even been compiled into
your version of `gawk`, since it slows down execution.

---

Next: [Future Extensions](#Future-Extensions), Previous: [Compatibility Mode](#Compatibility-Mode), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.2 Making Additions to `gawk`

If you find that you want to enhance `gawk` in a significant
fashion, you are perfectly free to do so.  That is the point of having
free software; the source code is available and you are free to change
it as you want (see [Copying](#Copying)).

This section discusses the ways you might want to change `gawk`
as well as any considerations you should bear in mind.

&bull; [Accessing The Source](#Accessing-The-Source):  Accessing the Git repository.
&bull; [Adding Code](#Adding-Code):  Adding code to the main body of
                                `gawk`.
&bull; [New Ports](#New-Ports):  Porting `gawk` to a new operating
                                system.
&bull; [Derived Files](#Derived-Files):  Why derived files are kept in the Git
                                repository.

---

Next: [Adding Code](#Adding-Code), Up: [Additions](#Additions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.2.1 Accessing The `gawk` Git Repository

As `gawk` is Free Software, the source code is always available.
[Gawk Distribution](#Gawk-Distribution) describes how to get and build the formal,
released versions of `gawk`.

However, if you want to modify `gawk` and contribute back your
changes, you will probably wish to work with the development version.
To do so, you will need to access the `gawk` source code
repository.  The code is maintained using the
[Git distributed version control system](https://git-scm.com).
You will need to install it if your system doesn&rsquo;t have it.
Once you have done so, use the command:

    git clone git://git.savannah.gnu.org/gawk.git
    

This clones the `gawk` repository.  If you are behind a
firewall that does not allow you to use the Git native protocol, you
can still access the repository using:

    git clone https://git.savannah.gnu.org/r/gawk.git
    

Once you have made changes, you can use &lsquo;git diff&rsquo; to produce a
patch, and send that to the `gawk` maintainer; see [Bugs](#Bugs),
for how to do that.

Once upon a time there was Git&ndash;CVS gateway for use by people who could
not install Git. However, this gateway no longer works, so you may have
better luck using a more modern version control system like Bazaar,
that has a Git plug-in for working with Git repositories.

---

Next: [New Ports](#New-Ports), Previous: [Accessing The Source](#Accessing-The-Source), Up: [Additions](#Additions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.2.2 Adding New Features

You are free to add any new features you like to `gawk`.
However, if you want your changes to be incorporated into the `gawk`
distribution, there are several steps that you need to take in order to
make it possible to include them:

1.  Before building the new feature into `gawk` itself,
consider writing it as an extension
(see [Dynamic Extensions](#Dynamic-Extensions)).
If that&rsquo;s not possible, continue with the rest of the steps in this list.

2.  Be prepared to sign the appropriate paperwork.
In order for the FSF to distribute your changes, you must either place
those changes in the public domain and submit a signed statement to that
effect, or assign the copyright in your changes to the FSF.
Both of these actions are easy to do and *many* people have done so
already. If you have questions, please contact me
(see [Bugs](#Bugs)),
or &ldquo;assign at gnu dot org&rdquo;.

3.  Get the latest version.
It is much easier for me to integrate changes if they are relative to
the most recent distributed version of `gawk`, or better yet,
relative to the latest code in the Git repository.  If your version of
`gawk` is very old, I may not be able to integrate your changes at all.
(See [Getting](#Getting),
for information on getting the latest version of `gawk`.)

4.  Follow the GNU Coding Standards.
This document describes how GNU software should be written. If you haven&rsquo;t
read it, please do so, preferably *before* starting to modify `gawk`.
(The GNU Coding Standards are available from
the GNU Project&rsquo;s
[website](https://www.gnu.org/prep/standards/).
Texinfo, Info, and DVI versions are also available.)

5. 
Use the `gawk` coding style.
The C code for `gawk` follows the instructions in the
GNU Coding Standards, with minor exceptions.  The code is formatted
using the traditional &ldquo;K&R&rdquo; style, particularly as regards to the placement
of braces and the use of TABs.  In brief, the coding rules for `gawk`
are as follows:

-  Use ANSI/ISO style (prototype) function headers when defining functions.

-  Put the name of the function at the beginning of its own line.

-  Use &lsquo;#elif&rsquo; instead of nesting &lsquo;#if&rsquo; inside &lsquo;#else&rsquo;.

-  Put the return type of the function, even if it is `int`, on the
line above the line with the name and arguments of the function.

-  Put spaces around parentheses used in control structures
(`if`, `while`, `for`, `do`, `switch`,
and `return`).

-  Do not put spaces in front of parentheses used in function calls.

-  Put spaces around all C operators and after commas in function calls.

-  Do not use the comma operator to produce multiple side effects, except
in `for` loop initialization and increment parts, and in macro bodies.

-  Use real TABs for indenting, not spaces.

-  Use the &ldquo;K&R&rdquo; brace layout style.

-  Use comparisons against `NULL` and `'\0'` in the conditions of
`if`, `while`, and `for` statements, as well as in the `case`s
of `switch` statements, instead of just the
plain pointer or character value.

-  Use `true` and `false` for `bool` values,
the `NULL` symbolic constant for pointer values,
and the character constant `'\0'` where appropriate, instead of `1`
and `0`.

-  Provide one-line descriptive comments for each function.

-  Do not use the `alloca()` function for allocating memory off the
stack.  Its use causes more portability trouble than is worth the minor
benefit of not having to free the storage. Instead, use `malloc()`
and `free()`.

-  Do not use comparisons of the form &lsquo;! strcmp(a, b)&rsquo; or similar.
As Henry Spencer once said, &ldquo;`strcmp()` is not a boolean!&rdquo;
Instead, use &lsquo;strcmp(a, b) == 0&rsquo;.

-  If adding new bit flag values, use explicit hexadecimal constants
(`0x001`, `0x002`, `0x004`, and so on) instead of
shifting one left by successive amounts (&lsquo;(1<<0)&rsquo;, &lsquo;(1<<1)&rsquo;,
and so on).

> NOTE: If I have to reformat your code to follow the coding style used in
> `gawk`, I may not bother to integrate your changes at all.

6. 
Update the documentation.
Along with your new code, please supply new sections and/or chapters
for this Web page.  If at all possible, please use real
Texinfo, instead of just supplying unformatted ASCII text (although
even that is better than no documentation at all).
Conventions to be followed in GAWK: Effective AWK Programming are provided
after the &lsquo;@bye&rsquo; at the end of the Texinfo source file.
If possible, please update the `man` page as well.

You will also have to sign paperwork for your documentation changes.

7. 
Submit changes as unified diffs.
Use &lsquo;diff -u -r -N&rsquo; to compare
the original `gawk` source tree with your version.
I recommend using the GNU version of `diff`, or best of all,
&lsquo;git diff&rsquo; or &lsquo;git format-patch&rsquo;.
Send the output produced by `diff` to me when you
submit your changes.
(See [Bugs](#Bugs), for the electronic mail
information.)

Using this format makes it easy for me to apply your changes to the
master version of the `gawk` source code (using `patch`).
If I have to apply the changes manually, using a text editor, I may
not do so, particularly if there are lots of changes.

8.  Include an entry for the ChangeLog file with your submission.
This helps further minimize the amount of work I have to do,
making it easier for me to accept patches.
It is simplest if you just make this part of your diff.

Although this sounds like a lot of work, please remember that while you
may write the new code, I have to maintain it and support it. If it
isn&rsquo;t possible for me to do that with a minimum of extra work, then I
probably will not.

---

Next: [Derived Files](#Derived-Files), Previous: [Adding Code](#Adding-Code), Up: [Additions](#Additions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.2.3 Porting `gawk` to a New Operating System

If you want to port `gawk` to a new operating system, there are
several steps:

1.  Follow the guidelines in
the previous section
concerning coding style, submission of diffs, and so on.

2.  Be prepared to sign the appropriate paperwork.
In order for the FSF to distribute your code, you must either place
your code in the public domain and submit a signed statement to that
effect, or assign the copyright in your code to the FSF.
Both of these actions are easy to do and *many* people have done so
already. If you have questions, please contact me, or
[gnu@gnu.org](mailto:gnu@gnu.org).

3.  When doing a port, bear in mind that your code must coexist peacefully
with the rest of `gawk` and the other ports. Avoid gratuitous
changes to the system-independent parts of the code. If at all possible,
avoid sprinkling &lsquo;#ifdef&rsquo;s just for your port throughout the
code.

If the changes needed for a particular system affect too much of the
code, I probably will not accept them.  In such a case, you can, of course,
distribute your changes on your own, as long as you comply
with the GPL
(see [Copying](#Copying)).

4.  A number of the files that come with `gawk` are maintained by other
people.  Thus, you should not change them
unless it is for a very good reason; i.e., changes are not out of the
question, but changes to these files are scrutinized extra carefully.
The files are
dfa.c,
dfa.h,
getopt.c,
getopt.h,
getopt1.c,
getopt_int.h,
gettext.h,
regcomp.c,
regex.c,
regex.h,
regex_internal.c,
regex_internal.h,
and
regexec.c.

5.  A number of other files are provided by the GNU
Autotools (Autoconf, Automake, and GNU `gettext`).
You should not change them either, unless it is for a very
good reason. The files are
ABOUT-NLS,
config.guess,
config.rpath,
config.sub,
depcomp,
INSTALL,
install-sh,
missing,
mkinstalldirs,
xalloc.h,
and
ylwrap.

6.  Be willing to continue to maintain the port.
Non-Unix operating systems are supported by volunteers who maintain
the code needed to compile and run `gawk` on their systems. If no-one
volunteers to maintain a port, it becomes unsupported and it may
be necessary to remove it from the distribution.

7.  Supply an appropriate gawkmisc.??? file.
Each port has its own gawkmisc.??? that implements certain
operating system specific functions. This is cleaner than a plethora of
&lsquo;#ifdef&rsquo;s scattered throughout the code.  The gawkmisc.c in
the main source directory includes the appropriate
gawkmisc.??? file from each subdirectory.
Be sure to update it as well.

Each port&rsquo;s gawkmisc.??? file has a suffix reminiscent of the machine
or operating system for the port&mdash;for example, pc/gawkmisc.pc and
vms/gawkmisc.vms. The use of separate suffixes, instead of plain
gawkmisc.c, makes it possible to move files from a port&rsquo;s subdirectory
into the main subdirectory, without accidentally destroying the real
gawkmisc.c file.  (Currently, this is only an issue for the
PC operating system ports.)

8.  Supply a Makefile as well as any other C source and header files that are
necessary for your operating system.  All your code should be in a
separate subdirectory, with a name that is the same as, or reminiscent
of, either your operating system or the computer system.  If possible,
try to structure things so that it is not necessary to move files out
of the subdirectory into the main source directory.  If that is not
possible, then be sure to avoid using names for your files that
duplicate the names of files in the main source directory.

9.  Update the documentation.
Please write a section (or sections) for this Web page describing the
installation and compilation steps needed to compile and/or install
`gawk` for your system.

Following these steps makes it much easier to integrate your changes
into `gawk` and have them coexist happily with other
operating systems&rsquo; code that is already there.

In the code that you supply and maintain, feel free to use a
coding style and brace layout that suits your taste.

---

Previous: [New Ports](#New-Ports), Up: [Additions](#Additions)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.2.4 Why Generated Files Are Kept In Git

If you look at the `gawk` source in the Git
repository, you will notice that it includes files that are automatically
generated by GNU infrastructure tools, such as Makefile.in from
Automake and even configure from Autoconf.

This is different from many Free Software projects that do not store
the derived files, because that keeps the repository less cluttered,
and it is easier to see the substantive changes when comparing versions
and trying to understand what changed between commits.

However, there are several reasons why the `gawk` maintainer
likes to have everything in the repository.

First, because it is then easy to reproduce any given version completely,
without relying upon the availability of (older, likely obsolete, and
maybe even impossible to find) other tools.

As an extreme example, if you ever even think about trying to compile,
oh, say, the V7 `awk`, you will discover that not only do you
have to bootstrap the V7 `yacc` to do so, but you also need the
V7 `lex`.  And the latter is pretty much impossible to bring up
on a modern GNU/Linux system.[115](#FOOT115)

(Or, let&rsquo;s say `gawk` 1.2 required `bison` whatever-it-was
in 1989 and that there was no awkgram.c file in the repository.  Is
there a guarantee that we could find that `bison` version? Or that
*it* would build?)

If the repository has all the generated files, then it&rsquo;s easy to just check
them out and build. (Or *easier*, depending upon how far back we go.)

And that brings us to the second (and stronger) reason why all the files
really need to be in Git.  It boils down to who do you cater
to&mdash;the `gawk` developer(s), or the user who just wants to check
out a version and try it out?

The `gawk` maintainer
wants it to be possible for any interested `awk` user in the
world to just clone the repository, check out the branch of interest and
build it. Without their having to have the correct version(s) of the
autotools.[116](#FOOT116)
That is the point of the bootstrap.sh file.  It touches the
various other files in the right order such that

    # The canonical incantation for building GNU software:
    ./bootstrap.sh && ./configure && make
    

will *just work*.

This is extremely important for the `master` and
`gawk-X.Y-stable` branches.

Further, the `gawk` maintainer would argue that it&rsquo;s also
important for the `gawk` developers. When he tried to check out
the `xgawk` branch[117](#FOOT117) to build it, he
couldn&rsquo;t. (No ltmain.sh file, and he had no idea how to create it,
and that was not the only problem.)

He felt *extremely* frustrated.  With respect to that branch,
the maintainer is no different than Jane User who wants to try to build
`gawk-4.1-stable` or `master` from the repository.

Thus, the maintainer thinks that it&rsquo;s not just important, but critical,
that for any given branch, the above incantation *just works*.

A third reason to have all the files is that without them, using &lsquo;git
bisect&rsquo; to try to find the commit that introduced a bug is exceedingly
difficult. The maintainer tried to do that on another project that
requires running bootstrapping scripts just to create `configure`
and so on; it was really painful. When the repository is self-contained,
using `git bisect` in it is very easy.

What are some of the consequences and/or actions to take?

1.  We don&rsquo;t mind that there are differing files in the different branches
as a result of different versions of the autotools.

1.  It&rsquo;s the maintainer&rsquo;s job to merge them and he will deal with it.

2.  He is really good at &lsquo;git diff x y > /tmp/diff1 ; gvim /tmp/diff1&rsquo; to
remove the diffs that aren&rsquo;t of interest in order to review code.

2.  It would certainly help if everyone used the same versions of the GNU tools
as he does, which in general are the latest released versions of
Automake,
Autoconf,
`bison`,
and
GNU `gettext`.

Installing from source is quite easy. It&rsquo;s how the maintainer worked for years
(and still works).
He had /usr/local/bin at the front of his `PATH` and just did:

    wget https://ftp.gnu.org/gnu/package/package-x.y.z.tar.gz
    tar -xpzvf package-x.y.z.tar.gz
    cd package-x.y.z
    ./configure && make && make check
    make install    # as root
    

> NOTE: Because of the &lsquo;https://&rsquo; URL, you may have to supply the
> --no-check-certificate option to `wget` to download
> the file.

Most of the above was originally written by the maintainer to other
`gawk` developers.  It raised the objection from one of
the developers &ldquo;&hellip; that anybody pulling down the source from
Git is not an end user.&rdquo;

However, this is not true. There are &ldquo;power `awk` users&rdquo;
who can build `gawk` (using the magic incantation shown previously)
but who can&rsquo;t program in C.  Thus, the major branches should be
kept buildable all the time.

It was then suggested that there be a `cron` job to create
nightly tarballs of &ldquo;the source.&rdquo;  Here, the problem is that there
are source trees, corresponding to the various branches! So,
nightly tarballs aren&rsquo;t the answer, especially as the repository can go
for weeks without significant change being introduced.

Fortunately, the Git server can meet this need. For any given
branch named branchname, use:

    wget https://git.savannah.gnu.org/cgit/gawk.git/snapshot/gawk-branchname.tar.gz
    

to retrieve a snapshot of the given branch.

---

Next: [Implementation Limitations](#Implementation-Limitations), Previous: [Additions](#Additions), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.3 Probable Future Extensions

> AWK is a language similar to PERL, only considerably more elegant.

&mdash; *Arnold Robbins*

> Hey!

&mdash; *Larry Wall*

The TODO file in the `master` branch of the `gawk`
Git repository lists possible future enhancements.  Some of these relate
to the source code, and others to possible new features.  Please see
that file for the list.
See [Additions](#Additions),
if you are interested in tackling any of the projects listed there.

---

Next: [Extension Design](#Extension-Design), Previous: [Future Extensions](#Future-Extensions), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.4 Some Limitations of the Implementation

This following table describes limits of `gawk` on a Unix-like
system (although it is variable even then). Other systems may have
different limits.

ItemLimitCharacters in a character class2^(number of bits per byte)Length of input record`MAX_INT`Length of output recordUnlimitedLength of source lineUnlimitedNumber of fields in a record`MAX_LONG`Number of file redirectionsUnlimitedNumber of input records in one file`MAX_LONG`Number of input records total`MAX_LONG`Number of pipe redirectionsmin(number of processes per user, number of open files)Numeric valuesDouble-precision floating point (if not using MPFR)Size of a field`MAX_INT`Size of a literal string`MAX_INT`Size of a printf string`MAX_INT`
---

Next: [Old Extension Mechanism](#Old-Extension-Mechanism), Previous: [Implementation Limitations](#Implementation-Limitations), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.5 Extension API Design

This section documents the design of the extension API,
including a discussion of some of the history and problems that needed
to be solved.

The first version of extensions for `gawk` was developed in
the mid-1990s and released with `gawk` 3.1 in the late 1990s.
The basic mechanisms and design remained unchanged for close to 15 years,
until 2012.

The old extension mechanism used data types and functions from
`gawk` itself, with a &ldquo;clever hack&rdquo; to install extension
functions.

`gawk` included some sample extensions, of which a few were
really useful.  However, it was clear from the outset that the extension
mechanism was bolted onto the side and was not really well thought out.

&bull; [Old Extension Problems](#Old-Extension-Problems):  Problems with the old mechanism.
&bull; [Extension New Mechanism Goals](#Extension-New-Mechanism-Goals):  Goals for the new mechanism.
&bull; [Extension Other Design Decisions](#Extension-Other-Design-Decisions):  Some other design decisions.
&bull; [Extension Future Growth](#Extension-Future-Growth):  Some room for future growth.

---

Next: [Extension New Mechanism Goals](#Extension-New-Mechanism-Goals), Up: [Extension Design](#Extension-Design)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.5.1 Problems With The Old Mechanism

The old extension mechanism had several problems:

-  It depended heavily upon `gawk` internals.  Any time the
`NODE` structure[118](#FOOT118) changed, an extension would have to be
recompiled. Furthermore, to really write extensions required understanding
something about `gawk`&rsquo;s internal functions.  There was some
documentation in this Web page, but it was quite minimal.

-  Being able to call into `gawk` from an extension required linker
facilities that are common on Unix-derived systems but that did
not work on MS-Windows systems; users wanting extensions on MS-Windows
had to statically link them into `gawk`, even though MS-Windows supports
dynamic loading of shared objects.

-  The API would change occasionally as `gawk` changed; no compatibility
between versions was ever offered or planned for.

Despite the drawbacks, the `xgawk` project developers forked
`gawk` and developed several significant extensions. They also
enhanced `gawk`&rsquo;s facilities relating to file inclusion and
shared object access.

A new API was desired for a long time, but only in 2012 did the
`gawk` maintainer and the `xgawk` developers finally
start working on it together.  More information about the `xgawk`
project is provided in [gawkextlib](#gawkextlib).

---

Next: [Extension Other Design Decisions](#Extension-Other-Design-Decisions), Previous: [Old Extension Problems](#Old-Extension-Problems), Up: [Extension Design](#Extension-Design)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.5.2 Goals For A New Mechanism

Some goals for the new API were:

-  The API should be independent of `gawk` internals.  Changes in
`gawk` internals should not be visible to the writer of an
extension function.

-  The API should provide *binary* compatibility across `gawk`
releases as long as the API itself does not change.

-  The API should enable extensions written in C or C++ to have roughly the
same &ldquo;appearance&rdquo; to `awk`-level code as `awk`
functions do. This means that extensions should have:

- - The ability to access function parameters.

- - The ability to turn an undefined parameter into an array (call by reference).

- - The ability to create, access and update global variables.

- - Easy access to all the elements of an array at once (&ldquo;array flattening&rdquo;)
in order to loop over all the element in an easy fashion for C code.

- - The ability to create arrays (including `gawk`&rsquo;s true
arrays of arrays).

Some additional important goals were:

-  The API should use only features in ISO C 90, so that extensions
can be written using the widest range of C and C++ compilers. The header
should include the appropriate &lsquo;#ifdef __cplusplus&rsquo; and &lsquo;extern "C"&rsquo;
magic so that a C++ compiler could be used.  (If using C++, the runtime
system has to be smart enough to call any constructors and destructors,
as `gawk` is a C program. As of this writing, this has not been
tested.)

-  The API mechanism should not require access to `gawk`&rsquo;s
symbols[119](#FOOT119) by the compile-time or dynamic linker,
in order to enable creation of extensions that also work on MS-Windows.

During development, it became clear that there were other features
that should be available to extensions, which were also subsequently
provided:

-  Extensions should have the ability to hook into `gawk`&rsquo;s
I/O redirection mechanism.  In particular, the `xgawk`
developers provided a so-called &ldquo;open hook&rdquo; to take over reading
records.  During development, this was generalized to allow
extensions to hook into input processing, output processing, and
two-way I/O.

-  An extension should be able to provide a &ldquo;call back&rdquo; function
to perform cleanup actions when `gawk` exits.

-  An extension should be able to provide a version string so that
`gawk`&rsquo;s --version option can provide information
about extensions as well.

The requirement to avoid access to `gawk`&rsquo;s symbols is, at first
glance, a difficult one to meet.

One design, apparently used by Perl and Ruby and maybe others, would
be to make the mainline `gawk` code into a library, with the
`gawk` utility a small C `main()` function linked against
the library.

This seemed like the tail wagging the dog, complicating build and
installation and making a simple copy of the `gawk` executable
from one system to another (or one place to another on the same
system!) into a chancy operation.

Pat Rankin suggested the solution that was adopted.
See [Extension Mechanism Outline](#Extension-Mechanism-Outline), for the details.

---

Next: [Extension Future Growth](#Extension-Future-Growth), Previous: [Extension New Mechanism Goals](#Extension-New-Mechanism-Goals), Up: [Extension Design](#Extension-Design)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.5.3 Other Design Decisions

As an arbitrary design decision, extensions can read the values of
predefined variables and arrays (such as `ARGV` and `FS`), but cannot
change them, with the exception of `PROCINFO`.

The reason for this is to prevent an extension function from affecting
the flow of an `awk` program outside its control.  While a real
`awk` function can do what it likes, that is at the discretion
of the programmer.  An extension function should provide a service or
make a C API available for use within `awk`, and not mess with
`FS` or `ARGC` and `ARGV`.

In addition, it becomes easy to start down a slippery slope. How
much access to `gawk` facilities do extensions need?
Do they need `getline`?  What about calling `gsub()` or
compiling regular expressions?  What about calling into `awk`
functions? (*That* would be messy.)

In order to avoid these issues, the `gawk` developers chose
to start with the simplest, most basic features that are still truly useful.

Another decision is that although `gawk` provides nice things like
MPFR, and arrays indexed internally by integers, these features are not
being brought out to the API in order to keep things simple and close to
traditional `awk` semantics.  (In fact, arrays indexed internally
by integers are so transparent that they aren&rsquo;t even documented!)

Additionally, all functions in the API check that their pointer
input parameters are not `NULL`. If they are, they return an error.
(It is a good idea for extension code to verify that
pointers received from `gawk` are not `NULL`.
Such a thing should not happen, but the `gawk` developers
are only human, and they have been known to occasionally make
mistakes.)

With time, the API will undoubtedly evolve; the `gawk` developers
expect this to be driven by user needs. For now, the current API seems
to provide a minimal yet powerful set of features for creating extensions.

---

Previous: [Extension Other Design Decisions](#Extension-Other-Design-Decisions), Up: [Extension Design](#Extension-Design)   [[Contents](#SEC_Contents)][[Index](#Index)]

#### C.5.4 Room For Future Growth

The API can later be expanded, in two ways:

- `gawk` passes an &ldquo;extension id&rdquo; into the extension when it
first loads the extension.  The extension then passes this id back
to `gawk` with each function call.  This mechanism allows
`gawk` to identify the extension calling into it, should it need
to know.

-  Similarly, the extension passes a &ldquo;name space&rdquo; into `gawk`
when it registers each extension function.  This accommodates a possible future
mechanism for grouping extension functions and possibly avoiding name
conflicts.

Of course, as of this writing, no decisions have been made with respect
to any of the above.

---

Next: [Notes summary](#Notes-summary), Previous: [Extension Design](#Extension-Design), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.6 Compatibility For Old Extensions

[Dynamic Extensions](#Dynamic-Extensions), describes the supported API and mechanisms
for writing extensions for `gawk`.  This API was introduced
in version 4.1.  However, for many years `gawk`
provided an extension mechanism that required knowledge of `gawk`
internals and that was not as well designed.

In order to provide a transition period, `gawk` version 4.1
continues to support the original extension mechanism.
This will be true for the life of exactly one major release.  This support
will be withdrawn, and removed from the source code, at the next major
release.

Briefly, original-style extensions should be compiled by including the
awk.h header file in the extension source code. Additionally,
you must define the identifier &lsquo;GAWK&rsquo; when building (use
&lsquo;-DGAWK&rsquo; with Unix-style compilers).  Otherwise, the definitions
in gawkapi.h will cause conflicts with those in awk.h
and your extension will not compile.

Just as in previous versions, you load an old-style extension with the
`extension()` built-in function (which is not otherwise documented).
This function in turn finds and loads the shared object file containing
the extension and calls its `dl_load()` C routine.

Because original-style and new-style extensions use different initialization
routines (`dl_load()` versus `dlload()`), they may safely
be installed in the same directory (to be found by `AWKLIBPATH`)
without conflict.

The `gawk` development team strongly recommends that you
convert any old extensions that you may have to use the new API
described in [Dynamic Extensions](#Dynamic-Extensions).

---

Previous: [Old Extension Mechanism](#Old-Extension-Mechanism), Up: [Notes](#Notes)   [[Contents](#SEC_Contents)][[Index](#Index)]

### C.7 Summary

- `gawk`&rsquo;s extensions can be disabled with either the
--traditional option or with the --posix option.
The --parsedebug option is available if `gawk` is
compiled with &lsquo;-DDEBUG&rsquo;.

-  The source code for `gawk` is maintained in a publicly
accessible Git repository. Anyone may check it out and view the source.

-  Contributions to `gawk` are welcome. Following the steps
outlined in this chapter will make it easier to integrate
your contributions into the code base.
This applies both to new feature contributions and to ports to
additional operating systems.

- `gawk` has some limits&mdash;generally those that are imposed by
the machine architecture.

-  The extension API design was intended to solve a number of problems
with the previous extension mechanism, enable features needed by
the `xgawk` project, and provide binary compatibility going forward.

-  The previous extension mechanism is still supported in version 4.1
of `gawk`, but it *will* be removed in the next major release.

---

Next: [Glossary](#Glossary), Previous: [Notes](#Notes), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## Appendix D Basic Programming Concepts

This appendix attempts to define some of the basic concepts
and terms that are used throughout the rest of this Web page.
As this Web page is specifically about `awk`,
and not about computer programming in general, the coverage here
is by necessity fairly cursory and simplistic.
(If you need more background, there are many
other introductory texts that you should refer to instead.)

&bull; [Basic High Level](#Basic-High-Level):  The high level view.
&bull; [Basic Data Typing](#Basic-Data-Typing):  A very quick intro to data types.

---

Next: [Basic Data Typing](#Basic-Data-Typing), Up: [Basic Concepts](#Basic-Concepts)   [[Contents](#SEC_Contents)][[Index](#Index)]

### D.1 What a Program Does

At the most basic level, the job of a program is to process
some input data and produce results.
See [Figure D.1](#figure_002dgeneral_002dflow).

![General program flow](general-program.png)

**Figure D.1: **General Program Flow

The &ldquo;program&rdquo; in the figure can be either a compiled
program[120](#FOOT120)
(such as `ls`),
or it may be *interpreted*.  In the latter case, a machine-executable
program such as `awk` reads your program, and then uses the
instructions in your program to process the data.

When you write a program, it usually consists
of the following, very basic set of steps,
as shown in [Figure D.2](#figure_002dprocess_002dflow):

![Basic Program Stages](process-flow.png)

**Figure D.2: **Basic Program Steps

Initialization
These are the things you do before actually starting to process
data, such as checking arguments, initializing any data you need
to work with, and so on.
This step corresponds to `awk`&rsquo;s `BEGIN` rule
(see [BEGIN/END](#BEGIN_002fEND)).

If you were baking a cake, this might consist of laying out all the
mixing bowls and the baking pan, and making sure you have all the
ingredients that you need.

Processing
This is where the actual work is done.  Your program reads data,
one logical chunk at a time, and processes it as appropriate.

In most programming languages, you have to manually manage the reading
of data, checking to see if there is more each time you read a chunk.
`awk`&rsquo;s pattern-action paradigm
(see [Getting Started](#Getting-Started))
handles the mechanics of this for you.

In baking a cake, the processing corresponds to the actual labor:
breaking eggs, mixing the flour, water, and other ingredients, and then putting the cake
into the oven.

Clean Up
Once you&rsquo;ve processed all the data, you may have things you need to
do before exiting.
This step corresponds to `awk`&rsquo;s `END` rule
(see [BEGIN/END](#BEGIN_002fEND)).

After the cake comes out of the oven, you still have to wrap it in
plastic wrap to keep anyone from tasting it, as well as wash
the mixing bowls and utensils.

An *algorithm* is a detailed set of instructions necessary to accomplish
a task, or process data.  It is much the same as a recipe for baking
a cake.  Programs implement algorithms.  Often, it is up to you to design
the algorithm and implement it, simultaneously.

The &ldquo;logical chunks&rdquo; we talked about previously are called *records*,
similar to the records a company keeps on employees, a school keeps for
students, or a doctor keeps for patients.
Each record has many component parts, such as first and last names,
date of birth, address, and so on.  The component parts are referred
to as the *fields* of the record.

The act of reading data is termed *input*, and that of
generating results, not too surprisingly, is termed *output*.
They are often referred to together as &ldquo;input/output,&rdquo;
and even more often, as &ldquo;I/O&rdquo; for short.
(You will also see &ldquo;input&rdquo; and &ldquo;output&rdquo; used as verbs.)

`awk` manages the reading of data for you, as well as the
breaking it up into records and fields.  Your program&rsquo;s job is to
tell `awk` what to do with the data.  You do this by describing
*patterns* in the data to look for, and *actions* to execute
when those patterns are seen.  This *data-driven* nature of
`awk` programs usually makes them both easier to write
and easier to read.

---

Previous: [Basic High Level](#Basic-High-Level), Up: [Basic Concepts](#Basic-Concepts)   [[Contents](#SEC_Contents)][[Index](#Index)]

### D.2 Data Values in a Computer

In a program,
you keep track of information and values in things called *variables*.
A variable is just a name for a given value, such as `first_name`,
`last_name`, `address`, and so on.
`awk` has several predefined variables, and it has
special names to refer to the current input record
and the fields of the record.
You may also group multiple
associated values under one name, as an array.

Data, particularly in `awk`, consists of either numeric
values, such as 42 or 3.1415927, or string values.
String values are essentially anything that&rsquo;s not a number, such as a name.
Strings are sometimes referred to as *character data*, since they
store the individual characters that comprise them.
Individual variables, as well as numeric and string variables, are
referred to as *scalar* values.
Groups of values, such as arrays, are not scalars.

[Computer Arithmetic](#Computer-Arithmetic), provided a basic introduction to numeric
types (integer and floating-point) and how they are used in a computer.
Please review that information, including a number of caveats that
were presented.

While you are probably used to the idea of a number without a value (i.e., zero),
it takes a bit more getting used to the idea of zero-length character data.
Nevertheless, such a thing exists.
It is called the *null string*.
The null string is character data that has no value.
In other words, it is empty.  It is written in `awk` programs
like this: `""`.

Humans are used to working in decimal; i.e., base 10.  In base 10,
numbers go from 0 to 9, and then &ldquo;roll over&rdquo; into the next
column.  (Remember grade school? 42 = 4 x 10 + 2.)

There are other number bases though.  Computers commonly use base 2
or *binary*, base 8 or *octal*, and base 16 or *hexadecimal*.
In binary, each column represents two times the value in the column to
its right. Each column may contain either a 0 or a 1.
Thus, binary 1010 represents (1 x 8) + (0 x 4) + (1 x 2)
+ (0 x 1), or decimal 10.
Octal and hexadecimal are discussed more in
[Nondecimal-numbers](#Nondecimal_002dnumbers).

At the very lowest level, computers store values as groups of binary digits,
or *bits*.  Modern computers group bits into groups of eight, called *bytes*.
Advanced applications sometimes have to manipulate bits directly,
and `gawk` provides functions for doing so.

Programs are written in programming languages.
Hundreds, if not thousands, of programming languages exist.
One of the most popular is the C programming language.
The C language had a very strong influence on the design of
the `awk` language.

There have been several versions of C.  The first is often referred to
as &ldquo;K&R&rdquo; C, after the initials of Brian Kernighan and Dennis Ritchie,
the authors of the first book on C.  (Dennis Ritchie created the language,
and Brian Kernighan was one of the creators of `awk`.)

In the mid-1980s, an effort began to produce an international standard
for C.  This work culminated in 1989, with the production of the ANSI
standard for C.  This standard became an ISO standard in 1990.
In 1999, a revised ISO C standard was approved and released.
Where it makes sense, POSIX `awk` is compatible with 1999 ISO C.

---

Next: [Copying](#Copying), Previous: [Basic Concepts](#Basic-Concepts), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## Glossary
Action
A series of `awk` statements attached to a rule.  If the rule&rsquo;s
pattern matches an input record, `awk` executes the
rule&rsquo;s action.  Actions are always enclosed in braces.
(See [Action Overview](#Action-Overview).)

Ada
A programming language originally defined by the U.S. Department of
Defense for embedded programming. It was designed to enforce good
Software Engineering practices.

Amazing `awk` Assembler
Henry Spencer at the University of Toronto wrote a retargetable assembler
completely as `sed` and `awk` scripts.  It is thousands
of lines long, including machine descriptions for several eight-bit
microcomputers.  It is a good example of a program that would have been
better written in another language.

Amazingly Workable Formatter (`awf`)
Henry Spencer at the University of Toronto wrote a formatter that accepts
a large subset of the &lsquo;nroff -ms&rsquo; and &lsquo;nroff -man&rsquo; formatting
commands, using `awk` and `sh`.

Anchor
The regexp metacharacters &lsquo;^&rsquo; and &lsquo;$&rsquo;, which force the match
to the beginning or end of the string, respectively.

ANSI
The American National Standards Institute.  This organization produces
many standards, among them the standards for the C and C++ programming
languages.
These standards often become international standards as well. See also
&ldquo;ISO.&rdquo;

Argument
An argument can be two different things.  It can be an option or a
file name passed to a command while invoking it from the command line, or
it can be something passed to a *function* inside a program, e.g.
inside `awk`.

In the latter case, an argument can be passed to a function in two ways.
Either it is given to the called function by value, i.e., a copy of the
value of the variable is made available to the called function, but the
original variable cannot be modified by the function itself; or it is
given by reference, i.e., a pointer to the interested variable is passed to
the function, which can then directly modify it. In `awk`
scalars are passed by value, and arrays are passed by reference.
See &ldquo;Pass By Value/Reference.&rdquo;

Array
A grouping of multiple values under the same name.
Most languages just provide sequential arrays.
`awk` provides associative arrays.

Assertion
A statement in a program that a condition is true at this point in the program.
Useful for reasoning about how a program is supposed to behave.

Assignment
An `awk` expression that changes the value of some `awk`
variable or data object.  An object that you can assign to is called an
*lvalue*.  The assigned values are called *rvalues*.
See [Assignment Ops](#Assignment-Ops).

Associative Array
Arrays in which the indices may be numbers or strings, not just
sequential integers in a fixed range.

`awk` Language
The language in which `awk` programs are written.

`awk` Program
An `awk` program consists of a series of *patterns* and
*actions*, collectively known as *rules*.  For each input record
given to the program, the program&rsquo;s rules are all processed in turn.
`awk` programs may also contain function definitions.

`awk` Script
Another name for an `awk` program.

Bash
The GNU version of the standard shell
(the Bourne-Again SHell).
See also &ldquo;Bourne Shell.&rdquo;

Binary
Base-two notation, where the digits are `0`&ndash;`1`. Since
electronic circuitry works &ldquo;naturally&rdquo; in base 2 (just think of Off/On),
everything inside a computer is calculated using base 2. Each digit
represents the presence (or absence) of a power of 2 and is called a
*bit*. So, for example, the base-two number `10101` is
the same as decimal 21, ((1 x 16) + (1 x 4) + (1 x 1)).

Since base-two numbers quickly become
very long to read and write, they are usually grouped by 3 (i.e., they are
read as octal numbers), or by 4 (i.e., they are read as hexadecimal
numbers). There is no direct way to insert base 2 numbers in a C program.
If need arises, such numbers are usually inserted as octal or hexadecimal
numbers. The number of base-two digits that fit into registers used for
representing integer numbers in computers is a rough indication of the
computing power of the computer itself.  Most computers nowadays use 64
bits for representing integer numbers in their registers, but 32-bit,
16-bit and 8-bit registers have been widely used in the past.
See [Nondecimal-numbers](#Nondecimal_002dnumbers).

Bit
Short for &ldquo;Binary Digit.&rdquo;
All values in computer memory ultimately reduce to binary digits: values
that are either zero or one.
Groups of bits may be interpreted differently&mdash;as integers,
floating-point numbers, character data, addresses of other
memory objects, or other data.
`awk` lets you work with floating-point numbers and strings.
`gawk` lets you manipulate bit values with the built-in
functions described in
[Bitwise Functions](#Bitwise-Functions).

Computers are often defined by how many bits they use to represent integer
values.  Typical systems are 32-bit systems, but 64-bit systems are
becoming increasingly popular, and 16-bit systems have essentially
disappeared.

Boolean Expression
Named after the English mathematician Boole. See also &ldquo;Logical Expression.&rdquo;

Bourne Shell
The standard shell (/bin/sh) on Unix and Unix-like systems,
originally written by Steven R. Bourne at Bell Laboratories.
Many shells (Bash, `ksh`, `pdksh`, `zsh`) are
generally upwardly compatible with the Bourne shell.

Braces
The characters &lsquo;{&rsquo; and &lsquo;}&rsquo;.  Braces are used in
`awk` for delimiting actions, compound statements, and function
bodies.

Bracket Expression
Inside a *regular expression*, an expression included in square
brackets, meant to designate a single character as belonging to a
specified character class. A bracket expression can contain a list of one
or more characters, like &lsquo;[abc]&rsquo;, a range of characters, like
&lsquo;[A-Z]&rsquo;, or a name, delimited by &lsquo;:&rsquo;, that designates a known set
of characters, like &lsquo;[:digit:]&rsquo;. The form of bracket expression
enclosed between &lsquo;:&rsquo; is independent of the underlying representation
of the character themselves, which could utilize the ASCII, EBCDIC, or
Unicode codesets, depending on the architecture of the computer system, and on
localization.
See also &ldquo;Regular Expression.&rdquo;

Built-in Function
The `awk` language provides built-in functions that perform various
numerical, I/O-related, and string computations.  Examples are
`sqrt()` (for the square root of a number) and `substr()` (for a
substring of a string).
`gawk` provides functions for timestamp management, bit manipulation,
array sorting, type checking,
and runtime string translation.
(See [Built-in](#Built_002din).)

Built-in Variable
`ARGC`,
`ARGV`,
`CONVFMT`,
`ENVIRON`,
`FILENAME`,
`FNR`,
`FS`,
`NF`,
`NR`,
`OFMT`,
`OFS`,
`ORS`,
`RLENGTH`,
`RSTART`,
`RS`,
and
`SUBSEP`
are the variables that have special meaning to `awk`.
In addition,
`ARGIND`,
`BINMODE`,
`ERRNO`,
`FIELDWIDTHS`,
`FPAT`,
`IGNORECASE`,
`LINT`,
`PROCINFO`,
`RT`,
and
`TEXTDOMAIN`
are the variables that have special meaning to `gawk`.
Changing some of them affects `awk`&rsquo;s running environment.
(See [Built-in Variables](#Built_002din-Variables).)

C
The system programming language that most GNU software is written in.  The
`awk` programming language has C-like syntax, and this Web page
points out similarities between `awk` and C when appropriate.

In general, `gawk` attempts to be as similar to the 1990 version
of ISO C as makes sense.

C Shell
The C Shell (`csh` or its improved version, `tcsh`) is a Unix shell that was
created by Bill Joy in the late 1970s. The C shell was differentiated from
other shells by its interactive features and overall style, which
looks more like C. The C Shell is not backward compatible with the Bourne
Shell, so special attention is required when converting scripts
written for other Unix shells to the C shell, especially with regard to the management of
shell variables.
See also &ldquo;Bourne Shell.&rdquo;

C++
A popular object-oriented programming language derived from C.

Character Class
See &ldquo;Bracket Expression.&rdquo;

Character List
See &ldquo;Bracket Expression.&rdquo;

Character Set
The set of numeric codes used by a computer system to represent the
characters (letters, numbers, punctuation, etc.) of a particular country
or place. The most common character set in use today is ASCII (American
Standard Code for Information Interchange).  Many European
countries use an extension of ASCII known as ISO-8859-1 (ISO Latin-1).
The [Unicode character set](http://www.unicode.org) is
increasingly popular and standard, and is particularly
widely used on GNU/Linux systems.

CHEM
A preprocessor for `pic` that reads descriptions of molecules
and produces `pic` input for drawing them.
It was written in `awk`
by Brian Kernighan and Jon Bentley, and is available from
[http://netlib.org/typesetting/chem](http://netlib.org/typesetting/chem).

Comparison Expression
A relation that is either true or false, such as &lsquo;a < b&rsquo;.
Comparison expressions are used in `if`, `while`, `do`,
and `for`
statements, and in patterns to select which input records to process.
(See [Typing and Comparison](#Typing-and-Comparison).)

Compiler
A program that translates human-readable source code into
machine-executable object code.  The object code is then executed
directly by the computer.
See also &ldquo;Interpreter.&rdquo;

Complemented Bracket Expression
The negation of a *bracket expression*.  All that is *not*
described by a given bracket expression. The symbol &lsquo;^&rsquo; precedes
the negated bracket expression.  E.g.: &lsquo;[^[:digit:]]&rsquo;
designates whatever character is not a digit. &lsquo;[^bad]&rsquo;
designates whatever character is not one of the letters &lsquo;b&rsquo;, &lsquo;a&rsquo;,
or &lsquo;d&rsquo;.
See &ldquo;Bracket Expression.&rdquo;

Compound Statement
A series of `awk` statements, enclosed in curly braces.  Compound
statements may be nested.
(See [Statements](#Statements).)

Computed Regexps
See &ldquo;Dynamic Regular Expressions.&rdquo;

Concatenation
Concatenating two strings means sticking them together, one after another,
producing a new string.  For example, the string &lsquo;foo&rsquo; concatenated with
the string &lsquo;bar&rsquo; gives the string &lsquo;foobar&rsquo;.
(See [Concatenation](#Concatenation).)

Conditional Expression
An expression using the &lsquo;?:&rsquo; ternary operator, such as
&lsquo;expr1 ? expr2 : expr3&rsquo;.  The expression
expr1 is evaluated; if the result is true, the value of the whole
expression is the value of expr2; otherwise the value is
expr3.  In either case, only one of expr2 and expr3
is evaluated. (See [Conditional Exp](#Conditional-Exp).)

Control Statement
A control statement is an instruction to perform a given operation or a set
of operations inside an `awk` program, if a given condition is
true. Control statements are: `if`, `for`, `while`, and
`do`
(see [Statements](#Statements)).

Cookie
A peculiar goodie, token, saying or remembrance
produced by or presented to a program. (With thanks to Professor Doug McIlroy.)

Coprocess
A subordinate program with which two-way communications is possible.

Curly Braces
See &ldquo;Braces.&rdquo;

Dark Corner
An area in the language where specifications often were (or still
are) not clear, leading to unexpected or undesirable behavior.
Such areas are marked in this Web page with
&ldquo;(d.c.)&rdquo; in the text
and are indexed under the heading &ldquo;dark corner.&rdquo;

Data Driven
A description of `awk` programs, where you specify the data you
are interested in processing, and what to do when that data is seen.

Data Objects
These are numbers and strings of characters.  Numbers are converted into
strings and vice versa, as needed.
(See [Conversion](#Conversion).)

Deadlock
The situation in which two communicating processes are each waiting
for the other to perform an action.

Debugger
A program used to help developers remove &ldquo;bugs&rdquo; from (de-bug)
their programs.

Double Precision
An internal representation of numbers that can have fractional parts.
Double precision numbers keep track of more digits than do single precision
numbers, but operations on them are sometimes more expensive.  This is the way
`awk` stores numeric values.  It is the C type `double`.

Dynamic Regular Expression
A dynamic regular expression is a regular expression written as an
ordinary expression.  It could be a string constant, such as
`"foo"`, but it may also be an expression whose value can vary.
(See [Computed Regexps](#Computed-Regexps).)

Empty String
See &ldquo;Null String.&rdquo;

Environment
A collection of strings, of the form &lsquo;name=val&rsquo;, that each
program has available to it. Users generally place values into the
environment in order to provide information to various programs. Typical
examples are the environment variables `HOME` and `PATH`.

Epoch
The date used as the &ldquo;beginning of time&rdquo; for timestamps.
Time values in most systems are represented as seconds since the epoch,
with library functions available for converting these values into
standard date and time formats.

The epoch on Unix and POSIX systems is 1970-01-01 00:00:00 UTC.
See also &ldquo;GMT&rdquo; and &ldquo;UTC.&rdquo;

Escape Sequences
A special sequence of characters used for describing nonprinting
characters, such as &lsquo;\n&rsquo; for newline or &lsquo;\033&rsquo; for the ASCII
ESC (Escape) character. (See [Escape Sequences](#Escape-Sequences).)

Extension
An additional feature or change to a programming language or
utility not defined by that language&rsquo;s or utility&rsquo;s standard.
`gawk` has (too) many extensions over POSIX `awk`.

FDL
See &ldquo;Free Documentation License.&rdquo;

Field
When `awk` reads an input record, it splits the record into pieces
separated by whitespace (or by a separator regexp that you can
change by setting the predefined variable `FS`).  Such pieces are
called fields.  If the pieces are of fixed length, you can use the built-in
variable `FIELDWIDTHS` to describe their lengths.
If you wish to specify the contents of fields instead of the field
separator, you can use the predefined variable `FPAT` to do so.
(See [Field Separators](#Field-Separators),
[Constant Size](#Constant-Size),
and
[Splitting By Content](#Splitting-By-Content).)

Flag
A variable whose truth value indicates the existence or nonexistence
of some condition.

Floating-Point Number
Often referred to in mathematical terms as a &ldquo;rational&rdquo; or real number,
this is just a number that can have a fractional part.
See also &ldquo;Double Precision&rdquo; and &ldquo;Single Precision.&rdquo;

Format
Format strings control the appearance of output in the
`strftime()` and `sprintf()` functions, and in the
`printf` statement as well.  Also, data conversions from numbers to strings
are controlled by the format strings contained in the predefined variables
`CONVFMT` and `OFMT`. (See [Control Letters](#Control-Letters).)

Fortran
Shorthand for FORmula TRANslator, one of the first programming languages
available for scientific calculations. It was created by John Backus,
and has been available since 1957. It is still in use today.

Free Documentation License
This document describes the terms under which this Web page
is published and may be copied. (See [GNU Free Documentation License](#GNU-Free-Documentation-License).)

Free Software Foundation
A nonprofit organization dedicated
to the production and distribution of freely distributable software.
It was founded by Richard M. Stallman, the author of the original
Emacs editor.  GNU Emacs is the most widely used version of Emacs today.

FSF
See &ldquo;Free Software Foundation.&rdquo;

Function
A part of an `awk` program that can be invoked from every point of
the program, to perform a task.  `awk` has several built-in
functions.
Users can define their own functions in every part of the program.
Function can be recursive, i.e., they may invoke themselves.
See [Functions](#Functions).
In `gawk` it is also possible to have functions shared
among different programs, and included where required using the
`@include` directive
(see [Include Files](#Include-Files)).
In `gawk` the name of the function that should be invoked
can be generated at run time, i.e., dynamically.
The `gawk` extension API provides constructor functions
(see [Constructor Functions](#Constructor-Functions)).

`gawk`
The GNU implementation of `awk`.

General Public License
This document describes the terms under which `gawk` and its source
code may be distributed. (See [Copying](#Copying).)

GMT
&ldquo;Greenwich Mean Time.&rdquo;
This is the old term for UTC.
It is the time of day used internally for Unix and POSIX systems.
See also &ldquo;Epoch&rdquo; and &ldquo;UTC.&rdquo;

GNU
&ldquo;GNU&rsquo;s not Unix&rdquo;.  An on-going project of the Free Software Foundation
to create a complete, freely distributable, POSIX-compliant computing
environment.

GNU/Linux
A variant of the GNU system using the Linux kernel, instead of the
Free Software Foundation&rsquo;s Hurd kernel.
The Linux kernel is a stable, efficient, full-featured clone of Unix that has
been ported to a variety of architectures.
It is most popular on PC-class systems, but runs well on a variety of
other systems too.
The Linux kernel source code is available under the terms of the GNU General
Public License, which is perhaps its most important aspect.

GPL
See &ldquo;General Public License.&rdquo;

Hexadecimal
Base 16 notation, where the digits are `0`&ndash;`9` and
`A`&ndash;`F`, with &lsquo;A&rsquo;
representing 10, &lsquo;B&rsquo; representing 11, and so on, up to &lsquo;F&rsquo; for 15.
Hexadecimal numbers are written in C using a leading &lsquo;0x&rsquo;,
to indicate their base.  Thus, `0x12` is 18 ((1 x 16) + 2).
See [Nondecimal-numbers](#Nondecimal_002dnumbers).

I/O
Abbreviation for &ldquo;Input/Output,&rdquo; the act of moving data into and/or
out of a running program.

Input Record
A single chunk of data that is read in by `awk`.  Usually, an `awk` input
record consists of one line of text.
(See [Records](#Records).)

Integer
A whole number, i.e., a number that does not have a fractional part.

Internationalization
The process of writing or modifying a program so
that it can use multiple languages without requiring
further source code changes.

Interpreter
A program that reads human-readable source code directly, and uses
the instructions in it to process data and produce results.
`awk` is typically (but not always) implemented as an interpreter.
See also &ldquo;Compiler.&rdquo;

Interval Expression
A component of a regular expression that lets you specify repeated matches of
some part of the regexp.  Interval expressions were not originally available
in `awk` programs.

ISO
The International Organization for Standardization.
This organization produces international standards for many things, including
programming languages, such as C and C++.
In the computer arena, important standards like those for C, C++, and POSIX
become both American national and ISO international standards simultaneously.
This Web page refers to Standard C as &ldquo;ISO C&rdquo; throughout.
See [the ISO website](https://www.iso.org/iso/home/about.htm) for more
information about the name of the organization and its language-independent
three-letter acronym.

Java
A modern programming language originally developed by Sun Microsystems
(now Oracle) supporting Object-Oriented programming.  Although usually
implemented by compiling to the instructions for a standard virtual
machine (the JVM), the language can be compiled to native code.

Keyword
In the `awk` language, a keyword is a word that has special
meaning.  Keywords are reserved and may not be used as variable names.

`gawk`&rsquo;s keywords are:
`BEGIN`,
`BEGINFILE`,
`END`,
`ENDFILE`,
`break`,
`case`,
`continue`,
`default`,
`delete`,
`do&hellip;while`,
`else`,
`exit`,
`for&hellip;in`,
`for`,
`function`,
`func`,
`if`,
`next`,
`nextfile`,
`switch`,
and
`while`.

Korn Shell
The Korn Shell (`ksh`) is a Unix shell which was developed by David Korn at Bell
Laboratories in the early 1980s. The Korn Shell is backward-compatible with the Bourne
shell and includes many features of the C shell.
See also &ldquo;Bourne Shell.&rdquo;

Lesser General Public License
This document describes the terms under which binary library archives
or shared objects,
and their source code may be distributed.

LGPL
See &ldquo;Lesser General Public License.&rdquo;

Linux
See &ldquo;GNU/Linux.&rdquo;

Localization
The process of providing the data necessary for an
internationalized program to work in a particular language.

Logical Expression
An expression using the operators for logic, AND, OR, and NOT, written
&lsquo;&&&rsquo;, &lsquo;||&rsquo;, and &lsquo;!&rsquo; in `awk`. Often called Boolean
expressions, after the mathematician who pioneered this kind of
mathematical logic.

Lvalue
An expression that can appear on the left side of an assignment
operator.  In most languages, lvalues can be variables or array
elements.  In `awk`, a field designator can also be used as an
lvalue.

Matching
The act of testing a string against a regular expression.  If the
regexp describes the contents of the string, it is said to *match* it.

Metacharacters
Characters used within a regexp that do not stand for themselves.
Instead, they denote regular expression operations, such as repetition,
grouping, or alternation.

Nesting
Nesting is where information is organized in layers, or where objects
contain other similar objects.
In `gawk` the `@include`
directive can be nested. The &ldquo;natural&rdquo; nesting of arithmetic and
logical operations can be changed using parentheses
(see [Precedence](#Precedence)).

No-op
An operation that does nothing.

Null String
A string with no characters in it.  It is represented explicitly in
`awk` programs by placing two double quote characters next to
each other (`""`).  It can appear in input data by having two successive
occurrences of the field separator appear next to each other.

Number
A numeric-valued data object.  Modern `awk` implementations use
double precision floating-point to represent numbers.
Ancient `awk` implementations used single precision floating-point.

Octal
Base-eight notation, where the digits are `0`&ndash;`7`.
Octal numbers are written in C using a leading &lsquo;0&rsquo;,
to indicate their base.  Thus, `013` is 11 ((1 x 8) + 3).
See [Nondecimal-numbers](#Nondecimal_002dnumbers).

Output Record
A single chunk of data that is written out by `awk`.  Usually, an
`awk` output record consists of one or more lines of text.
See [Records](#Records).

Pattern
Patterns tell `awk` which input records are interesting to which
rules.

A pattern is an arbitrary conditional expression against which input is
tested.  If the condition is satisfied, the pattern is said to *match*
the input record.  A typical pattern might compare the input record against
a regular expression. (See [Pattern Overview](#Pattern-Overview).)

PEBKAC
An acronym describing what is possibly the most frequent
source of computer usage problems. (Problem Exists Between
Keyboard And Chair.)

Plug-in
See &ldquo;Extensions.&rdquo;

POSIX
The name for a series of standards
that specify a Portable Operating System interface.  The &ldquo;IX&rdquo; denotes
the Unix heritage of these standards.  The main standard of interest for
`awk` users is
IEEE Standard for Information Technology, Standard 1003.1-2008.
The 2008 POSIX standard can be found online at
[http://pubs.opengroup.org/onlinepubs/9699919799/](http://pubs.opengroup.org/onlinepubs/9699919799/).

Precedence
The order in which operations are performed when operators are used
without explicit parentheses.

Private
Variables and/or functions that are meant for use exclusively by library
functions and not for the main `awk` program. Special care must be
taken when naming such variables and functions.
(See [Library Names](#Library-Names).)

Range (of input lines)
A sequence of consecutive lines from the input file(s).  A pattern
can specify ranges of input lines for `awk` to process or it can
specify single lines. (See [Pattern Overview](#Pattern-Overview).)

Record
See &ldquo;Input record&rdquo; and &ldquo;Output record.&rdquo;

Recursion
When a function calls itself, either directly or indirectly.
If this is clear, stop, and proceed to the next entry.
Otherwise, refer to the entry for &ldquo;recursion.&rdquo;

Redirection
Redirection means performing input from something other than the standard input
stream, or performing output to something other than the standard output stream.

You can redirect input to the `getline` statement using
the &lsquo;<&rsquo;, &lsquo;|&rsquo;, and &lsquo;|&&rsquo; operators.
You can redirect the output of the `print` and `printf` statements
to a file or a system command, using the &lsquo;>&rsquo;, &lsquo;>>&rsquo;, &lsquo;|&rsquo;, and &lsquo;|&&rsquo;
operators.
(See [Getline](#Getline),
and [Redirection](#Redirection).)

Reference Counts
An internal mechanism in `gawk` to minimize the amount of memory
needed to store the value of string variables. If the value assumed by
a variable is used in more than one place, only one copy of the value
itself is kept, and the associated reference count is increased when the
same value is used by an additional variable, and decreased when the related
variable is no longer in use. When the reference count goes to zero,
the memory space used to store the value of the variable is freed.

Regexp
See &ldquo;Regular Expression.&rdquo;

Regular Expression
A regular expression (&ldquo;regexp&rdquo; for short) is a pattern that denotes a
set of strings, possibly an infinite set.  For example, the regular expression
&lsquo;R.*xp&rsquo; matches any string starting with the letter &lsquo;R&rsquo;
and ending with the letters &lsquo;xp&rsquo;.  In `awk`, regular expressions are
used in patterns and in conditional expressions.  Regular expressions may contain
escape sequences. (See [Regexp](#Regexp).)

Regular Expression Constant
A regular expression constant is a regular expression written within
slashes, such as `/foo/`.  This regular expression is chosen
when you write the `awk` program and cannot be changed during
its execution. (See [Regexp Usage](#Regexp-Usage).)

Regular Expression Operators
See &ldquo;Metacharacters.&rdquo;

Rounding
Rounding the result of an arithmetic operation can be tricky.
More than one way of rounding exists, and in `gawk`
it is possible to choose which method should be used in a program.
See [Setting the rounding mode](#Setting-the-rounding-mode).

Rule
A segment of an `awk` program that specifies how to process single
input records.  A rule consists of a *pattern* and an *action*.
`awk` reads an input record; then, for each rule, if the input record
satisfies the rule&rsquo;s pattern, `awk` executes the rule&rsquo;s action.
Otherwise, the rule does nothing for that input record.

Rvalue
A value that can appear on the right side of an assignment operator.
In `awk`, essentially every expression has a value. These values
are rvalues.

Scalar
A single value, be it a number or a string.
Regular variables are scalars; arrays and functions are not.

Search Path
In `gawk`, a list of directories to search for `awk` program source files.
In the shell, a list of directories to search for executable programs.

`sed`
See &ldquo;Stream Editor.&rdquo;

Seed
The initial value, or starting point, for a sequence of random numbers.

Shell
The command interpreter for Unix and POSIX-compliant systems.
The shell works both interactively, and as a programming language
for batch files, or shell scripts.

Short-Circuit
The nature of the `awk` logical operators &lsquo;&&&rsquo; and &lsquo;||&rsquo;.
If the value of the entire expression is determinable from evaluating just
the lefthand side of these operators, the righthand side is not
evaluated.
(See [Boolean Ops](#Boolean-Ops).)

Side Effect
A side effect occurs when an expression has an effect aside from merely
producing a value.  Assignment expressions, increment and decrement
expressions, and function calls have side effects.
(See [Assignment Ops](#Assignment-Ops).)

Single Precision
An internal representation of numbers that can have fractional parts.
Single precision numbers keep track of fewer digits than do double precision
numbers, but operations on them are sometimes less expensive in terms of CPU time.
This is the type used by some ancient versions of `awk` to store
numeric values.  It is the C type `float`.

Space
The character generated by hitting the space bar on the keyboard.

Special File
A file name interpreted internally by `gawk`, instead of being handed
directly to the underlying operating system&mdash;for example, /dev/stderr.
(See [Special Files](#Special-Files).)

Statement
An expression inside an `awk` program in the action part
of a pattern&ndash;action rule, or inside an
`awk` function. A statement can be a variable assignment,
an array operation, a loop, etc.

Stream Editor
A program that reads records from an input stream and processes them one
or more at a time.  This is in contrast with batch programs, which may
expect to read their input files in entirety before starting to do
anything, as well as with interactive programs which require input from the
user.

String
A datum consisting of a sequence of characters, such as &lsquo;I am a
string&rsquo;.  Constant strings are written with double quotes in the
`awk` language and may contain escape sequences.
(See [Escape Sequences](#Escape-Sequences).)

Tab
The character generated by hitting the TAB key on the keyboard.
It usually expands to up to eight spaces upon output.

Text Domain
A unique name that identifies an application.
Used for grouping messages that are translated at runtime
into the local language.

Timestamp
A value in the &ldquo;seconds since the epoch&rdquo; format used by Unix
and POSIX systems.  Used for the `gawk` functions
`mktime()`, `strftime()`, and `systime()`.
See also &ldquo;Epoch,&rdquo; &ldquo;GMT,&rdquo; and &ldquo;UTC.&rdquo;

Unix
A computer operating system originally developed in the early 1970&rsquo;s at
AT&T Bell Laboratories.  It initially became popular in universities around
the world and later moved into commercial environments as a software
development system and network server system. There are many commercial
versions of Unix, as well as several work-alike systems whose source code
is freely available (such as GNU/Linux, [NetBSD](http://www.netbsd.org),
[FreeBSD](https://www.freebsd.org), and [OpenBSD](http://www.openbsd.org)).

UTC
The accepted abbreviation for &ldquo;Universal Coordinated Time.&rdquo;
This is standard time in Greenwich, England, which is used as a
reference time for day and date calculations.
See also &ldquo;Epoch&rdquo; and &ldquo;GMT.&rdquo;

Variable
A name for a value. In `awk`, variables may be either scalars
or arrays.

Whitespace
A sequence of space, TAB, or newline characters occurring inside an input
record or a string.

---

Next: [GNU Free Documentation License](#GNU-Free-Documentation-License), Previous: [Glossary](#Glossary), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## GNU General Public License

Version 3, 29 June 2007

    Copyright &copy; 2007 Free Software Foundation, Inc. [https://fsf.org/](https://fsf.org/)
    
    Everyone is permitted to copy and distribute verbatim copies of this
    license document, but changing it is not allowed.
    

### Preamble

The GNU General Public License is a free, copyleft license for
software and other kinds of works.

The licenses for most software and other practical works are designed
to take away your freedom to share and change the works.  By contrast,
the GNU General Public License is intended to guarantee your freedom
to share and change all versions of a program&mdash;to make sure it remains
free software for all its users.  We, the Free Software Foundation,
use the GNU General Public License for most of our software; it
applies also to any other work released this way by its authors.  You
can apply it to your programs, too.

When we speak of free software, we are referring to freedom, not
price.  Our General Public Licenses are designed to make sure that you
have the freedom to distribute copies of free software (and charge for
them if you wish), that you receive source code or can get it if you
want it, that you can change the software or use pieces of it in new
free programs, and that you know you can do these things.

To protect your rights, we need to prevent others from denying you
these rights or asking you to surrender the rights.  Therefore, you
have certain responsibilities if you distribute copies of the
software, or if you modify it: responsibilities to respect the freedom
of others.

For example, if you distribute copies of such a program, whether
gratis or for a fee, you must pass on to the recipients the same
freedoms that you received.  You must make sure that they, too,
receive or can get the source code.  And you must show them these
terms so they know their rights.

Developers that use the GNU GPL protect your rights with two steps:
(1) assert copyright on the software, and (2) offer you this License
giving you legal permission to copy, distribute and/or modify it.

For the developers&rsquo; and authors&rsquo; protection, the GPL clearly explains
that there is no warranty for this free software.  For both users&rsquo; and
authors&rsquo; sake, the GPL requires that modified versions be marked as
changed, so that their problems will not be attributed erroneously to
authors of previous versions.

Some devices are designed to deny users access to install or run
modified versions of the software inside them, although the
manufacturer can do so.  This is fundamentally incompatible with the
aim of protecting users&rsquo; freedom to change the software.  The
systematic pattern of such abuse occurs in the area of products for
individuals to use, which is precisely where it is most unacceptable.
Therefore, we have designed this version of the GPL to prohibit the
practice for those products.  If such problems arise substantially in
other domains, we stand ready to extend this provision to those
domains in future versions of the GPL, as needed to protect the
freedom of users.

Finally, every program is threatened constantly by software patents.
States should not allow patents to restrict development and use of
software on general-purpose computers, but in those that do, we wish
to avoid the special danger that patents applied to a free program
could make it effectively proprietary.  To prevent this, the GPL
assures that patents cannot be used to render the program non-free.

The precise terms and conditions for copying, distribution and
modification follow.

### TERMS AND CONDITIONS

1.  Definitions.

&ldquo;This License&rdquo; refers to version 3 of the GNU General Public License.

&ldquo;Copyright&rdquo; also means copyright-like laws that apply to other kinds
of works, such as semiconductor masks.

&ldquo;The Program&rdquo; refers to any copyrightable work licensed under this
License.  Each licensee is addressed as &ldquo;you&rdquo;.  &ldquo;Licensees&rdquo; and
&ldquo;recipients&rdquo; may be individuals or organizations.

To &ldquo;modify&rdquo; a work means to copy from or adapt all or part of the work
in a fashion requiring copyright permission, other than the making of
an exact copy.  The resulting work is called a &ldquo;modified version&rdquo; of
the earlier work or a work &ldquo;based on&rdquo; the earlier work.

A &ldquo;covered work&rdquo; means either the unmodified Program or a work based
on the Program.

To &ldquo;propagate&rdquo; a work means to do anything with it that, without
permission, would make you directly or secondarily liable for
infringement under applicable copyright law, except executing it on a
computer or modifying a private copy.  Propagation includes copying,
distribution (with or without modification), making available to the
public, and in some countries other activities as well.

To &ldquo;convey&rdquo; a work means any kind of propagation that enables other
parties to make or receive copies.  Mere interaction with a user
through a computer network, with no transfer of a copy, is not
conveying.

An interactive user interface displays &ldquo;Appropriate Legal Notices&rdquo; to
the extent that it includes a convenient and prominently visible
feature that (1) displays an appropriate copyright notice, and (2)
tells the user that there is no warranty for the work (except to the
extent that warranties are provided), that licensees may convey the
work under this License, and how to view a copy of this License.  If
the interface presents a list of user commands or options, such as a
menu, a prominent item in the list meets this criterion.

2.  Source Code.

The &ldquo;source code&rdquo; for a work means the preferred form of the work for
making modifications to it.  &ldquo;Object code&rdquo; means any non-source form
of a work.

A &ldquo;Standard Interface&rdquo; means an interface that either is an official
standard defined by a recognized standards body, or, in the case of
interfaces specified for a particular programming language, one that
is widely used among developers working in that language.

The &ldquo;System Libraries&rdquo; of an executable work include anything, other
than the work as a whole, that (a) is included in the normal form of
packaging a Major Component, but which is not part of that Major
Component, and (b) serves only to enable use of the work with that
Major Component, or to implement a Standard Interface for which an
implementation is available to the public in source code form.  A
&ldquo;Major Component&rdquo;, in this context, means a major essential component
(kernel, window system, and so on) of the specific operating system
(if any) on which the executable work runs, or a compiler used to
produce the work, or an object code interpreter used to run it.

The &ldquo;Corresponding Source&rdquo; for a work in object code form means all
the source code needed to generate, install, and (for an executable
work) run the object code and to modify the work, including scripts to
control those activities.  However, it does not include the work&rsquo;s
System Libraries, or general-purpose tools or generally available free
programs which are used unmodified in performing those activities but
which are not part of the work.  For example, Corresponding Source
includes interface definition files associated with source files for
the work, and the source code for shared libraries and dynamically
linked subprograms that the work is specifically designed to require,
such as by intimate data communication or control flow between those
subprograms and other parts of the work.

The Corresponding Source need not include anything that users can
regenerate automatically from other parts of the Corresponding Source.

The Corresponding Source for a work in source code form is that same
work.

3.  Basic Permissions.

All rights granted under this License are granted for the term of
copyright on the Program, and are irrevocable provided the stated
conditions are met.  This License explicitly affirms your unlimited
permission to run the unmodified Program.  The output from running a
covered work is covered by this License only if the output, given its
content, constitutes a covered work.  This License acknowledges your
rights of fair use or other equivalent, as provided by copyright law.

You may make, run and propagate covered works that you do not convey,
without conditions so long as your license otherwise remains in force.
You may convey covered works to others for the sole purpose of having
them make modifications exclusively for you, or provide you with
facilities for running those works, provided that you comply with the
terms of this License in conveying all material for which you do not
control copyright.  Those thus making or running the covered works for
you must do so exclusively on your behalf, under your direction and
control, on terms that prohibit them from making any copies of your
copyrighted material outside their relationship with you.

Conveying under any other circumstances is permitted solely under the
conditions stated below.  Sublicensing is not allowed; section 10
makes it unnecessary.

4.  Protecting Users&rsquo; Legal Rights From Anti-Circumvention Law.

No covered work shall be deemed part of an effective technological
measure under any applicable law fulfilling obligations under article
11 of the WIPO copyright treaty adopted on 20 December 1996, or
similar laws prohibiting or restricting circumvention of such
measures.

When you convey a covered work, you waive any legal power to forbid
circumvention of technological measures to the extent such
circumvention is effected by exercising rights under this License with
respect to the covered work, and you disclaim any intention to limit
operation or modification of the work as a means of enforcing, against
the work&rsquo;s users, your or third parties&rsquo; legal rights to forbid
circumvention of technological measures.

5.  Conveying Verbatim Copies.

You may convey verbatim copies of the Program&rsquo;s source code as you
receive it, in any medium, provided that you conspicuously and
appropriately publish on each copy an appropriate copyright notice;
keep intact all notices stating that this License and any
non-permissive terms added in accord with section 7 apply to the code;
keep intact all notices of the absence of any warranty; and give all
recipients a copy of this License along with the Program.

You may charge any price or no price for each copy that you convey,
and you may offer support or warranty protection for a fee.

6.  Conveying Modified Source Versions.

You may convey a work based on the Program, or the modifications to
produce it from the Program, in the form of source code under the
terms of section 4, provided that you also meet all of these
conditions:

1.  The work must carry prominent notices stating that you modified it,
and giving a relevant date.

2.  The work must carry prominent notices stating that it is released
under this License and any conditions added under section 7.  This
requirement modifies the requirement in section 4 to &ldquo;keep intact all
notices&rdquo;.

3.  You must license the entire work, as a whole, under this License to
anyone who comes into possession of a copy.  This License will
therefore apply, along with any applicable section 7 additional terms,
to the whole of the work, and all its parts, regardless of how they
are packaged.  This License gives no permission to license the work in
any other way, but it does not invalidate such permission if you have
separately received it.

4.  If the work has interactive user interfaces, each must display
Appropriate Legal Notices; however, if the Program has interactive
interfaces that do not display Appropriate Legal Notices, your work
need not make them do so.

A compilation of a covered work with other separate and independent
works, which are not by their nature extensions of the covered work,
and which are not combined with it such as to form a larger program,
in or on a volume of a storage or distribution medium, is called an
&ldquo;aggregate&rdquo; if the compilation and its resulting copyright are not
used to limit the access or legal rights of the compilation&rsquo;s users
beyond what the individual works permit.  Inclusion of a covered work
in an aggregate does not cause this License to apply to the other
parts of the aggregate.

7.  Conveying Non-Source Forms.

You may convey a covered work in object code form under the terms of
sections 4 and 5, provided that you also convey the machine-readable
Corresponding Source under the terms of this License, in one of these
ways:

1.  Convey the object code in, or embodied in, a physical product
(including a physical distribution medium), accompanied by the
Corresponding Source fixed on a durable physical medium customarily
used for software interchange.

2.  Convey the object code in, or embodied in, a physical product
(including a physical distribution medium), accompanied by a written
offer, valid for at least three years and valid for as long as you
offer spare parts or customer support for that product model, to give
anyone who possesses the object code either (1) a copy of the
Corresponding Source for all the software in the product that is
covered by this License, on a durable physical medium customarily used
for software interchange, for a price no more than your reasonable
cost of physically performing this conveying of source, or (2) access
to copy the Corresponding Source from a network server at no charge.

3.  Convey individual copies of the object code with a copy of the written
offer to provide the Corresponding Source.  This alternative is
allowed only occasionally and noncommercially, and only if you
received the object code with such an offer, in accord with subsection
6b.

4.  Convey the object code by offering access from a designated place
(gratis or for a charge), and offer equivalent access to the
Corresponding Source in the same way through the same place at no
further charge.  You need not require recipients to copy the
Corresponding Source along with the object code.  If the place to copy
the object code is a network server, the Corresponding Source may be
on a different server (operated by you or a third party) that supports
equivalent copying facilities, provided you maintain clear directions
next to the object code saying where to find the Corresponding Source.
Regardless of what server hosts the Corresponding Source, you remain
obligated to ensure that it is available for as long as needed to
satisfy these requirements.

5.  Convey the object code using peer-to-peer transmission, provided you
inform other peers where the object code and Corresponding Source of
the work are being offered to the general public at no charge under
subsection 6d.

A separable portion of the object code, whose source code is excluded
from the Corresponding Source as a System Library, need not be
included in conveying the object code work.

A &ldquo;User Product&rdquo; is either (1) a &ldquo;consumer product&rdquo;, which means any
tangible personal property which is normally used for personal,
family, or household purposes, or (2) anything designed or sold for
incorporation into a dwelling.  In determining whether a product is a
consumer product, doubtful cases shall be resolved in favor of
coverage.  For a particular product received by a particular user,
&ldquo;normally used&rdquo; refers to a typical or common use of that class of
product, regardless of the status of the particular user or of the way
in which the particular user actually uses, or expects or is expected
to use, the product.  A product is a consumer product regardless of
whether the product has substantial commercial, industrial or
non-consumer uses, unless such uses represent the only significant
mode of use of the product.

&ldquo;Installation Information&rdquo; for a User Product means any methods,
procedures, authorization keys, or other information required to
install and execute modified versions of a covered work in that User
Product from a modified version of its Corresponding Source.  The
information must suffice to ensure that the continued functioning of
the modified object code is in no case prevented or interfered with
solely because modification has been made.

If you convey an object code work under this section in, or with, or
specifically for use in, a User Product, and the conveying occurs as
part of a transaction in which the right of possession and use of the
User Product is transferred to the recipient in perpetuity or for a
fixed term (regardless of how the transaction is characterized), the
Corresponding Source conveyed under this section must be accompanied
by the Installation Information.  But this requirement does not apply
if neither you nor any third party retains the ability to install
modified object code on the User Product (for example, the work has
been installed in ROM).

The requirement to provide Installation Information does not include a
requirement to continue to provide support service, warranty, or
updates for a work that has been modified or installed by the
recipient, or for the User Product in which it has been modified or
installed.  Access to a network may be denied when the modification
itself materially and adversely affects the operation of the network
or violates the rules and protocols for communication across the
network.

Corresponding Source conveyed, and Installation Information provided,
in accord with this section must be in a format that is publicly
documented (and with an implementation available to the public in
source code form), and must require no special password or key for
unpacking, reading or copying.

8.  Additional Terms.

&ldquo;Additional permissions&rdquo; are terms that supplement the terms of this
License by making exceptions from one or more of its conditions.
Additional permissions that are applicable to the entire Program shall
be treated as though they were included in this License, to the extent
that they are valid under applicable law.  If additional permissions
apply only to part of the Program, that part may be used separately
under those permissions, but the entire Program remains governed by
this License without regard to the additional permissions.

When you convey a copy of a covered work, you may at your option
remove any additional permissions from that copy, or from any part of
it.  (Additional permissions may be written to require their own
removal in certain cases when you modify the work.)  You may place
additional permissions on material, added by you to a covered work,
for which you have or can give appropriate copyright permission.

Notwithstanding any other provision of this License, for material you
add to a covered work, you may (if authorized by the copyright holders
of that material) supplement the terms of this License with terms:

1.  Disclaiming warranty or limiting liability differently from the terms
of sections 15 and 16 of this License; or

2.  Requiring preservation of specified reasonable legal notices or author
attributions in that material or in the Appropriate Legal Notices
displayed by works containing it; or

3.  Prohibiting misrepresentation of the origin of that material, or
requiring that modified versions of such material be marked in
reasonable ways as different from the original version; or

4.  Limiting the use for publicity purposes of names of licensors or
authors of the material; or

5.  Declining to grant rights under trademark law for use of some trade
names, trademarks, or service marks; or

6.  Requiring indemnification of licensors and authors of that material by
anyone who conveys the material (or modified versions of it) with
contractual assumptions of liability to the recipient, for any
liability that these contractual assumptions directly impose on those
licensors and authors.

All other non-permissive additional terms are considered &ldquo;further
restrictions&rdquo; within the meaning of section 10.  If the Program as you
received it, or any part of it, contains a notice stating that it is
governed by this License along with a term that is a further
restriction, you may remove that term.  If a license document contains
a further restriction but permits relicensing or conveying under this
License, you may add to a covered work material governed by the terms
of that license document, provided that the further restriction does
not survive such relicensing or conveying.

If you add terms to a covered work in accord with this section, you
must place, in the relevant source files, a statement of the
additional terms that apply to those files, or a notice indicating
where to find the applicable terms.

Additional terms, permissive or non-permissive, may be stated in the
form of a separately written license, or stated as exceptions; the
above requirements apply either way.

9.  Termination.

You may not propagate or modify a covered work except as expressly
provided under this License.  Any attempt otherwise to propagate or
modify it is void, and will automatically terminate your rights under
this License (including any patent licenses granted under the third
paragraph of section 11).

However, if you cease all violation of this License, then your license
from a particular copyright holder is reinstated (a) provisionally,
unless and until the copyright holder explicitly and finally
terminates your license, and (b) permanently, if the copyright holder
fails to notify you of the violation by some reasonable means prior to
60 days after the cessation.

Moreover, your license from a particular copyright holder is
reinstated permanently if the copyright holder notifies you of the
violation by some reasonable means, this is the first time you have
received notice of violation of this License (for any work) from that
copyright holder, and you cure the violation prior to 30 days after
your receipt of the notice.

Termination of your rights under this section does not terminate the
licenses of parties who have received copies or rights from you under
this License.  If your rights have been terminated and not permanently
reinstated, you do not qualify to receive new licenses for the same
material under section 10.

10.  Acceptance Not Required for Having Copies.

You are not required to accept this License in order to receive or run
a copy of the Program.  Ancillary propagation of a covered work
occurring solely as a consequence of using peer-to-peer transmission
to receive a copy likewise does not require acceptance.  However,
nothing other than this License grants you permission to propagate or
modify any covered work.  These actions infringe copyright if you do
not accept this License.  Therefore, by modifying or propagating a
covered work, you indicate your acceptance of this License to do so.

11.  Automatic Licensing of Downstream Recipients.

Each time you convey a covered work, the recipient automatically
receives a license from the original licensors, to run, modify and
propagate that work, subject to this License.  You are not responsible
for enforcing compliance by third parties with this License.

An &ldquo;entity transaction&rdquo; is a transaction transferring control of an
organization, or substantially all assets of one, or subdividing an
organization, or merging organizations.  If propagation of a covered
work results from an entity transaction, each party to that
transaction who receives a copy of the work also receives whatever
licenses to the work the party&rsquo;s predecessor in interest had or could
give under the previous paragraph, plus a right to possession of the
Corresponding Source of the work from the predecessor in interest, if
the predecessor has it or can get it with reasonable efforts.

You may not impose any further restrictions on the exercise of the
rights granted or affirmed under this License.  For example, you may
not impose a license fee, royalty, or other charge for exercise of
rights granted under this License, and you may not initiate litigation
(including a cross-claim or counterclaim in a lawsuit) alleging that
any patent claim is infringed by making, using, selling, offering for
sale, or importing the Program or any portion of it.

12.  Patents.

A &ldquo;contributor&rdquo; is a copyright holder who authorizes use under this
License of the Program or a work on which the Program is based.  The
work thus licensed is called the contributor&rsquo;s &ldquo;contributor version&rdquo;.

A contributor&rsquo;s &ldquo;essential patent claims&rdquo; are all patent claims owned
or controlled by the contributor, whether already acquired or
hereafter acquired, that would be infringed by some manner, permitted
by this License, of making, using, or selling its contributor version,
but do not include claims that would be infringed only as a
consequence of further modification of the contributor version.  For
purposes of this definition, &ldquo;control&rdquo; includes the right to grant
patent sublicenses in a manner consistent with the requirements of
this License.

Each contributor grants you a non-exclusive, worldwide, royalty-free
patent license under the contributor&rsquo;s essential patent claims, to
make, use, sell, offer for sale, import and otherwise run, modify and
propagate the contents of its contributor version.

In the following three paragraphs, a &ldquo;patent license&rdquo; is any express
agreement or commitment, however denominated, not to enforce a patent
(such as an express permission to practice a patent or covenant not to
sue for patent infringement).  To &ldquo;grant&rdquo; such a patent license to a
party means to make such an agreement or commitment not to enforce a
patent against the party.

If you convey a covered work, knowingly relying on a patent license,
and the Corresponding Source of the work is not available for anyone
to copy, free of charge and under the terms of this License, through a
publicly available network server or other readily accessible means,
then you must either (1) cause the Corresponding Source to be so
available, or (2) arrange to deprive yourself of the benefit of the
patent license for this particular work, or (3) arrange, in a manner
consistent with the requirements of this License, to extend the patent
license to downstream recipients.  &ldquo;Knowingly relying&rdquo; means you have
actual knowledge that, but for the patent license, your conveying the
covered work in a country, or your recipient&rsquo;s use of the covered work
in a country, would infringe one or more identifiable patents in that
country that you have reason to believe are valid.

If, pursuant to or in connection with a single transaction or
arrangement, you convey, or propagate by procuring conveyance of, a
covered work, and grant a patent license to some of the parties
receiving the covered work authorizing them to use, propagate, modify
or convey a specific copy of the covered work, then the patent license
you grant is automatically extended to all recipients of the covered
work and works based on it.

A patent license is &ldquo;discriminatory&rdquo; if it does not include within the
scope of its coverage, prohibits the exercise of, or is conditioned on
the non-exercise of one or more of the rights that are specifically
granted under this License.  You may not convey a covered work if you
are a party to an arrangement with a third party that is in the
business of distributing software, under which you make payment to the
third party based on the extent of your activity of conveying the
work, and under which the third party grants, to any of the parties
who would receive the covered work from you, a discriminatory patent
license (a) in connection with copies of the covered work conveyed by
you (or copies made from those copies), or (b) primarily for and in
connection with specific products or compilations that contain the
covered work, unless you entered into that arrangement, or that patent
license was granted, prior to 28 March 2007.

Nothing in this License shall be construed as excluding or limiting
any implied license or other defenses to infringement that may
otherwise be available to you under applicable patent law.

13.  No Surrender of Others&rsquo; Freedom.

If conditions are imposed on you (whether by court order, agreement or
otherwise) that contradict the conditions of this License, they do not
excuse you from the conditions of this License.  If you cannot convey
a covered work so as to satisfy simultaneously your obligations under
this License and any other pertinent obligations, then as a
consequence you may not convey it at all.  For example, if you agree
to terms that obligate you to collect a royalty for further conveying
from those to whom you convey the Program, the only way you could
satisfy both those terms and this License would be to refrain entirely
from conveying the Program.

14.  Use with the GNU Affero General Public License.

Notwithstanding any other provision of this License, you have
permission to link or combine any covered work with a work licensed
under version 3 of the GNU Affero General Public License into a single
combined work, and to convey the resulting work.  The terms of this
License will continue to apply to the part which is the covered work,
but the special requirements of the GNU Affero General Public License,
section 13, concerning interaction through a network will apply to the
combination as such.

15.  Revised Versions of this License.

The Free Software Foundation may publish revised and/or new versions
of the GNU General Public License from time to time.  Such new
versions will be similar in spirit to the present version, but may
differ in detail to address new problems or concerns.

Each version is given a distinguishing version number.  If the Program
specifies that a certain numbered version of the GNU General Public
License &ldquo;or any later version&rdquo; applies to it, you have the option of
following the terms and conditions either of that numbered version or
of any later version published by the Free Software Foundation.  If
the Program does not specify a version number of the GNU General
Public License, you may choose any version ever published by the Free
Software Foundation.

If the Program specifies that a proxy can decide which future versions
of the GNU General Public License can be used, that proxy&rsquo;s public
statement of acceptance of a version permanently authorizes you to
choose that version for the Program.

Later license versions may give you additional or different
permissions.  However, no additional obligations are imposed on any
author or copyright holder as a result of your choosing to follow a
later version.

16.  Disclaimer of Warranty.

THERE IS NO WARRANTY FOR THE PROGRAM, TO THE EXTENT PERMITTED BY
APPLICABLE LAW.  EXCEPT WHEN OTHERWISE STATED IN WRITING THE COPYRIGHT
HOLDERS AND/OR OTHER PARTIES PROVIDE THE PROGRAM &ldquo;AS IS&rdquo; WITHOUT
WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT
LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
A PARTICULAR PURPOSE.  THE ENTIRE RISK AS TO THE QUALITY AND
PERFORMANCE OF THE PROGRAM IS WITH YOU.  SHOULD THE PROGRAM PROVE
DEFECTIVE, YOU ASSUME THE COST OF ALL NECESSARY SERVICING, REPAIR OR
CORRECTION.

17.  Limitation of Liability.

IN NO EVENT UNLESS REQUIRED BY APPLICABLE LAW OR AGREED TO IN WRITING
WILL ANY COPYRIGHT HOLDER, OR ANY OTHER PARTY WHO MODIFIES AND/OR
CONVEYS THE PROGRAM AS PERMITTED ABOVE, BE LIABLE TO YOU FOR DAMAGES,
INCLUDING ANY GENERAL, SPECIAL, INCIDENTAL OR CONSEQUENTIAL DAMAGES
ARISING OUT OF THE USE OR INABILITY TO USE THE PROGRAM (INCLUDING BUT
NOT LIMITED TO LOSS OF DATA OR DATA BEING RENDERED INACCURATE OR
LOSSES SUSTAINED BY YOU OR THIRD PARTIES OR A FAILURE OF THE PROGRAM
TO OPERATE WITH ANY OTHER PROGRAMS), EVEN IF SUCH HOLDER OR OTHER
PARTY HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.

18.  Interpretation of Sections 15 and 16.

If the disclaimer of warranty and limitation of liability provided
above cannot be given local legal effect according to their terms,
reviewing courts shall apply local law that most closely approximates
an absolute waiver of all civil liability in connection with the
Program, unless a warranty or assumption of liability accompanies a
copy of the Program in return for a fee.

### END OF TERMS AND CONDITIONS

### How to Apply These Terms to Your New Programs

If you develop a new program, and you want it to be of the greatest
possible use to the public, the best way to achieve this is to make it
free software which everyone can redistribute and change under these
terms.

To do so, attach the following notices to the program.  It is safest
to attach them to the start of each source file to most effectively
state the exclusion of warranty; and each file should have at least
the &ldquo;copyright&rdquo; line and a pointer to where the full notice is found.

    one line to give the program's name and a brief idea of what it does.
    Copyright (C) yearname of author
    
    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or (at
    your option) any later version.
    
    This program is distributed in the hope that it will be useful, but
    WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
    General Public License for more details.
    
    You should have received a copy of the GNU General Public License
    along with this program.  If not, see [https://www.gnu.org/licenses/](https://www.gnu.org/licenses/).
    

Also add information on how to contact you by electronic and paper mail.

If the program does terminal interaction, make it output a short
notice like this when it starts in an interactive mode:

    program Copyright (C) yearname of author
    This program comes with ABSOLUTELY NO WARRANTY; for details type &lsquo;show w&rsquo;.
    This is free software, and you are welcome to redistribute it
    under certain conditions; type &lsquo;show c&rsquo; for details.
    

The hypothetical commands &lsquo;show w&rsquo; and &lsquo;show c&rsquo; should show
the appropriate parts of the General Public License.  Of course, your
program&rsquo;s commands might be different; for a GUI interface, you would
use an &ldquo;about box&rdquo;.

You should also get your employer (if you work as a programmer) or school,
if any, to sign a &ldquo;copyright disclaimer&rdquo; for the program, if necessary.
For more information on this, and how to apply and follow the GNU GPL, see
[https://www.gnu.org/licenses/](https://www.gnu.org/licenses/).

The GNU General Public License does not permit incorporating your
program into proprietary programs.  If your program is a subroutine
library, you may consider it more useful to permit linking proprietary
applications with the library.  If this is what you want to do, use
the GNU Lesser General Public License instead of this License.  But
first, please read [https://www.gnu.org/philosophy/why-not-lgpl.html](https://www.gnu.org/philosophy/why-not-lgpl.html).

---

Next: [Index](#Index), Previous: [Copying](#Copying), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## GNU Free Documentation License

Version 1.3, 3 November 2008

    Copyright &copy; 2000, 2001, 2002, 2007, 2008 Free Software Foundation, Inc.
    [https://fsf.org/](https://fsf.org/)
    
    Everyone is permitted to copy and distribute verbatim copies
    of this license document, but changing it is not allowed.
    

1.  PREAMBLE

The purpose of this License is to make a manual, textbook, or other
functional and useful document *free* in the sense of freedom: to
assure everyone the effective freedom to copy and redistribute it,
with or without modifying it, either commercially or noncommercially.
Secondarily, this License preserves for the author and publisher a way
to get credit for their work, while not being considered responsible
for modifications made by others.

This License is a kind of &ldquo;copyleft&rdquo;, which means that derivative
works of the document must themselves be free in the same sense.  It
complements the GNU General Public License, which is a copyleft
license designed for free software.

We have designed this License in order to use it for manuals for free
software, because free software needs free documentation: a free
program should come with manuals providing the same freedoms that the
software does.  But this License is not limited to software manuals;
it can be used for any textual work, regardless of subject matter or
whether it is published as a printed book.  We recommend this License
principally for works whose purpose is instruction or reference.

2.  APPLICABILITY AND DEFINITIONS

This License applies to any manual or other work, in any medium, that
contains a notice placed by the copyright holder saying it can be
distributed under the terms of this License.  Such a notice grants a
world-wide, royalty-free license, unlimited in duration, to use that
work under the conditions stated herein.  The &ldquo;Document&rdquo;, below,
refers to any such manual or work.  Any member of the public is a
licensee, and is addressed as &ldquo;you&rdquo;.  You accept the license if you
copy, modify or distribute the work in a way requiring permission
under copyright law.

A &ldquo;Modified Version&rdquo; of the Document means any work containing the
Document or a portion of it, either copied verbatim, or with
modifications and/or translated into another language.

A &ldquo;Secondary Section&rdquo; is a named appendix or a front-matter section
of the Document that deals exclusively with the relationship of the
publishers or authors of the Document to the Document&rsquo;s overall
subject (or to related matters) and contains nothing that could fall
directly within that overall subject.  (Thus, if the Document is in
part a textbook of mathematics, a Secondary Section may not explain
any mathematics.)  The relationship could be a matter of historical
connection with the subject or with related matters, or of legal,
commercial, philosophical, ethical or political position regarding
them.

The &ldquo;Invariant Sections&rdquo; are certain Secondary Sections whose titles
are designated, as being those of Invariant Sections, in the notice
that says that the Document is released under this License.  If a
section does not fit the above definition of Secondary then it is not
allowed to be designated as Invariant.  The Document may contain zero
Invariant Sections.  If the Document does not identify any Invariant
Sections then there are none.

The &ldquo;Cover Texts&rdquo; are certain short passages of text that are listed,
as Front-Cover Texts or Back-Cover Texts, in the notice that says that
the Document is released under this License.  A Front-Cover Text may
be at most 5 words, and a Back-Cover Text may be at most 25 words.

A &ldquo;Transparent&rdquo; copy of the Document means a machine-readable copy,
represented in a format whose specification is available to the
general public, that is suitable for revising the document
straightforwardly with generic text editors or (for images composed of
pixels) generic paint programs or (for drawings) some widely available
drawing editor, and that is suitable for input to text formatters or
for automatic translation to a variety of formats suitable for input
to text formatters.  A copy made in an otherwise Transparent file
format whose markup, or absence of markup, has been arranged to thwart
or discourage subsequent modification by readers is not Transparent.
An image format is not Transparent if used for any substantial amount
of text.  A copy that is not &ldquo;Transparent&rdquo; is called &ldquo;Opaque&rdquo;.

Examples of suitable formats for Transparent copies include plain
ASCII without markup, Texinfo input format, LaTeX input
format, SGML or XML using a publicly available
DTD, and standard-conforming simple HTML,
PostScript or PDF designed for human modification.  Examples
of transparent image formats include PNG, XCF and
JPG.  Opaque formats include proprietary formats that can be
read and edited only by proprietary word processors, SGML or
XML for which the DTD and/or processing tools are
not generally available, and the machine-generated HTML,
PostScript or PDF produced by some word processors for
output purposes only.

The &ldquo;Title Page&rdquo; means, for a printed book, the title page itself,
plus such following pages as are needed to hold, legibly, the material
this License requires to appear in the title page.  For works in
formats which do not have any title page as such, &ldquo;Title Page&rdquo; means
the text near the most prominent appearance of the work&rsquo;s title,
preceding the beginning of the body of the text.

The &ldquo;publisher&rdquo; means any person or entity that distributes copies
of the Document to the public.

A section &ldquo;Entitled XYZ&rdquo; means a named subunit of the Document whose
title either is precisely XYZ or contains XYZ in parentheses following
text that translates XYZ in another language.  (Here XYZ stands for a
specific section name mentioned below, such as &ldquo;Acknowledgements&rdquo;,
&ldquo;Dedications&rdquo;, &ldquo;Endorsements&rdquo;, or &ldquo;History&rdquo;.)  To &ldquo;Preserve the Title&rdquo;
of such a section when you modify the Document means that it remains a
section &ldquo;Entitled XYZ&rdquo; according to this definition.

The Document may include Warranty Disclaimers next to the notice which
states that this License applies to the Document.  These Warranty
Disclaimers are considered to be included by reference in this
License, but only as regards disclaiming warranties: any other
implication that these Warranty Disclaimers may have is void and has
no effect on the meaning of this License.

3.  VERBATIM COPYING

You may copy and distribute the Document in any medium, either
commercially or noncommercially, provided that this License, the
copyright notices, and the license notice saying this License applies
to the Document are reproduced in all copies, and that you add no other
conditions whatsoever to those of this License.  You may not use
technical measures to obstruct or control the reading or further
copying of the copies you make or distribute.  However, you may accept
compensation in exchange for copies.  If you distribute a large enough
number of copies you must also follow the conditions in section 3.

You may also lend copies, under the same conditions stated above, and
you may publicly display copies.

4.  COPYING IN QUANTITY

If you publish printed copies (or copies in media that commonly have
printed covers) of the Document, numbering more than 100, and the
Document&rsquo;s license notice requires Cover Texts, you must enclose the
copies in covers that carry, clearly and legibly, all these Cover
Texts: Front-Cover Texts on the front cover, and Back-Cover Texts on
the back cover.  Both covers must also clearly and legibly identify
you as the publisher of these copies.  The front cover must present
the full title with all words of the title equally prominent and
visible.  You may add other material on the covers in addition.
Copying with changes limited to the covers, as long as they preserve
the title of the Document and satisfy these conditions, can be treated
as verbatim copying in other respects.

If the required texts for either cover are too voluminous to fit
legibly, you should put the first ones listed (as many as fit
reasonably) on the actual cover, and continue the rest onto adjacent
pages.

If you publish or distribute Opaque copies of the Document numbering
more than 100, you must either include a machine-readable Transparent
copy along with each Opaque copy, or state in or with each Opaque copy
a computer-network location from which the general network-using
public has access to download using public-standard network protocols
a complete Transparent copy of the Document, free of added material.
If you use the latter option, you must take reasonably prudent steps,
when you begin distribution of Opaque copies in quantity, to ensure
that this Transparent copy will remain thus accessible at the stated
location until at least one year after the last time you distribute an
Opaque copy (directly or through your agents or retailers) of that
edition to the public.

It is requested, but not required, that you contact the authors of the
Document well before redistributing any large number of copies, to give
them a chance to provide you with an updated version of the Document.

5.  MODIFICATIONS

You may copy and distribute a Modified Version of the Document under
the conditions of sections 2 and 3 above, provided that you release
the Modified Version under precisely this License, with the Modified
Version filling the role of the Document, thus licensing distribution
and modification of the Modified Version to whoever possesses a copy
of it.  In addition, you must do these things in the Modified Version:

1.  Use in the Title Page (and on the covers, if any) a title distinct
from that of the Document, and from those of previous versions
(which should, if there were any, be listed in the History section
of the Document).  You may use the same title as a previous version
if the original publisher of that version gives permission.

2.  List on the Title Page, as authors, one or more persons or entities
responsible for authorship of the modifications in the Modified
Version, together with at least five of the principal authors of the
Document (all of its principal authors, if it has fewer than five),
unless they release you from this requirement.

3.  State on the Title page the name of the publisher of the
Modified Version, as the publisher.

4.  Preserve all the copyright notices of the Document.

5.  Add an appropriate copyright notice for your modifications
adjacent to the other copyright notices.

6.  Include, immediately after the copyright notices, a license notice
giving the public permission to use the Modified Version under the
terms of this License, in the form shown in the Addendum below.

7.  Preserve in that license notice the full lists of Invariant Sections
and required Cover Texts given in the Document&rsquo;s license notice.

8.  Include an unaltered copy of this License.

9.  Preserve the section Entitled &ldquo;History&rdquo;, Preserve its Title, and add
to it an item stating at least the title, year, new authors, and
publisher of the Modified Version as given on the Title Page.  If
there is no section Entitled &ldquo;History&rdquo; in the Document, create one
stating the title, year, authors, and publisher of the Document as
given on its Title Page, then add an item describing the Modified
Version as stated in the previous sentence.

10.  Preserve the network location, if any, given in the Document for
public access to a Transparent copy of the Document, and likewise
the network locations given in the Document for previous versions
it was based on.  These may be placed in the &ldquo;History&rdquo; section.
You may omit a network location for a work that was published at
least four years before the Document itself, or if the original
publisher of the version it refers to gives permission.

11.  For any section Entitled &ldquo;Acknowledgements&rdquo; or &ldquo;Dedications&rdquo;, Preserve
the Title of the section, and preserve in the section all the
substance and tone of each of the contributor acknowledgements and/or
dedications given therein.

12.  Preserve all the Invariant Sections of the Document,
unaltered in their text and in their titles.  Section numbers
or the equivalent are not considered part of the section titles.

13.  Delete any section Entitled &ldquo;Endorsements&rdquo;.  Such a section
may not be included in the Modified Version.

14.  Do not retitle any existing section to be Entitled &ldquo;Endorsements&rdquo; or
to conflict in title with any Invariant Section.

15.  Preserve any Warranty Disclaimers.

If the Modified Version includes new front-matter sections or
appendices that qualify as Secondary Sections and contain no material
copied from the Document, you may at your option designate some or all
of these sections as invariant.  To do this, add their titles to the
list of Invariant Sections in the Modified Version&rsquo;s license notice.
These titles must be distinct from any other section titles.

You may add a section Entitled &ldquo;Endorsements&rdquo;, provided it contains
nothing but endorsements of your Modified Version by various
parties&mdash;for example, statements of peer review or that the text has
been approved by an organization as the authoritative definition of a
standard.

You may add a passage of up to five words as a Front-Cover Text, and a
passage of up to 25 words as a Back-Cover Text, to the end of the list
of Cover Texts in the Modified Version.  Only one passage of
Front-Cover Text and one of Back-Cover Text may be added by (or
through arrangements made by) any one entity.  If the Document already
includes a cover text for the same cover, previously added by you or
by arrangement made by the same entity you are acting on behalf of,
you may not add another; but you may replace the old one, on explicit
permission from the previous publisher that added the old one.

The author(s) and publisher(s) of the Document do not by this License
give permission to use their names for publicity for or to assert or
imply endorsement of any Modified Version.

6.  COMBINING DOCUMENTS

You may combine the Document with other documents released under this
License, under the terms defined in section 4 above for modified
versions, provided that you include in the combination all of the
Invariant Sections of all of the original documents, unmodified, and
list them all as Invariant Sections of your combined work in its
license notice, and that you preserve all their Warranty Disclaimers.

The combined work need only contain one copy of this License, and
multiple identical Invariant Sections may be replaced with a single
copy.  If there are multiple Invariant Sections with the same name but
different contents, make the title of each such section unique by
adding at the end of it, in parentheses, the name of the original
author or publisher of that section if known, or else a unique number.
Make the same adjustment to the section titles in the list of
Invariant Sections in the license notice of the combined work.

In the combination, you must combine any sections Entitled &ldquo;History&rdquo;
in the various original documents, forming one section Entitled
&ldquo;History&rdquo;; likewise combine any sections Entitled &ldquo;Acknowledgements&rdquo;,
and any sections Entitled &ldquo;Dedications&rdquo;.  You must delete all
sections Entitled &ldquo;Endorsements.&rdquo;

7.  COLLECTIONS OF DOCUMENTS

You may make a collection consisting of the Document and other documents
released under this License, and replace the individual copies of this
License in the various documents with a single copy that is included in
the collection, provided that you follow the rules of this License for
verbatim copying of each of the documents in all other respects.

You may extract a single document from such a collection, and distribute
it individually under this License, provided you insert a copy of this
License into the extracted document, and follow this License in all
other respects regarding verbatim copying of that document.

8.  AGGREGATION WITH INDEPENDENT WORKS

A compilation of the Document or its derivatives with other separate
and independent documents or works, in or on a volume of a storage or
distribution medium, is called an &ldquo;aggregate&rdquo; if the copyright
resulting from the compilation is not used to limit the legal rights
of the compilation&rsquo;s users beyond what the individual works permit.
When the Document is included in an aggregate, this License does not
apply to the other works in the aggregate which are not themselves
derivative works of the Document.

If the Cover Text requirement of section 3 is applicable to these
copies of the Document, then if the Document is less than one half of
the entire aggregate, the Document&rsquo;s Cover Texts may be placed on
covers that bracket the Document within the aggregate, or the
electronic equivalent of covers if the Document is in electronic form.
Otherwise they must appear on printed covers that bracket the whole
aggregate.

9.  TRANSLATION

Translation is considered a kind of modification, so you may
distribute translations of the Document under the terms of section 4.
Replacing Invariant Sections with translations requires special
permission from their copyright holders, but you may include
translations of some or all Invariant Sections in addition to the
original versions of these Invariant Sections.  You may include a
translation of this License, and all the license notices in the
Document, and any Warranty Disclaimers, provided that you also include
the original English version of this License and the original versions
of those notices and disclaimers.  In case of a disagreement between
the translation and the original version of this License or a notice
or disclaimer, the original version will prevail.

If a section in the Document is Entitled &ldquo;Acknowledgements&rdquo;,
&ldquo;Dedications&rdquo;, or &ldquo;History&rdquo;, the requirement (section 4) to Preserve
its Title (section 1) will typically require changing the actual
title.

10.  TERMINATION

You may not copy, modify, sublicense, or distribute the Document
except as expressly provided under this License.  Any attempt
otherwise to copy, modify, sublicense, or distribute it is void, and
will automatically terminate your rights under this License.

However, if you cease all violation of this License, then your license
from a particular copyright holder is reinstated (a) provisionally,
unless and until the copyright holder explicitly and finally
terminates your license, and (b) permanently, if the copyright holder
fails to notify you of the violation by some reasonable means prior to
60 days after the cessation.

Moreover, your license from a particular copyright holder is
reinstated permanently if the copyright holder notifies you of the
violation by some reasonable means, this is the first time you have
received notice of violation of this License (for any work) from that
copyright holder, and you cure the violation prior to 30 days after
your receipt of the notice.

Termination of your rights under this section does not terminate the
licenses of parties who have received copies or rights from you under
this License.  If your rights have been terminated and not permanently
reinstated, receipt of a copy of some or all of the same material does
not give you any rights to use it.

11.  FUTURE REVISIONS OF THIS LICENSE

The Free Software Foundation may publish new, revised versions
of the GNU Free Documentation License from time to time.  Such new
versions will be similar in spirit to the present version, but may
differ in detail to address new problems or concerns.  See
[https://www.gnu.org/copyleft/](https://www.gnu.org/copyleft/).

Each version of the License is given a distinguishing version number.
If the Document specifies that a particular numbered version of this
License &ldquo;or any later version&rdquo; applies to it, you have the option of
following the terms and conditions either of that specified version or
of any later version that has been published (not as a draft) by the
Free Software Foundation.  If the Document does not specify a version
number of this License, you may choose any version ever published (not
as a draft) by the Free Software Foundation.  If the Document
specifies that a proxy can decide which future versions of this
License can be used, that proxy&rsquo;s public statement of acceptance of a
version permanently authorizes you to choose that version for the
Document.

12.  RELICENSING

&ldquo;Massive Multiauthor Collaboration Site&rdquo; (or &ldquo;MMC Site&rdquo;) means any
World Wide Web server that publishes copyrightable works and also
provides prominent facilities for anybody to edit those works.  A
public wiki that anybody can edit is an example of such a server.  A
&ldquo;Massive Multiauthor Collaboration&rdquo; (or &ldquo;MMC&rdquo;) contained in the
site means any set of copyrightable works thus published on the MMC
site.

&ldquo;CC-BY-SA&rdquo; means the Creative Commons Attribution-Share Alike 3.0
license published by Creative Commons Corporation, a not-for-profit
corporation with a principal place of business in San Francisco,
California, as well as future copyleft versions of that license
published by that same organization.

&ldquo;Incorporate&rdquo; means to publish or republish a Document, in whole or
in part, as part of another Document.

An MMC is &ldquo;eligible for relicensing&rdquo; if it is licensed under this
License, and if all works that were first published under this License
somewhere other than this MMC, and subsequently incorporated in whole
or in part into the MMC, (1) had no cover texts or invariant sections,
and (2) were thus incorporated prior to November 1, 2008.

The operator of an MMC Site may republish an MMC contained in the site
under CC-BY-SA on the same site at any time before August 1, 2009,
provided the MMC is eligible for relicensing.

### ADDENDUM: How to use this License for your documents

To use this License in a document you have written, include a copy of
the License in the document and put the following copyright and
license notices just after the title page:

      Copyright (C)  yearyour name.
      Permission is granted to copy, distribute and/or modify this document
      under the terms of the GNU Free Documentation License, Version 1.3
      or any later version published by the Free Software Foundation;
      with no Invariant Sections, no Front-Cover Texts, and no Back-Cover
      Texts.  A copy of the license is included in the section entitled ``GNU
      Free Documentation License''.
    

If you have Invariant Sections, Front-Cover Texts and Back-Cover Texts,
replace the &ldquo;with&hellip;Texts.&rdquo; line with this:

        with the Invariant Sections being list their titles, with
        the Front-Cover Texts being list, and with the Back-Cover Texts
        being list.
    

If you have Invariant Sections without Cover Texts, or some other
combination of the three, merge those two alternatives to suit the
situation.

If your document contains nontrivial examples of program code, we
recommend releasing these examples in parallel under your choice of
free software license, such as the GNU General Public License,
to permit their use in free software.

---

Previous: [GNU Free Documentation License](#GNU-Free-Documentation-License), Up: [Top](#Top)   [[Contents](#SEC_Contents)][[Index](#Index)]

## Index
Jump to:   [!](#Index_cp_symbol-1)
   
["](#Index_cp_symbol-2)
   
[#](#Index_cp_symbol-3)
   
[$](#Index_cp_symbol-4)
   
[%](#Index_cp_symbol-5)
   
[&](#Index_cp_symbol-6)
   
['](#Index_cp_symbol-7)
   
[(](#Index_cp_symbol-8)
   
[*](#Index_cp_symbol-9)
   
[+](#Index_cp_symbol-10)
   
[,](#Index_cp_symbol-11)
   
[-](#Index_cp_symbol-12)
   
[.](#Index_cp_symbol-13)
   
[/](#Index_cp_symbol-14)
   
[;](#Index_cp_symbol-15)
   
[<](#Index_cp_symbol-16)
   
[=](#Index_cp_symbol-17)
   
[>](#Index_cp_symbol-18)
   
[?](#Index_cp_symbol-19)
   
[@](#Index_cp_symbol-20)
   
[[](#Index_cp_symbol-21)
   
[\](#Index_cp_symbol-22)
   
[^](#Index_cp_symbol-23)
   
[_](#Index_cp_symbol-24)
   
[{](#Index_cp_symbol-25)
   
[|](#Index_cp_symbol-26)
   
[~](#Index_cp_symbol-27)
   

[A](#Index_cp_letter-A)
   
[B](#Index_cp_letter-B)
   
[C](#Index_cp_letter-C)
   
[D](#Index_cp_letter-D)
   
[E](#Index_cp_letter-E)
   
[F](#Index_cp_letter-F)
   
[G](#Index_cp_letter-G)
   
[H](#Index_cp_letter-H)
   
[I](#Index_cp_letter-I)
   
[J](#Index_cp_letter-J)
   
[K](#Index_cp_letter-K)
   
[L](#Index_cp_letter-L)
   
[M](#Index_cp_letter-M)
   
[N](#Index_cp_letter-N)
   
[O](#Index_cp_letter-O)
   
[P](#Index_cp_letter-P)
   
[Q](#Index_cp_letter-Q)
   
[R](#Index_cp_letter-R)
   
[S](#Index_cp_letter-S)
   
[T](#Index_cp_letter-T)
   
[U](#Index_cp_letter-U)
   
[V](#Index_cp_letter-V)
   
[W](#Index_cp_letter-W)
   
[X](#Index_cp_letter-X)
   
[Y](#Index_cp_letter-Y)
   
[Z](#Index_cp_letter-Z)
   
Index Entry  Section
---
[!](!)[`!` (exclamation point), `!`  operator](#index-_0021-_0028exclamation-point_0029_002c-_0021-operator): [Boolean Ops](#Boolean-Ops)[`!` (exclamation point), `!` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021-operator-1): [Precedence](#Precedence)[`!` (exclamation point), `!` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021-operator-2): [Ranges](#Ranges)[`!` (exclamation point), `!` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021-operator-3): [Egrep Program](#Egrep-Program)[`!` (exclamation point), `!=` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_003d-operator): [Comparison Operators](#Comparison-Operators)[`!` (exclamation point), `!=` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_003d-operator-1): [Precedence](#Precedence)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator): [Regexp Usage](#Regexp-Usage)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-1): [Computed Regexps](#Computed-Regexps)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-2): [Case-sensitivity](#Case_002dsensitivity)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-3): [Regexp Constants](#Regexp-Constants)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-4): [Comparison Operators](#Comparison-Operators)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-5): [Comparison Operators](#Comparison-Operators)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-6): [Precedence](#Precedence)[`!` (exclamation point), `!~` operator](#index-_0021-_0028exclamation-point_0029_002c-_0021_007e-operator-7): [Expression Patterns](#Expression-Patterns)
---
["](")[`"` (double quote), in regexp constants](#index-_0022-_0028double-quote_0029_002c-in-regexp-constants): [Computed Regexps](#Computed-Regexps)[`"` (double quote), in shell commands](#index-_0022-_0028double-quote_0029_002c-in-shell-commands): [Quoting](#Quoting)
---
[#](#)[`#` (number sign), `#!` (executable scripts)](#index-_0023-_0028number-sign_0029_002c-_0023_0021-_0028executable-scripts_0029): [Executable Scripts](#Executable-Scripts)[`#` (number sign), commenting](#index-_0023-_0028number-sign_0029_002c-commenting): [Comments](#Comments)
---
[$]($)[`$` (dollar sign), `$` field operator](#index-_0024-_0028dollar-sign_0029_002c-_0024-field-operator): [Fields](#Fields)[`$` (dollar sign), `$` field operator](#index-_0024-_0028dollar-sign_0029_002c-_0024-field-operator-1): [Precedence](#Precedence)[`$` (dollar sign), incrementing fields and arrays](#index-_0024-_0028dollar-sign_0029_002c-incrementing-fields-and-arrays): [Increment Ops](#Increment-Ops)[`$` (dollar sign), regexp operator](#index-_0024-_0028dollar-sign_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)
---
[%](%)[`%` (percent sign), `%` operator](#index-_0025-_0028percent-sign_0029_002c-_0025-operator): [Precedence](#Precedence)[`%` (percent sign), `%=` operator](#index-_0025-_0028percent-sign_0029_002c-_0025_003d-operator): [Assignment Ops](#Assignment-Ops)[`%` (percent sign), `%=` operator](#index-_0025-_0028percent-sign_0029_002c-_0025_003d-operator-1): [Precedence](#Precedence)
---
[&](&)[`&` (ampersand), `&&` operator](#index-_0026-_0028ampersand_0029_002c-_0026_0026-operator): [Boolean Ops](#Boolean-Ops)[`&` (ampersand), `&&` operator](#index-_0026-_0028ampersand_0029_002c-_0026_0026-operator-1): [Precedence](#Precedence)[`&` (ampersand), `gsub()`/`gensub()`/`sub()` functions and](#index-_0026-_0028ampersand_0029_002c-gsub_0028_0029_002fgensub_0028_0029_002fsub_0028_0029-functions-and): [Gory Details](#Gory-Details)
---
['](')[`'` (single quote)](#index-_0027-_0028single-quote_0029): [One-shot](#One_002dshot)[`'` (single quote) in `gawk` command lines](#index-_0027-_0028single-quote_0029-in-gawk-command-lines): [Long](#Long)[`'` (single quote), in shell commands](#index-_0027-_0028single-quote_0029_002c-in-shell-commands): [Quoting](#Quoting)[`'` (single quote), vs. apostrophe](#index-_0027-_0028single-quote_0029_002c-vs_002e-apostrophe): [Comments](#Comments)[`'` (single quote), with double quotes](#index-_0027-_0028single-quote_0029_002c-with-double-quotes): [Quoting](#Quoting)
---
[(](()[`()` (parentheses), in a profile](#index-_0028_0029-_0028parentheses_0029_002c-in-a-profile): [Profiling](#Profiling)[`()` (parentheses), regexp operator](#index-_0028_0029-_0028parentheses_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)
---
[*](*)[`*` (asterisk), `*` operator, as multiplication operator](#index-_002a-_0028asterisk_0029_002c-_002a-operator_002c-as-multiplication-operator): [Precedence](#Precedence)[`*` (asterisk), `*` operator, as regexp operator](#index-_002a-_0028asterisk_0029_002c-_002a-operator_002c-as-regexp-operator): [Regexp Operators](#Regexp-Operators)[`*` (asterisk), `*` operator, null strings, matching](#index-_002a-_0028asterisk_0029_002c-_002a-operator_002c-null-strings_002c-matching): [String Functions](#String-Functions)[`*` (asterisk), `**` operator](#index-_002a-_0028asterisk_0029_002c-_002a_002a-operator): [Arithmetic Ops](#Arithmetic-Ops)[`*` (asterisk), `**` operator](#index-_002a-_0028asterisk_0029_002c-_002a_002a-operator-1): [Precedence](#Precedence)[`*` (asterisk), `**=` operator](#index-_002a-_0028asterisk_0029_002c-_002a_002a_003d-operator): [Assignment Ops](#Assignment-Ops)[`*` (asterisk), `**=` operator](#index-_002a-_0028asterisk_0029_002c-_002a_002a_003d-operator-1): [Precedence](#Precedence)[`*` (asterisk), `*=` operator](#index-_002a-_0028asterisk_0029_002c-_002a_003d-operator): [Assignment Ops](#Assignment-Ops)[`*` (asterisk), `*=` operator](#index-_002a-_0028asterisk_0029_002c-_002a_003d-operator-1): [Precedence](#Precedence)
---
[+](+)[`+` (plus sign), `+` operator](#index-_002b-_0028plus-sign_0029_002c-_002b-operator): [Precedence](#Precedence)[`+` (plus sign), `+` operator](#index-_002b-_0028plus-sign_0029_002c-_002b-operator-1): [Precedence](#Precedence)[`+` (plus sign), `++` operator](#index-_002b-_0028plus-sign_0029_002c-_002b_002b-operator): [Increment Ops](#Increment-Ops)[`+` (plus sign), `++` operator](#index-_002b-_0028plus-sign_0029_002c-_002b_002b-operator-1): [Increment Ops](#Increment-Ops)[`+` (plus sign), `++` operator](#index-_002b-_0028plus-sign_0029_002c-_002b_002b-operator-2): [Precedence](#Precedence)[`+` (plus sign), `+=` operator](#index-_002b-_0028plus-sign_0029_002c-_002b_003d-operator): [Assignment Ops](#Assignment-Ops)[`+` (plus sign), `+=` operator](#index-_002b-_0028plus-sign_0029_002c-_002b_003d-operator-1): [Precedence](#Precedence)[`+` (plus sign), regexp operator](#index-_002b-_0028plus-sign_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)
---
[,](,)[`,` (comma), in range patterns](#index-_002c-_0028comma_0029_002c-in-range-patterns): [Ranges](#Ranges)
---
[-](-)[`-` (hyphen), `-` operator](#index-_002d-_0028hyphen_0029_002c-_002d-operator): [Precedence](#Precedence)[`-` (hyphen), `-` operator](#index-_002d-_0028hyphen_0029_002c-_002d-operator-1): [Precedence](#Precedence)[`-` (hyphen), `--` operator](#index-_002d-_0028hyphen_0029_002c-_002d_002d-operator): [Increment Ops](#Increment-Ops)[`-` (hyphen), `--` operator](#index-_002d-_0028hyphen_0029_002c-_002d_002d-operator-1): [Precedence](#Precedence)[`-` (hyphen), `-=` operator](#index-_002d-_0028hyphen_0029_002c-_002d_003d-operator): [Assignment Ops](#Assignment-Ops)[`-` (hyphen), `-=` operator](#index-_002d-_0028hyphen_0029_002c-_002d_003d-operator-1): [Precedence](#Precedence)[`-` (hyphen), filenames beginning with](#index-_002d-_0028hyphen_0029_002c-filenames-beginning-with): [Options](#Options)[`-` (hyphen), in bracket expressions](#index-_002d-_0028hyphen_0029_002c-in-bracket-expressions): [Bracket Expressions](#Bracket-Expressions)[--assign option](#index-_002d_002dassign-option): [Options](#Options)[--bignum option](#index-_002d_002dbignum-option): [Options](#Options)[--characters-as-bytes option](#index-_002d_002dcharacters_002das_002dbytes-option): [Options](#Options)[--copyright option](#index-_002d_002dcopyright-option): [Options](#Options)[--debug option](#index-_002d_002ddebug-option): [Options](#Options)[--disable-extensions configuration option](#index-_002d_002ddisable_002dextensions-configuration-option): [Additional Configuration Options](#Additional-Configuration-Options)[--disable-lint configuration option](#index-_002d_002ddisable_002dlint-configuration-option): [Additional Configuration Options](#Additional-Configuration-Options)[--disable-mpfr configuration option](#index-_002d_002ddisable_002dmpfr-configuration-option): [Additional Configuration Options](#Additional-Configuration-Options)[--disable-nls configuration option](#index-_002d_002ddisable_002dnls-configuration-option): [Additional Configuration Options](#Additional-Configuration-Options)[--dump-variables option](#index-_002d_002ddump_002dvariables-option): [Options](#Options)[--dump-variables option, using for library functions](#index-_002d_002ddump_002dvariables-option_002c-using-for-library-functions): [Library Names](#Library-Names)[--enable-versioned-extension-dir configuration option](#index-_002d_002denable_002dversioned_002dextension_002ddir-configuration-option): [Additional Configuration Options](#Additional-Configuration-Options)[--exec option](#index-_002d_002dexec-option): [Options](#Options)[--field-separator option](#index-_002d_002dfield_002dseparator-option): [Options](#Options)[--file option](#index-_002d_002dfile-option): [Options](#Options)[--gen-pot option](#index-_002d_002dgen_002dpot-option): [Options](#Options)[--gen-pot option](#index-_002d_002dgen_002dpot-option-1): [String Extraction](#String-Extraction)[--gen-pot option](#index-_002d_002dgen_002dpot-option-2): [String Extraction](#String-Extraction)[--help option](#index-_002d_002dhelp-option): [Options](#Options)[--include option](#index-_002d_002dinclude-option): [Options](#Options)[--lint option](#index-_002d_002dlint-option): [Command Line](#Command-Line)[--lint option](#index-_002d_002dlint-option-1): [Options](#Options)[--lint-old option](#index-_002d_002dlint_002dold-option): [Options](#Options)[--load option](#index-_002d_002dload-option): [Options](#Options)[--no-optimize option](#index-_002d_002dno_002doptimize-option): [Options](#Options)[--non-decimal-data option](#index-_002d_002dnon_002ddecimal_002ddata-option): [Options](#Options)[--non-decimal-data option](#index-_002d_002dnon_002ddecimal_002ddata-option-1): [Nondecimal Data](#Nondecimal-Data)[--non-decimal-data option, `strtonum()` function and](#index-_002d_002dnon_002ddecimal_002ddata-option_002c-strtonum_0028_0029-function-and): [Nondecimal Data](#Nondecimal-Data)[--optimize option](#index-_002d_002doptimize-option): [Options](#Options)[--posix option](#index-_002d_002dposix-option): [Options](#Options)[--posix option, `--traditional` option and](#index-_002d_002dposix-option_002c-_002d_002dtraditional-option-and): [Options](#Options)[--pretty-print option](#index-_002d_002dpretty_002dprint-option): [Options](#Options)[--profile option](#index-_002d_002dprofile-option): [Options](#Options)[--profile option](#index-_002d_002dprofile-option-1): [Profiling](#Profiling)[--re-interval option](#index-_002d_002dre_002dinterval-option): [Options](#Options)[--sandbox option](#index-_002d_002dsandbox-option): [Options](#Options)[--sandbox option, disabling `system()` function](#index-_002d_002dsandbox-option_002c-disabling-system_0028_0029-function): [I/O Functions](#I_002fO-Functions)[--sandbox option, input redirection with `getline`](#index-_002d_002dsandbox-option_002c-input-redirection-with-getline): [Getline](#Getline)[--sandbox option, output redirection with `print`, `printf`](#index-_002d_002dsandbox-option_002c-output-redirection-with-print_002c-printf): [Redirection](#Redirection)[--source option](#index-_002d_002dsource-option): [Options](#Options)[--traditional option](#index-_002d_002dtraditional-option): [Options](#Options)[--traditional option, `--posix` option and](#index-_002d_002dtraditional-option_002c-_002d_002dposix-option-and): [Options](#Options)[--use-lc-numeric option](#index-_002d_002duse_002dlc_002dnumeric-option): [Options](#Options)[--version option](#index-_002d_002dversion-option): [Options](#Options)[--with-whiny-user-strftime configuration option](#index-_002d_002dwith_002dwhiny_002duser_002dstrftime-configuration-option): [Additional Configuration Options](#Additional-Configuration-Options)[-b option](#index-_002db-option): [Options](#Options)[-c option](#index-_002dc-option): [Options](#Options)[-C option](#index-_002dC-option): [Options](#Options)[-d option](#index-_002dd-option): [Options](#Options)[-D option](#index-_002dD-option): [Options](#Options)[-e option](#index-_002de-option): [Options](#Options)[-E option](#index-_002dE-option): [Options](#Options)[-e option](#index-_002de-option-1): [Options](#Options)[-f option](#index-_002df-option): [Long](#Long)[-F option](#index-_002dF-option): [Options](#Options)[-f option](#index-_002df-option-1): [Options](#Options)[-F option, -Ft sets `FS` to TAB](#index-_002dF-option_002c-_002dFt-sets-FS-to-TAB): [Options](#Options)[-F option, command-line](#index-_002dF-option_002c-command_002dline): [Command Line Field Separator](#Command-Line-Field-Separator)[-f option, multiple uses](#index-_002df-option_002c-multiple-uses): [Options](#Options)[-g option](#index-_002dg-option): [Options](#Options)[-h option](#index-_002dh-option): [Options](#Options)[-i option](#index-_002di-option): [Options](#Options)[-l option](#index-_002dl-option): [Options](#Options)[-l option](#index-_002dl-option-1): [Options](#Options)[-L option](#index-_002dL-option): [Options](#Options)[-M option](#index-_002dM-option): [Options](#Options)[-n option](#index-_002dn-option): [Options](#Options)[-N option](#index-_002dN-option): [Options](#Options)[-o option](#index-_002do-option): [Options](#Options)[-O option](#index-_002dO-option): [Options](#Options)[-p option](#index-_002dp-option): [Options](#Options)[-P option](#index-_002dP-option): [Options](#Options)[-r option](#index-_002dr-option): [Options](#Options)[-s option](#index-_002ds-option): [Options](#Options)[-S option](#index-_002dS-option): [Options](#Options)[-v option](#index-_002dv-option): [Options](#Options)[-V option](#index-_002dV-option): [Options](#Options)[-v option](#index-_002dv-option-1): [Assignment Options](#Assignment-Options)[-W option](#index-_002dW-option): [Options](#Options)
---
[.](.)[`.` (period), regexp operator](#index-_002e-_0028period_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[`.gmo` files](#index-_002egmo-files): [Explaining gettext](#Explaining-gettext)[`.gmo` files, specifying directory of](#index-_002egmo-files_002c-specifying-directory-of): [Explaining gettext](#Explaining-gettext)[`.gmo` files, specifying directory of](#index-_002egmo-files_002c-specifying-directory-of-1): [Programmer i18n](#Programmer-i18n)[`.mo` files, converting from `.po`](#index-_002emo-files_002c-converting-from-_002epo): [I18N Example](#I18N-Example)[`.po` files](#index-_002epo-files): [Explaining gettext](#Explaining-gettext)[`.po` files](#index-_002epo-files-1): [Translator i18n](#Translator-i18n)[`.po` files, converting to `.mo`](#index-_002epo-files_002c-converting-to-_002emo): [I18N Example](#I18N-Example)[`.pot` files](#index-_002epot-files): [Explaining gettext](#Explaining-gettext)
---
[/](/)[`/` (forward slash) to enclose regular expressions](#index-_002f-_0028forward-slash_0029-to-enclose-regular-expressions): [Regexp](#Regexp)[`/` (forward slash), `/` operator](#index-_002f-_0028forward-slash_0029_002c-_002f-operator): [Precedence](#Precedence)[`/` (forward slash), `/=` operator](#index-_002f-_0028forward-slash_0029_002c-_002f_003d-operator): [Assignment Ops](#Assignment-Ops)[`/` (forward slash), `/=` operator](#index-_002f-_0028forward-slash_0029_002c-_002f_003d-operator-1): [Precedence](#Precedence)[`/` (forward slash), `/=` operator, vs. `/=&hellip;/` regexp constant](#index-_002f-_0028forward-slash_0029_002c-_002f_003d-operator_002c-vs_002e-_002f_003d_2026_002f-regexp-constant): [Assignment Ops](#Assignment-Ops)[`/` (forward slash), patterns and](#index-_002f-_0028forward-slash_0029_002c-patterns-and): [Expression Patterns](#Expression-Patterns)[`/=` operator vs. `/=&hellip;/` regexp constant](#index-_002f_003d-operator-vs_002e-_002f_003d_2026_002f-regexp-constant): [Assignment Ops](#Assignment-Ops)[`/dev/&hellip;` special files](#index-_002fdev_002f_2026-special-files): [Special FD](#Special-FD)[`/dev/fd/N` special files (`gawk`)](#index-_002fdev_002ffd_002fN-special-files-_0028gawk_0029): [Special FD](#Special-FD)[`/inet/&hellip;` special files (`gawk`)](#index-_002finet_002f_2026-special-files-_0028gawk_0029): [TCP/IP Networking](#TCP_002fIP-Networking)[`/inet4/&hellip;` special files (`gawk`)](#index-_002finet4_002f_2026-special-files-_0028gawk_0029): [TCP/IP Networking](#TCP_002fIP-Networking)[`/inet6/&hellip;` special files (`gawk`)](#index-_002finet6_002f_2026-special-files-_0028gawk_0029): [TCP/IP Networking](#TCP_002fIP-Networking)
---
[;](;)[`;` (semicolon), `AWKPATH` variable and](#index-_003b-_0028semicolon_0029_002c-AWKPATH-variable-and): [PC Using](#PC-Using)[`;` (semicolon), separating statements in actions](#index-_003b-_0028semicolon_0029_002c-separating-statements-in-actions): [Statements/Lines](#Statements_002fLines)[`;` (semicolon), separating statements in actions](#index-_003b-_0028semicolon_0029_002c-separating-statements-in-actions-1): [Action Overview](#Action-Overview)[`;` (semicolon), separating statements in actions](#index-_003b-_0028semicolon_0029_002c-separating-statements-in-actions-2): [Statements](#Statements)
---
[<](<)[`<` (left angle bracket), `<` operator](#index-_003c-_0028left-angle-bracket_0029_002c-_003c-operator): [Comparison Operators](#Comparison-Operators)[`<` (left angle bracket), `<` operator](#index-_003c-_0028left-angle-bracket_0029_002c-_003c-operator-1): [Precedence](#Precedence)[`<` (left angle bracket), `<` operator (I/O)](#index-_003c-_0028left-angle-bracket_0029_002c-_003c-operator-_0028I_002fO_0029): [Getline/File](#Getline_002fFile)[`<` (left angle bracket), `<=` operator](#index-_003c-_0028left-angle-bracket_0029_002c-_003c_003d-operator): [Comparison Operators](#Comparison-Operators)[`<` (left angle bracket), `<=` operator](#index-_003c-_0028left-angle-bracket_0029_002c-_003c_003d-operator-1): [Precedence](#Precedence)
---
[=](=)[`=` (equals sign), `=` operator](#index-_003d-_0028equals-sign_0029_002c-_003d-operator): [Assignment Ops](#Assignment-Ops)[`=` (equals sign), `==` operator](#index-_003d-_0028equals-sign_0029_002c-_003d_003d-operator): [Comparison Operators](#Comparison-Operators)[`=` (equals sign), `==` operator](#index-_003d-_0028equals-sign_0029_002c-_003d_003d-operator-1): [Precedence](#Precedence)
---
[>](>)[`>` (right angle bracket), `>` operator](#index-_003e-_0028right-angle-bracket_0029_002c-_003e-operator): [Comparison Operators](#Comparison-Operators)[`>` (right angle bracket), `>` operator](#index-_003e-_0028right-angle-bracket_0029_002c-_003e-operator-1): [Precedence](#Precedence)[`>` (right angle bracket), `>` operator (I/O)](#index-_003e-_0028right-angle-bracket_0029_002c-_003e-operator-_0028I_002fO_0029): [Redirection](#Redirection)[`>` (right angle bracket), `>=` operator](#index-_003e-_0028right-angle-bracket_0029_002c-_003e_003d-operator): [Comparison Operators](#Comparison-Operators)[`>` (right angle bracket), `>=` operator](#index-_003e-_0028right-angle-bracket_0029_002c-_003e_003d-operator-1): [Precedence](#Precedence)[`>` (right angle bracket), `>>` operator (I/O)](#index-_003e-_0028right-angle-bracket_0029_002c-_003e_003e-operator-_0028I_002fO_0029): [Redirection](#Redirection)[`>` (right angle bracket), `>>` operator (I/O)](#index-_003e-_0028right-angle-bracket_0029_002c-_003e_003e-operator-_0028I_002fO_0029-1): [Precedence](#Precedence)
---
[?](?)[`?` (question mark), `?:` operator](#index-_003f-_0028question-mark_0029_002c-_003f_003a-operator): [Precedence](#Precedence)[`?` (question mark), regexp operator](#index-_003f-_0028question-mark_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[`?` (question mark), regexp operator](#index-_003f-_0028question-mark_0029_002c-regexp-operator-1): [GNU Regexp Operators](#GNU-Regexp-Operators)
---
[@](@)[`@`-notation for indirect function calls](#index-_0040_002dnotation-for-indirect-function-calls): [Indirect Calls](#Indirect-Calls)[`@include` directive](#index-_0040include-directive): [Include Files](#Include-Files)[`@load` directive](#index-_0040load-directive): [Loading Shared Libraries](#Loading-Shared-Libraries)
---
[[]([)[`[]` (square brackets), regexp operator](#index-_005b_005d-_0028square-brackets_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)
---
[\](\)[`\` (backslash)](#index-_005c-_0028backslash_0029): [Comments](#Comments)[`\` (backslash), as field separator](#index-_005c-_0028backslash_0029_002c-as-field-separator): [Command Line Field Separator](#Command-Line-Field-Separator)[`\` (backslash), continuing lines and](#index-_005c-_0028backslash_0029_002c-continuing-lines-and): [Statements/Lines](#Statements_002fLines)[`\` (backslash), continuing lines and, comments and](#index-_005c-_0028backslash_0029_002c-continuing-lines-and_002c-comments-and): [Statements/Lines](#Statements_002fLines)[`\` (backslash), continuing lines and, in `csh`](#index-_005c-_0028backslash_0029_002c-continuing-lines-and_002c-in-csh): [Statements/Lines](#Statements_002fLines)[`\` (backslash), `gsub()`/`gensub()`/`sub()` functions and](#index-_005c-_0028backslash_0029_002c-gsub_0028_0029_002fgensub_0028_0029_002fsub_0028_0029-functions-and): [Gory Details](#Gory-Details)[`\` (backslash), in bracket expressions](#index-_005c-_0028backslash_0029_002c-in-bracket-expressions): [Bracket Expressions](#Bracket-Expressions)[`\` (backslash), in escape sequences](#index-_005c-_0028backslash_0029_002c-in-escape-sequences): [Escape Sequences](#Escape-Sequences)[`\` (backslash), in escape sequences](#index-_005c-_0028backslash_0029_002c-in-escape-sequences-1): [Escape Sequences](#Escape-Sequences)[`\` (backslash), in escape sequences, POSIX and](#index-_005c-_0028backslash_0029_002c-in-escape-sequences_002c-POSIX-and): [Escape Sequences](#Escape-Sequences)[`\` (backslash), in regexp constants](#index-_005c-_0028backslash_0029_002c-in-regexp-constants): [Computed Regexps](#Computed-Regexps)[`\` (backslash), in shell commands](#index-_005c-_0028backslash_0029_002c-in-shell-commands): [Quoting](#Quoting)[`\` (backslash), regexp operator](#index-_005c-_0028backslash_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[`\` (backslash), `\"` escape sequence](#index-_005c-_0028backslash_0029_002c-_005c_0022-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\'` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005c_0027-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\/` escape sequence](#index-_005c-_0028backslash_0029_002c-_005c_002f-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\<` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005c_003c-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\>` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005c_003e-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\a` escape sequence](#index-_005c-_0028backslash_0029_002c-_005ca-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\b` escape sequence](#index-_005c-_0028backslash_0029_002c-_005cb-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\B` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005cB-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\f` escape sequence](#index-_005c-_0028backslash_0029_002c-_005cf-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\n` escape sequence](#index-_005c-_0028backslash_0029_002c-_005cn-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\`nnn escape sequence](#index-_005c-_0028backslash_0029_002c-_005cnnn-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\r` escape sequence](#index-_005c-_0028backslash_0029_002c-_005cr-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\s` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005cs-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\S` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005cS-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\t` escape sequence](#index-_005c-_0028backslash_0029_002c-_005ct-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\v` escape sequence](#index-_005c-_0028backslash_0029_002c-_005cv-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\w` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005cw-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\W` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005cW-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\x` escape sequence](#index-_005c-_0028backslash_0029_002c-_005cx-escape-sequence): [Escape Sequences](#Escape-Sequences)[`\` (backslash), `\y` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005cy-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`\` (backslash), `\`` operator (`gawk`)](#index-_005c-_0028backslash_0029_002c-_005c_0060-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)
---
[^](^)[`^` (caret), in bracket expressions](#index-_005e-_0028caret_0029_002c-in-bracket-expressions): [Bracket Expressions](#Bracket-Expressions)[`^` (caret), in `FS`](#index-_005e-_0028caret_0029_002c-in-FS): [Regexp Field Splitting](#Regexp-Field-Splitting)[`^` (caret), regexp operator](#index-_005e-_0028caret_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[`^` (caret), regexp operator](#index-_005e-_0028caret_0029_002c-regexp-operator-1): [GNU Regexp Operators](#GNU-Regexp-Operators)[`^` (caret), `^` operator](#index-_005e-_0028caret_0029_002c-_005e-operator): [Precedence](#Precedence)[`^` (caret), `^=` operator](#index-_005e-_0028caret_0029_002c-_005e_003d-operator): [Assignment Ops](#Assignment-Ops)[`^` (caret), `^=` operator](#index-_005e-_0028caret_0029_002c-_005e_003d-operator-1): [Precedence](#Precedence)
---
[_](_)[`_` (underscore), C macro](#index-_005f-_0028underscore_0029_002c-C-macro): [Explaining gettext](#Explaining-gettext)[`_` (underscore), in names of private variables](#index-_005f-_0028underscore_0029_002c-in-names-of-private-variables): [Library Names](#Library-Names)[`_` (underscore), translatable string](#index-_005f-_0028underscore_0029_002c-translatable-string): [Programmer i18n](#Programmer-i18n)[`_gr_init()` user-defined function](#index-_005fgr_005finit_0028_0029-user_002ddefined-function): [Group Functions](#Group-Functions)[`_ord_init()` user-defined function](#index-_005ford_005finit_0028_0029-user_002ddefined-function): [Ordinal Functions](#Ordinal-Functions)[`_pw_init()` user-defined function](#index-_005fpw_005finit_0028_0029-user_002ddefined-function): [Passwd Functions](#Passwd-Functions)
---
[{]({)[`{}` (braces)](#index-_007b_007d-_0028braces_0029): [Profiling](#Profiling)[`{}` (braces), actions and](#index-_007b_007d-_0028braces_0029_002c-actions-and): [Action Overview](#Action-Overview)[`{}` (braces), statements, grouping](#index-_007b_007d-_0028braces_0029_002c-statements_002c-grouping): [Statements](#Statements)
---
[|](|)[`|` (vertical bar)](#index-_007c-_0028vertical-bar_0029): [Regexp Operators](#Regexp-Operators)[`|` (vertical bar), `|` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c-operator-_0028I_002fO_0029): [Getline/Pipe](#Getline_002fPipe)[`|` (vertical bar), `|` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c-operator-_0028I_002fO_0029-1): [Redirection](#Redirection)[`|` (vertical bar), `|` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c-operator-_0028I_002fO_0029-2): [Precedence](#Precedence)[`|` (vertical bar), `|&` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c_0026-operator-_0028I_002fO_0029): [Getline/Coprocess](#Getline_002fCoprocess)[`|` (vertical bar), `|&` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c_0026-operator-_0028I_002fO_0029-1): [Redirection](#Redirection)[`|` (vertical bar), `|&` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c_0026-operator-_0028I_002fO_0029-2): [Precedence](#Precedence)[`|` (vertical bar), `|&` operator (I/O)](#index-_007c-_0028vertical-bar_0029_002c-_007c_0026-operator-_0028I_002fO_0029-3): [Two-way I/O](#Two_002dway-I_002fO)[`|` (vertical bar), `|&` operator (I/O), pipes, closing](#index-_007c-_0028vertical-bar_0029_002c-_007c_0026-operator-_0028I_002fO_0029_002c-pipes_002c-closing): [Close Files And Pipes](#Close-Files-And-Pipes)[`|` (vertical bar), `||` operator](#index-_007c-_0028vertical-bar_0029_002c-_007c_007c-operator): [Boolean Ops](#Boolean-Ops)[`|` (vertical bar), `||` operator](#index-_007c-_0028vertical-bar_0029_002c-_007c_007c-operator-1): [Precedence](#Precedence)
---
[~](~)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator): [Regexp Usage](#Regexp-Usage)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-1): [Computed Regexps](#Computed-Regexps)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-2): [Case-sensitivity](#Case_002dsensitivity)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-3): [Regexp Constants](#Regexp-Constants)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-4): [Comparison Operators](#Comparison-Operators)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-5): [Comparison Operators](#Comparison-Operators)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-6): [Precedence](#Precedence)[`~` (tilde), `~` operator](#index-_007e-_0028tilde_0029_002c-_007e-operator-7): [Expression Patterns](#Expression-Patterns)
---
[A](A)[accessing fields](#index-accessing-fields): [Fields](#Fields)[accessing global variables from extensions](#index-accessing-global-variables-from-extensions): [Symbol Table Access](#Symbol-Table-Access)[account information](#index-account-information): [Passwd Functions](#Passwd-Functions)[account information](#index-account-information-1): [Group Functions](#Group-Functions)[actions](#index-actions): [Action Overview](#Action-Overview)[actions, control statements in](#index-actions_002c-control-statements-in): [Statements](#Statements)[actions, default](#index-actions_002c-default): [Very Simple](#Very-Simple)[actions, empty](#index-actions_002c-empty): [Very Simple](#Very-Simple)[Ada programming language](#index-Ada-programming-language): [Glossary](#Glossary)[adding, features to `gawk`](#index-adding_002c-features-to-gawk): [Adding Code](#Adding-Code)[adding, fields](#index-adding_002c-fields): [Changing Fields](#Changing-Fields)[advanced features, fixed-width data](#index-advanced-features_002c-fixed_002dwidth-data): [Constant Size](#Constant-Size)[advanced features, `gawk`](#index-advanced-features_002c-gawk): [Advanced Features](#Advanced-Features)[advanced features, network programming](#index-advanced-features_002c-network-programming): [TCP/IP Networking](#TCP_002fIP-Networking)[advanced features, nondecimal input data](#index-advanced-features_002c-nondecimal-input-data): [Nondecimal Data](#Nondecimal-Data)[advanced features, processes, communicating with](#index-advanced-features_002c-processes_002c-communicating-with): [Two-way I/O](#Two_002dway-I_002fO)[advanced features, specifying field content](#index-advanced-features_002c-specifying-field-content): [Splitting By Content](#Splitting-By-Content)[Aho, Alfred](#index-Aho_002c-Alfred): [History](#History)[Aho, Alfred](#index-Aho_002c-Alfred-1): [Contributors](#Contributors)[alarm clock example program](#index-alarm-clock-example-program): [Alarm Program](#Alarm-Program)[`alarm.awk` program](#index-alarm_002eawk-program): [Alarm Program](#Alarm-Program)[algorithms](#index-algorithms): [Basic High Level](#Basic-High-Level)[allocating memory for extensions](#index-allocating-memory-for-extensions): [Memory Allocation Functions](#Memory-Allocation-Functions)[amazing `awk` assembler (`aaa`)](#index-amazing-awk-assembler-_0028aaa_0029): [Glossary](#Glossary)[amazingly workable formatter (`awf`)](#index-amazingly-workable-formatter-_0028awf_0029): [Glossary](#Glossary)[ambiguity, syntactic: `/=` operator vs. `/=&hellip;/` regexp constant](#index-ambiguity_002c-syntactic_003a-_002f_003d-operator-vs_002e-_002f_003d_2026_002f-regexp-constant): [Assignment Ops](#Assignment-Ops)[ampersand (`&`), `&&` operator](#index-ampersand-_0028_0026_0029_002c-_0026_0026-operator): [Boolean Ops](#Boolean-Ops)[ampersand (`&`), `&&` operator](#index-ampersand-_0028_0026_0029_002c-_0026_0026-operator-1): [Precedence](#Precedence)[ampersand (`&`), `gsub()`/`gensub()`/`sub()` functions and](#index-ampersand-_0028_0026_0029_002c-gsub_0028_0029_002fgensub_0028_0029_002fsub_0028_0029-functions-and): [Gory Details](#Gory-Details)[`anagram.awk` program](#index-anagram_002eawk-program): [Anagram Program](#Anagram-Program)[anagrams, finding](#index-anagrams_002c-finding): [Anagram Program](#Anagram-Program)[AND bitwise operation](#index-AND-bitwise-operation): [Bitwise Functions](#Bitwise-Functions)[and Boolean-logic operator](#index-and-Boolean_002dlogic-operator): [Boolean Ops](#Boolean-Ops)[`and()` function (`gawk`)](#index-and_0028_0029-function-_0028gawk_0029): [Bitwise Functions](#Bitwise-Functions)[ANSI](#index-ANSI): [Glossary](#Glossary)[API informational variables](#index-API-informational-variables): [Extension API Informational Variables](#Extension-API-Informational-Variables)[API version](#index-API-version): [Extension Versioning](#Extension-Versioning)[arbitrary precision](#index-arbitrary-precision): [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)[arbitrary precision integers](#index-arbitrary-precision-integers): [Arbitrary Precision Integers](#Arbitrary-Precision-Integers)[arbitrary-precision](#index-arbitrary_002dprecision): [Computer Arithmetic](#Computer-Arithmetic)[archaeologists](#index-archaeologists): [Bugs](#Bugs)[arctangent](#index-arctangent): [Numeric Functions](#Numeric-Functions)[`ARGC`/`ARGV` variables](#index-ARGC_002fARGV-variables): [Auto-set](#Auto_002dset)[`ARGC`/`ARGV` variables, command-line arguments](#index-ARGC_002fARGV-variables_002c-command_002dline-arguments): [Other Arguments](#Other-Arguments)[`ARGC`/`ARGV` variables, how to use](#index-ARGC_002fARGV-variables_002c-how-to-use): [ARGC and ARGV](#ARGC-and-ARGV)[`ARGC`/`ARGV` variables, portability and](#index-ARGC_002fARGV-variables_002c-portability-and): [Executable Scripts](#Executable-Scripts)[`ARGIND` variable](#index-ARGIND-variable): [Auto-set](#Auto_002dset)[`ARGIND` variable, command-line arguments](#index-ARGIND-variable_002c-command_002dline-arguments): [Other Arguments](#Other-Arguments)[arguments, command-line](#index-arguments_002c-command_002dline): [Other Arguments](#Other-Arguments)[arguments, command-line](#index-arguments_002c-command_002dline-1): [Auto-set](#Auto_002dset)[arguments, command-line](#index-arguments_002c-command_002dline-2): [ARGC and ARGV](#ARGC-and-ARGV)[arguments, command-line, invoking `awk`](#index-arguments_002c-command_002dline_002c-invoking-awk): [Command Line](#Command-Line)[arguments, in function calls](#index-arguments_002c-in-function-calls): [Function Calls](#Function-Calls)[arguments, processing](#index-arguments_002c-processing): [Getopt Function](#Getopt-Function)[`ARGV` array, indexing into](#index-ARGV-array_002c-indexing-into): [Other Arguments](#Other-Arguments)[arithmetic operators](#index-arithmetic-operators): [Arithmetic Ops](#Arithmetic-Ops)[array manipulation in extensions](#index-array-manipulation-in-extensions): [Array Manipulation](#Array-Manipulation)[array members](#index-array-members): [Reference to Elements](#Reference-to-Elements)[array scanning order, controlling](#index-array-scanning-order_002c-controlling): [Controlling Scanning](#Controlling-Scanning)[array, number of elements](#index-array_002c-number-of-elements): [String Functions](#String-Functions)[arrays](#index-arrays): [Arrays](#Arrays)[arrays of arrays](#index-arrays-of-arrays): [Arrays of Arrays](#Arrays-of-Arrays)[arrays, an example of using](#index-arrays_002c-an-example-of-using): [Array Example](#Array-Example)[arrays, and `IGNORECASE` variable](#index-arrays_002c-and-IGNORECASE-variable): [Array Intro](#Array-Intro)[arrays, as parameters to functions](#index-arrays_002c-as-parameters-to-functions): [Pass By Value/Reference](#Pass-By-Value_002fReference)[arrays, associative](#index-arrays_002c-associative): [Array Intro](#Array-Intro)[arrays, associative, library functions and](#index-arrays_002c-associative_002c-library-functions-and): [Library Names](#Library-Names)[arrays, deleting entire contents](#index-arrays_002c-deleting-entire-contents): [Delete](#Delete)[arrays, elements that don&rsquo;t exist](#index-arrays_002c-elements-that-don_0027t-exist): [Reference to Elements](#Reference-to-Elements)[arrays, elements, assigning values](#index-arrays_002c-elements_002c-assigning-values): [Assigning Elements](#Assigning-Elements)[arrays, elements, deleting](#index-arrays_002c-elements_002c-deleting): [Delete](#Delete)[arrays, elements, order of access by `in` operator](#index-arrays_002c-elements_002c-order-of-access-by-in-operator): [Scanning an Array](#Scanning-an-Array)[arrays, elements, retrieving number of](#index-arrays_002c-elements_002c-retrieving-number-of): [String Functions](#String-Functions)[arrays, `for` statement and](#index-arrays_002c-for-statement-and): [Scanning an Array](#Scanning-an-Array)[arrays, indexing](#index-arrays_002c-indexing): [Array Intro](#Array-Intro)[arrays, merging into strings](#index-arrays_002c-merging-into-strings): [Join Function](#Join-Function)[arrays, multidimensional](#index-arrays_002c-multidimensional): [Multidimensional](#Multidimensional)[arrays, multidimensional, scanning](#index-arrays_002c-multidimensional_002c-scanning): [Multiscanning](#Multiscanning)[arrays, numeric subscripts](#index-arrays_002c-numeric-subscripts): [Numeric Array Subscripts](#Numeric-Array-Subscripts)[arrays, referencing elements](#index-arrays_002c-referencing-elements): [Reference to Elements](#Reference-to-Elements)[arrays, scanning](#index-arrays_002c-scanning): [Scanning an Array](#Scanning-an-Array)[arrays, sorting](#index-arrays_002c-sorting): [Array Sorting Functions](#Array-Sorting-Functions)[arrays, sorting, and `IGNORECASE` variable](#index-arrays_002c-sorting_002c-and-IGNORECASE-variable): [Array Sorting Functions](#Array-Sorting-Functions)[arrays, sparse](#index-arrays_002c-sparse): [Array Intro](#Array-Intro)[arrays, subscripts, uninitialized variables as](#index-arrays_002c-subscripts_002c-uninitialized-variables-as): [Uninitialized Subscripts](#Uninitialized-Subscripts)[arrays, unassigned elements](#index-arrays_002c-unassigned-elements): [Reference to Elements](#Reference-to-Elements)[artificial intelligence, `gawk` and](#index-artificial-intelligence_002c-gawk-and): [Distribution contents](#Distribution-contents)[ASCII](#index-ASCII): [Ordinal Functions](#Ordinal-Functions)[ASCII](#index-ASCII-1): [Glossary](#Glossary)[`asort()` function (`gawk`)](#index-asort_0028_0029-function-_0028gawk_0029): [String Functions](#String-Functions)[`asort()` function (`gawk`)](#index-asort_0028_0029-function-_0028gawk_0029-1): [Array Sorting Functions](#Array-Sorting-Functions)[`asort()` function (`gawk`), arrays, sorting](#index-asort_0028_0029-function-_0028gawk_0029_002c-arrays_002c-sorting): [Array Sorting Functions](#Array-Sorting-Functions)[`asorti()` function (`gawk`)](#index-asorti_0028_0029-function-_0028gawk_0029): [String Functions](#String-Functions)[`asorti()` function (`gawk`)](#index-asorti_0028_0029-function-_0028gawk_0029-1): [Array Sorting Functions](#Array-Sorting-Functions)[`asorti()` function (`gawk`), arrays, sorting](#index-asorti_0028_0029-function-_0028gawk_0029_002c-arrays_002c-sorting): [Array Sorting Functions](#Array-Sorting-Functions)[`assert()` function (C library)](#index-assert_0028_0029-function-_0028C-library_0029): [Assert Function](#Assert-Function)[`assert()` user-defined function](#index-assert_0028_0029-user_002ddefined-function): [Assert Function](#Assert-Function)[assertions](#index-assertions): [Assert Function](#Assert-Function)[assign values to variables, in debugger](#index-assign-values-to-variables_002c-in-debugger): [Viewing And Changing Data](#Viewing-And-Changing-Data)[assignment operators](#index-assignment-operators): [Assignment Ops](#Assignment-Ops)[assignment operators, evaluation order](#index-assignment-operators_002c-evaluation-order): [Assignment Ops](#Assignment-Ops)[assignment operators, lvalues/rvalues](#index-assignment-operators_002c-lvalues_002frvalues): [Assignment Ops](#Assignment-Ops)[assignments as filenames](#index-assignments-as-filenames): [Ignoring Assigns](#Ignoring-Assigns)[associative arrays](#index-associative-arrays): [Array Intro](#Array-Intro)[asterisk (`*`), `*` operator, as multiplication operator](#index-asterisk-_0028_002a_0029_002c-_002a-operator_002c-as-multiplication-operator): [Precedence](#Precedence)[asterisk (`*`), `*` operator, as regexp operator](#index-asterisk-_0028_002a_0029_002c-_002a-operator_002c-as-regexp-operator): [Regexp Operators](#Regexp-Operators)[asterisk (`*`), `*` operator, null strings, matching](#index-asterisk-_0028_002a_0029_002c-_002a-operator_002c-null-strings_002c-matching): [String Functions](#String-Functions)[asterisk (`*`), `**` operator](#index-asterisk-_0028_002a_0029_002c-_002a_002a-operator): [Arithmetic Ops](#Arithmetic-Ops)[asterisk (`*`), `**` operator](#index-asterisk-_0028_002a_0029_002c-_002a_002a-operator-1): [Precedence](#Precedence)[asterisk (`*`), `**=` operator](#index-asterisk-_0028_002a_0029_002c-_002a_002a_003d-operator): [Assignment Ops](#Assignment-Ops)[asterisk (`*`), `**=` operator](#index-asterisk-_0028_002a_0029_002c-_002a_002a_003d-operator-1): [Precedence](#Precedence)[asterisk (`*`), `*=` operator](#index-asterisk-_0028_002a_0029_002c-_002a_003d-operator): [Assignment Ops](#Assignment-Ops)[asterisk (`*`), `*=` operator](#index-asterisk-_0028_002a_0029_002c-_002a_003d-operator-1): [Precedence](#Precedence)[`atan2()` function](#index-atan2_0028_0029-function): [Numeric Functions](#Numeric-Functions)[automatic displays, in debugger](#index-automatic-displays_002c-in-debugger): [Debugger Info](#Debugger-Info)[`awf` (amazingly workable formatter) program](#index-awf-_0028amazingly-workable-formatter_0029-program): [Glossary](#Glossary)[`awk` debugging, enabling](#index-awk-debugging_002c-enabling): [Options](#Options)[`awk` language, POSIX version](#index-awk-language_002c-POSIX-version): [Assignment Ops](#Assignment-Ops)[`awk` profiling, enabling](#index-awk-profiling_002c-enabling): [Options](#Options)[`awk` programs](#index-awk-programs): [Getting Started](#Getting-Started)[`awk` programs](#index-awk-programs-1): [Executable Scripts](#Executable-Scripts)[`awk` programs](#index-awk-programs-2): [Two Rules](#Two-Rules)[`awk` programs, complex](#index-awk-programs_002c-complex): [When](#When)[`awk` programs, documenting](#index-awk-programs_002c-documenting): [Comments](#Comments)[`awk` programs, documenting](#index-awk-programs_002c-documenting-1): [Library Names](#Library-Names)[`awk` programs, examples of](#index-awk-programs_002c-examples-of): [Sample Programs](#Sample-Programs)[`awk` programs, execution of](#index-awk-programs_002c-execution-of): [Next Statement](#Next-Statement)[`awk` programs, internationalizing](#index-awk-programs_002c-internationalizing): [I18N Functions](#I18N-Functions)[`awk` programs, internationalizing](#index-awk-programs_002c-internationalizing-1): [Programmer i18n](#Programmer-i18n)[`awk` programs, lengthy](#index-awk-programs_002c-lengthy): [Long](#Long)[`awk` programs, lengthy, assertions](#index-awk-programs_002c-lengthy_002c-assertions): [Assert Function](#Assert-Function)[`awk` programs, location of](#index-awk-programs_002c-location-of): [Options](#Options)[`awk` programs, location of](#index-awk-programs_002c-location-of-1): [Options](#Options)[`awk` programs, location of](#index-awk-programs_002c-location-of-2): [Options](#Options)[`awk` programs, one-line examples](#index-awk-programs_002c-one_002dline-examples): [Very Simple](#Very-Simple)[`awk` programs, profiling](#index-awk-programs_002c-profiling): [Profiling](#Profiling)[`awk` programs, running](#index-awk-programs_002c-running): [Running gawk](#Running-gawk)[`awk` programs, running](#index-awk-programs_002c-running-1): [Long](#Long)[`awk` programs, running, from shell scripts](#index-awk-programs_002c-running_002c-from-shell-scripts): [One-shot](#One_002dshot)[`awk` programs, running, without input files](#index-awk-programs_002c-running_002c-without-input-files): [Read Terminal](#Read-Terminal)[`awk` programs, shell variables in](#index-awk-programs_002c-shell-variables-in): [Using Shell Variables](#Using-Shell-Variables)[`awk`, function of](#index-awk_002c-function-of): [Getting Started](#Getting-Started)[`awk`, `gawk` and](#index-awk_002c-gawk-and): [Preface](#Preface)[`awk`, `gawk` and](#index-awk_002c-gawk-and-1): [This Manual](#This-Manual)[`awk`, history of](#index-awk_002c-history-of): [History](#History)[`awk`, implementation issues, pipes](#index-awk_002c-implementation-issues_002c-pipes): [Redirection](#Redirection)[`awk`, implementations](#index-awk_002c-implementations): [Other Versions](#Other-Versions)[`awk`, implementations, limits](#index-awk_002c-implementations_002c-limits): [Getline Notes](#Getline-Notes)[`awk`, invoking](#index-awk_002c-invoking): [Command Line](#Command-Line)[`awk`, new vs. old](#index-awk_002c-new-vs_002e-old): [Names](#Names)[`awk`, new vs. old, `OFMT` variable](#index-awk_002c-new-vs_002e-old_002c-OFMT-variable): [Strings And Numbers](#Strings-And-Numbers)[`awk`, POSIX and](#index-awk_002c-POSIX-and): [Preface](#Preface)[`awk`, POSIX and, See Also POSIX `awk`](#index-awk_002c-POSIX-and_002c-See-Also-POSIX-awk): [Preface](#Preface)[`awk`, regexp constants and](#index-awk_002c-regexp-constants-and): [Comparison Operators](#Comparison-Operators)[`awk`, See Also `gawk`](#index-awk_002c-See-Also-gawk): [Preface](#Preface)[`awk`, terms describing](#index-awk_002c-terms-describing): [This Manual](#This-Manual)[`awk`, uses for](#index-awk_002c-uses-for): [Preface](#Preface)[`awk`, uses for](#index-awk_002c-uses-for-1): [Getting Started](#Getting-Started)[`awk`, uses for](#index-awk_002c-uses-for-2): [When](#When)[`awk`, versions of](#index-awk_002c-versions-of): [V7/SVR3.1](#V7_002fSVR3_002e1)[`awk`, versions of, changes between SVR3.1 and SVR4](#index-awk_002c-versions-of_002c-changes-between-SVR3_002e1-and-SVR4): [SVR4](#SVR4)[`awk`, versions of, changes between SVR4 and POSIX `awk`](#index-awk_002c-versions-of_002c-changes-between-SVR4-and-POSIX-awk): [POSIX](#POSIX)[`awk`, versions of, changes between V7 and SVR3.1](#index-awk_002c-versions-of_002c-changes-between-V7-and-SVR3_002e1): [V7/SVR3.1](#V7_002fSVR3_002e1)[`awk`, versions of, See Also Brian Kernighan&rsquo;s `awk`](#index-awk_002c-versions-of_002c-See-Also-Brian-Kernighan_0027s-awk): [BTL](#BTL)[`awk`, versions of, See Also Brian Kernighan&rsquo;s `awk`](#index-awk_002c-versions-of_002c-See-Also-Brian-Kernighan_0027s-awk-1): [Other Versions](#Other-Versions)[`awka` compiler for `awk`](#index-awka-compiler-for-awk): [Other Versions](#Other-Versions)[`AWKLIBPATH` environment variable](#index-AWKLIBPATH-environment-variable): [AWKLIBPATH Variable](#AWKLIBPATH-Variable)[`AWKPATH` environment variable](#index-AWKPATH-environment-variable): [AWKPATH Variable](#AWKPATH-Variable)[`AWKPATH` environment variable](#index-AWKPATH-environment-variable-1): [PC Using](#PC-Using)[`awkprof.out` file](#index-awkprof_002eout-file): [Profiling](#Profiling)[`awksed.awk` program](#index-awksed_002eawk-program): [Simple Sed](#Simple-Sed)[awkvars.out file](#index-awkvars_002eout-file): [Options](#Options)
---
[B](B)[`b` debugger command (alias for `break`)](#index-b-debugger-command-_0028alias-for-break_0029): [Breakpoint Control](#Breakpoint-Control)[backslash (`\`)](#index-backslash-_0028_005c_0029): [Comments](#Comments)[backslash (`\`), as field separator](#index-backslash-_0028_005c_0029_002c-as-field-separator): [Command Line Field Separator](#Command-Line-Field-Separator)[backslash (`\`), continuing lines and](#index-backslash-_0028_005c_0029_002c-continuing-lines-and): [Statements/Lines](#Statements_002fLines)[backslash (`\`), continuing lines and, comments and](#index-backslash-_0028_005c_0029_002c-continuing-lines-and_002c-comments-and): [Statements/Lines](#Statements_002fLines)[backslash (`\`), continuing lines and, in `csh`](#index-backslash-_0028_005c_0029_002c-continuing-lines-and_002c-in-csh): [Statements/Lines](#Statements_002fLines)[backslash (`\`), `gsub()`/`gensub()`/`sub()` functions and](#index-backslash-_0028_005c_0029_002c-gsub_0028_0029_002fgensub_0028_0029_002fsub_0028_0029-functions-and): [Gory Details](#Gory-Details)[backslash (`\`), in bracket expressions](#index-backslash-_0028_005c_0029_002c-in-bracket-expressions): [Bracket Expressions](#Bracket-Expressions)[backslash (`\`), in escape sequences](#index-backslash-_0028_005c_0029_002c-in-escape-sequences): [Escape Sequences](#Escape-Sequences)[backslash (`\`), in escape sequences](#index-backslash-_0028_005c_0029_002c-in-escape-sequences-1): [Escape Sequences](#Escape-Sequences)[backslash (`\`), in escape sequences, POSIX and](#index-backslash-_0028_005c_0029_002c-in-escape-sequences_002c-POSIX-and): [Escape Sequences](#Escape-Sequences)[backslash (`\`), in regexp constants](#index-backslash-_0028_005c_0029_002c-in-regexp-constants): [Computed Regexps](#Computed-Regexps)[backslash (`\`), in shell commands](#index-backslash-_0028_005c_0029_002c-in-shell-commands): [Quoting](#Quoting)[backslash (`\`), regexp operator](#index-backslash-_0028_005c_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[backslash (`\`), `\"` escape sequence](#index-backslash-_0028_005c_0029_002c-_005c_0022-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\'` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005c_0027-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\/` escape sequence](#index-backslash-_0028_005c_0029_002c-_005c_002f-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\<` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005c_003c-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\>` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005c_003e-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\a` escape sequence](#index-backslash-_0028_005c_0029_002c-_005ca-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\b` escape sequence](#index-backslash-_0028_005c_0029_002c-_005cb-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\B` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005cB-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\f` escape sequence](#index-backslash-_0028_005c_0029_002c-_005cf-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\n` escape sequence](#index-backslash-_0028_005c_0029_002c-_005cn-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\`nnn escape sequence](#index-backslash-_0028_005c_0029_002c-_005cnnn-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\r` escape sequence](#index-backslash-_0028_005c_0029_002c-_005cr-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\s` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005cs-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\S` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005cS-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\t` escape sequence](#index-backslash-_0028_005c_0029_002c-_005ct-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\v` escape sequence](#index-backslash-_0028_005c_0029_002c-_005cv-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\w` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005cw-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\W` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005cW-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\x` escape sequence](#index-backslash-_0028_005c_0029_002c-_005cx-escape-sequence): [Escape Sequences](#Escape-Sequences)[backslash (`\`), `\y` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005cy-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[backslash (`\`), `\`` operator (`gawk`)](#index-backslash-_0028_005c_0029_002c-_005c_0060-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`backtrace` debugger command](#index-backtrace-debugger-command): [Execution Stack](#Execution-Stack)[Beebe, Nelson H.F.](#index-Beebe_002c-Nelson-H_002eF_002e): [Acknowledgments](#Acknowledgments)[Beebe, Nelson H.F.](#index-Beebe_002c-Nelson-H_002eF_002e-1): [Other Versions](#Other-Versions)[`BEGIN` pattern](#index-BEGIN-pattern): [Field Separators](#Field-Separators)[`BEGIN` pattern](#index-BEGIN-pattern-1): [BEGIN/END](#BEGIN_002fEND)[`BEGIN` pattern](#index-BEGIN-pattern-2): [Using BEGIN/END](#Using-BEGIN_002fEND)[`BEGIN` pattern, and profiling](#index-BEGIN-pattern_002c-and-profiling): [Profiling](#Profiling)[`BEGIN` pattern, `assert()` user-defined function and](#index-BEGIN-pattern_002c-assert_0028_0029-user_002ddefined-function-and): [Assert Function](#Assert-Function)[`BEGIN` pattern, Boolean patterns and](#index-BEGIN-pattern_002c-Boolean-patterns-and): [Expression Patterns](#Expression-Patterns)[`BEGIN` pattern, `exit` statement and](#index-BEGIN-pattern_002c-exit-statement-and): [Exit Statement](#Exit-Statement)[`BEGIN` pattern, `getline` and](#index-BEGIN-pattern_002c-getline-and): [Getline Notes](#Getline-Notes)[`BEGIN` pattern, headings, adding](#index-BEGIN-pattern_002c-headings_002c-adding): [Print Examples](#Print-Examples)[`BEGIN` pattern, `next`/`nextfile` statements and](#index-BEGIN-pattern_002c-next_002fnextfile-statements-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`BEGIN` pattern, `next`/`nextfile` statements and](#index-BEGIN-pattern_002c-next_002fnextfile-statements-and-1): [Next Statement](#Next-Statement)[`BEGIN` pattern, `OFS`/`ORS` variables, assigning values to](#index-BEGIN-pattern_002c-OFS_002fORS-variables_002c-assigning-values-to): [Output Separators](#Output-Separators)[`BEGIN` pattern, operators and](#index-BEGIN-pattern_002c-operators-and): [Using BEGIN/END](#Using-BEGIN_002fEND)[`BEGIN` pattern, `print` statement and](#index-BEGIN-pattern_002c-print-statement-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`BEGIN` pattern, `pwcat` program](#index-BEGIN-pattern_002c-pwcat-program): [Passwd Functions](#Passwd-Functions)[`BEGIN` pattern, running `awk` programs and](#index-BEGIN-pattern_002c-running-awk-programs-and): [Cut Program](#Cut-Program)[`BEGIN` pattern, `TEXTDOMAIN` variable and](#index-BEGIN-pattern_002c-TEXTDOMAIN-variable-and): [Programmer i18n](#Programmer-i18n)[`BEGINFILE` pattern](#index-BEGINFILE-pattern): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`BEGINFILE` pattern, Boolean patterns and](#index-BEGINFILE-pattern_002c-Boolean-patterns-and): [Expression Patterns](#Expression-Patterns)[`beginfile()` user-defined function](#index-beginfile_0028_0029-user_002ddefined-function): [Filetrans Function](#Filetrans-Function)[Bentley, Jon](#index-Bentley_002c-Jon): [Glossary](#Glossary)[Benzinger, Michael](#index-Benzinger_002c-Michael): [Contributors](#Contributors)[Berry, Karl](#index-Berry_002c-Karl): [Acknowledgments](#Acknowledgments)[Berry, Karl](#index-Berry_002c-Karl-1): [Acknowledgments](#Acknowledgments)[Berry, Karl](#index-Berry_002c-Karl-2): [Ranges and Locales](#Ranges-and-Locales)[binary input/output](#index-binary-input_002foutput): [User-modified](#User_002dmodified)[`bindtextdomain()` function (C library)](#index-bindtextdomain_0028_0029-function-_0028C-library_0029): [Explaining gettext](#Explaining-gettext)[`bindtextdomain()` function (`gawk`)](#index-bindtextdomain_0028_0029-function-_0028gawk_0029): [I18N Functions](#I18N-Functions)[`bindtextdomain()` function (`gawk`)](#index-bindtextdomain_0028_0029-function-_0028gawk_0029-1): [Programmer i18n](#Programmer-i18n)[`bindtextdomain()` function (`gawk`), portability and](#index-bindtextdomain_0028_0029-function-_0028gawk_0029_002c-portability-and): [I18N Portability](#I18N-Portability)[`BINMODE` variable](#index-BINMODE-variable): [User-modified](#User_002dmodified)[`BINMODE` variable](#index-BINMODE-variable-1): [PC Using](#PC-Using)[bit-manipulation functions](#index-bit_002dmanipulation-functions): [Bitwise Functions](#Bitwise-Functions)[`bits2str()` user-defined function](#index-bits2str_0028_0029-user_002ddefined-function): [Bitwise Functions](#Bitwise-Functions)[bitwise AND](#index-bitwise-AND): [Bitwise Functions](#Bitwise-Functions)[bitwise complement](#index-bitwise-complement): [Bitwise Functions](#Bitwise-Functions)[bitwise OR](#index-bitwise-OR): [Bitwise Functions](#Bitwise-Functions)[bitwise XOR](#index-bitwise-XOR): [Bitwise Functions](#Bitwise-Functions)[bitwise, complement](#index-bitwise_002c-complement): [Bitwise Functions](#Bitwise-Functions)[bitwise, operations](#index-bitwise_002c-operations): [Bitwise Functions](#Bitwise-Functions)[bitwise, shift](#index-bitwise_002c-shift): [Bitwise Functions](#Bitwise-Functions)[body, in actions](#index-body_002c-in-actions): [Statements](#Statements)[body, in loops](#index-body_002c-in-loops): [While Statement](#While-Statement)[Boolean expressions](#index-Boolean-expressions): [Boolean Ops](#Boolean-Ops)[Boolean expressions, as patterns](#index-Boolean-expressions_002c-as-patterns): [Expression Patterns](#Expression-Patterns)[Boolean operators, See Boolean expressions](#index-Boolean-operators_002c-See-Boolean-expressions): [Boolean Ops](#Boolean-Ops)[Bourne shell, quoting rules for](#index-Bourne-shell_002c-quoting-rules-for): [Quoting](#Quoting)[braces (`{}`)](#index-braces-_0028_007b_007d_0029): [Profiling](#Profiling)[braces (`{}`), actions and](#index-braces-_0028_007b_007d_0029_002c-actions-and): [Action Overview](#Action-Overview)[braces (`{}`), statements, grouping](#index-braces-_0028_007b_007d_0029_002c-statements_002c-grouping): [Statements](#Statements)[bracket expressions](#index-bracket-expressions): [Regexp Operators](#Regexp-Operators)[bracket expressions](#index-bracket-expressions-1): [Bracket Expressions](#Bracket-Expressions)[bracket expressions, character classes](#index-bracket-expressions_002c-character-classes): [Bracket Expressions](#Bracket-Expressions)[bracket expressions, collating elements](#index-bracket-expressions_002c-collating-elements): [Bracket Expressions](#Bracket-Expressions)[bracket expressions, collating symbols](#index-bracket-expressions_002c-collating-symbols): [Bracket Expressions](#Bracket-Expressions)[bracket expressions, complemented](#index-bracket-expressions_002c-complemented): [Regexp Operators](#Regexp-Operators)[bracket expressions, equivalence classes](#index-bracket-expressions_002c-equivalence-classes): [Bracket Expressions](#Bracket-Expressions)[bracket expressions, non-ASCII](#index-bracket-expressions_002c-non_002dASCII): [Bracket Expressions](#Bracket-Expressions)[bracket expressions, range expressions](#index-bracket-expressions_002c-range-expressions): [Bracket Expressions](#Bracket-Expressions)[`break` debugger command](#index-break-debugger-command): [Breakpoint Control](#Breakpoint-Control)[`break` statement](#index-break-statement): [Break Statement](#Break-Statement)[breakpoint](#index-breakpoint): [Debugging Terms](#Debugging-Terms)[breakpoint at location, how to delete](#index-breakpoint-at-location_002c-how-to-delete): [Breakpoint Control](#Breakpoint-Control)[breakpoint commands](#index-breakpoint-commands): [Debugger Execution Control](#Debugger-Execution-Control)[breakpoint condition](#index-breakpoint-condition): [Breakpoint Control](#Breakpoint-Control)[breakpoint, delete by number](#index-breakpoint_002c-delete-by-number): [Breakpoint Control](#Breakpoint-Control)[breakpoint, how to disable or enable](#index-breakpoint_002c-how-to-disable-or-enable): [Breakpoint Control](#Breakpoint-Control)[breakpoint, setting](#index-breakpoint_002c-setting): [Breakpoint Control](#Breakpoint-Control)[Brennan, Michael](#index-Brennan_002c-Michael): [Foreword3](#Foreword3)[Brennan, Michael](#index-Brennan_002c-Michael-1): [Foreword4](#Foreword4)[Brennan, Michael](#index-Brennan_002c-Michael-2): [Acknowledgments](#Acknowledgments)[Brennan, Michael](#index-Brennan_002c-Michael-3): [Delete](#Delete)[Brennan, Michael](#index-Brennan_002c-Michael-4): [Simple Sed](#Simple-Sed)[Brennan, Michael](#index-Brennan_002c-Michael-5): [Other Versions](#Other-Versions)[Brennan, Michael](#index-Brennan_002c-Michael-6): [Other Versions](#Other-Versions)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk): [When](#When)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-1): [Escape Sequences](#Escape-Sequences)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-2): [GNU Regexp Operators](#GNU-Regexp-Operators)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-3): [Regexp Field Splitting](#Regexp-Field-Splitting)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-4): [Getline/Pipe](#Getline_002fPipe)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-5): [Concatenation](#Concatenation)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-6): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-7): [Break Statement](#Break-Statement)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-8): [Continue Statement](#Continue-Statement)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-9): [Nextfile Statement](#Nextfile-Statement)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-10): [Delete](#Delete)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-11): [String Functions](#String-Functions)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-12): [Gory Details](#Gory-Details)[Brian Kernighan&rsquo;s `awk`](#index-Brian-Kernighan_0027s-awk-13): [I/O Functions](#I_002fO-Functions)[Brian Kernighan&rsquo;s `awk`, extensions](#index-Brian-Kernighan_0027s-awk_002c-extensions): [BTL](#BTL)[Brian Kernighan&rsquo;s `awk`, source code](#index-Brian-Kernighan_0027s-awk_002c-source-code): [Other Versions](#Other-Versions)[Brini, Davide](#index-Brini_002c-Davide): [Signature Program](#Signature-Program)[Brink, Jeroen](#index-Brink_002c-Jeroen): [DOS Quoting](#DOS-Quoting)[Broder, Alan J.](#index-Broder_002c-Alan-J_002e): [Contributors](#Contributors)[Brown, Martin](#index-Brown_002c-Martin): [Contributors](#Contributors)[BSD-based operating systems](#index-BSD_002dbased-operating-systems): [Glossary](#Glossary)[`bt` debugger command (alias for `backtrace`)](#index-bt-debugger-command-_0028alias-for-backtrace_0029): [Execution Stack](#Execution-Stack)[Buening, Andreas](#index-Buening_002c-Andreas): [Acknowledgments](#Acknowledgments)[Buening, Andreas](#index-Buening_002c-Andreas-1): [Contributors](#Contributors)[Buening, Andreas](#index-Buening_002c-Andreas-2): [Maintainers](#Maintainers)[buffering, input/output](#index-buffering_002c-input_002foutput): [I/O Functions](#I_002fO-Functions)[buffering, input/output](#index-buffering_002c-input_002foutput-1): [Two-way I/O](#Two_002dway-I_002fO)[buffering, interactive vs. noninteractive](#index-buffering_002c-interactive-vs_002e-noninteractive): [I/O Functions](#I_002fO-Functions)[buffers, flushing](#index-buffers_002c-flushing): [I/O Functions](#I_002fO-Functions)[buffers, flushing](#index-buffers_002c-flushing-1): [I/O Functions](#I_002fO-Functions)[buffers, operators for](#index-buffers_002c-operators-for): [GNU Regexp Operators](#GNU-Regexp-Operators)[bug reports, email address, `bug-gawk@gnu.org`](#index-bug-reports_002c-email-address_002c-bug_002dgawk_0040gnu_002eorg): [Bug address](#Bug-address)[`bug-gawk@gnu.org` bug reporting address](#index-bug_002dgawk_0040gnu_002eorg-bug-reporting-address): [Bug address](#Bug-address)[built-in functions](#index-built_002din-functions): [Functions](#Functions)[built-in functions, evaluation order](#index-built_002din-functions_002c-evaluation-order): [Calling Built-in](#Calling-Built_002din)[BusyBox Awk](#index-BusyBox-Awk): [Other Versions](#Other-Versions)
---
[C](C)[c.e., See common extensions](#index-c_002ee_002e_002c-See-common-extensions): [Conventions](#Conventions)[call by reference](#index-call-by-reference): [Pass By Value/Reference](#Pass-By-Value_002fReference)[call by value](#index-call-by-value): [Pass By Value/Reference](#Pass-By-Value_002fReference)[call stack, display in debugger](#index-call-stack_002c-display-in-debugger): [Execution Stack](#Execution-Stack)[caret (`^`), in bracket expressions](#index-caret-_0028_005e_0029_002c-in-bracket-expressions): [Bracket Expressions](#Bracket-Expressions)[caret (`^`), regexp operator](#index-caret-_0028_005e_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[caret (`^`), regexp operator](#index-caret-_0028_005e_0029_002c-regexp-operator-1): [GNU Regexp Operators](#GNU-Regexp-Operators)[caret (`^`), `^` operator](#index-caret-_0028_005e_0029_002c-_005e-operator): [Precedence](#Precedence)[caret (`^`), `^=` operator](#index-caret-_0028_005e_0029_002c-_005e_003d-operator): [Assignment Ops](#Assignment-Ops)[caret (`^`), `^=` operator](#index-caret-_0028_005e_0029_002c-_005e_003d-operator-1): [Precedence](#Precedence)[`case` keyword](#index-case-keyword): [Switch Statement](#Switch-Statement)[case sensitivity, and regexps](#index-case-sensitivity_002c-and-regexps): [User-modified](#User_002dmodified)[case sensitivity, and string comparisons](#index-case-sensitivity_002c-and-string-comparisons): [User-modified](#User_002dmodified)[case sensitivity, array indices and](#index-case-sensitivity_002c-array-indices-and): [Array Intro](#Array-Intro)[case sensitivity, converting case](#index-case-sensitivity_002c-converting-case): [String Functions](#String-Functions)[case sensitivity, example programs](#index-case-sensitivity_002c-example-programs): [Library Functions](#Library-Functions)[case sensitivity, `gawk`](#index-case-sensitivity_002c-gawk): [Case-sensitivity](#Case_002dsensitivity)[case sensitivity, regexps and](#index-case-sensitivity_002c-regexps-and): [Case-sensitivity](#Case_002dsensitivity)[CGI, `awk` scripts for](#index-CGI_002c-awk-scripts-for): [Options](#Options)[character classes, See bracket expressions](#index-character-classes_002c-See-bracket-expressions): [Regexp Operators](#Regexp-Operators)[character lists in regular expression](#index-character-lists-in-regular-expression): [Bracket Expressions](#Bracket-Expressions)[character lists, See bracket expressions](#index-character-lists_002c-See-bracket-expressions): [Regexp Operators](#Regexp-Operators)[character sets (machine character encodings)](#index-character-sets-_0028machine-character-encodings_0029): [Ordinal Functions](#Ordinal-Functions)[character sets (machine character encodings)](#index-character-sets-_0028machine-character-encodings_0029-1): [Glossary](#Glossary)[character sets, See Also bracket expressions](#index-character-sets_002c-See-Also-bracket-expressions): [Regexp Operators](#Regexp-Operators)[characters, counting](#index-characters_002c-counting): [Wc Program](#Wc-Program)[characters, transliterating](#index-characters_002c-transliterating): [Translate Program](#Translate-Program)[characters, values of as numbers](#index-characters_002c-values-of-as-numbers): [Ordinal Functions](#Ordinal-Functions)[Chassell, Robert J.](#index-Chassell_002c-Robert-J_002e): [Acknowledgments](#Acknowledgments)[`chdir()` extension function](#index-chdir_0028_0029-extension-function): [Extension Sample File Functions](#Extension-Sample-File-Functions)[checking for MPFR](#index-checking-for-MPFR): [Checking for MPFR](#Checking-for-MPFR)[`chem` utility](#index-chem-utility): [Glossary](#Glossary)[`chr()` extension function](#index-chr_0028_0029-extension-function): [Extension Sample Ord](#Extension-Sample-Ord)[`chr()` user-defined function](#index-chr_0028_0029-user_002ddefined-function): [Ordinal Functions](#Ordinal-Functions)[`clear` debugger command](#index-clear-debugger-command): [Breakpoint Control](#Breakpoint-Control)[Cliff random numbers](#index-Cliff-random-numbers): [Cliff Random Function](#Cliff-Random-Function)[`cliff_rand()` user-defined function](#index-cliff_005frand_0028_0029-user_002ddefined-function): [Cliff Random Function](#Cliff-Random-Function)[close file or coprocess](#index-close-file-or-coprocess): [I/O Functions](#I_002fO-Functions)[`close()` function](#index-close_0028_0029-function): [Close Files And Pipes](#Close-Files-And-Pipes)[`close()` function](#index-close_0028_0029-function-1): [I/O Functions](#I_002fO-Functions)[`close()` function, portability](#index-close_0028_0029-function_002c-portability): [Close Files And Pipes](#Close-Files-And-Pipes)[`close()` function, return value](#index-close_0028_0029-function_002c-return-value): [Close Files And Pipes](#Close-Files-And-Pipes)[`close()` function, two-way pipes and](#index-close_0028_0029-function_002c-two_002dway-pipes-and): [Two-way I/O](#Two_002dway-I_002fO)[Close, Diane](#index-Close_002c-Diane): [Manual History](#Manual-History)[Close, Diane](#index-Close_002c-Diane-1): [Contributors](#Contributors)[Collado, Manuel](#index-Collado_002c-Manuel): [Acknowledgments](#Acknowledgments)[collating elements](#index-collating-elements): [Bracket Expressions](#Bracket-Expressions)[collating symbols](#index-collating-symbols): [Bracket Expressions](#Bracket-Expressions)[Colombo, Antonio](#index-Colombo_002c-Antonio): [Acknowledgments](#Acknowledgments)[Colombo, Antonio](#index-Colombo_002c-Antonio-1): [Contributors](#Contributors)[columns, aligning](#index-columns_002c-aligning): [Print Examples](#Print-Examples)[columns, cutting](#index-columns_002c-cutting): [Cut Program](#Cut-Program)[comma (`,`), in range patterns](#index-comma-_0028_002c_0029_002c-in-range-patterns): [Ranges](#Ranges)[command completion, in debugger](#index-command-completion_002c-in-debugger): [Readline Support](#Readline-Support)[command line arguments, `PROCINFO["argv"`](#index-command-line-arguments_002c-PROCINFO_005b_0022argv_0022): [Auto-set](#Auto_002dset)[command line, arguments](#index-command-line_002c-arguments): [Other Arguments](#Other-Arguments)[command line, arguments](#index-command-line_002c-arguments-1): [Auto-set](#Auto_002dset)[command line, arguments](#index-command-line_002c-arguments-2): [ARGC and ARGV](#ARGC-and-ARGV)[command line, directories on](#index-command-line_002c-directories-on): [Command-line directories](#Command_002dline-directories)[command line, formats](#index-command-line_002c-formats): [Running gawk](#Running-gawk)[command line, `FS` on, setting](#index-command-line_002c-FS-on_002c-setting): [Command Line Field Separator](#Command-Line-Field-Separator)[command line, invoking `awk` from](#index-command-line_002c-invoking-awk-from): [Command Line](#Command-Line)[command line, option -f](#index-command-line_002c-option-_002df): [Long](#Long)[command line, options](#index-command-line_002c-options): [Options](#Options)[command line, options, end of](#index-command-line_002c-options_002c-end-of): [Options](#Options)[command line, variables, assigning on](#index-command-line_002c-variables_002c-assigning-on): [Assignment Options](#Assignment-Options)[command-line options, processing](#index-command_002dline-options_002c-processing): [Getopt Function](#Getopt-Function)[command-line options, string extraction](#index-command_002dline-options_002c-string-extraction): [String Extraction](#String-Extraction)[`commands` debugger command](#index-commands-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[commands to execute at breakpoint](#index-commands-to-execute-at-breakpoint): [Debugger Execution Control](#Debugger-Execution-Control)[commenting](#index-commenting): [Comments](#Comments)[commenting, backslash continuation and](#index-commenting_002c-backslash-continuation-and): [Statements/Lines](#Statements_002fLines)[common extensions, `**` operator](#index-common-extensions_002c-_002a_002a-operator): [Arithmetic Ops](#Arithmetic-Ops)[common extensions, `**=` operator](#index-common-extensions_002c-_002a_002a_003d-operator): [Assignment Ops](#Assignment-Ops)[common extensions, `/dev/stderr` special file](#index-common-extensions_002c-_002fdev_002fstderr-special-file): [Special FD](#Special-FD)[common extensions, `/dev/stdin` special file](#index-common-extensions_002c-_002fdev_002fstdin-special-file): [Special FD](#Special-FD)[common extensions, `/dev/stdout` special file](#index-common-extensions_002c-_002fdev_002fstdout-special-file): [Special FD](#Special-FD)[common extensions, `BINMODE` variable](#index-common-extensions_002c-BINMODE-variable): [PC Using](#PC-Using)[common extensions, `delete` to delete entire arrays](#index-common-extensions_002c-delete-to-delete-entire-arrays): [Delete](#Delete)[common extensions, `func` keyword](#index-common-extensions_002c-func-keyword): [Definition Syntax](#Definition-Syntax)[common extensions, `length()` applied to an array](#index-common-extensions_002c-length_0028_0029-applied-to-an-array): [String Functions](#String-Functions)[common extensions, `RS` as a regexp](#index-common-extensions_002c-RS-as-a-regexp): [gawk split records](#gawk-split-records)[common extensions, single character fields](#index-common-extensions_002c-single-character-fields): [Single Character Fields](#Single-Character-Fields)[common extensions, `\x` escape sequence](#index-common-extensions_002c-_005cx-escape-sequence): [Escape Sequences](#Escape-Sequences)[`comp.lang.awk` newsgroup](#index-comp_002elang_002eawk-newsgroup): [Usenet](#Usenet)[comparison expressions](#index-comparison-expressions): [Typing and Comparison](#Typing-and-Comparison)[comparison expressions, as patterns](#index-comparison-expressions_002c-as-patterns): [Expression Patterns](#Expression-Patterns)[comparison expressions, string vs. regexp](#index-comparison-expressions_002c-string-vs_002e-regexp): [Comparison Operators](#Comparison-Operators)[compatibility mode (`gawk`), extensions](#index-compatibility-mode-_0028gawk_0029_002c-extensions): [POSIX/GNU](#POSIX_002fGNU)[compatibility mode (`gawk`), file names](#index-compatibility-mode-_0028gawk_0029_002c-file-names): [Special Caveats](#Special-Caveats)[compatibility mode (`gawk`), hexadecimal numbers](#index-compatibility-mode-_0028gawk_0029_002c-hexadecimal-numbers): [Nondecimal-numbers](#Nondecimal_002dnumbers)[compatibility mode (`gawk`), octal numbers](#index-compatibility-mode-_0028gawk_0029_002c-octal-numbers): [Nondecimal-numbers](#Nondecimal_002dnumbers)[compatibility mode (`gawk`), specifying](#index-compatibility-mode-_0028gawk_0029_002c-specifying): [Options](#Options)[compiled programs](#index-compiled-programs): [Basic High Level](#Basic-High-Level)[compiled programs](#index-compiled-programs-1): [Glossary](#Glossary)[compiling `gawk` for Cygwin](#index-compiling-gawk-for-Cygwin): [Cygwin](#Cygwin)[compiling `gawk` for MS-Windows](#index-compiling-gawk-for-MS_002dWindows): [PC Compiling](#PC-Compiling)[compiling `gawk` for VMS](#index-compiling-gawk-for-VMS): [VMS Compilation](#VMS-Compilation)[`compl()` function (`gawk`)](#index-compl_0028_0029-function-_0028gawk_0029): [Bitwise Functions](#Bitwise-Functions)[complement, bitwise](#index-complement_002c-bitwise): [Bitwise Functions](#Bitwise-Functions)[compound statements, control statements and](#index-compound-statements_002c-control-statements-and): [Statements](#Statements)[concatenating](#index-concatenating): [Concatenation](#Concatenation)[`condition` debugger command](#index-condition-debugger-command): [Breakpoint Control](#Breakpoint-Control)[conditional expressions](#index-conditional-expressions): [Conditional Exp](#Conditional-Exp)[configuration option, `--disable-extensions`](#index-configuration-option_002c-_002d_002ddisable_002dextensions): [Additional Configuration Options](#Additional-Configuration-Options)[configuration option, `--disable-lint`](#index-configuration-option_002c-_002d_002ddisable_002dlint): [Additional Configuration Options](#Additional-Configuration-Options)[configuration option, `--disable-mpfr`](#index-configuration-option_002c-_002d_002ddisable_002dmpfr): [Additional Configuration Options](#Additional-Configuration-Options)[configuration option, `--disable-nls`](#index-configuration-option_002c-_002d_002ddisable_002dnls): [Additional Configuration Options](#Additional-Configuration-Options)[configuration option, `--enable-versioned-extension-dir`](#index-configuration-option_002c-_002d_002denable_002dversioned_002dextension_002ddir): [Additional Configuration Options](#Additional-Configuration-Options)[configuration option, `--with-whiny-user-strftime`](#index-configuration-option_002c-_002d_002dwith_002dwhiny_002duser_002dstrftime): [Additional Configuration Options](#Additional-Configuration-Options)[configuration options, `gawk`](#index-configuration-options_002c-gawk): [Additional Configuration Options](#Additional-Configuration-Options)[constant regexps](#index-constant-regexps): [Regexp Usage](#Regexp-Usage)[constants, nondecimal](#index-constants_002c-nondecimal): [Nondecimal Data](#Nondecimal-Data)[constants, numeric](#index-constants_002c-numeric): [Scalar Constants](#Scalar-Constants)[constants, types of](#index-constants_002c-types-of): [Constants](#Constants)[continue program, in debugger](#index-continue-program_002c-in-debugger): [Debugger Execution Control](#Debugger-Execution-Control)[`continue` statement](#index-continue-statement): [Continue Statement](#Continue-Statement)[control statements](#index-control-statements): [Statements](#Statements)[controlling array scanning order](#index-controlling-array-scanning-order): [Controlling Scanning](#Controlling-Scanning)[convert string to lower case](#index-convert-string-to-lower-case): [String Functions](#String-Functions)[convert string to number](#index-convert-string-to-number): [String Functions](#String-Functions)[convert string to upper case](#index-convert-string-to-upper-case): [String Functions](#String-Functions)[converting integer array subscripts](#index-converting-integer-array-subscripts): [Numeric Array Subscripts](#Numeric-Array-Subscripts)[converting, dates to timestamps](#index-converting_002c-dates-to-timestamps): [Time Functions](#Time-Functions)[converting, numbers to strings](#index-converting_002c-numbers-to-strings): [Strings And Numbers](#Strings-And-Numbers)[converting, numbers to strings](#index-converting_002c-numbers-to-strings-1): [Bitwise Functions](#Bitwise-Functions)[converting, strings to numbers](#index-converting_002c-strings-to-numbers): [Strings And Numbers](#Strings-And-Numbers)[converting, strings to numbers](#index-converting_002c-strings-to-numbers-1): [Bitwise Functions](#Bitwise-Functions)[`CONVFMT` variable](#index-CONVFMT-variable): [Strings And Numbers](#Strings-And-Numbers)[`CONVFMT` variable](#index-CONVFMT-variable-1): [User-modified](#User_002dmodified)[`CONVFMT` variable, and array subscripts](#index-CONVFMT-variable_002c-and-array-subscripts): [Numeric Array Subscripts](#Numeric-Array-Subscripts)[cookie](#index-cookie): [Glossary](#Glossary)[coprocesses](#index-coprocesses): [Redirection](#Redirection)[coprocesses](#index-coprocesses-1): [Two-way I/O](#Two_002dway-I_002fO)[coprocesses, closing](#index-coprocesses_002c-closing): [Close Files And Pipes](#Close-Files-And-Pipes)[coprocesses, `getline` from](#index-coprocesses_002c-getline-from): [Getline/Coprocess](#Getline_002fCoprocess)[`cos()` function](#index-cos_0028_0029-function): [Numeric Functions](#Numeric-Functions)[cosine](#index-cosine): [Numeric Functions](#Numeric-Functions)[counting](#index-counting): [Wc Program](#Wc-Program)[`csh` utility](#index-csh-utility): [Statements/Lines](#Statements_002fLines)[`csh` utility, `POSIXLY_CORRECT` environment variable](#index-csh-utility_002c-POSIXLY_005fCORRECT-environment-variable): [Options](#Options)[`csh` utility, `|&` operator, comparison with](#index-csh-utility_002c-_007c_0026-operator_002c-comparison-with): [Two-way I/O](#Two_002dway-I_002fO)[`ctime()` user-defined function](#index-ctime_0028_0029-user_002ddefined-function): [Function Example](#Function-Example)[Curreli, Marco](#index-Curreli_002c-Marco): [Contributors](#Contributors)[currency symbols, localization](#index-currency-symbols_002c-localization): [Explaining gettext](#Explaining-gettext)[current system time](#index-current-system-time): [Time Functions](#Time-Functions)[`custom.h` file](#index-custom_002eh-file): [Configuration Philosophy](#Configuration-Philosophy)[customized input parser](#index-customized-input-parser): [Input Parsers](#Input-Parsers)[customized output wrapper](#index-customized-output-wrapper): [Output Wrappers](#Output-Wrappers)[customized two-way processor](#index-customized-two_002dway-processor): [Two-way processors](#Two_002dway-processors)[`cut` utility](#index-cut-utility): [Cut Program](#Cut-Program)[`cut` utility](#index-cut-utility-1): [Cut Program](#Cut-Program)[`cut.awk` program](#index-cut_002eawk-program): [Cut Program](#Cut-Program)
---
[D](D)[`d` debugger command (alias for `delete`)](#index-d-debugger-command-_0028alias-for-delete_0029): [Breakpoint Control](#Breakpoint-Control)[d.c., See dark corner](#index-d_002ec_002e_002c-See-dark-corner): [Conventions](#Conventions)[dark corner](#index-dark-corner): [Conventions](#Conventions)[dark corner](#index-dark-corner-1): [Glossary](#Glossary)[dark corner, `"0"` is actually true](#index-dark-corner_002c-_00220_0022-is-actually-true): [Truth Values](#Truth-Values)[dark corner, `/=` operator vs. `/=&hellip;/` regexp constant](#index-dark-corner_002c-_002f_003d-operator-vs_002e-_002f_003d_2026_002f-regexp-constant): [Assignment Ops](#Assignment-Ops)[dark corner, `ARGV` variable, value of](#index-dark-corner_002c-ARGV-variable_002c-value-of): [Executable Scripts](#Executable-Scripts)[dark corner, array subscripts](#index-dark-corner_002c-array-subscripts): [Uninitialized Subscripts](#Uninitialized-Subscripts)[dark corner, `break` statement](#index-dark-corner_002c-break-statement): [Break Statement](#Break-Statement)[dark corner, `close()` function](#index-dark-corner_002c-close_0028_0029-function): [Close Files And Pipes](#Close-Files-And-Pipes)[dark corner, command-line arguments](#index-dark-corner_002c-command_002dline-arguments): [Assignment Options](#Assignment-Options)[dark corner, `continue` statement](#index-dark-corner_002c-continue-statement): [Continue Statement](#Continue-Statement)[dark corner, `CONVFMT` variable](#index-dark-corner_002c-CONVFMT-variable): [Strings And Numbers](#Strings-And-Numbers)[dark corner, empty programs](#index-dark-corner_002c-empty-programs): [Command Line](#Command-Line)[dark corner, escape sequences](#index-dark-corner_002c-escape-sequences): [Other Arguments](#Other-Arguments)[dark corner, escape sequences, for metacharacters](#index-dark-corner_002c-escape-sequences_002c-for-metacharacters): [Escape Sequences](#Escape-Sequences)[dark corner, `exit` statement](#index-dark-corner_002c-exit-statement): [Exit Statement](#Exit-Statement)[dark corner, field separators](#index-dark-corner_002c-field-separators): [Full Line Fields](#Full-Line-Fields)[dark corner, `FILENAME` variable](#index-dark-corner_002c-FILENAME-variable): [Getline Notes](#Getline-Notes)[dark corner, `FILENAME` variable](#index-dark-corner_002c-FILENAME-variable-1): [Auto-set](#Auto_002dset)[dark corner, `FNR`/`NR` variables](#index-dark-corner_002c-FNR_002fNR-variables): [Auto-set](#Auto_002dset)[dark corner, format-control characters](#index-dark-corner_002c-format_002dcontrol-characters): [Control Letters](#Control-Letters)[dark corner, format-control characters](#index-dark-corner_002c-format_002dcontrol-characters-1): [Control Letters](#Control-Letters)[dark corner, `FS` as null string](#index-dark-corner_002c-FS-as-null-string): [Single Character Fields](#Single-Character-Fields)[dark corner, input files](#index-dark-corner_002c-input-files): [awk split records](#awk-split-records)[dark corner, invoking `awk`](#index-dark-corner_002c-invoking-awk): [Command Line](#Command-Line)[dark corner, `length()` function](#index-dark-corner_002c-length_0028_0029-function): [String Functions](#String-Functions)[dark corner, locale&rsquo;s decimal point character](#index-dark-corner_002c-locale_0027s-decimal-point-character): [Locale influences conversions](#Locale-influences-conversions)[dark corner, multiline records](#index-dark-corner_002c-multiline-records): [Multiple Line](#Multiple-Line)[dark corner, `NF` variable, decrementing](#index-dark-corner_002c-NF-variable_002c-decrementing): [Changing Fields](#Changing-Fields)[dark corner, `OFMT` variable](#index-dark-corner_002c-OFMT-variable): [OFMT](#OFMT)[dark corner, range patterns, line continuation and](#index-dark-corner_002c-range-patterns_002c-line-continuation-and): [Ranges](#Ranges)[dark corner, regexp as second argument to `index()`](#index-dark-corner_002c-regexp-as-second-argument-to-index_0028_0029): [String Functions](#String-Functions)[dark corner, regexp constants](#index-dark-corner_002c-regexp-constants): [Standard Regexp Constants](#Standard-Regexp-Constants)[dark corner, regexp constants, `/=` operator and](#index-dark-corner_002c-regexp-constants_002c-_002f_003d-operator-and): [Assignment Ops](#Assignment-Ops)[dark corner, regexp constants, as arguments to user-defined functions](#index-dark-corner_002c-regexp-constants_002c-as-arguments-to-user_002ddefined-functions): [Standard Regexp Constants](#Standard-Regexp-Constants)[dark corner, `split()` function](#index-dark-corner_002c-split_0028_0029-function): [String Functions](#String-Functions)[dark corner, strings, storing](#index-dark-corner_002c-strings_002c-storing): [gawk split records](#gawk-split-records)[dark corner, value of `ARGV[0]`](#index-dark-corner_002c-value-of-ARGV_005b0_005d): [Auto-set](#Auto_002dset)[dark corner, `^`, in `FS`](#index-dark-corner_002c-_005e_002c-in-FS): [Regexp Field Splitting](#Regexp-Field-Splitting)[data, fixed-width](#index-data_002c-fixed_002dwidth): [Constant Size](#Constant-Size)[data-driven languages](#index-data_002ddriven-languages): [Basic High Level](#Basic-High-Level)[database, group, reading](#index-database_002c-group_002c-reading): [Group Functions](#Group-Functions)[database, users, reading](#index-database_002c-users_002c-reading): [Passwd Functions](#Passwd-Functions)[`date` utility, GNU](#index-date-utility_002c-GNU): [Time Functions](#Time-Functions)[`date` utility, POSIX](#index-date-utility_002c-POSIX): [Time Functions](#Time-Functions)[dates, converting to timestamps](#index-dates_002c-converting-to-timestamps): [Time Functions](#Time-Functions)[dates, information related to, localization](#index-dates_002c-information-related-to_002c-localization): [Explaining gettext](#Explaining-gettext)[Davies, Stephen](#index-Davies_002c-Stephen): [Acknowledgments](#Acknowledgments)[Davies, Stephen](#index-Davies_002c-Stephen-1): [Contributors](#Contributors)[Day, Robert P.J.](#index-Day_002c-Robert-P_002eJ_002e): [Acknowledgments](#Acknowledgments)[`dcgettext()` function (`gawk`)](#index-dcgettext_0028_0029-function-_0028gawk_0029): [I18N Functions](#I18N-Functions)[`dcgettext()` function (`gawk`)](#index-dcgettext_0028_0029-function-_0028gawk_0029-1): [Programmer i18n](#Programmer-i18n)[`dcgettext()` function (`gawk`), portability and](#index-dcgettext_0028_0029-function-_0028gawk_0029_002c-portability-and): [I18N Portability](#I18N-Portability)[`dcngettext()` function (`gawk`)](#index-dcngettext_0028_0029-function-_0028gawk_0029): [I18N Functions](#I18N-Functions)[`dcngettext()` function (`gawk`)](#index-dcngettext_0028_0029-function-_0028gawk_0029-1): [Programmer i18n](#Programmer-i18n)[`dcngettext()` function (`gawk`), portability and](#index-dcngettext_0028_0029-function-_0028gawk_0029_002c-portability-and): [I18N Portability](#I18N-Portability)[deadlocks](#index-deadlocks): [Two-way I/O](#Two_002dway-I_002fO)[debugger commands, `b` (`break`)](#index-debugger-commands_002c-b-_0028break_0029): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `backtrace`](#index-debugger-commands_002c-backtrace): [Execution Stack](#Execution-Stack)[debugger commands, `break`](#index-debugger-commands_002c-break): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `bt` (`backtrace`)](#index-debugger-commands_002c-bt-_0028backtrace_0029): [Execution Stack](#Execution-Stack)[debugger commands, `c` (`continue`)](#index-debugger-commands_002c-c-_0028continue_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `clear`](#index-debugger-commands_002c-clear): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `commands`](#index-debugger-commands_002c-commands): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `condition`](#index-debugger-commands_002c-condition): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `continue`](#index-debugger-commands_002c-continue): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `d` (`delete`)](#index-debugger-commands_002c-d-_0028delete_0029): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `delete`](#index-debugger-commands_002c-delete): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `disable`](#index-debugger-commands_002c-disable): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `display`](#index-debugger-commands_002c-display): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `down`](#index-debugger-commands_002c-down): [Execution Stack](#Execution-Stack)[debugger commands, `dump`](#index-debugger-commands_002c-dump): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `e` (`enable`)](#index-debugger-commands_002c-e-_0028enable_0029): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `enable`](#index-debugger-commands_002c-enable): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `end`](#index-debugger-commands_002c-end): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `eval`](#index-debugger-commands_002c-eval): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `f` (`frame`)](#index-debugger-commands_002c-f-_0028frame_0029): [Execution Stack](#Execution-Stack)[debugger commands, `finish`](#index-debugger-commands_002c-finish): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `frame`](#index-debugger-commands_002c-frame): [Execution Stack](#Execution-Stack)[debugger commands, `h` (`help`)](#index-debugger-commands_002c-h-_0028help_0029): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `help`](#index-debugger-commands_002c-help): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `i` (`info`)](#index-debugger-commands_002c-i-_0028info_0029): [Debugger Info](#Debugger-Info)[debugger commands, `ignore`](#index-debugger-commands_002c-ignore): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `info`](#index-debugger-commands_002c-info): [Debugger Info](#Debugger-Info)[debugger commands, `l` (`list`)](#index-debugger-commands_002c-l-_0028list_0029): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `list`](#index-debugger-commands_002c-list): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `n` (`next`)](#index-debugger-commands_002c-n-_0028next_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `next`](#index-debugger-commands_002c-next): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `nexti`](#index-debugger-commands_002c-nexti): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `ni` (`nexti`)](#index-debugger-commands_002c-ni-_0028nexti_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `o` (`option`)](#index-debugger-commands_002c-o-_0028option_0029): [Debugger Info](#Debugger-Info)[debugger commands, `option`](#index-debugger-commands_002c-option): [Debugger Info](#Debugger-Info)[debugger commands, `p` (`print`)](#index-debugger-commands_002c-p-_0028print_0029): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `print`](#index-debugger-commands_002c-print): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `printf`](#index-debugger-commands_002c-printf): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `q` (`quit`)](#index-debugger-commands_002c-q-_0028quit_0029): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `quit`](#index-debugger-commands_002c-quit): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `r` (`run`)](#index-debugger-commands_002c-r-_0028run_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `return`](#index-debugger-commands_002c-return): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `run`](#index-debugger-commands_002c-run): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `s` (`step`)](#index-debugger-commands_002c-s-_0028step_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `set`](#index-debugger-commands_002c-set): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `si` (`stepi`)](#index-debugger-commands_002c-si-_0028stepi_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `silent`](#index-debugger-commands_002c-silent): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `step`](#index-debugger-commands_002c-step): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `stepi`](#index-debugger-commands_002c-stepi): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `t` (`tbreak`)](#index-debugger-commands_002c-t-_0028tbreak_0029): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `tbreak`](#index-debugger-commands_002c-tbreak): [Breakpoint Control](#Breakpoint-Control)[debugger commands, `trace`](#index-debugger-commands_002c-trace): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[debugger commands, `u` (`until`)](#index-debugger-commands_002c-u-_0028until_0029): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `undisplay`](#index-debugger-commands_002c-undisplay): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `until`](#index-debugger-commands_002c-until): [Debugger Execution Control](#Debugger-Execution-Control)[debugger commands, `unwatch`](#index-debugger-commands_002c-unwatch): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `up`](#index-debugger-commands_002c-up): [Execution Stack](#Execution-Stack)[debugger commands, `w` (`watch`)](#index-debugger-commands_002c-w-_0028watch_0029): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `watch`](#index-debugger-commands_002c-watch): [Viewing And Changing Data](#Viewing-And-Changing-Data)[debugger commands, `where` (`backtrace`)](#index-debugger-commands_002c-where-_0028backtrace_0029): [Execution Stack](#Execution-Stack)[debugger default list amount](#index-debugger-default-list-amount): [Debugger Info](#Debugger-Info)[debugger history file](#index-debugger-history-file): [Debugger Info](#Debugger-Info)[debugger history size](#index-debugger-history-size): [Debugger Info](#Debugger-Info)[debugger options](#index-debugger-options): [Debugger Info](#Debugger-Info)[debugger prompt](#index-debugger-prompt): [Debugger Info](#Debugger-Info)[debugger, how to start](#index-debugger_002c-how-to-start): [Debugger Invocation](#Debugger-Invocation)[debugger, read commands from a file](#index-debugger_002c-read-commands-from-a-file): [Debugger Info](#Debugger-Info)[debugging `awk` programs](#index-debugging-awk-programs): [Debugger](#Debugger)[debugging `gawk`, bug reports](#index-debugging-gawk_002c-bug-reports): [Bugs](#Bugs)[decimal point character, locale specific](#index-decimal-point-character_002c-locale-specific): [Options](#Options)[decrement operators](#index-decrement-operators): [Increment Ops](#Increment-Ops)[`default` keyword](#index-default-keyword): [Switch Statement](#Switch-Statement)[Deifik, Scott](#index-Deifik_002c-Scott): [Acknowledgments](#Acknowledgments)[Deifik, Scott](#index-Deifik_002c-Scott-1): [Contributors](#Contributors)[`delete`array](#index-delete-array): [Delete](#Delete)[delete breakpoint at location](#index-delete-breakpoint-at-location): [Breakpoint Control](#Breakpoint-Control)[delete breakpoint by number](#index-delete-breakpoint-by-number): [Breakpoint Control](#Breakpoint-Control)[`delete` debugger command](#index-delete-debugger-command): [Breakpoint Control](#Breakpoint-Control)[`delete` statement](#index-delete-statement): [Delete](#Delete)[delete watchpoint](#index-delete-watchpoint): [Viewing And Changing Data](#Viewing-And-Changing-Data)[deleting elements in arrays](#index-deleting-elements-in-arrays): [Delete](#Delete)[deleting entire arrays](#index-deleting-entire-arrays): [Delete](#Delete)[Demaille, Akim](#index-Demaille_002c-Akim): [Acknowledgments](#Acknowledgments)[describe call stack frame, in debugger](#index-describe-call-stack-frame_002c-in-debugger): [Debugger Info](#Debugger-Info)[differences between `gawk` and `awk`](#index-differences-between-gawk-and-awk): [String Functions](#String-Functions)[differences in `awk` and `gawk`, `ARGC`/`ARGV` variables](#index-differences-in-awk-and-gawk_002c-ARGC_002fARGV-variables): [ARGC and ARGV](#ARGC-and-ARGV)[differences in `awk` and `gawk`, `ARGIND` variable](#index-differences-in-awk-and-gawk_002c-ARGIND-variable): [Auto-set](#Auto_002dset)[differences in `awk` and `gawk`, array elements, deleting](#index-differences-in-awk-and-gawk_002c-array-elements_002c-deleting): [Delete](#Delete)[differences in `awk` and `gawk`, `AWKLIBPATH` environment variable](#index-differences-in-awk-and-gawk_002c-AWKLIBPATH-environment-variable): [AWKLIBPATH Variable](#AWKLIBPATH-Variable)[differences in `awk` and `gawk`, `AWKPATH` environment variable](#index-differences-in-awk-and-gawk_002c-AWKPATH-environment-variable): [AWKPATH Variable](#AWKPATH-Variable)[differences in `awk` and `gawk`, `BEGIN`/`END` patterns](#index-differences-in-awk-and-gawk_002c-BEGIN_002fEND-patterns): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[differences in `awk` and `gawk`, `BEGINFILE`/`ENDFILE` patterns](#index-differences-in-awk-and-gawk_002c-BEGINFILE_002fENDFILE-patterns): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[differences in `awk` and `gawk`, `BINMODE` variable](#index-differences-in-awk-and-gawk_002c-BINMODE-variable): [User-modified](#User_002dmodified)[differences in `awk` and `gawk`, `BINMODE` variable](#index-differences-in-awk-and-gawk_002c-BINMODE-variable-1): [PC Using](#PC-Using)[differences in `awk` and `gawk`, `close()` function](#index-differences-in-awk-and-gawk_002c-close_0028_0029-function): [Close Files And Pipes](#Close-Files-And-Pipes)[differences in `awk` and `gawk`, `close()` function](#index-differences-in-awk-and-gawk_002c-close_0028_0029-function-1): [Close Files And Pipes](#Close-Files-And-Pipes)[differences in `awk` and `gawk`, command-line directories](#index-differences-in-awk-and-gawk_002c-command_002dline-directories): [Command-line directories](#Command_002dline-directories)[differences in `awk` and `gawk`, `ERRNO` variable](#index-differences-in-awk-and-gawk_002c-ERRNO-variable): [Auto-set](#Auto_002dset)[differences in `awk` and `gawk`, error messages](#index-differences-in-awk-and-gawk_002c-error-messages): [Special FD](#Special-FD)[differences in `awk` and `gawk`, `FIELDWIDTHS` variable](#index-differences-in-awk-and-gawk_002c-FIELDWIDTHS-variable): [User-modified](#User_002dmodified)[differences in `awk` and `gawk`, `FPAT` variable](#index-differences-in-awk-and-gawk_002c-FPAT-variable): [User-modified](#User_002dmodified)[differences in `awk` and `gawk`, `FUNCTAB` variable](#index-differences-in-awk-and-gawk_002c-FUNCTAB-variable): [Auto-set](#Auto_002dset)[differences in `awk` and `gawk`, function arguments (`gawk`)](#index-differences-in-awk-and-gawk_002c-function-arguments-_0028gawk_0029): [Calling Built-in](#Calling-Built_002din)[differences in `awk` and `gawk`, `getline` command](#index-differences-in-awk-and-gawk_002c-getline-command): [Getline](#Getline)[differences in `awk` and `gawk`, `IGNORECASE` variable](#index-differences-in-awk-and-gawk_002c-IGNORECASE-variable): [User-modified](#User_002dmodified)[differences in `awk` and `gawk`, implementation limitations](#index-differences-in-awk-and-gawk_002c-implementation-limitations): [Getline Notes](#Getline-Notes)[differences in `awk` and `gawk`, implementation limitations](#index-differences-in-awk-and-gawk_002c-implementation-limitations-1): [Redirection](#Redirection)[differences in `awk` and `gawk`, indirect function calls](#index-differences-in-awk-and-gawk_002c-indirect-function-calls): [Indirect Calls](#Indirect-Calls)[differences in `awk` and `gawk`, input/output operators](#index-differences-in-awk-and-gawk_002c-input_002foutput-operators): [Getline/Coprocess](#Getline_002fCoprocess)[differences in `awk` and `gawk`, input/output operators](#index-differences-in-awk-and-gawk_002c-input_002foutput-operators-1): [Redirection](#Redirection)[differences in `awk` and `gawk`, line continuations](#index-differences-in-awk-and-gawk_002c-line-continuations): [Conditional Exp](#Conditional-Exp)[differences in `awk` and `gawk`, `LINT` variable](#index-differences-in-awk-and-gawk_002c-LINT-variable): [User-modified](#User_002dmodified)[differences in `awk` and `gawk`, `match()` function](#index-differences-in-awk-and-gawk_002c-match_0028_0029-function): [String Functions](#String-Functions)[differences in `awk` and `gawk`, `print`/`printf` statements](#index-differences-in-awk-and-gawk_002c-print_002fprintf-statements): [Format Modifiers](#Format-Modifiers)[differences in `awk` and `gawk`, `PROCINFO` array](#index-differences-in-awk-and-gawk_002c-PROCINFO-array): [Auto-set](#Auto_002dset)[differences in `awk` and `gawk`, read timeouts](#index-differences-in-awk-and-gawk_002c-read-timeouts): [Read Timeout](#Read-Timeout)[differences in `awk` and `gawk`, record separators](#index-differences-in-awk-and-gawk_002c-record-separators): [awk split records](#awk-split-records)[differences in `awk` and `gawk`, regexp constants](#index-differences-in-awk-and-gawk_002c-regexp-constants): [Standard Regexp Constants](#Standard-Regexp-Constants)[differences in `awk` and `gawk`, regular expressions](#index-differences-in-awk-and-gawk_002c-regular-expressions): [Case-sensitivity](#Case_002dsensitivity)[differences in `awk` and `gawk`, retrying input](#index-differences-in-awk-and-gawk_002c-retrying-input): [Retrying Input](#Retrying-Input)[differences in `awk` and `gawk`, `RS`/`RT` variables](#index-differences-in-awk-and-gawk_002c-RS_002fRT-variables): [gawk split records](#gawk-split-records)[differences in `awk` and `gawk`, `RT` variable](#index-differences-in-awk-and-gawk_002c-RT-variable): [Auto-set](#Auto_002dset)[differences in `awk` and `gawk`, single-character fields](#index-differences-in-awk-and-gawk_002c-single_002dcharacter-fields): [Single Character Fields](#Single-Character-Fields)[differences in `awk` and `gawk`, `split()` function](#index-differences-in-awk-and-gawk_002c-split_0028_0029-function): [String Functions](#String-Functions)[differences in `awk` and `gawk`, strings](#index-differences-in-awk-and-gawk_002c-strings): [Scalar Constants](#Scalar-Constants)[differences in `awk` and `gawk`, strings, storing](#index-differences-in-awk-and-gawk_002c-strings_002c-storing): [gawk split records](#gawk-split-records)[differences in `awk` and `gawk`, `SYMTAB` variable](#index-differences-in-awk-and-gawk_002c-SYMTAB-variable): [Auto-set](#Auto_002dset)[differences in `awk` and `gawk`, `TEXTDOMAIN` variable](#index-differences-in-awk-and-gawk_002c-TEXTDOMAIN-variable): [User-modified](#User_002dmodified)[differences in `awk` and `gawk`, trunc-mod operation](#index-differences-in-awk-and-gawk_002c-trunc_002dmod-operation): [Arithmetic Ops](#Arithmetic-Ops)[directories, command-line](#index-directories_002c-command_002dline): [Command-line directories](#Command_002dline-directories)[directories, searching](#index-directories_002c-searching): [Programs Exercises](#Programs-Exercises)[directories, searching for loadable extensions](#index-directories_002c-searching-for-loadable-extensions): [AWKLIBPATH Variable](#AWKLIBPATH-Variable)[directories, searching for source files](#index-directories_002c-searching-for-source-files): [AWKPATH Variable](#AWKPATH-Variable)[disable breakpoint](#index-disable-breakpoint): [Breakpoint Control](#Breakpoint-Control)[`disable` debugger command](#index-disable-debugger-command): [Breakpoint Control](#Breakpoint-Control)[`display` debugger command](#index-display-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[display debugger options](#index-display-debugger-options): [Debugger Info](#Debugger-Info)[division](#index-division): [Arithmetic Ops](#Arithmetic-Ops)[`do`-`while` statement](#index-do_002dwhile-statement): [Do Statement](#Do-Statement)[`do`-`while` statement, use of regexps in](#index-do_002dwhile-statement_002c-use-of-regexps-in): [Regexp Usage](#Regexp-Usage)[documentation, of `awk` programs](#index-documentation_002c-of-awk-programs): [Library Names](#Library-Names)[documentation, online](#index-documentation_002c-online): [Manual History](#Manual-History)[documents, searching](#index-documents_002c-searching): [Dupword Program](#Dupword-Program)[dollar sign (`$`), `$` field operator](#index-dollar-sign-_0028_0024_0029_002c-_0024-field-operator): [Fields](#Fields)[dollar sign (`$`), `$` field operator](#index-dollar-sign-_0028_0024_0029_002c-_0024-field-operator-1): [Precedence](#Precedence)[dollar sign (`$`), incrementing fields and arrays](#index-dollar-sign-_0028_0024_0029_002c-incrementing-fields-and-arrays): [Increment Ops](#Increment-Ops)[dollar sign (`$`), regexp operator](#index-dollar-sign-_0028_0024_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[double quote (`"`), in regexp constants](#index-double-quote-_0028_0022_0029_002c-in-regexp-constants): [Computed Regexps](#Computed-Regexps)[double quote (`"`), in shell commands](#index-double-quote-_0028_0022_0029_002c-in-shell-commands): [Quoting](#Quoting)[double-precision](#index-double_002dprecision): [Computer Arithmetic](#Computer-Arithmetic)[`down` debugger command](#index-down-debugger-command): [Execution Stack](#Execution-Stack)[Drepper, Ulrich](#index-Drepper_002c-Ulrich): [Acknowledgments](#Acknowledgments)[Duman, Patrice](#index-Duman_002c-Patrice): [Acknowledgments](#Acknowledgments)[dump all variables of a program](#index-dump-all-variables-of-a-program): [Options](#Options)[`dump` debugger command](#index-dump-debugger-command): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[`dupword.awk` program](#index-dupword_002eawk-program): [Dupword Program](#Dupword-Program)[dynamic profiling](#index-dynamic-profiling): [Profiling](#Profiling)[dynamically loaded extensions](#index-dynamically-loaded-extensions): [Dynamic Extensions](#Dynamic-Extensions)
---
[E](E)[`e` debugger command (alias for `enable`)](#index-e-debugger-command-_0028alias-for-enable_0029): [Breakpoint Control](#Breakpoint-Control)[EBCDIC](#index-EBCDIC): [Ordinal Functions](#Ordinal-Functions)[effective group ID of `gawk` user](#index-effective-group-ID-of-gawk-user): [Auto-set](#Auto_002dset)[effective user ID of `gawk` user](#index-effective-user-ID-of-gawk-user): [Auto-set](#Auto_002dset)[`egrep` utility](#index-egrep-utility): [Bracket Expressions](#Bracket-Expressions)[`egrep` utility](#index-egrep-utility-1): [Egrep Program](#Egrep-Program)[`egrep.awk` program](#index-egrep_002eawk-program): [Egrep Program](#Egrep-Program)[elements in arrays, assigning values](#index-elements-in-arrays_002c-assigning-values): [Assigning Elements](#Assigning-Elements)[elements in arrays, deleting](#index-elements-in-arrays_002c-deleting): [Delete](#Delete)[elements in arrays, order of access by `in` operator](#index-elements-in-arrays_002c-order-of-access-by-in-operator): [Scanning an Array](#Scanning-an-Array)[elements in arrays, scanning](#index-elements-in-arrays_002c-scanning): [Scanning an Array](#Scanning-an-Array)[elements of arrays](#index-elements-of-arrays): [Reference to Elements](#Reference-to-Elements)[email address for bug reports, `bug-gawk@gnu.org`](#index-email-address-for-bug-reports_002c-bug_002dgawk_0040gnu_002eorg): [Bug address](#Bug-address)[empty array elements](#index-empty-array-elements): [Reference to Elements](#Reference-to-Elements)[empty pattern](#index-empty-pattern): [Empty](#Empty)[empty strings](#index-empty-strings): [awk split records](#awk-split-records)[empty strings, See null strings](#index-empty-strings_002c-See-null-strings): [Regexp Field Splitting](#Regexp-Field-Splitting)[`EMRED`](#index-EMRED): [TCP/IP Networking](#TCP_002fIP-Networking)[enable breakpoint](#index-enable-breakpoint): [Breakpoint Control](#Breakpoint-Control)[`enable` debugger command](#index-enable-debugger-command): [Breakpoint Control](#Breakpoint-Control)[`end` debugger command](#index-end-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`END` pattern](#index-END-pattern): [BEGIN/END](#BEGIN_002fEND)[`END` pattern](#index-END-pattern-1): [Using BEGIN/END](#Using-BEGIN_002fEND)[`END` pattern, and profiling](#index-END-pattern_002c-and-profiling): [Profiling](#Profiling)[`END` pattern, `assert()` user-defined function and](#index-END-pattern_002c-assert_0028_0029-user_002ddefined-function-and): [Assert Function](#Assert-Function)[`END` pattern, Boolean patterns and](#index-END-pattern_002c-Boolean-patterns-and): [Expression Patterns](#Expression-Patterns)[`END` pattern, `exit` statement and](#index-END-pattern_002c-exit-statement-and): [Exit Statement](#Exit-Statement)[`END` pattern, `next`/`nextfile` statements and](#index-END-pattern_002c-next_002fnextfile-statements-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`END` pattern, `next`/`nextfile` statements and](#index-END-pattern_002c-next_002fnextfile-statements-and-1): [Next Statement](#Next-Statement)[`END` pattern, operators and](#index-END-pattern_002c-operators-and): [Using BEGIN/END](#Using-BEGIN_002fEND)[`END` pattern, `print` statement and](#index-END-pattern_002c-print-statement-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`ENDFILE` pattern](#index-ENDFILE-pattern): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`ENDFILE` pattern, Boolean patterns and](#index-ENDFILE-pattern_002c-Boolean-patterns-and): [Expression Patterns](#Expression-Patterns)[`endfile()` user-defined function](#index-endfile_0028_0029-user_002ddefined-function): [Filetrans Function](#Filetrans-Function)[`endgrent()` function (C library)](#index-endgrent_0028_0029-function-_0028C-library_0029): [Group Functions](#Group-Functions)[`endgrent()` user-defined function](#index-endgrent_0028_0029-user_002ddefined-function): [Group Functions](#Group-Functions)[`endpwent()` function (C library)](#index-endpwent_0028_0029-function-_0028C-library_0029): [Passwd Functions](#Passwd-Functions)[`endpwent()` user-defined function](#index-endpwent_0028_0029-user_002ddefined-function): [Passwd Functions](#Passwd-Functions)[English, Steve](#index-English_002c-Steve): [Advanced Features](#Advanced-Features)[`ENVIRON` array](#index-ENVIRON-array): [Auto-set](#Auto_002dset)[environment variables used by `gawk`](#index-environment-variables-used-by-gawk): [Environment Variables](#Environment-Variables)[environment variables, in `ENVIRON` array](#index-environment-variables_002c-in-ENVIRON-array): [Auto-set](#Auto_002dset)[epoch, definition of](#index-epoch_002c-definition-of): [Glossary](#Glossary)[equals sign (`=`), `=` operator](#index-equals-sign-_0028_003d_0029_002c-_003d-operator): [Assignment Ops](#Assignment-Ops)[equals sign (`=`), `==` operator](#index-equals-sign-_0028_003d_0029_002c-_003d_003d-operator): [Comparison Operators](#Comparison-Operators)[equals sign (`=`), `==` operator](#index-equals-sign-_0028_003d_0029_002c-_003d_003d-operator-1): [Precedence](#Precedence)[EREs (Extended Regular Expressions)](#index-EREs-_0028Extended-Regular-Expressions_0029): [Bracket Expressions](#Bracket-Expressions)[`ERRNO` variable](#index-ERRNO-variable): [Auto-set](#Auto_002dset)[`ERRNO` variable](#index-ERRNO-variable-1): [TCP/IP Networking](#TCP_002fIP-Networking)[`ERRNO` variable, with `BEGINFILE` pattern](#index-ERRNO-variable_002c-with-BEGINFILE-pattern): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`ERRNO` variable, with `close()` function](#index-ERRNO-variable_002c-with-close_0028_0029-function): [Close Files And Pipes](#Close-Files-And-Pipes)[`ERRNO` variable, with `getline` command](#index-ERRNO-variable_002c-with-getline-command): [Getline](#Getline)[error handling](#index-error-handling): [Special FD](#Special-FD)[error handling, `ERRNO` variable and](#index-error-handling_002c-ERRNO-variable-and): [Auto-set](#Auto_002dset)[error output](#index-error-output): [Special FD](#Special-FD)[escape processing, `gsub()`/`gensub()`/`sub()` functions](#index-escape-processing_002c-gsub_0028_0029_002fgensub_0028_0029_002fsub_0028_0029-functions): [Gory Details](#Gory-Details)[escape sequences, in strings](#index-escape-sequences_002c-in-strings): [Escape Sequences](#Escape-Sequences)[`eval` debugger command](#index-eval-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[evaluate expressions, in debugger](#index-evaluate-expressions_002c-in-debugger): [Viewing And Changing Data](#Viewing-And-Changing-Data)[evaluation order](#index-evaluation-order): [Increment Ops](#Increment-Ops)[evaluation order, concatenation](#index-evaluation-order_002c-concatenation): [Concatenation](#Concatenation)[evaluation order, functions](#index-evaluation-order_002c-functions): [Calling Built-in](#Calling-Built_002din)[examining fields](#index-examining-fields): [Fields](#Fields)[exclamation point (`!`), `!` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021-operator): [Boolean Ops](#Boolean-Ops)[exclamation point (`!`), `!` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021-operator-1): [Precedence](#Precedence)[exclamation point (`!`), `!` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021-operator-2): [Egrep Program](#Egrep-Program)[exclamation point (`!`), `!=` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_003d-operator): [Comparison Operators](#Comparison-Operators)[exclamation point (`!`), `!=` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_003d-operator-1): [Precedence](#Precedence)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator): [Regexp Usage](#Regexp-Usage)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-1): [Computed Regexps](#Computed-Regexps)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-2): [Case-sensitivity](#Case_002dsensitivity)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-3): [Regexp Constants](#Regexp-Constants)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-4): [Comparison Operators](#Comparison-Operators)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-5): [Comparison Operators](#Comparison-Operators)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-6): [Precedence](#Precedence)[exclamation point (`!`), `!~` operator](#index-exclamation-point-_0028_0021_0029_002c-_0021_007e-operator-7): [Expression Patterns](#Expression-Patterns)[`exit` debugger command](#index-exit-debugger-command): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[`exit` statement](#index-exit-statement): [Exit Statement](#Exit-Statement)[exit status, of `gawk`](#index-exit-status_002c-of-gawk): [Exit Status](#Exit-Status)[exit status, of VMS](#index-exit-status_002c-of-VMS): [VMS Running](#VMS-Running)[exit the debugger](#index-exit-the-debugger): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[exit the debugger](#index-exit-the-debugger-1): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[`exp()` function](#index-exp_0028_0029-function): [Numeric Functions](#Numeric-Functions)[`expand` utility](#index-expand-utility): [Very Simple](#Very-Simple)[Expat XML parser library](#index-Expat-XML-parser-library): [gawkextlib](#gawkextlib)[exponent](#index-exponent): [Numeric Functions](#Numeric-Functions)[expressions](#index-expressions): [Expressions](#Expressions)[expressions, as patterns](#index-expressions_002c-as-patterns): [Expression Patterns](#Expression-Patterns)[expressions, assignment](#index-expressions_002c-assignment): [Assignment Ops](#Assignment-Ops)[expressions, Boolean](#index-expressions_002c-Boolean): [Boolean Ops](#Boolean-Ops)[expressions, comparison](#index-expressions_002c-comparison): [Typing and Comparison](#Typing-and-Comparison)[expressions, conditional](#index-expressions_002c-conditional): [Conditional Exp](#Conditional-Exp)[expressions, matching, See comparison expressions](#index-expressions_002c-matching_002c-See-comparison-expressions): [Typing and Comparison](#Typing-and-Comparison)[expressions, selecting](#index-expressions_002c-selecting): [Conditional Exp](#Conditional-Exp)[Extended Regular Expressions (EREs)](#index-Extended-Regular-Expressions-_0028EREs_0029): [Bracket Expressions](#Bracket-Expressions)[extension API](#index-extension-API): [Extension API Description](#Extension-API-Description)[extension API informational variables](#index-extension-API-informational-variables): [Extension API Informational Variables](#Extension-API-Informational-Variables)[extension API version](#index-extension-API-version): [Extension Versioning](#Extension-Versioning)[extension API, version number](#index-extension-API_002c-version-number): [Auto-set](#Auto_002dset)[extension example](#index-extension-example): [Extension Example](#Extension-Example)[extension registration](#index-extension-registration): [Registration Functions](#Registration-Functions)[extension search path](#index-extension-search-path): [Finding Extensions](#Finding-Extensions)[extensions distributed with `gawk`](#index-extensions-distributed-with-gawk): [Extension Samples](#Extension-Samples)[extensions, allocating memory](#index-extensions_002c-allocating-memory): [Memory Allocation Functions](#Memory-Allocation-Functions)[extensions, Brian Kernighan&rsquo;s `awk`](#index-extensions_002c-Brian-Kernighan_0027s-awk): [BTL](#BTL)[extensions, Brian Kernighan&rsquo;s `awk`](#index-extensions_002c-Brian-Kernighan_0027s-awk-1): [Common Extensions](#Common-Extensions)[extensions, common, `**` operator](#index-extensions_002c-common_002c-_002a_002a-operator): [Arithmetic Ops](#Arithmetic-Ops)[extensions, common, `**=` operator](#index-extensions_002c-common_002c-_002a_002a_003d-operator): [Assignment Ops](#Assignment-Ops)[extensions, common, `/dev/stderr` special file](#index-extensions_002c-common_002c-_002fdev_002fstderr-special-file): [Special FD](#Special-FD)[extensions, common, `/dev/stdin` special file](#index-extensions_002c-common_002c-_002fdev_002fstdin-special-file): [Special FD](#Special-FD)[extensions, common, `/dev/stdout` special file](#index-extensions_002c-common_002c-_002fdev_002fstdout-special-file): [Special FD](#Special-FD)[extensions, common, `BINMODE` variable](#index-extensions_002c-common_002c-BINMODE-variable): [PC Using](#PC-Using)[extensions, common, `delete` to delete entire arrays](#index-extensions_002c-common_002c-delete-to-delete-entire-arrays): [Delete](#Delete)[extensions, common, `fflush()` function](#index-extensions_002c-common_002c-fflush_0028_0029-function): [I/O Functions](#I_002fO-Functions)[extensions, common, `func` keyword](#index-extensions_002c-common_002c-func-keyword): [Definition Syntax](#Definition-Syntax)[extensions, common, `length()` applied to an array](#index-extensions_002c-common_002c-length_0028_0029-applied-to-an-array): [String Functions](#String-Functions)[extensions, common, `RS` as a regexp](#index-extensions_002c-common_002c-RS-as-a-regexp): [gawk split records](#gawk-split-records)[extensions, common, single character fields](#index-extensions_002c-common_002c-single-character-fields): [Single Character Fields](#Single-Character-Fields)[extensions, common, `\x` escape sequence](#index-extensions_002c-common_002c-_005cx-escape-sequence): [Escape Sequences](#Escape-Sequences)[extensions, in `gawk`, not in POSIX `awk`](#index-extensions_002c-in-gawk_002c-not-in-POSIX-awk): [POSIX/GNU](#POSIX_002fGNU)[extensions, loading, `@load` directive](#index-extensions_002c-loading_002c-_0040load-directive): [Loading Shared Libraries](#Loading-Shared-Libraries)[extensions, `mawk`](#index-extensions_002c-mawk): [Common Extensions](#Common-Extensions)[extensions, where to find](#index-extensions_002c-where-to-find): [gawkextlib](#gawkextlib)[`extract.awk` program](#index-extract_002eawk-program): [Extract Program](#Extract-Program)[extraction, of marked strings (internationalization)](#index-extraction_002c-of-marked-strings-_0028internationalization_0029): [String Extraction](#String-Extraction)
---
[F](F)[`f` debugger command (alias for `frame`)](#index-f-debugger-command-_0028alias-for-frame_0029): [Execution Stack](#Execution-Stack)[false, logical](#index-false_002c-logical): [Truth Values](#Truth-Values)[FDL (Free Documentation License)](#index-FDL-_0028Free-Documentation-License_0029): [GNU Free Documentation License](#GNU-Free-Documentation-License)[features, adding to `gawk`](#index-features_002c-adding-to-gawk): [Adding Code](#Adding-Code)[features, deprecated](#index-features_002c-deprecated): [Obsolete](#Obsolete)[features, undocumented](#index-features_002c-undocumented): [Undocumented](#Undocumented)[Fenlason, Jay](#index-Fenlason_002c-Jay): [History](#History)[Fenlason, Jay](#index-Fenlason_002c-Jay-1): [Contributors](#Contributors)[`fflush()` function](#index-fflush_0028_0029-function): [I/O Functions](#I_002fO-Functions)[field numbers](#index-field-numbers): [Nonconstant Fields](#Nonconstant-Fields)[field operator `$`](#index-field-operator-_0024): [Fields](#Fields)[field operators, dollar sign as](#index-field-operators_002c-dollar-sign-as): [Fields](#Fields)[field separator, in multiline records](#index-field-separator_002c-in-multiline-records): [Multiple Line](#Multiple-Line)[field separator, on command line](#index-field-separator_002c-on-command-line): [Command Line Field Separator](#Command-Line-Field-Separator)[field separator, POSIX and](#index-field-separator_002c-POSIX-and): [Full Line Fields](#Full-Line-Fields)[field separators](#index-field-separators): [Field Separators](#Field-Separators)[field separators](#index-field-separators-1): [User-modified](#User_002dmodified)[field separators](#index-field-separators-2): [User-modified](#User_002dmodified)[field separators, choice of](#index-field-separators_002c-choice-of): [Field Separators](#Field-Separators)[field separators, `FIELDWIDTHS` variable and](#index-field-separators_002c-FIELDWIDTHS-variable-and): [User-modified](#User_002dmodified)[field separators, `FPAT` variable and](#index-field-separators_002c-FPAT-variable-and): [User-modified](#User_002dmodified)[field separators, regular expressions as](#index-field-separators_002c-regular-expressions-as): [Field Separators](#Field-Separators)[field separators, regular expressions as](#index-field-separators_002c-regular-expressions-as-1): [Regexp Field Splitting](#Regexp-Field-Splitting)[field separators, See Also `OFS`](#index-field-separators_002c-See-Also-OFS): [Changing Fields](#Changing-Fields)[field separators, spaces as](#index-field-separators_002c-spaces-as): [Cut Program](#Cut-Program)[fields](#index-fields): [Reading Files](#Reading-Files)[fields](#index-fields-1): [Fields](#Fields)[fields](#index-fields-2): [Basic High Level](#Basic-High-Level)[fields, adding](#index-fields_002c-adding): [Changing Fields](#Changing-Fields)[fields, changing contents of](#index-fields_002c-changing-contents-of): [Changing Fields](#Changing-Fields)[fields, cutting](#index-fields_002c-cutting): [Cut Program](#Cut-Program)[fields, examining](#index-fields_002c-examining): [Fields](#Fields)[fields, number of](#index-fields_002c-number-of): [Fields](#Fields)[fields, numbers](#index-fields_002c-numbers): [Nonconstant Fields](#Nonconstant-Fields)[fields, printing](#index-fields_002c-printing): [Print Examples](#Print-Examples)[fields, separating](#index-fields_002c-separating): [Field Separators](#Field-Separators)[fields, separating](#index-fields_002c-separating-1): [Field Separators](#Field-Separators)[fields, single-character](#index-fields_002c-single_002dcharacter): [Single Character Fields](#Single-Character-Fields)[`FIELDWIDTHS` variable](#index-FIELDWIDTHS-variable): [Fixed width data](#Fixed-width-data)[`FIELDWIDTHS` variable](#index-FIELDWIDTHS-variable-1): [User-modified](#User_002dmodified)[file descriptors](#index-file-descriptors): [Special FD](#Special-FD)[file inclusion, `@include` directive](#index-file-inclusion_002c-_0040include-directive): [Include Files](#Include-Files)[file names, distinguishing](#index-file-names_002c-distinguishing): [Auto-set](#Auto_002dset)[file names, in compatibility mode](#index-file-names_002c-in-compatibility-mode): [Special Caveats](#Special-Caveats)[file names, standard streams in `gawk`](#index-file-names_002c-standard-streams-in-gawk): [Special FD](#Special-FD)[`FILENAME` variable](#index-FILENAME-variable): [Reading Files](#Reading-Files)[`FILENAME` variable](#index-FILENAME-variable-1): [Auto-set](#Auto_002dset)[`FILENAME` variable, `getline`, setting with](#index-FILENAME-variable_002c-getline_002c-setting-with): [Getline Notes](#Getline-Notes)[filenames, assignments as](#index-filenames_002c-assignments-as): [Ignoring Assigns](#Ignoring-Assigns)[files, `.gmo`](#index-files_002c-_002egmo): [Explaining gettext](#Explaining-gettext)[files, `.gmo`, specifying directory of](#index-files_002c-_002egmo_002c-specifying-directory-of): [Explaining gettext](#Explaining-gettext)[files, `.gmo`, specifying directory of](#index-files_002c-_002egmo_002c-specifying-directory-of-1): [Programmer i18n](#Programmer-i18n)[files, `.mo`, converting from `.po`](#index-files_002c-_002emo_002c-converting-from-_002epo): [I18N Example](#I18N-Example)[files, `.po`](#index-files_002c-_002epo): [Explaining gettext](#Explaining-gettext)[files, `.po`](#index-files_002c-_002epo-1): [Translator i18n](#Translator-i18n)[files, `.po`, converting to `.mo`](#index-files_002c-_002epo_002c-converting-to-_002emo): [I18N Example](#I18N-Example)[files, `.pot`](#index-files_002c-_002epot): [Explaining gettext](#Explaining-gettext)[files, `/dev/&hellip;` special files](#index-files_002c-_002fdev_002f_2026-special-files): [Special FD](#Special-FD)[files, `/inet/&hellip;` (`gawk`)](#index-files_002c-_002finet_002f_2026-_0028gawk_0029): [TCP/IP Networking](#TCP_002fIP-Networking)[files, `/inet4/&hellip;` (`gawk`)](#index-files_002c-_002finet4_002f_2026-_0028gawk_0029): [TCP/IP Networking](#TCP_002fIP-Networking)[files, `/inet6/&hellip;` (`gawk`)](#index-files_002c-_002finet6_002f_2026-_0028gawk_0029): [TCP/IP Networking](#TCP_002fIP-Networking)[files, `awk` programs in](#index-files_002c-awk-programs-in): [Long](#Long)[files, `awkprof.out`](#index-files_002c-awkprof_002eout): [Profiling](#Profiling)[files, awkvars.out](#index-files_002c-awkvars_002eout): [Options](#Options)[files, closing](#index-files_002c-closing): [I/O Functions](#I_002fO-Functions)[files, descriptors, See file descriptors](#index-files_002c-descriptors_002c-See-file-descriptors): [Special FD](#Special-FD)[files, group](#index-files_002c-group): [Group Functions](#Group-Functions)[files, initialization and cleanup](#index-files_002c-initialization-and-cleanup): [Filetrans Function](#Filetrans-Function)[files, input, See input files](#index-files_002c-input_002c-See-input-files): [Read Terminal](#Read-Terminal)[files, log, timestamps in](#index-files_002c-log_002c-timestamps-in): [Time Functions](#Time-Functions)[files, managing](#index-files_002c-managing): [Data File Management](#Data-File-Management)[files, managing, data file boundaries](#index-files_002c-managing_002c-data-file-boundaries): [Filetrans Function](#Filetrans-Function)[files, message object](#index-files_002c-message-object): [Explaining gettext](#Explaining-gettext)[files, message object, converting from portable object files](#index-files_002c-message-object_002c-converting-from-portable-object-files): [I18N Example](#I18N-Example)[files, message object, specifying directory of](#index-files_002c-message-object_002c-specifying-directory-of): [Explaining gettext](#Explaining-gettext)[files, message object, specifying directory of](#index-files_002c-message-object_002c-specifying-directory-of-1): [Programmer i18n](#Programmer-i18n)[files, multiple passes over](#index-files_002c-multiple-passes-over): [Other Arguments](#Other-Arguments)[files, multiple, duplicating output into](#index-files_002c-multiple_002c-duplicating-output-into): [Tee Program](#Tee-Program)[files, output, See output files](#index-files_002c-output_002c-See-output-files): [Close Files And Pipes](#Close-Files-And-Pipes)[files, password](#index-files_002c-password): [Passwd Functions](#Passwd-Functions)[files, portable object](#index-files_002c-portable-object): [Explaining gettext](#Explaining-gettext)[files, portable object](#index-files_002c-portable-object-1): [Translator i18n](#Translator-i18n)[files, portable object template](#index-files_002c-portable-object-template): [Explaining gettext](#Explaining-gettext)[files, portable object, converting to message object files](#index-files_002c-portable-object_002c-converting-to-message-object-files): [I18N Example](#I18N-Example)[files, portable object, generating](#index-files_002c-portable-object_002c-generating): [Options](#Options)[files, processing, `ARGIND` variable and](#index-files_002c-processing_002c-ARGIND-variable-and): [Auto-set](#Auto_002dset)[files, reading](#index-files_002c-reading): [Rewind Function](#Rewind-Function)[files, reading, multiline records](#index-files_002c-reading_002c-multiline-records): [Multiple Line](#Multiple-Line)[files, searching for regular expressions](#index-files_002c-searching-for-regular-expressions): [Egrep Program](#Egrep-Program)[files, skipping](#index-files_002c-skipping): [File Checking](#File-Checking)[files, source, search path for](#index-files_002c-source_002c-search-path-for): [Programs Exercises](#Programs-Exercises)[files, splitting](#index-files_002c-splitting): [Split Program](#Split-Program)[files, Texinfo, extracting programs from](#index-files_002c-Texinfo_002c-extracting-programs-from): [Extract Program](#Extract-Program)[find substring in string](#index-find-substring-in-string): [String Functions](#String-Functions)[finding extensions](#index-finding-extensions): [Finding Extensions](#Finding-Extensions)[`finish` debugger command](#index-finish-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[Fish, Fred](#index-Fish_002c-Fred): [Contributors](#Contributors)[fixed-width data](#index-fixed_002dwidth-data): [Constant Size](#Constant-Size)[flag variables](#index-flag-variables): [Boolean Ops](#Boolean-Ops)[flag variables](#index-flag-variables-1): [Tee Program](#Tee-Program)[floating-point, numbers](#index-floating_002dpoint_002c-numbers): [Computer Arithmetic](#Computer-Arithmetic)[floating-point, numbers, arbitrary precision](#index-floating_002dpoint_002c-numbers_002c-arbitrary-precision): [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)[floating-point, numbers, arbitrary-precision](#index-floating_002dpoint_002c-numbers_002c-arbitrary_002dprecision): [Computer Arithmetic](#Computer-Arithmetic)[floating-point, numbers, double-precision](#index-floating_002dpoint_002c-numbers_002c-double_002dprecision): [Computer Arithmetic](#Computer-Arithmetic)[floating-point, numbers, single-precision](#index-floating_002dpoint_002c-numbers_002c-single_002dprecision): [Computer Arithmetic](#Computer-Arithmetic)[floating-point, VAX/VMS](#index-floating_002dpoint_002c-VAX_002fVMS): [VMS Running](#VMS-Running)[flush buffered output](#index-flush-buffered-output): [I/O Functions](#I_002fO-Functions)[`fnmatch()` extension function](#index-fnmatch_0028_0029-extension-function): [Extension Sample Fnmatch](#Extension-Sample-Fnmatch)[`FNR` variable](#index-FNR-variable): [Records](#Records)[`FNR` variable](#index-FNR-variable-1): [Auto-set](#Auto_002dset)[`FNR` variable, changing](#index-FNR-variable_002c-changing): [Auto-set](#Auto_002dset)[`for` statement](#index-for-statement): [For Statement](#For-Statement)[`for` statement, looping over arrays](#index-for-statement_002c-looping-over-arrays): [Scanning an Array](#Scanning-an-Array)[`fork()` extension function](#index-fork_0028_0029-extension-function): [Extension Sample Fork](#Extension-Sample-Fork)[format specifiers](#index-format-specifiers): [Basic Printf](#Basic-Printf)[format specifiers, mixing regular with positional specifiers](#index-format-specifiers_002c-mixing-regular-with-positional-specifiers): [Printf Ordering](#Printf-Ordering)[format specifiers, `printf` statement](#index-format-specifiers_002c-printf-statement): [Control Letters](#Control-Letters)[format specifiers, `strftime()` function (`gawk`)](#index-format-specifiers_002c-strftime_0028_0029-function-_0028gawk_0029): [Time Functions](#Time-Functions)[format time string](#index-format-time-string): [Time Functions](#Time-Functions)[formats, numeric output](#index-formats_002c-numeric-output): [OFMT](#OFMT)[formatting output](#index-formatting-output): [Printf](#Printf)[formatting strings](#index-formatting-strings): [String Functions](#String-Functions)[forward slash (`/`) to enclose regular expressions](#index-forward-slash-_0028_002f_0029-to-enclose-regular-expressions): [Regexp](#Regexp)[forward slash (`/`), `/` operator](#index-forward-slash-_0028_002f_0029_002c-_002f-operator): [Precedence](#Precedence)[forward slash (`/`), `/=` operator](#index-forward-slash-_0028_002f_0029_002c-_002f_003d-operator): [Assignment Ops](#Assignment-Ops)[forward slash (`/`), `/=` operator](#index-forward-slash-_0028_002f_0029_002c-_002f_003d-operator-1): [Precedence](#Precedence)[forward slash (`/`), `/=` operator, vs. `/=&hellip;/` regexp constant](#index-forward-slash-_0028_002f_0029_002c-_002f_003d-operator_002c-vs_002e-_002f_003d_2026_002f-regexp-constant): [Assignment Ops](#Assignment-Ops)[forward slash (`/`), patterns and](#index-forward-slash-_0028_002f_0029_002c-patterns-and): [Expression Patterns](#Expression-Patterns)[`FPAT` variable](#index-FPAT-variable): [Splitting By Content](#Splitting-By-Content)[`FPAT` variable](#index-FPAT-variable-1): [User-modified](#User_002dmodified)[`frame` debugger command](#index-frame-debugger-command): [Execution Stack](#Execution-Stack)[Free Documentation License (FDL)](#index-Free-Documentation-License-_0028FDL_0029): [GNU Free Documentation License](#GNU-Free-Documentation-License)[Free Software Foundation (FSF)](#index-Free-Software-Foundation-_0028FSF_0029): [Manual History](#Manual-History)[Free Software Foundation (FSF)](#index-Free-Software-Foundation-_0028FSF_0029-1): [Getting](#Getting)[Free Software Foundation (FSF)](#index-Free-Software-Foundation-_0028FSF_0029-2): [Glossary](#Glossary)[Free Software Foundation (FSF)](#index-Free-Software-Foundation-_0028FSF_0029-3): [Glossary](#Glossary)[FreeBSD](#index-FreeBSD): [Glossary](#Glossary)[`FS` variable](#index-FS-variable): [Field Separators](#Field-Separators)[`FS` variable](#index-FS-variable-1): [User-modified](#User_002dmodified)[`FS` variable, `--field-separator` option and](#index-FS-variable_002c-_002d_002dfield_002dseparator-option-and): [Options](#Options)[`FS` variable, as null string](#index-FS-variable_002c-as-null-string): [Single Character Fields](#Single-Character-Fields)[`FS` variable, as TAB character](#index-FS-variable_002c-as-TAB-character): [Options](#Options)[`FS` variable, changing value of](#index-FS-variable_002c-changing-value-of): [Field Separators](#Field-Separators)[`FS` variable, running `awk` programs and](#index-FS-variable_002c-running-awk-programs-and): [Cut Program](#Cut-Program)[`FS` variable, setting from command line](#index-FS-variable_002c-setting-from-command-line): [Command Line Field Separator](#Command-Line-Field-Separator)[`FS`, containing `^`](#index-FS_002c-containing-_005e): [Regexp Field Splitting](#Regexp-Field-Splitting)[`FS`, in multiline records](#index-FS_002c-in-multiline-records): [Multiple Line](#Multiple-Line)[FSF (Free Software Foundation)](#index-FSF-_0028Free-Software-Foundation_0029): [Manual History](#Manual-History)[FSF (Free Software Foundation)](#index-FSF-_0028Free-Software-Foundation_0029-1): [Getting](#Getting)[FSF (Free Software Foundation)](#index-FSF-_0028Free-Software-Foundation_0029-2): [Glossary](#Glossary)[FSF (Free Software Foundation)](#index-FSF-_0028Free-Software-Foundation_0029-3): [Glossary](#Glossary)[`fts()` extension function](#index-fts_0028_0029-extension-function): [Extension Sample File Functions](#Extension-Sample-File-Functions)[`FUNCTAB` array](#index-FUNCTAB-array): [Auto-set](#Auto_002dset)[function calls](#index-function-calls): [Function Calls](#Function-Calls)[function calls, indirect](#index-function-calls_002c-indirect): [Indirect Calls](#Indirect-Calls)[function calls, indirect, `@`-notation for](#index-function-calls_002c-indirect_002c-_0040_002dnotation-for): [Indirect Calls](#Indirect-Calls)[function definition example](#index-function-definition-example): [Function Example](#Function-Example)[function pointers](#index-function-pointers): [Indirect Calls](#Indirect-Calls)[functions, arrays as parameters to](#index-functions_002c-arrays-as-parameters-to): [Pass By Value/Reference](#Pass-By-Value_002fReference)[functions, built-in](#index-functions_002c-built_002din): [Function Calls](#Function-Calls)[functions, built-in](#index-functions_002c-built_002din-1): [Functions](#Functions)[functions, built-in, evaluation order](#index-functions_002c-built_002din_002c-evaluation-order): [Calling Built-in](#Calling-Built_002din)[functions, defining](#index-functions_002c-defining): [Definition Syntax](#Definition-Syntax)[functions, library](#index-functions_002c-library): [Library Functions](#Library-Functions)[functions, library, assertions](#index-functions_002c-library_002c-assertions): [Assert Function](#Assert-Function)[functions, library, associative arrays and](#index-functions_002c-library_002c-associative-arrays-and): [Library Names](#Library-Names)[functions, library, C library](#index-functions_002c-library_002c-C-library): [Getopt Function](#Getopt-Function)[functions, library, character values as numbers](#index-functions_002c-library_002c-character-values-as-numbers): [Ordinal Functions](#Ordinal-Functions)[functions, library, Cliff random numbers](#index-functions_002c-library_002c-Cliff-random-numbers): [Cliff Random Function](#Cliff-Random-Function)[functions, library, command-line options](#index-functions_002c-library_002c-command_002dline-options): [Getopt Function](#Getopt-Function)[functions, library, example program for using](#index-functions_002c-library_002c-example-program-for-using): [Igawk Program](#Igawk-Program)[functions, library, group database, reading](#index-functions_002c-library_002c-group-database_002c-reading): [Group Functions](#Group-Functions)[functions, library, managing data files](#index-functions_002c-library_002c-managing-data-files): [Data File Management](#Data-File-Management)[functions, library, managing time](#index-functions_002c-library_002c-managing-time): [Getlocaltime Function](#Getlocaltime-Function)[functions, library, merging arrays into strings](#index-functions_002c-library_002c-merging-arrays-into-strings): [Join Function](#Join-Function)[functions, library, rounding numbers](#index-functions_002c-library_002c-rounding-numbers): [Round Function](#Round-Function)[functions, library, user database, reading](#index-functions_002c-library_002c-user-database_002c-reading): [Passwd Functions](#Passwd-Functions)[functions, names of](#index-functions_002c-names-of): [Definition Syntax](#Definition-Syntax)[functions, recursive](#index-functions_002c-recursive): [Definition Syntax](#Definition-Syntax)[functions, string-translation](#index-functions_002c-string_002dtranslation): [I18N Functions](#I18N-Functions)[functions, undefined](#index-functions_002c-undefined): [Pass By Value/Reference](#Pass-By-Value_002fReference)[functions, user-defined](#index-functions_002c-user_002ddefined): [User-defined](#User_002ddefined)[functions, user-defined, calling](#index-functions_002c-user_002ddefined_002c-calling): [Function Caveats](#Function-Caveats)[functions, user-defined, counts, in a profile](#index-functions_002c-user_002ddefined_002c-counts_002c-in-a-profile): [Profiling](#Profiling)[functions, user-defined, library of](#index-functions_002c-user_002ddefined_002c-library-of): [Library Functions](#Library-Functions)[functions, user-defined, `next`/`nextfile` statements and](#index-functions_002c-user_002ddefined_002c-next_002fnextfile-statements-and): [Next Statement](#Next-Statement)[functions, user-defined, `next`/`nextfile` statements and](#index-functions_002c-user_002ddefined_002c-next_002fnextfile-statements-and-1): [Nextfile Statement](#Nextfile-Statement)
---
[G](G)[G-d](#index-G_002dd): [Acknowledgments](#Acknowledgments)[G., Daniel Richard](#index-G_002e_002c-Daniel-Richard): [Acknowledgments](#Acknowledgments)[G., Daniel Richard](#index-G_002e_002c-Daniel-Richard-1): [Maintainers](#Maintainers)[Garfinkle, Scott](#index-Garfinkle_002c-Scott): [Contributors](#Contributors)[`gawk` program, dynamic profiling](#index-gawk-program_002c-dynamic-profiling): [Profiling](#Profiling)[`gawk` version](#index-gawk-version): [Auto-set](#Auto_002dset)[`gawk`, `ARGIND` variable in](#index-gawk_002c-ARGIND-variable-in): [Other Arguments](#Other-Arguments)[`gawk`, `awk` and](#index-gawk_002c-awk-and): [Preface](#Preface)[`gawk`, `awk` and](#index-gawk_002c-awk-and-1): [This Manual](#This-Manual)[`gawk`, bitwise operations in](#index-gawk_002c-bitwise-operations-in): [Bitwise Functions](#Bitwise-Functions)[`gawk`, `break` statement in](#index-gawk_002c-break-statement-in): [Break Statement](#Break-Statement)[`gawk`, character classes and](#index-gawk_002c-character-classes-and): [Bracket Expressions](#Bracket-Expressions)[`gawk`, coding style in](#index-gawk_002c-coding-style-in): [Adding Code](#Adding-Code)[`gawk`, command-line options, and regular expressions](#index-gawk_002c-command_002dline-options_002c-and-regular-expressions): [GNU Regexp Operators](#GNU-Regexp-Operators)[`gawk`, configuring](#index-gawk_002c-configuring): [Configuration Philosophy](#Configuration-Philosophy)[`gawk`, configuring, options](#index-gawk_002c-configuring_002c-options): [Additional Configuration Options](#Additional-Configuration-Options)[`gawk`, `continue` statement in](#index-gawk_002c-continue-statement-in): [Continue Statement](#Continue-Statement)[`gawk`, distribution](#index-gawk_002c-distribution): [Distribution contents](#Distribution-contents)[`gawk`, `ERRNO` variable in](#index-gawk_002c-ERRNO-variable-in): [Getline](#Getline)[`gawk`, `ERRNO` variable in](#index-gawk_002c-ERRNO-variable-in-1): [Close Files And Pipes](#Close-Files-And-Pipes)[`gawk`, `ERRNO` variable in](#index-gawk_002c-ERRNO-variable-in-2): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`gawk`, `ERRNO` variable in](#index-gawk_002c-ERRNO-variable-in-3): [Auto-set](#Auto_002dset)[`gawk`, `ERRNO` variable in](#index-gawk_002c-ERRNO-variable-in-4): [TCP/IP Networking](#TCP_002fIP-Networking)[`gawk`, escape sequences](#index-gawk_002c-escape-sequences): [Escape Sequences](#Escape-Sequences)[`gawk`, extensions, disabling](#index-gawk_002c-extensions_002c-disabling): [Options](#Options)[`gawk`, features, adding](#index-gawk_002c-features_002c-adding): [Adding Code](#Adding-Code)[`gawk`, features, advanced](#index-gawk_002c-features_002c-advanced): [Advanced Features](#Advanced-Features)[`gawk`, field separators and](#index-gawk_002c-field-separators-and): [User-modified](#User_002dmodified)[`gawk`, `FIELDWIDTHS` variable in](#index-gawk_002c-FIELDWIDTHS-variable-in): [Fixed width data](#Fixed-width-data)[`gawk`, `FIELDWIDTHS` variable in](#index-gawk_002c-FIELDWIDTHS-variable-in-1): [User-modified](#User_002dmodified)[`gawk`, file names in](#index-gawk_002c-file-names-in): [Special Files](#Special-Files)[`gawk`, format-control characters](#index-gawk_002c-format_002dcontrol-characters): [Control Letters](#Control-Letters)[`gawk`, format-control characters](#index-gawk_002c-format_002dcontrol-characters-1): [Control Letters](#Control-Letters)[`gawk`, `FPAT` variable in](#index-gawk_002c-FPAT-variable-in): [Splitting By Content](#Splitting-By-Content)[`gawk`, `FPAT` variable in](#index-gawk_002c-FPAT-variable-in-1): [User-modified](#User_002dmodified)[`gawk`, `FUNCTAB` array in](#index-gawk_002c-FUNCTAB-array-in): [Auto-set](#Auto_002dset)[`gawk`, function arguments and](#index-gawk_002c-function-arguments-and): [Calling Built-in](#Calling-Built_002din)[`gawk`, hexadecimal numbers and](#index-gawk_002c-hexadecimal-numbers-and): [Nondecimal-numbers](#Nondecimal_002dnumbers)[`gawk`, `IGNORECASE` variable in](#index-gawk_002c-IGNORECASE-variable-in): [Case-sensitivity](#Case_002dsensitivity)[`gawk`, `IGNORECASE` variable in](#index-gawk_002c-IGNORECASE-variable-in-1): [User-modified](#User_002dmodified)[`gawk`, `IGNORECASE` variable in](#index-gawk_002c-IGNORECASE-variable-in-2): [Array Intro](#Array-Intro)[`gawk`, `IGNORECASE` variable in](#index-gawk_002c-IGNORECASE-variable-in-3): [String Functions](#String-Functions)[`gawk`, `IGNORECASE` variable in](#index-gawk_002c-IGNORECASE-variable-in-4): [Array Sorting Functions](#Array-Sorting-Functions)[`gawk`, implementation issues](#index-gawk_002c-implementation-issues): [Notes](#Notes)[`gawk`, implementation issues, debugging](#index-gawk_002c-implementation-issues_002c-debugging): [Compatibility Mode](#Compatibility-Mode)[`gawk`, implementation issues, downward compatibility](#index-gawk_002c-implementation-issues_002c-downward-compatibility): [Compatibility Mode](#Compatibility-Mode)[`gawk`, implementation issues, limits](#index-gawk_002c-implementation-issues_002c-limits): [Getline Notes](#Getline-Notes)[`gawk`, implementation issues, pipes](#index-gawk_002c-implementation-issues_002c-pipes): [Redirection](#Redirection)[`gawk`, installing](#index-gawk_002c-installing): [Installation](#Installation)[`gawk`, internationalization and, See internationalization](#index-gawk_002c-internationalization-and_002c-See-internationalization): [Internationalization](#Internationalization)[`gawk`, interpreter, adding code to](#index-gawk_002c-interpreter_002c-adding-code-to): [Using Internal File Ops](#Using-Internal-File-Ops)[`gawk`, interval expressions and](#index-gawk_002c-interval-expressions-and): [Regexp Operators](#Regexp-Operators)[`gawk`, line continuation in](#index-gawk_002c-line-continuation-in): [Conditional Exp](#Conditional-Exp)[`gawk`, `LINT` variable in](#index-gawk_002c-LINT-variable-in): [User-modified](#User_002dmodified)[`gawk`, list of contributors to](#index-gawk_002c-list-of-contributors-to): [Contributors](#Contributors)[`gawk`, MS-Windows version of](#index-gawk_002c-MS_002dWindows-version-of): [PC Using](#PC-Using)[`gawk`, newlines in](#index-gawk_002c-newlines-in): [Statements/Lines](#Statements_002fLines)[`gawk`, octal numbers and](#index-gawk_002c-octal-numbers-and): [Nondecimal-numbers](#Nondecimal_002dnumbers)[`gawk`, predefined variables and](#index-gawk_002c-predefined-variables-and): [Built-in Variables](#Built_002din-Variables)[`gawk`, `PROCINFO` array in](#index-gawk_002c-PROCINFO-array-in): [Auto-set](#Auto_002dset)[`gawk`, `PROCINFO` array in](#index-gawk_002c-PROCINFO-array-in-1): [Time Functions](#Time-Functions)[`gawk`, `PROCINFO` array in](#index-gawk_002c-PROCINFO-array-in-2): [Two-way I/O](#Two_002dway-I_002fO)[`gawk`, regexp constants and](#index-gawk_002c-regexp-constants-and): [Standard Regexp Constants](#Standard-Regexp-Constants)[`gawk`, regular expressions, case sensitivity](#index-gawk_002c-regular-expressions_002c-case-sensitivity): [Case-sensitivity](#Case_002dsensitivity)[`gawk`, regular expressions, operators](#index-gawk_002c-regular-expressions_002c-operators): [GNU Regexp Operators](#GNU-Regexp-Operators)[`gawk`, regular expressions, precedence](#index-gawk_002c-regular-expressions_002c-precedence): [Regexp Operators](#Regexp-Operators)[`gawk`, `RT` variable in](#index-gawk_002c-RT-variable-in): [awk split records](#awk-split-records)[`gawk`, `RT` variable in](#index-gawk_002c-RT-variable-in-1): [Multiple Line](#Multiple-Line)[`gawk`, `RT` variable in](#index-gawk_002c-RT-variable-in-2): [Auto-set](#Auto_002dset)[`gawk`, See Also `awk`](#index-gawk_002c-See-Also-awk): [Preface](#Preface)[`gawk`, source code, obtaining](#index-gawk_002c-source-code_002c-obtaining): [Getting](#Getting)[`gawk`, splitting fields and](#index-gawk_002c-splitting-fields-and): [Testing field creation](#Testing-field-creation)[`gawk`, string-translation functions](#index-gawk_002c-string_002dtranslation-functions): [I18N Functions](#I18N-Functions)[`gawk`, `SYMTAB` array in](#index-gawk_002c-SYMTAB-array-in): [Auto-set](#Auto_002dset)[`gawk`, `TEXTDOMAIN` variable in](#index-gawk_002c-TEXTDOMAIN-variable-in): [User-modified](#User_002dmodified)[`gawk`, timestamps](#index-gawk_002c-timestamps): [Time Functions](#Time-Functions)[`gawk`, uses for](#index-gawk_002c-uses-for): [Preface](#Preface)[`gawk`, versions of, information about, printing](#index-gawk_002c-versions-of_002c-information-about_002c-printing): [Options](#Options)[`gawk`, VMS version of](#index-gawk_002c-VMS-version-of): [VMS Installation](#VMS-Installation)[`gawk`, word-boundary operator](#index-gawk_002c-word_002dboundary-operator): [GNU Regexp Operators](#GNU-Regexp-Operators)[`gawkextlib`](#index-gawkextlib): [gawkextlib](#gawkextlib)[`gawkextlib` project](#index-gawkextlib-project): [gawkextlib](#gawkextlib)[`gawklibpath_append` shell function](#index-gawklibpath_005fappend-shell-function): [Shell Startup Files](#Shell-Startup-Files)[`gawklibpath_default` shell function](#index-gawklibpath_005fdefault-shell-function): [Shell Startup Files](#Shell-Startup-Files)[`gawklibpath_prepend` shell function](#index-gawklibpath_005fprepend-shell-function): [Shell Startup Files](#Shell-Startup-Files)[`gawkpath_append` shell function](#index-gawkpath_005fappend-shell-function): [Shell Startup Files](#Shell-Startup-Files)[`gawkpath_default` shell function](#index-gawkpath_005fdefault-shell-function): [Shell Startup Files](#Shell-Startup-Files)[`gawkpath_prepend` shell function](#index-gawkpath_005fprepend-shell-function): [Shell Startup Files](#Shell-Startup-Files)[General Public License (GPL)](#index-General-Public-License-_0028GPL_0029): [Glossary](#Glossary)[General Public License, See GPL](#index-General-Public-License_002c-See-GPL): [Manual History](#Manual-History)[generate time values](#index-generate-time-values): [Time Functions](#Time-Functions)[`gensub()` function (`gawk`)](#index-gensub_0028_0029-function-_0028gawk_0029): [Standard Regexp Constants](#Standard-Regexp-Constants)[`gensub()` function (`gawk`)](#index-gensub_0028_0029-function-_0028gawk_0029-1): [String Functions](#String-Functions)[`gensub()` function (`gawk`), escape processing](#index-gensub_0028_0029-function-_0028gawk_0029_002c-escape-processing): [Gory Details](#Gory-Details)[`getaddrinfo()` function (C library)](#index-getaddrinfo_0028_0029-function-_0028C-library_0029): [TCP/IP Networking](#TCP_002fIP-Networking)[`getgrent()` function (C library)](#index-getgrent_0028_0029-function-_0028C-library_0029): [Group Functions](#Group-Functions)[`getgrent()` function (C library)](#index-getgrent_0028_0029-function-_0028C-library_0029-1): [Group Functions](#Group-Functions)[`getgrent()` user-defined function](#index-getgrent_0028_0029-user_002ddefined-function): [Group Functions](#Group-Functions)[`getgrent()` user-defined function](#index-getgrent_0028_0029-user_002ddefined-function-1): [Group Functions](#Group-Functions)[`getgrgid()` function (C library)](#index-getgrgid_0028_0029-function-_0028C-library_0029): [Group Functions](#Group-Functions)[`getgrgid()` user-defined function](#index-getgrgid_0028_0029-user_002ddefined-function): [Group Functions](#Group-Functions)[`getgrnam()` function (C library)](#index-getgrnam_0028_0029-function-_0028C-library_0029): [Group Functions](#Group-Functions)[`getgrnam()` user-defined function](#index-getgrnam_0028_0029-user_002ddefined-function): [Group Functions](#Group-Functions)[`getgruser()` function (C library)](#index-getgruser_0028_0029-function-_0028C-library_0029): [Group Functions](#Group-Functions)[`getgruser()` function, user-defined](#index-getgruser_0028_0029-function_002c-user_002ddefined): [Group Functions](#Group-Functions)[`getline` command](#index-getline-command): [Reading Files](#Reading-Files)[`getline` command, coprocesses, using from](#index-getline-command_002c-coprocesses_002c-using-from): [Getline/Coprocess](#Getline_002fCoprocess)[`getline` command, coprocesses, using from](#index-getline-command_002c-coprocesses_002c-using-from-1): [Close Files And Pipes](#Close-Files-And-Pipes)[`getline` command, deadlock and](#index-getline-command_002c-deadlock-and): [Two-way I/O](#Two_002dway-I_002fO)[`getline` command, explicit input with](#index-getline-command_002c-explicit-input-with): [Getline](#Getline)[`getline` command, `FILENAME` variable and](#index-getline-command_002c-FILENAME-variable-and): [Getline Notes](#Getline-Notes)[`getline` command, return values](#index-getline-command_002c-return-values): [Getline](#Getline)[`getline` command, variants](#index-getline-command_002c-variants): [Getline Summary](#Getline-Summary)[`getline` command, `_gr_init()` user-defined function](#index-getline-command_002c-_005fgr_005finit_0028_0029-user_002ddefined-function): [Group Functions](#Group-Functions)[`getline` command, `_pw_init()` function](#index-getline-command_002c-_005fpw_005finit_0028_0029-function): [Passwd Functions](#Passwd-Functions)[`getline` from a file](#index-getline-from-a-file): [Getline/File](#Getline_002fFile)[`getline` into a variable](#index-getline-into-a-variable): [Getline/Variable](#Getline_002fVariable)[`getline` statement, `BEGINFILE`/`ENDFILE` patterns and](#index-getline-statement_002c-BEGINFILE_002fENDFILE-patterns-and): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`getlocaltime()` user-defined function](#index-getlocaltime_0028_0029-user_002ddefined-function): [Getlocaltime Function](#Getlocaltime-Function)[`getopt()` function (C library)](#index-getopt_0028_0029-function-_0028C-library_0029): [Getopt Function](#Getopt-Function)[`getopt()` user-defined function](#index-getopt_0028_0029-user_002ddefined-function): [Getopt Function](#Getopt-Function)[`getopt()` user-defined function](#index-getopt_0028_0029-user_002ddefined-function-1): [Getopt Function](#Getopt-Function)[`getpwent()` function (C library)](#index-getpwent_0028_0029-function-_0028C-library_0029): [Passwd Functions](#Passwd-Functions)[`getpwent()` function (C library)](#index-getpwent_0028_0029-function-_0028C-library_0029-1): [Passwd Functions](#Passwd-Functions)[`getpwent()` user-defined function](#index-getpwent_0028_0029-user_002ddefined-function): [Passwd Functions](#Passwd-Functions)[`getpwent()` user-defined function](#index-getpwent_0028_0029-user_002ddefined-function-1): [Passwd Functions](#Passwd-Functions)[`getpwnam()` function (C library)](#index-getpwnam_0028_0029-function-_0028C-library_0029): [Passwd Functions](#Passwd-Functions)[`getpwnam()` user-defined function](#index-getpwnam_0028_0029-user_002ddefined-function): [Passwd Functions](#Passwd-Functions)[`getpwuid()` function (C library)](#index-getpwuid_0028_0029-function-_0028C-library_0029): [Passwd Functions](#Passwd-Functions)[`getpwuid()` user-defined function](#index-getpwuid_0028_0029-user_002ddefined-function): [Passwd Functions](#Passwd-Functions)[`gettext` library](#index-gettext-library): [Explaining gettext](#Explaining-gettext)[`gettext` library, locale categories](#index-gettext-library_002c-locale-categories): [Explaining gettext](#Explaining-gettext)[`gettext()` function (C library)](#index-gettext_0028_0029-function-_0028C-library_0029): [Explaining gettext](#Explaining-gettext)[`gettimeofday()` extension function](#index-gettimeofday_0028_0029-extension-function): [Extension Sample Time](#Extension-Sample-Time)[`git` utility](#index-git-utility): [gawkextlib](#gawkextlib)[`git` utility](#index-git-utility-1): [Other Versions](#Other-Versions)[`git` utility](#index-git-utility-2): [Accessing The Source](#Accessing-The-Source)[`git` utility](#index-git-utility-3): [Adding Code](#Adding-Code)[Git, use of for `gawk` source code](#index-Git_002c-use-of-for-gawk-source-code): [Derived Files](#Derived-Files)[GNITS mailing list](#index-GNITS-mailing-list): [Acknowledgments](#Acknowledgments)[GNU `awk`, See `gawk`](#index-GNU-awk_002c-See-gawk): [Preface](#Preface)[GNU Free Documentation License](#index-GNU-Free-Documentation-License): [GNU Free Documentation License](#GNU-Free-Documentation-License)[GNU General Public License](#index-GNU-General-Public-License): [Glossary](#Glossary)[GNU Lesser General Public License](#index-GNU-Lesser-General-Public-License): [Glossary](#Glossary)[GNU long options](#index-GNU-long-options): [Command Line](#Command-Line)[GNU long options](#index-GNU-long-options-1): [Options](#Options)[GNU long options, printing list of](#index-GNU-long-options_002c-printing-list-of): [Options](#Options)[GNU Project](#index-GNU-Project): [Manual History](#Manual-History)[GNU Project](#index-GNU-Project-1): [Glossary](#Glossary)[GNU/Linux](#index-GNU_002fLinux): [Manual History](#Manual-History)[GNU/Linux](#index-GNU_002fLinux-1): [I18N Example](#I18N-Example)[GNU/Linux](#index-GNU_002fLinux-2): [Glossary](#Glossary)[Gordon, Assaf](#index-Gordon_002c-Assaf): [Contributors](#Contributors)[GPL (General Public License)](#index-GPL-_0028General-Public-License_0029): [Manual History](#Manual-History)[GPL (General Public License)](#index-GPL-_0028General-Public-License_0029-1): [Glossary](#Glossary)[GPL (General Public License), printing](#index-GPL-_0028General-Public-License_0029_002c-printing): [Options](#Options)[`grcat` program](#index-grcat-program): [Group Functions](#Group-Functions)[Grigera, Juan](#index-Grigera_002c-Juan): [Contributors](#Contributors)[group database, reading](#index-group-database_002c-reading): [Group Functions](#Group-Functions)[group file](#index-group-file): [Group Functions](#Group-Functions)[group ID of `gawk` user](#index-group-ID-of-gawk-user): [Auto-set](#Auto_002dset)[groups, information about](#index-groups_002c-information-about): [Group Functions](#Group-Functions)[`gsub()` function](#index-gsub_0028_0029-function): [Standard Regexp Constants](#Standard-Regexp-Constants)[`gsub()` function](#index-gsub_0028_0029-function-1): [String Functions](#String-Functions)[`gsub()` function, arguments of](#index-gsub_0028_0029-function_002c-arguments-of): [String Functions](#String-Functions)[`gsub()` function, escape processing](#index-gsub_0028_0029-function_002c-escape-processing): [Gory Details](#Gory-Details)[Guerrero, Juan Manuel](#index-Guerrero_002c-Juan-Manuel): [Acknowledgments](#Acknowledgments)[Guerrero, Juan Manuel](#index-Guerrero_002c-Juan-Manuel-1): [Contributors](#Contributors)[Guerrero, Juan Manuel](#index-Guerrero_002c-Juan-Manuel-2): [Maintainers](#Maintainers)
---
[H](H)[`h` debugger command (alias for `help`)](#index-h-debugger-command-_0028alias-for-help_0029): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[Hankerson, Darrel](#index-Hankerson_002c-Darrel): [Acknowledgments](#Acknowledgments)[Hankerson, Darrel](#index-Hankerson_002c-Darrel-1): [Contributors](#Contributors)[Haque, John](#index-Haque_002c-John): [Contributors](#Contributors)[Hartholz, Elaine](#index-Hartholz_002c-Elaine): [Acknowledgments](#Acknowledgments)[Hartholz, Marshall](#index-Hartholz_002c-Marshall): [Acknowledgments](#Acknowledgments)[Hasegawa, Isamu](#index-Hasegawa_002c-Isamu): [Contributors](#Contributors)[`help` debugger command](#index-help-debugger-command): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[hexadecimal numbers](#index-hexadecimal-numbers): [Nondecimal-numbers](#Nondecimal_002dnumbers)[hexadecimal values, enabling interpretation of](#index-hexadecimal-values_002c-enabling-interpretation-of): [Options](#Options)[history expansion, in debugger](#index-history-expansion_002c-in-debugger): [Readline Support](#Readline-Support)[`histsort.awk` program](#index-histsort_002eawk-program): [History Sorting](#History-Sorting)[Hughes, Phil](#index-Hughes_002c-Phil): [Acknowledgments](#Acknowledgments)[`HUP` signal, for dynamic profiling](#index-HUP-signal_002c-for-dynamic-profiling): [Profiling](#Profiling)[hyphen (`-`), `-` operator](#index-hyphen-_0028_002d_0029_002c-_002d-operator): [Precedence](#Precedence)[hyphen (`-`), `-` operator](#index-hyphen-_0028_002d_0029_002c-_002d-operator-1): [Precedence](#Precedence)[hyphen (`-`), `--` operator](#index-hyphen-_0028_002d_0029_002c-_002d_002d-operator): [Increment Ops](#Increment-Ops)[hyphen (`-`), `--` operator](#index-hyphen-_0028_002d_0029_002c-_002d_002d-operator-1): [Precedence](#Precedence)[hyphen (`-`), `-=` operator](#index-hyphen-_0028_002d_0029_002c-_002d_003d-operator): [Assignment Ops](#Assignment-Ops)[hyphen (`-`), `-=` operator](#index-hyphen-_0028_002d_0029_002c-_002d_003d-operator-1): [Precedence](#Precedence)[hyphen (`-`), filenames beginning with](#index-hyphen-_0028_002d_0029_002c-filenames-beginning-with): [Options](#Options)[hyphen (`-`), in bracket expressions](#index-hyphen-_0028_002d_0029_002c-in-bracket-expressions): [Bracket Expressions](#Bracket-Expressions)
---
[I](I)[`i` debugger command (alias for `info`)](#index-i-debugger-command-_0028alias-for-info_0029): [Debugger Info](#Debugger-Info)[`id` utility](#index-id-utility): [Id Program](#Id-Program)[`id.awk` program](#index-id_002eawk-program): [Id Program](#Id-Program)[`if` statement](#index-if-statement): [If Statement](#If-Statement)[`if` statement, actions, changing](#index-if-statement_002c-actions_002c-changing): [Ranges](#Ranges)[`if` statement, use of regexps in](#index-if-statement_002c-use-of-regexps-in): [Regexp Usage](#Regexp-Usage)[`igawk.sh` program](#index-igawk_002esh-program): [Igawk Program](#Igawk-Program)[ignore breakpoint](#index-ignore-breakpoint): [Breakpoint Control](#Breakpoint-Control)[`ignore` debugger command](#index-ignore-debugger-command): [Breakpoint Control](#Breakpoint-Control)[`IGNORECASE` variable](#index-IGNORECASE-variable): [User-modified](#User_002dmodified)[`IGNORECASE` variable, and array indices](#index-IGNORECASE-variable_002c-and-array-indices): [Array Intro](#Array-Intro)[`IGNORECASE` variable, and array sorting functions](#index-IGNORECASE-variable_002c-and-array-sorting-functions): [Array Sorting Functions](#Array-Sorting-Functions)[`IGNORECASE` variable, in example programs](#index-IGNORECASE-variable_002c-in-example-programs): [Library Functions](#Library-Functions)[`IGNORECASE` variable, with `~` and `!~` operators](#index-IGNORECASE-variable_002c-with-_007e-and-_0021_007e-operators): [Case-sensitivity](#Case_002dsensitivity)[Illumos](#index-Illumos): [Other Versions](#Other-Versions)[Illumos, POSIX-compliant `awk`](#index-Illumos_002c-POSIX_002dcompliant-awk): [Other Versions](#Other-Versions)[implementation issues, `gawk`](#index-implementation-issues_002c-gawk): [Notes](#Notes)[implementation issues, `gawk`, debugging](#index-implementation-issues_002c-gawk_002c-debugging): [Compatibility Mode](#Compatibility-Mode)[implementation issues, `gawk`, limits](#index-implementation-issues_002c-gawk_002c-limits): [Getline Notes](#Getline-Notes)[implementation issues, `gawk`, limits](#index-implementation-issues_002c-gawk_002c-limits-1): [Redirection](#Redirection)[`in` operator](#index-in-operator): [Comparison Operators](#Comparison-Operators)[`in` operator](#index-in-operator-1): [Precedence](#Precedence)[`in` operator](#index-in-operator-2): [For Statement](#For-Statement)[`in` operator, index existence in multidimensional arrays](#index-in-operator_002c-index-existence-in-multidimensional-arrays): [Multidimensional](#Multidimensional)[`in` operator, order of array access](#index-in-operator_002c-order-of-array-access): [Scanning an Array](#Scanning-an-Array)[`in` operator, testing if array element exists](#index-in-operator_002c-testing-if-array-element-exists): [Reference to Elements](#Reference-to-Elements)[`in` operator, use in loops](#index-in-operator_002c-use-in-loops): [Scanning an Array](#Scanning-an-Array)[including files, `@include` directive](#index-including-files_002c-_0040include-directive): [Include Files](#Include-Files)[increment operators](#index-increment-operators): [Increment Ops](#Increment-Ops)[`index()` function](#index-index_0028_0029-function): [String Functions](#String-Functions)[indexing arrays](#index-indexing-arrays): [Array Intro](#Array-Intro)[indirect function calls](#index-indirect-function-calls): [Indirect Calls](#Indirect-Calls)[indirect function calls, `@`-notation](#index-indirect-function-calls_002c-_0040_002dnotation): [Indirect Calls](#Indirect-Calls)[infinite precision](#index-infinite-precision): [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)[`info` debugger command](#index-info-debugger-command): [Debugger Info](#Debugger-Info)[initialization, automatic](#index-initialization_002c-automatic): [More Complex](#More-Complex)[`inplace` extension](#index-inplace-extension): [Extension Sample Inplace](#Extension-Sample-Inplace)[input files](#index-input-files): [Reading Files](#Reading-Files)[input files, closing](#index-input-files_002c-closing): [Close Files And Pipes](#Close-Files-And-Pipes)[input files, counting elements in](#index-input-files_002c-counting-elements-in): [Wc Program](#Wc-Program)[input files, examples](#index-input-files_002c-examples): [Sample Data Files](#Sample-Data-Files)[input files, reading](#index-input-files_002c-reading): [Reading Files](#Reading-Files)[input files, running `awk` without](#index-input-files_002c-running-awk-without): [Read Terminal](#Read-Terminal)[input files, running `awk` without](#index-input-files_002c-running-awk-without-1): [Read Terminal](#Read-Terminal)[input files, variable assignments and](#index-input-files_002c-variable-assignments-and): [Other Arguments](#Other-Arguments)[input pipeline](#index-input-pipeline): [Getline/Pipe](#Getline_002fPipe)[input record, length of](#index-input-record_002c-length-of): [String Functions](#String-Functions)[input redirection](#index-input-redirection): [Getline/File](#Getline_002fFile)[input, data, nondecimal](#index-input_002c-data_002c-nondecimal): [Nondecimal Data](#Nondecimal-Data)[input, explicit](#index-input_002c-explicit): [Getline](#Getline)[input, files, See input files](#index-input_002c-files_002c-See-input-files): [Multiple Line](#Multiple-Line)[input, multiline records](#index-input_002c-multiline-records): [Multiple Line](#Multiple-Line)[input, splitting into records](#index-input_002c-splitting-into-records): [Records](#Records)[input, standard](#index-input_002c-standard): [Read Terminal](#Read-Terminal)[input, standard](#index-input_002c-standard-1): [Special FD](#Special-FD)[input/output functions](#index-input_002foutput-functions): [I/O Functions](#I_002fO-Functions)[input/output, binary](#index-input_002foutput_002c-binary): [User-modified](#User_002dmodified)[input/output, from `BEGIN` and `END`](#index-input_002foutput_002c-from-BEGIN-and-END): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[input/output, two-way](#index-input_002foutput_002c-two_002dway): [Two-way I/O](#Two_002dway-I_002fO)[insomnia, cure for](#index-insomnia_002c-cure-for): [Alarm Program](#Alarm-Program)[installation, VMS](#index-installation_002c-VMS): [VMS Installation](#VMS-Installation)[installing `gawk`](#index-installing-gawk): [Installation](#Installation)[instruction tracing, in debugger](#index-instruction-tracing_002c-in-debugger): [Debugger Info](#Debugger-Info)[`INT` signal (MS-Windows)](#index-INT-signal-_0028MS_002dWindows_0029): [Profiling](#Profiling)[`int()` function](#index-int_0028_0029-function): [Numeric Functions](#Numeric-Functions)[integer array indices](#index-integer-array-indices): [Numeric Array Subscripts](#Numeric-Array-Subscripts)[integers, arbitrary precision](#index-integers_002c-arbitrary-precision): [Arbitrary Precision Integers](#Arbitrary-Precision-Integers)[integers, unsigned](#index-integers_002c-unsigned): [Computer Arithmetic](#Computer-Arithmetic)[interacting with other programs](#index-interacting-with-other-programs): [I/O Functions](#I_002fO-Functions)[internationalization](#index-internationalization): [I18N Functions](#I18N-Functions)[internationalization](#index-internationalization-1): [I18N and L10N](#I18N-and-L10N)[internationalization, localization](#index-internationalization_002c-localization): [User-modified](#User_002dmodified)[internationalization, localization](#index-internationalization_002c-localization-1): [Internationalization](#Internationalization)[internationalization, localization, character classes](#index-internationalization_002c-localization_002c-character-classes): [Bracket Expressions](#Bracket-Expressions)[internationalization, localization, `gawk` and](#index-internationalization_002c-localization_002c-gawk-and): [Internationalization](#Internationalization)[internationalization, localization, locale categories](#index-internationalization_002c-localization_002c-locale-categories): [Explaining gettext](#Explaining-gettext)[internationalization, localization, marked strings](#index-internationalization_002c-localization_002c-marked-strings): [Programmer i18n](#Programmer-i18n)[internationalization, localization, portability and](#index-internationalization_002c-localization_002c-portability-and): [I18N Portability](#I18N-Portability)[internationalizing a program](#index-internationalizing-a-program): [Explaining gettext](#Explaining-gettext)[interpreted programs](#index-interpreted-programs): [Basic High Level](#Basic-High-Level)[interpreted programs](#index-interpreted-programs-1): [Glossary](#Glossary)[interval expressions, regexp operator](#index-interval-expressions_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[`inventory-shipped` file](#index-inventory_002dshipped-file): [Sample Data Files](#Sample-Data-Files)[invoke shell command](#index-invoke-shell-command): [I/O Functions](#I_002fO-Functions)[`isarray()` function (`gawk`)](#index-isarray_0028_0029-function-_0028gawk_0029): [Type Functions](#Type-Functions)[ISO](#index-ISO): [Glossary](#Glossary)[ISO 8859-1](#index-ISO-8859_002d1): [Glossary](#Glossary)[ISO Latin-1](#index-ISO-Latin_002d1): [Glossary](#Glossary)
---
[J](J)[Jacobs, Andrew](#index-Jacobs_002c-Andrew): [Passwd Functions](#Passwd-Functions)[Jaegermann, Michal](#index-Jaegermann_002c-Michal): [Acknowledgments](#Acknowledgments)[Jaegermann, Michal](#index-Jaegermann_002c-Michal-1): [Contributors](#Contributors)[Java implementation of `awk`](#index-Java-implementation-of-awk): [Other Versions](#Other-Versions)[Java programming language](#index-Java-programming-language): [Glossary](#Glossary)[`jawk`](#index-jawk): [Other Versions](#Other-Versions)[Jedi knights](#index-Jedi-knights): [Undocumented](#Undocumented)[Johansen, Chris](#index-Johansen_002c-Chris): [Signature Program](#Signature-Program)[`join()` user-defined function](#index-join_0028_0029-user_002ddefined-function): [Join Function](#Join-Function)
---
[K](K)[Kahrs, J&uuml;rgen](#index-Kahrs_002c-Jurgen): [Acknowledgments](#Acknowledgments)[Kahrs, J&uuml;rgen](#index-Kahrs_002c-Jurgen-1): [Contributors](#Contributors)[Kasal, Stepan](#index-Kasal_002c-Stepan): [Acknowledgments](#Acknowledgments)[Kenobi, Obi-Wan](#index-Kenobi_002c-Obi_002dWan): [Undocumented](#Undocumented)[Kernighan, Brian](#index-Kernighan_002c-Brian): [History](#History)[Kernighan, Brian](#index-Kernighan_002c-Brian-1): [Conventions](#Conventions)[Kernighan, Brian](#index-Kernighan_002c-Brian-2): [Acknowledgments](#Acknowledgments)[Kernighan, Brian](#index-Kernighan_002c-Brian-3): [Getline/Pipe](#Getline_002fPipe)[Kernighan, Brian](#index-Kernighan_002c-Brian-4): [Concatenation](#Concatenation)[Kernighan, Brian](#index-Kernighan_002c-Brian-5): [Library Functions](#Library-Functions)[Kernighan, Brian](#index-Kernighan_002c-Brian-6): [BTL](#BTL)[Kernighan, Brian](#index-Kernighan_002c-Brian-7): [Contributors](#Contributors)[Kernighan, Brian](#index-Kernighan_002c-Brian-8): [Other Versions](#Other-Versions)[Kernighan, Brian](#index-Kernighan_002c-Brian-9): [Basic Data Typing](#Basic-Data-Typing)[Kernighan, Brian](#index-Kernighan_002c-Brian-10): [Glossary](#Glossary)[`kill` command, dynamic profiling](#index-kill-command_002c-dynamic-profiling): [Profiling](#Profiling)[Knights, jedi](#index-Knights_002c-jedi): [Undocumented](#Undocumented)[Kwok, Conrad](#index-Kwok_002c-Conrad): [Contributors](#Contributors)
---
[L](L)[`l` debugger command (alias for `list`)](#index-l-debugger-command-_0028alias-for-list_0029): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[`labels.awk` program](#index-labels_002eawk-program): [Labels Program](#Labels-Program)[Langston, Peter](#index-Langston_002c-Peter): [Advanced Features](#Advanced-Features)[`LANGUAGE` environment variable](#index-LANGUAGE-environment-variable): [Explaining gettext](#Explaining-gettext)[languages, data-driven](#index-languages_002c-data_002ddriven): [Basic High Level](#Basic-High-Level)[`LC_ALL` locale category](#index-LC_005fALL-locale-category): [Explaining gettext](#Explaining-gettext)[`LC_COLLATE` locale category](#index-LC_005fCOLLATE-locale-category): [Explaining gettext](#Explaining-gettext)[`LC_CTYPE` locale category](#index-LC_005fCTYPE-locale-category): [Explaining gettext](#Explaining-gettext)[`LC_MESSAGES` locale category](#index-LC_005fMESSAGES-locale-category): [Explaining gettext](#Explaining-gettext)[`LC_MESSAGES` locale category, `bindtextdomain()` function (`gawk`)](#index-LC_005fMESSAGES-locale-category_002c-bindtextdomain_0028_0029-function-_0028gawk_0029): [Programmer i18n](#Programmer-i18n)[`LC_MONETARY` locale category](#index-LC_005fMONETARY-locale-category): [Explaining gettext](#Explaining-gettext)[`LC_NUMERIC` locale category](#index-LC_005fNUMERIC-locale-category): [Explaining gettext](#Explaining-gettext)[`LC_TIME` locale category](#index-LC_005fTIME-locale-category): [Explaining gettext](#Explaining-gettext)[left angle bracket (`<`), `<` operator](#index-left-angle-bracket-_0028_003c_0029_002c-_003c-operator): [Comparison Operators](#Comparison-Operators)[left angle bracket (`<`), `<` operator](#index-left-angle-bracket-_0028_003c_0029_002c-_003c-operator-1): [Precedence](#Precedence)[left angle bracket (`<`), `<` operator (I/O)](#index-left-angle-bracket-_0028_003c_0029_002c-_003c-operator-_0028I_002fO_0029): [Getline/File](#Getline_002fFile)[left angle bracket (`<`), `<=` operator](#index-left-angle-bracket-_0028_003c_0029_002c-_003c_003d-operator): [Comparison Operators](#Comparison-Operators)[left angle bracket (`<`), `<=` operator](#index-left-angle-bracket-_0028_003c_0029_002c-_003c_003d-operator-1): [Precedence](#Precedence)[left shift](#index-left-shift): [Bitwise Functions](#Bitwise-Functions)[left shift, bitwise](#index-left-shift_002c-bitwise): [Bitwise Functions](#Bitwise-Functions)[leftmost longest match](#index-leftmost-longest-match): [Multiple Line](#Multiple-Line)[length of input record](#index-length-of-input-record): [String Functions](#String-Functions)[length of string](#index-length-of-string): [String Functions](#String-Functions)[`length()` function](#index-length_0028_0029-function): [String Functions](#String-Functions)[Lesser General Public License (LGPL)](#index-Lesser-General-Public-License-_0028LGPL_0029): [Glossary](#Glossary)[LGPL (Lesser General Public License)](#index-LGPL-_0028Lesser-General-Public-License_0029): [Glossary](#Glossary)[libmawk](#index-libmawk): [Other Versions](#Other-Versions)[libraries of `awk` functions](#index-libraries-of-awk-functions): [Library Functions](#Library-Functions)[libraries of `awk` functions, assertions](#index-libraries-of-awk-functions_002c-assertions): [Assert Function](#Assert-Function)[libraries of `awk` functions, associative arrays and](#index-libraries-of-awk-functions_002c-associative-arrays-and): [Library Names](#Library-Names)[libraries of `awk` functions, character values as numbers](#index-libraries-of-awk-functions_002c-character-values-as-numbers): [Ordinal Functions](#Ordinal-Functions)[libraries of `awk` functions, command-line options](#index-libraries-of-awk-functions_002c-command_002dline-options): [Getopt Function](#Getopt-Function)[libraries of `awk` functions, example program for using](#index-libraries-of-awk-functions_002c-example-program-for-using): [Igawk Program](#Igawk-Program)[libraries of `awk` functions, group database, reading](#index-libraries-of-awk-functions_002c-group-database_002c-reading): [Group Functions](#Group-Functions)[libraries of `awk` functions, managing, data files](#index-libraries-of-awk-functions_002c-managing_002c-data-files): [Data File Management](#Data-File-Management)[libraries of `awk` functions, managing, time](#index-libraries-of-awk-functions_002c-managing_002c-time): [Getlocaltime Function](#Getlocaltime-Function)[libraries of `awk` functions, merging arrays into strings](#index-libraries-of-awk-functions_002c-merging-arrays-into-strings): [Join Function](#Join-Function)[libraries of `awk` functions, rounding numbers](#index-libraries-of-awk-functions_002c-rounding-numbers): [Round Function](#Round-Function)[libraries of `awk` functions, user database, reading](#index-libraries-of-awk-functions_002c-user-database_002c-reading): [Passwd Functions](#Passwd-Functions)[line breaks](#index-line-breaks): [Statements/Lines](#Statements_002fLines)[line continuations](#index-line-continuations): [Boolean Ops](#Boolean-Ops)[line continuations, `gawk`](#index-line-continuations_002c-gawk): [Conditional Exp](#Conditional-Exp)[line continuations, in `print` statement](#index-line-continuations_002c-in-print-statement): [Print Examples](#Print-Examples)[line continuations, with C shell](#index-line-continuations_002c-with-C-shell): [More Complex](#More-Complex)[lines, blank, printing](#index-lines_002c-blank_002c-printing): [Print](#Print)[lines, counting](#index-lines_002c-counting): [Wc Program](#Wc-Program)[lines, duplicate, removing](#index-lines_002c-duplicate_002c-removing): [History Sorting](#History-Sorting)[lines, matching ranges of](#index-lines_002c-matching-ranges-of): [Ranges](#Ranges)[lines, skipping between markers](#index-lines_002c-skipping-between-markers): [Ranges](#Ranges)[lint checking](#index-lint-checking): [User-modified](#User_002dmodified)[lint checking, array elements](#index-lint-checking_002c-array-elements): [Delete](#Delete)[lint checking, array subscripts](#index-lint-checking_002c-array-subscripts): [Uninitialized Subscripts](#Uninitialized-Subscripts)[lint checking, empty programs](#index-lint-checking_002c-empty-programs): [Command Line](#Command-Line)[lint checking, issuing warnings](#index-lint-checking_002c-issuing-warnings): [Options](#Options)[lint checking, `POSIXLY_CORRECT` environment variable](#index-lint-checking_002c-POSIXLY_005fCORRECT-environment-variable): [Options](#Options)[lint checking, undefined functions](#index-lint-checking_002c-undefined-functions): [Pass By Value/Reference](#Pass-By-Value_002fReference)[`LINT` variable](#index-LINT-variable): [User-modified](#User_002dmodified)[Linux](#index-Linux): [Manual History](#Manual-History)[Linux](#index-Linux-1): [I18N Example](#I18N-Example)[Linux](#index-Linux-2): [Glossary](#Glossary)[list all global variables, in debugger](#index-list-all-global-variables_002c-in-debugger): [Debugger Info](#Debugger-Info)[`list` debugger command](#index-list-debugger-command): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[list function definitions, in debugger](#index-list-function-definitions_002c-in-debugger): [Debugger Info](#Debugger-Info)[loading extensions, `@load` directive](#index-loading-extensions_002c-_0040load-directive): [Loading Shared Libraries](#Loading-Shared-Libraries)[loading, extensions](#index-loading_002c-extensions): [Options](#Options)[local variables, in a function](#index-local-variables_002c-in-a-function): [Variable Scope](#Variable-Scope)[locale categories](#index-locale-categories): [Explaining gettext](#Explaining-gettext)[locale decimal point character](#index-locale-decimal-point-character): [Options](#Options)[locale, definition of](#index-locale_002c-definition-of): [Locales](#Locales)[localization](#index-localization): [I18N and L10N](#I18N-and-L10N)[localization, See internationalization, localization](#index-localization_002c-See-internationalization_002c-localization): [I18N and L10N](#I18N-and-L10N)[log files, timestamps in](#index-log-files_002c-timestamps-in): [Time Functions](#Time-Functions)[`log()` function](#index-log_0028_0029-function): [Numeric Functions](#Numeric-Functions)[logarithm](#index-logarithm): [Numeric Functions](#Numeric-Functions)[logical false/true](#index-logical-false_002ftrue): [Truth Values](#Truth-Values)[logical operators, See Boolean expressions](#index-logical-operators_002c-See-Boolean-expressions): [Boolean Ops](#Boolean-Ops)[login information](#index-login-information): [Passwd Functions](#Passwd-Functions)[long options](#index-long-options): [Command Line](#Command-Line)[loops](#index-loops): [While Statement](#While-Statement)[loops, `break` statement and](#index-loops_002c-break-statement-and): [Break Statement](#Break-Statement)[loops, `continue` statements and](#index-loops_002c-continue-statements-and): [For Statement](#For-Statement)[loops, count for header, in a profile](#index-loops_002c-count-for-header_002c-in-a-profile): [Profiling](#Profiling)[loops, `do`-`while`](#index-loops_002c-do_002dwhile): [Do Statement](#Do-Statement)[loops, exiting](#index-loops_002c-exiting): [Break Statement](#Break-Statement)[loops, `for`, array scanning](#index-loops_002c-for_002c-array-scanning): [Scanning an Array](#Scanning-an-Array)[loops, `for`, iterative](#index-loops_002c-for_002c-iterative): [For Statement](#For-Statement)[loops, See Also `while` statement](#index-loops_002c-See-Also-while-statement): [While Statement](#While-Statement)[loops, `while`](#index-loops_002c-while): [While Statement](#While-Statement)[`ls` utility](#index-ls-utility): [More Complex](#More-Complex)[`lshift()` function (`gawk`)](#index-lshift_0028_0029-function-_0028gawk_0029): [Bitwise Functions](#Bitwise-Functions)[lvalues/rvalues](#index-lvalues_002frvalues): [Assignment Ops](#Assignment-Ops)
---
[M](M)[`mail-list` file](#index-mail_002dlist-file): [Sample Data Files](#Sample-Data-Files)[mailing labels, printing](#index-mailing-labels_002c-printing): [Labels Program](#Labels-Program)[mailing list, GNITS](#index-mailing-list_002c-GNITS): [Acknowledgments](#Acknowledgments)[Malmberg, John](#index-Malmberg_002c-John): [Acknowledgments](#Acknowledgments)[Malmberg, John](#index-Malmberg_002c-John-1): [Maintainers](#Maintainers)[Malmberg, John E.](#index-Malmberg_002c-John-E_002e): [Contributors](#Contributors)[mark parity](#index-mark-parity): [Ordinal Functions](#Ordinal-Functions)[marked string extraction (internationalization)](#index-marked-string-extraction-_0028internationalization_0029): [String Extraction](#String-Extraction)[marked strings, extracting](#index-marked-strings_002c-extracting): [String Extraction](#String-Extraction)[Marx, Groucho](#index-Marx_002c-Groucho): [Increment Ops](#Increment-Ops)[match regexp in string](#index-match-regexp-in-string): [String Functions](#String-Functions)[`match()` function](#index-match_0028_0029-function): [String Functions](#String-Functions)[`match()` function, `RSTART`/`RLENGTH` variables](#index-match_0028_0029-function_002c-RSTART_002fRLENGTH-variables): [String Functions](#String-Functions)[matching, expressions, See comparison expressions](#index-matching_002c-expressions_002c-See-comparison-expressions): [Typing and Comparison](#Typing-and-Comparison)[matching, leftmost longest](#index-matching_002c-leftmost-longest): [Multiple Line](#Multiple-Line)[matching, null strings](#index-matching_002c-null-strings): [String Functions](#String-Functions)[`mawk` utility](#index-mawk-utility): [Escape Sequences](#Escape-Sequences)[`mawk` utility](#index-mawk-utility-1): [Getline/Pipe](#Getline_002fPipe)[`mawk` utility](#index-mawk-utility-2): [Concatenation](#Concatenation)[`mawk` utility](#index-mawk-utility-3): [Nextfile Statement](#Nextfile-Statement)[`mawk` utility](#index-mawk-utility-4): [Other Versions](#Other-Versions)[maximum precision supported by MPFR library](#index-maximum-precision-supported-by-MPFR-library): [Auto-set](#Auto_002dset)[McIlroy, Doug](#index-McIlroy_002c-Doug): [Glossary](#Glossary)[McPhee, Patrick](#index-McPhee_002c-Patrick): [Contributors](#Contributors)[message object files](#index-message-object-files): [Explaining gettext](#Explaining-gettext)[message object files, converting from portable object files](#index-message-object-files_002c-converting-from-portable-object-files): [I18N Example](#I18N-Example)[message object files, specifying directory of](#index-message-object-files_002c-specifying-directory-of): [Explaining gettext](#Explaining-gettext)[message object files, specifying directory of](#index-message-object-files_002c-specifying-directory-of-1): [Programmer i18n](#Programmer-i18n)[messages from extensions](#index-messages-from-extensions): [Printing Messages](#Printing-Messages)[metacharacters in regular expressions](#index-metacharacters-in-regular-expressions): [Regexp Operators](#Regexp-Operators)[metacharacters, escape sequences for](#index-metacharacters_002c-escape-sequences-for): [Escape Sequences](#Escape-Sequences)[minimum precision required by MPFR library](#index-minimum-precision-required-by-MPFR-library): [Auto-set](#Auto_002dset)[`mktime()` function (`gawk`)](#index-mktime_0028_0029-function-_0028gawk_0029): [Time Functions](#Time-Functions)[modifiers, in format specifiers](#index-modifiers_002c-in-format-specifiers): [Format Modifiers](#Format-Modifiers)[monetary information, localization](#index-monetary-information_002c-localization): [Explaining gettext](#Explaining-gettext)[Moore, Duncan](#index-Moore_002c-Duncan): [Getline Notes](#Getline-Notes)[MPFR, checking availability of](#index-MPFR_002c-checking-availability-of): [Checking for MPFR](#Checking-for-MPFR)[`msgfmt` utility](#index-msgfmt-utility): [I18N Example](#I18N-Example)[multiple precision](#index-multiple-precision): [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic)[multiple-line records](#index-multiple_002dline-records): [Multiple Line](#Multiple-Line)
---
[N](N)[`n` debugger command (alias for `next`)](#index-n-debugger-command-_0028alias-for-next_0029): [Debugger Execution Control](#Debugger-Execution-Control)[names, arrays/variables](#index-names_002c-arrays_002fvariables): [Library Names](#Library-Names)[names, functions](#index-names_002c-functions): [Definition Syntax](#Definition-Syntax)[names, functions](#index-names_002c-functions-1): [Library Names](#Library-Names)[namespace issues](#index-namespace-issues): [Library Names](#Library-Names)[namespace issues, functions](#index-namespace-issues_002c-functions): [Definition Syntax](#Definition-Syntax)[NetBSD](#index-NetBSD): [Glossary](#Glossary)[networks, programming](#index-networks_002c-programming): [TCP/IP Networking](#TCP_002fIP-Networking)[networks, support for](#index-networks_002c-support-for): [Special Network](#Special-Network)[newlines](#index-newlines): [Statements/Lines](#Statements_002fLines)[newlines](#index-newlines-1): [Options](#Options)[newlines](#index-newlines-2): [Boolean Ops](#Boolean-Ops)[newlines, as record separators](#index-newlines_002c-as-record-separators): [awk split records](#awk-split-records)[newlines, in dynamic regexps](#index-newlines_002c-in-dynamic-regexps): [Computed Regexps](#Computed-Regexps)[newlines, in regexp constants](#index-newlines_002c-in-regexp-constants): [Computed Regexps](#Computed-Regexps)[newlines, printing](#index-newlines_002c-printing): [Print Examples](#Print-Examples)[newlines, separating statements in actions](#index-newlines_002c-separating-statements-in-actions): [Action Overview](#Action-Overview)[newlines, separating statements in actions](#index-newlines_002c-separating-statements-in-actions-1): [Statements](#Statements)[`next` debugger command](#index-next-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`next file` statement](#index-next-file-statement): [Feature History](#Feature-History)[`next` statement](#index-next-statement): [Boolean Ops](#Boolean-Ops)[`next` statement](#index-next-statement-1): [Next Statement](#Next-Statement)[`next` statement, `BEGIN`/`END` patterns and](#index-next-statement_002c-BEGIN_002fEND-patterns-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`next` statement, `BEGINFILE`/`ENDFILE` patterns and](#index-next-statement_002c-BEGINFILE_002fENDFILE-patterns-and): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`next` statement, user-defined functions and](#index-next-statement_002c-user_002ddefined-functions-and): [Next Statement](#Next-Statement)[`nextfile` statement](#index-nextfile-statement): [Nextfile Statement](#Nextfile-Statement)[`nextfile` statement, `BEGIN`/`END` patterns and](#index-nextfile-statement_002c-BEGIN_002fEND-patterns-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`nextfile` statement, `BEGINFILE`/`ENDFILE` patterns and](#index-nextfile-statement_002c-BEGINFILE_002fENDFILE-patterns-and): [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)[`nextfile` statement, user-defined functions and](#index-nextfile-statement_002c-user_002ddefined-functions-and): [Nextfile Statement](#Nextfile-Statement)[`nexti` debugger command](#index-nexti-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`NF` variable](#index-NF-variable): [Fields](#Fields)[`NF` variable](#index-NF-variable-1): [Auto-set](#Auto_002dset)[`NF` variable, decrementing](#index-NF-variable_002c-decrementing): [Changing Fields](#Changing-Fields)[`ni` debugger command (alias for `nexti`)](#index-ni-debugger-command-_0028alias-for-nexti_0029): [Debugger Execution Control](#Debugger-Execution-Control)[`noassign.awk` program](#index-noassign_002eawk-program): [Ignoring Assigns](#Ignoring-Assigns)[non-existent array elements](#index-non_002dexistent-array-elements): [Reference to Elements](#Reference-to-Elements)[not Boolean-logic operator](#index-not-Boolean_002dlogic-operator): [Boolean Ops](#Boolean-Ops)[`NR` variable](#index-NR-variable): [Records](#Records)[`NR` variable](#index-NR-variable-1): [Auto-set](#Auto_002dset)[`NR` variable, changing](#index-NR-variable_002c-changing): [Auto-set](#Auto_002dset)[null strings](#index-null-strings): [awk split records](#awk-split-records)[null strings](#index-null-strings-1): [Regexp Field Splitting](#Regexp-Field-Splitting)[null strings](#index-null-strings-2): [Truth Values](#Truth-Values)[null strings](#index-null-strings-3): [Basic Data Typing](#Basic-Data-Typing)[null strings in `gawk` arguments, quoting and](#index-null-strings-in-gawk-arguments_002c-quoting-and): [Quoting](#Quoting)[null strings, and deleting array elements](#index-null-strings_002c-and-deleting-array-elements): [Delete](#Delete)[null strings, as array subscripts](#index-null-strings_002c-as-array-subscripts): [Uninitialized Subscripts](#Uninitialized-Subscripts)[null strings, converting numbers to strings](#index-null-strings_002c-converting-numbers-to-strings): [Strings And Numbers](#Strings-And-Numbers)[null strings, matching](#index-null-strings_002c-matching): [String Functions](#String-Functions)[number as string of bits](#index-number-as-string-of-bits): [Bitwise Functions](#Bitwise-Functions)[number of array elements](#index-number-of-array-elements): [String Functions](#String-Functions)[number sign (`#`), `#!` (executable scripts)](#index-number-sign-_0028_0023_0029_002c-_0023_0021-_0028executable-scripts_0029): [Executable Scripts](#Executable-Scripts)[number sign (`#`), commenting](#index-number-sign-_0028_0023_0029_002c-commenting): [Comments](#Comments)[numbers, as array subscripts](#index-numbers_002c-as-array-subscripts): [Numeric Array Subscripts](#Numeric-Array-Subscripts)[numbers, as values of characters](#index-numbers_002c-as-values-of-characters): [Ordinal Functions](#Ordinal-Functions)[numbers, Cliff random](#index-numbers_002c-Cliff-random): [Cliff Random Function](#Cliff-Random-Function)[numbers, converting](#index-numbers_002c-converting): [Strings And Numbers](#Strings-And-Numbers)[numbers, converting](#index-numbers_002c-converting-1): [Bitwise Functions](#Bitwise-Functions)[numbers, converting, to strings](#index-numbers_002c-converting_002c-to-strings): [User-modified](#User_002dmodified)[numbers, converting, to strings](#index-numbers_002c-converting_002c-to-strings-1): [User-modified](#User_002dmodified)[numbers, hexadecimal](#index-numbers_002c-hexadecimal): [Nondecimal-numbers](#Nondecimal_002dnumbers)[numbers, octal](#index-numbers_002c-octal): [Nondecimal-numbers](#Nondecimal_002dnumbers)[numbers, rounding](#index-numbers_002c-rounding): [Round Function](#Round-Function)[numeric constants](#index-numeric-constants): [Scalar Constants](#Scalar-Constants)[numeric functions](#index-numeric-functions): [Numeric Functions](#Numeric-Functions)[numeric, output format](#index-numeric_002c-output-format): [OFMT](#OFMT)[numeric, strings](#index-numeric_002c-strings): [Variable Typing](#Variable-Typing)
---
[O](O)[`o` debugger command (alias for `option`)](#index-o-debugger-command-_0028alias-for-option_0029): [Debugger Info](#Debugger-Info)[obsolete features](#index-obsolete-features): [Obsolete](#Obsolete)[octal numbers](#index-octal-numbers): [Nondecimal-numbers](#Nondecimal_002dnumbers)[octal values, enabling interpretation of](#index-octal-values_002c-enabling-interpretation-of): [Options](#Options)[`OFMT` variable](#index-OFMT-variable): [OFMT](#OFMT)[`OFMT` variable](#index-OFMT-variable-1): [Strings And Numbers](#Strings-And-Numbers)[`OFMT` variable](#index-OFMT-variable-2): [User-modified](#User_002dmodified)[`OFMT` variable, POSIX `awk` and](#index-OFMT-variable_002c-POSIX-awk-and): [OFMT](#OFMT)[`OFS` variable](#index-OFS-variable): [Changing Fields](#Changing-Fields)[`OFS` variable](#index-OFS-variable-1): [Output Separators](#Output-Separators)[`OFS` variable](#index-OFS-variable-2): [User-modified](#User_002dmodified)[OpenBSD](#index-OpenBSD): [Glossary](#Glossary)[OpenSolaris](#index-OpenSolaris): [Other Versions](#Other-Versions)[operating systems, BSD-based](#index-operating-systems_002c-BSD_002dbased): [Manual History](#Manual-History)[operating systems, PC, `gawk` on](#index-operating-systems_002c-PC_002c-gawk-on): [PC Using](#PC-Using)[operating systems, PC, `gawk` on, installing](#index-operating-systems_002c-PC_002c-gawk-on_002c-installing): [PC Installation](#PC-Installation)[operating systems, porting `gawk` to](#index-operating-systems_002c-porting-gawk-to): [New Ports](#New-Ports)[operating systems, See Also GNU/Linux, PC operating systems, Unix](#index-operating-systems_002c-See-Also-GNU_002fLinux_002c-PC-operating-systems_002c-Unix): [Installation](#Installation)[operations, bitwise](#index-operations_002c-bitwise): [Bitwise Functions](#Bitwise-Functions)[operators, arithmetic](#index-operators_002c-arithmetic): [Arithmetic Ops](#Arithmetic-Ops)[operators, assignment](#index-operators_002c-assignment): [Assignment Ops](#Assignment-Ops)[operators, assignment](#index-operators_002c-assignment-1): [Assignment Ops](#Assignment-Ops)[operators, assignment, evaluation order](#index-operators_002c-assignment_002c-evaluation-order): [Assignment Ops](#Assignment-Ops)[operators, Boolean, See Boolean expressions](#index-operators_002c-Boolean_002c-See-Boolean-expressions): [Boolean Ops](#Boolean-Ops)[operators, decrement/increment](#index-operators_002c-decrement_002fincrement): [Increment Ops](#Increment-Ops)[operators, GNU-specific](#index-operators_002c-GNU_002dspecific): [GNU Regexp Operators](#GNU-Regexp-Operators)[operators, input/output](#index-operators_002c-input_002foutput): [Getline/File](#Getline_002fFile)[operators, input/output](#index-operators_002c-input_002foutput-1): [Getline/Pipe](#Getline_002fPipe)[operators, input/output](#index-operators_002c-input_002foutput-2): [Getline/Coprocess](#Getline_002fCoprocess)[operators, input/output](#index-operators_002c-input_002foutput-3): [Redirection](#Redirection)[operators, input/output](#index-operators_002c-input_002foutput-4): [Redirection](#Redirection)[operators, input/output](#index-operators_002c-input_002foutput-5): [Precedence](#Precedence)[operators, input/output](#index-operators_002c-input_002foutput-6): [Precedence](#Precedence)[operators, input/output](#index-operators_002c-input_002foutput-7): [Precedence](#Precedence)[operators, logical, See Boolean expressions](#index-operators_002c-logical_002c-See-Boolean-expressions): [Boolean Ops](#Boolean-Ops)[operators, precedence](#index-operators_002c-precedence): [Increment Ops](#Increment-Ops)[operators, precedence](#index-operators_002c-precedence-1): [Precedence](#Precedence)[operators, relational, See operators, comparison](#index-operators_002c-relational_002c-See-operators_002c-comparison): [Typing and Comparison](#Typing-and-Comparison)[operators, short-circuit](#index-operators_002c-short_002dcircuit): [Boolean Ops](#Boolean-Ops)[operators, string](#index-operators_002c-string): [Concatenation](#Concatenation)[operators, string-matching](#index-operators_002c-string_002dmatching): [Regexp Usage](#Regexp-Usage)[operators, string-matching, for buffers](#index-operators_002c-string_002dmatching_002c-for-buffers): [GNU Regexp Operators](#GNU-Regexp-Operators)[operators, word-boundary (`gawk`)](#index-operators_002c-word_002dboundary-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`option` debugger command](#index-option-debugger-command): [Debugger Info](#Debugger-Info)[options, command-line](#index-options_002c-command_002dline): [Options](#Options)[options, command-line, end of](#index-options_002c-command_002dline_002c-end-of): [Options](#Options)[options, command-line, invoking `awk`](#index-options_002c-command_002dline_002c-invoking-awk): [Command Line](#Command-Line)[options, command-line, processing](#index-options_002c-command_002dline_002c-processing): [Getopt Function](#Getopt-Function)[options, deprecated](#index-options_002c-deprecated): [Obsolete](#Obsolete)[options, long](#index-options_002c-long): [Command Line](#Command-Line)[options, long](#index-options_002c-long-1): [Options](#Options)[options, printing list of](#index-options_002c-printing-list-of): [Options](#Options)[OR bitwise operation](#index-OR-bitwise-operation): [Bitwise Functions](#Bitwise-Functions)[or Boolean-logic operator](#index-or-Boolean_002dlogic-operator): [Boolean Ops](#Boolean-Ops)[`or()` function (`gawk`)](#index-or_0028_0029-function-_0028gawk_0029): [Bitwise Functions](#Bitwise-Functions)[`ord()` extension function](#index-ord_0028_0029-extension-function): [Extension Sample Ord](#Extension-Sample-Ord)[`ord()` user-defined function](#index-ord_0028_0029-user_002ddefined-function): [Ordinal Functions](#Ordinal-Functions)[order of evaluation, concatenation](#index-order-of-evaluation_002c-concatenation): [Concatenation](#Concatenation)[`ORS` variable](#index-ORS-variable): [Output Separators](#Output-Separators)[`ORS` variable](#index-ORS-variable-1): [User-modified](#User_002dmodified)[output field separator, See `OFS` variable](#index-output-field-separator_002c-See-OFS-variable): [Changing Fields](#Changing-Fields)[output record separator, See `ORS` variable](#index-output-record-separator_002c-See-ORS-variable): [Output Separators](#Output-Separators)[output redirection](#index-output-redirection): [Redirection](#Redirection)[output wrapper](#index-output-wrapper): [Output Wrappers](#Output-Wrappers)[output, buffering](#index-output_002c-buffering): [I/O Functions](#I_002fO-Functions)[output, buffering](#index-output_002c-buffering-1): [I/O Functions](#I_002fO-Functions)[output, duplicating into files](#index-output_002c-duplicating-into-files): [Tee Program](#Tee-Program)[output, files, closing](#index-output_002c-files_002c-closing): [Close Files And Pipes](#Close-Files-And-Pipes)[output, format specifier, `OFMT`](#index-output_002c-format-specifier_002c-OFMT): [OFMT](#OFMT)[output, formatted](#index-output_002c-formatted): [Printf](#Printf)[output, pipes](#index-output_002c-pipes): [Redirection](#Redirection)[output, printing, See printing](#index-output_002c-printing_002c-See-printing): [Printing](#Printing)[output, records](#index-output_002c-records): [Output Separators](#Output-Separators)[output, standard](#index-output_002c-standard): [Special FD](#Special-FD)
---
[P](P)[`p` debugger command (alias for `print`)](#index-p-debugger-command-_0028alias-for-print_0029): [Viewing And Changing Data](#Viewing-And-Changing-Data)[Papadopoulos, Panos](#index-Papadopoulos_002c-Panos): [Contributors](#Contributors)[parent process ID of `gawk` process](#index-parent-process-ID-of-gawk-process): [Auto-set](#Auto_002dset)[parentheses `()`, in a profile](#index-parentheses-_0028_0029_002c-in-a-profile): [Profiling](#Profiling)[parentheses `()`, regexp operator](#index-parentheses-_0028_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[password file](#index-password-file): [Passwd Functions](#Passwd-Functions)[`patsplit()` function (`gawk`)](#index-patsplit_0028_0029-function-_0028gawk_0029): [String Functions](#String-Functions)[patterns](#index-patterns): [Patterns and Actions](#Patterns-and-Actions)[patterns, comparison expressions as](#index-patterns_002c-comparison-expressions-as): [Expression Patterns](#Expression-Patterns)[patterns, counts, in a profile](#index-patterns_002c-counts_002c-in-a-profile): [Profiling](#Profiling)[patterns, default](#index-patterns_002c-default): [Very Simple](#Very-Simple)[patterns, empty](#index-patterns_002c-empty): [Empty](#Empty)[patterns, expressions as](#index-patterns_002c-expressions-as): [Regexp Patterns](#Regexp-Patterns)[patterns, ranges in](#index-patterns_002c-ranges-in): [Ranges](#Ranges)[patterns, regexp constants as](#index-patterns_002c-regexp-constants-as): [Expression Patterns](#Expression-Patterns)[patterns, types of](#index-patterns_002c-types-of): [Pattern Overview](#Pattern-Overview)[`pawk` (profiling version of Brian Kernighan&rsquo;s `awk`)](#index-pawk-_0028profiling-version-of-Brian-Kernighan_0027s-awk_0029): [Other Versions](#Other-Versions)[`pawk`, `awk`-like facilities for Python](#index-pawk_002c-awk_002dlike-facilities-for-Python): [Other Versions](#Other-Versions)[PC operating systems, `gawk` on](#index-PC-operating-systems_002c-gawk-on): [PC Using](#PC-Using)[PC operating systems, `gawk` on, installing](#index-PC-operating-systems_002c-gawk-on_002c-installing): [PC Installation](#PC-Installation)[percent sign (`%`), `%` operator](#index-percent-sign-_0028_0025_0029_002c-_0025-operator): [Precedence](#Precedence)[percent sign (`%`), `%=` operator](#index-percent-sign-_0028_0025_0029_002c-_0025_003d-operator): [Assignment Ops](#Assignment-Ops)[percent sign (`%`), `%=` operator](#index-percent-sign-_0028_0025_0029_002c-_0025_003d-operator-1): [Precedence](#Precedence)[period (`.`), regexp operator](#index-period-_0028_002e_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[Perl](#index-Perl): [Future Extensions](#Future-Extensions)[Peters, Arno](#index-Peters_002c-Arno): [Contributors](#Contributors)[Peterson, Hal](#index-Peterson_002c-Hal): [Contributors](#Contributors)[pipe, closing](#index-pipe_002c-closing): [Close Files And Pipes](#Close-Files-And-Pipes)[pipe, input](#index-pipe_002c-input): [Getline/Pipe](#Getline_002fPipe)[pipe, output](#index-pipe_002c-output): [Redirection](#Redirection)[Pitts, Dave](#index-Pitts_002c-Dave): [Acknowledgments](#Acknowledgments)[Pitts, Dave](#index-Pitts_002c-Dave-1): [Maintainers](#Maintainers)[Plauger, P.J.](#index-Plauger_002c-P_002eJ_002e): [Library Functions](#Library-Functions)[plug-in](#index-plug_002din): [Extension Intro](#Extension-Intro)[plus sign (`+`), `+` operator](#index-plus-sign-_0028_002b_0029_002c-_002b-operator): [Precedence](#Precedence)[plus sign (`+`), `+` operator](#index-plus-sign-_0028_002b_0029_002c-_002b-operator-1): [Precedence](#Precedence)[plus sign (`+`), `++` operator](#index-plus-sign-_0028_002b_0029_002c-_002b_002b-operator): [Increment Ops](#Increment-Ops)[plus sign (`+`), `++` operator](#index-plus-sign-_0028_002b_0029_002c-_002b_002b-operator-1): [Increment Ops](#Increment-Ops)[plus sign (`+`), `++` operator](#index-plus-sign-_0028_002b_0029_002c-_002b_002b-operator-2): [Precedence](#Precedence)[plus sign (`+`), `+=` operator](#index-plus-sign-_0028_002b_0029_002c-_002b_003d-operator): [Assignment Ops](#Assignment-Ops)[plus sign (`+`), `+=` operator](#index-plus-sign-_0028_002b_0029_002c-_002b_003d-operator-1): [Precedence](#Precedence)[plus sign (`+`), regexp operator](#index-plus-sign-_0028_002b_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[pointers to functions](#index-pointers-to-functions): [Indirect Calls](#Indirect-Calls)[portability](#index-portability): [Escape Sequences](#Escape-Sequences)[portability, `#!` (executable scripts)](#index-portability_002c-_0023_0021-_0028executable-scripts_0029): [Executable Scripts](#Executable-Scripts)[portability, `**` operator and](#index-portability_002c-_002a_002a-operator-and): [Arithmetic Ops](#Arithmetic-Ops)[portability, `**=` operator and](#index-portability_002c-_002a_002a_003d-operator-and): [Assignment Ops](#Assignment-Ops)[portability, `ARGV` variable](#index-portability_002c-ARGV-variable): [Executable Scripts](#Executable-Scripts)[portability, backslash continuation and](#index-portability_002c-backslash-continuation-and): [Statements/Lines](#Statements_002fLines)[portability, backslash in escape sequences](#index-portability_002c-backslash-in-escape-sequences): [Escape Sequences](#Escape-Sequences)[portability, `close()` function and](#index-portability_002c-close_0028_0029-function-and): [Close Files And Pipes](#Close-Files-And-Pipes)[portability, data files as single record](#index-portability_002c-data-files-as-single-record): [gawk split records](#gawk-split-records)[portability, deleting array elements](#index-portability_002c-deleting-array-elements): [Delete](#Delete)[portability, example programs](#index-portability_002c-example-programs): [Library Functions](#Library-Functions)[portability, functions, defining](#index-portability_002c-functions_002c-defining): [Definition Syntax](#Definition-Syntax)[portability, `gawk`](#index-portability_002c-gawk): [New Ports](#New-Ports)[portability, `gettext` library and](#index-portability_002c-gettext-library-and): [Explaining gettext](#Explaining-gettext)[portability, internationalization and](#index-portability_002c-internationalization-and): [I18N Portability](#I18N-Portability)[portability, `length()` function](#index-portability_002c-length_0028_0029-function): [String Functions](#String-Functions)[portability, new `awk` vs. old `awk`](#index-portability_002c-new-awk-vs_002e-old-awk): [Strings And Numbers](#Strings-And-Numbers)[portability, `next` statement in user-defined functions](#index-portability_002c-next-statement-in-user_002ddefined-functions): [Pass By Value/Reference](#Pass-By-Value_002fReference)[portability, `NF` variable, decrementing](#index-portability_002c-NF-variable_002c-decrementing): [Changing Fields](#Changing-Fields)[portability, operators](#index-portability_002c-operators): [Increment Ops](#Increment-Ops)[portability, operators, not in POSIX `awk`](#index-portability_002c-operators_002c-not-in-POSIX-awk): [Precedence](#Precedence)[portability, `POSIXLY_CORRECT` environment variable](#index-portability_002c-POSIXLY_005fCORRECT-environment-variable): [Options](#Options)[portability, `substr()` function](#index-portability_002c-substr_0028_0029-function): [String Functions](#String-Functions)[portable object files](#index-portable-object-files): [Explaining gettext](#Explaining-gettext)[portable object files](#index-portable-object-files-1): [Translator i18n](#Translator-i18n)[portable object files, converting to message object files](#index-portable-object-files_002c-converting-to-message-object-files): [I18N Example](#I18N-Example)[portable object files, generating](#index-portable-object-files_002c-generating): [Options](#Options)[portable object template files](#index-portable-object-template-files): [Explaining gettext](#Explaining-gettext)[porting `gawk`](#index-porting-gawk): [New Ports](#New-Ports)[positional specifiers, `printf` statement](#index-positional-specifiers_002c-printf-statement): [Format Modifiers](#Format-Modifiers)[positional specifiers, `printf` statement](#index-positional-specifiers_002c-printf-statement-1): [Printf Ordering](#Printf-Ordering)[positional specifiers, `printf` statement, mixing with regular formats](#index-positional-specifiers_002c-printf-statement_002c-mixing-with-regular-formats): [Printf Ordering](#Printf-Ordering)[POSIX `awk`](#index-POSIX-awk): [This Manual](#This-Manual)[POSIX `awk`](#index-POSIX-awk-1): [Assignment Ops](#Assignment-Ops)[POSIX `awk`, `**` operator and](#index-POSIX-awk_002c-_002a_002a-operator-and): [Precedence](#Precedence)[POSIX `awk`, `**=` operator and](#index-POSIX-awk_002c-_002a_002a_003d-operator-and): [Assignment Ops](#Assignment-Ops)[POSIX `awk`, `<` operator and](#index-POSIX-awk_002c-_003c-operator-and): [Getline/File](#Getline_002fFile)[POSIX `awk`, arithmetic operators and](#index-POSIX-awk_002c-arithmetic-operators-and): [Arithmetic Ops](#Arithmetic-Ops)[POSIX `awk`, backslashes in string constants](#index-POSIX-awk_002c-backslashes-in-string-constants): [Escape Sequences](#Escape-Sequences)[POSIX `awk`, `BEGIN`/`END` patterns](#index-POSIX-awk_002c-BEGIN_002fEND-patterns): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[POSIX `awk`, bracket expressions and](#index-POSIX-awk_002c-bracket-expressions-and): [Bracket Expressions](#Bracket-Expressions)[POSIX `awk`, bracket expressions and, character classes](#index-POSIX-awk_002c-bracket-expressions-and_002c-character-classes): [Bracket Expressions](#Bracket-Expressions)[POSIX `awk`, bracket expressions and, character classes](#index-POSIX-awk_002c-bracket-expressions-and_002c-character-classes-1): [Bracket Expressions](#Bracket-Expressions)[POSIX `awk`, `break` statement and](#index-POSIX-awk_002c-break-statement-and): [Break Statement](#Break-Statement)[POSIX `awk`, changes in `awk` versions](#index-POSIX-awk_002c-changes-in-awk-versions): [POSIX](#POSIX)[POSIX `awk`, `continue` statement and](#index-POSIX-awk_002c-continue-statement-and): [Continue Statement](#Continue-Statement)[POSIX `awk`, `CONVFMT` variable and](#index-POSIX-awk_002c-CONVFMT-variable-and): [User-modified](#User_002dmodified)[POSIX `awk`, `date` utility and](#index-POSIX-awk_002c-date-utility-and): [Time Functions](#Time-Functions)[POSIX `awk`, field separators and](#index-POSIX-awk_002c-field-separators-and): [Full Line Fields](#Full-Line-Fields)[POSIX `awk`, `function` keyword in](#index-POSIX-awk_002c-function-keyword-in): [Definition Syntax](#Definition-Syntax)[POSIX `awk`, functions and, `gsub()`/`sub()`](#index-POSIX-awk_002c-functions-and_002c-gsub_0028_0029_002fsub_0028_0029): [Gory Details](#Gory-Details)[POSIX `awk`, functions and, `length()`](#index-POSIX-awk_002c-functions-and_002c-length_0028_0029): [String Functions](#String-Functions)[POSIX `awk`, GNU long options and](#index-POSIX-awk_002c-GNU-long-options-and): [Options](#Options)[POSIX `awk`, interval expressions in](#index-POSIX-awk_002c-interval-expressions-in): [Regexp Operators](#Regexp-Operators)[POSIX `awk`, `next`/`nextfile` statements and](#index-POSIX-awk_002c-next_002fnextfile-statements-and): [Next Statement](#Next-Statement)[POSIX `awk`, numeric strings and](#index-POSIX-awk_002c-numeric-strings-and): [Variable Typing](#Variable-Typing)[POSIX `awk`, `OFMT` variable and](#index-POSIX-awk_002c-OFMT-variable-and): [OFMT](#OFMT)[POSIX `awk`, `OFMT` variable and](#index-POSIX-awk_002c-OFMT-variable-and-1): [Strings And Numbers](#Strings-And-Numbers)[POSIX `awk`, period (`.`), using](#index-POSIX-awk_002c-period-_0028_002e_0029_002c-using): [Regexp Operators](#Regexp-Operators)[POSIX `awk`, `printf` format strings and](#index-POSIX-awk_002c-printf-format-strings-and): [Format Modifiers](#Format-Modifiers)[POSIX `awk`, regular expressions and](#index-POSIX-awk_002c-regular-expressions-and): [Regexp Operators](#Regexp-Operators)[POSIX `awk`, timestamps and](#index-POSIX-awk_002c-timestamps-and): [Time Functions](#Time-Functions)[POSIX `awk`, `|` I/O operator and](#index-POSIX-awk_002c-_007c-I_002fO-operator-and): [Getline/Pipe](#Getline_002fPipe)[POSIX mode](#index-POSIX-mode): [Options](#Options)[POSIX mode](#index-POSIX-mode-1): [Options](#Options)[POSIX, `awk` and](#index-POSIX_002c-awk-and): [Preface](#Preface)[POSIX, `gawk` extensions not included in](#index-POSIX_002c-gawk-extensions-not-included-in): [POSIX/GNU](#POSIX_002fGNU)[POSIX, programs, implementing in `awk`](#index-POSIX_002c-programs_002c-implementing-in-awk): [Clones](#Clones)[`POSIXLY_CORRECT` environment variable](#index-POSIXLY_005fCORRECT-environment-variable): [Options](#Options)[`PREC` variable](#index-PREC-variable): [User-modified](#User_002dmodified)[precedence](#index-precedence): [Increment Ops](#Increment-Ops)[precedence](#index-precedence-1): [Precedence](#Precedence)[precedence, regexp operators](#index-precedence_002c-regexp-operators): [Regexp Operators](#Regexp-Operators)[predefined variables](#index-predefined-variables): [Built-in Variables](#Built_002din-Variables)[predefined variables, `-v` option, setting with](#index-predefined-variables_002c-_002dv-option_002c-setting-with): [Options](#Options)[predefined variables, conveying information](#index-predefined-variables_002c-conveying-information): [Auto-set](#Auto_002dset)[predefined variables, user-modifiable](#index-predefined-variables_002c-user_002dmodifiable): [User-modified](#User_002dmodified)[`print` debugger command](#index-print-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[`print` statement](#index-print-statement): [Printing](#Printing)[`print` statement, `BEGIN`/`END` patterns and](#index-print-statement_002c-BEGIN_002fEND-patterns-and): [I/O And BEGIN/END](#I_002fO-And-BEGIN_002fEND)[`print` statement, commas, omitting](#index-print-statement_002c-commas_002c-omitting): [Print Examples](#Print-Examples)[`print` statement, I/O operators in](#index-print-statement_002c-I_002fO-operators-in): [Precedence](#Precedence)[`print` statement, line continuations and](#index-print-statement_002c-line-continuations-and): [Print Examples](#Print-Examples)[`print` statement, `OFMT` variable and](#index-print-statement_002c-OFMT-variable-and): [User-modified](#User_002dmodified)[`print` statement, See Also redirection, of output](#index-print-statement_002c-See-Also-redirection_002c-of-output): [Redirection](#Redirection)[`print` statement, `sprintf()` function and](#index-print-statement_002c-sprintf_0028_0029-function-and): [Round Function](#Round-Function)[print variables, in debugger](#index-print-variables_002c-in-debugger): [Viewing And Changing Data](#Viewing-And-Changing-Data)[`printf` debugger command](#index-printf-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[`printf` statement](#index-printf-statement): [Printing](#Printing)[`printf` statement](#index-printf-statement-1): [Printf](#Printf)[`printf` statement, columns, aligning](#index-printf-statement_002c-columns_002c-aligning): [Print Examples](#Print-Examples)[`printf` statement, format-control characters](#index-printf-statement_002c-format_002dcontrol-characters): [Control Letters](#Control-Letters)[`printf` statement, I/O operators in](#index-printf-statement_002c-I_002fO-operators-in): [Precedence](#Precedence)[`printf` statement, modifiers](#index-printf-statement_002c-modifiers): [Format Modifiers](#Format-Modifiers)[`printf` statement, positional specifiers](#index-printf-statement_002c-positional-specifiers): [Format Modifiers](#Format-Modifiers)[`printf` statement, positional specifiers](#index-printf-statement_002c-positional-specifiers-1): [Printf Ordering](#Printf-Ordering)[`printf` statement, positional specifiers, mixing with regular formats](#index-printf-statement_002c-positional-specifiers_002c-mixing-with-regular-formats): [Printf Ordering](#Printf-Ordering)[`printf` statement, See Also redirection, of output](#index-printf-statement_002c-See-Also-redirection_002c-of-output): [Redirection](#Redirection)[`printf` statement, `sprintf()` function and](#index-printf-statement_002c-sprintf_0028_0029-function-and): [Round Function](#Round-Function)[`printf` statement, syntax of](#index-printf-statement_002c-syntax-of): [Basic Printf](#Basic-Printf)[printing](#index-printing): [Printing](#Printing)[printing messages from extensions](#index-printing-messages-from-extensions): [Printing Messages](#Printing-Messages)[printing, list of options](#index-printing_002c-list-of-options): [Options](#Options)[printing, mailing labels](#index-printing_002c-mailing-labels): [Labels Program](#Labels-Program)[printing, unduplicated lines of text](#index-printing_002c-unduplicated-lines-of-text): [Uniq Program](#Uniq-Program)[printing, user information](#index-printing_002c-user-information): [Id Program](#Id-Program)[private variables](#index-private-variables): [Library Names](#Library-Names)[process group ID of `gawk` process](#index-process-group-ID-of-gawk-process): [Auto-set](#Auto_002dset)[process ID of `gawk` process](#index-process-ID-of-gawk-process): [Auto-set](#Auto_002dset)[processes, two-way communications with](#index-processes_002c-two_002dway-communications-with): [Two-way I/O](#Two_002dway-I_002fO)[processing data](#index-processing-data): [Basic High Level](#Basic-High-Level)[`PROCINFO` array](#index-PROCINFO-array): [Auto-set](#Auto_002dset)[`PROCINFO` array](#index-PROCINFO-array-1): [Time Functions](#Time-Functions)[`PROCINFO` array](#index-PROCINFO-array-2): [Passwd Functions](#Passwd-Functions)[`PROCINFO` array, and communications via ptys](#index-PROCINFO-array_002c-and-communications-via-ptys): [Two-way I/O](#Two_002dway-I_002fO)[`PROCINFO` array, and group membership](#index-PROCINFO-array_002c-and-group-membership): [Group Functions](#Group-Functions)[`PROCINFO` array, and user and group ID numbers](#index-PROCINFO-array_002c-and-user-and-group-ID-numbers): [Id Program](#Id-Program)[`PROCINFO` array, testing the field splitting](#index-PROCINFO-array_002c-testing-the-field-splitting): [Passwd Functions](#Passwd-Functions)[`PROCINFO`, values of `sorted_in`](#index-PROCINFO_002c-values-of-sorted_005fin): [Controlling Scanning](#Controlling-Scanning)[profiling `awk` programs](#index-profiling-awk-programs): [Profiling](#Profiling)[profiling `awk` programs, dynamically](#index-profiling-awk-programs_002c-dynamically): [Profiling](#Profiling)[program identifiers](#index-program-identifiers): [Auto-set](#Auto_002dset)[program, definition of](#index-program_002c-definition-of): [Getting Started](#Getting-Started)[programming conventions, `--non-decimal-data` option](#index-programming-conventions_002c-_002d_002dnon_002ddecimal_002ddata-option): [Nondecimal Data](#Nondecimal-Data)[programming conventions, `ARGC`/`ARGV` variables](#index-programming-conventions_002c-ARGC_002fARGV-variables): [Auto-set](#Auto_002dset)[programming conventions, `exit` statement](#index-programming-conventions_002c-exit-statement): [Exit Statement](#Exit-Statement)[programming conventions, function parameters](#index-programming-conventions_002c-function-parameters): [Return Statement](#Return-Statement)[programming conventions, functions, calling](#index-programming-conventions_002c-functions_002c-calling): [Calling Built-in](#Calling-Built_002din)[programming conventions, functions, writing](#index-programming-conventions_002c-functions_002c-writing): [Definition Syntax](#Definition-Syntax)[programming conventions, `gawk` extensions](#index-programming-conventions_002c-gawk-extensions): [Internal File Ops](#Internal-File-Ops)[programming conventions, private variable names](#index-programming-conventions_002c-private-variable-names): [Library Names](#Library-Names)[programming language, recipe for](#index-programming-language_002c-recipe-for): [History](#History)[programming languages, Ada](#index-programming-languages_002c-Ada): [Glossary](#Glossary)[programming languages, data-driven vs. procedural](#index-programming-languages_002c-data_002ddriven-vs_002e-procedural): [Getting Started](#Getting-Started)[programming languages, Java](#index-programming-languages_002c-Java): [Glossary](#Glossary)[programming, basic steps](#index-programming_002c-basic-steps): [Basic High Level](#Basic-High-Level)[programming, concepts](#index-programming_002c-concepts): [Basic Concepts](#Basic-Concepts)[programming, concepts](#index-programming_002c-concepts-1): [Basic Concepts](#Basic-Concepts)[`pwcat` program](#index-pwcat-program): [Passwd Functions](#Passwd-Functions)
---
[Q](Q)[`q` debugger command (alias for `quit`)](#index-q-debugger-command-_0028alias-for-quit_0029): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[QSE `awk`](#index-QSE-awk): [Other Versions](#Other-Versions)[Quanstrom, Erik](#index-Quanstrom_002c-Erik): [Alarm Program](#Alarm-Program)[question mark (`?`), `?:` operator](#index-question-mark-_0028_003f_0029_002c-_003f_003a-operator): [Precedence](#Precedence)[question mark (`?`), regexp operator](#index-question-mark-_0028_003f_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[question mark (`?`), regexp operator](#index-question-mark-_0028_003f_0029_002c-regexp-operator-1): [GNU Regexp Operators](#GNU-Regexp-Operators)[QuikTrim Awk](#index-QuikTrim-Awk): [Other Versions](#Other-Versions)[`quit` debugger command](#index-quit-debugger-command): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[`QUIT` signal (MS-Windows)](#index-QUIT-signal-_0028MS_002dWindows_0029): [Profiling](#Profiling)[quoting in `gawk` command lines](#index-quoting-in-gawk-command-lines): [Long](#Long)[quoting in `gawk` command lines, tricks for](#index-quoting-in-gawk-command-lines_002c-tricks-for): [Quoting](#Quoting)[quoting, for small awk programs](#index-quoting_002c-for-small-awk-programs): [Comments](#Comments)
---
[R](R)[`r` debugger command (alias for `run`)](#index-r-debugger-command-_0028alias-for-run_0029): [Debugger Execution Control](#Debugger-Execution-Control)[Rakitzis, Byron](#index-Rakitzis_002c-Byron): [History Sorting](#History-Sorting)[Ramey, Chet](#index-Ramey_002c-Chet): [Acknowledgments](#Acknowledgments)[Ramey, Chet](#index-Ramey_002c-Chet-1): [General Data Types](#General-Data-Types)[`rand()` function](#index-rand_0028_0029-function): [Numeric Functions](#Numeric-Functions)[random numbers, Cliff](#index-random-numbers_002c-Cliff): [Cliff Random Function](#Cliff-Random-Function)[random numbers, `rand()`/`srand()` functions](#index-random-numbers_002c-rand_0028_0029_002fsrand_0028_0029-functions): [Numeric Functions](#Numeric-Functions)[random numbers, seed of](#index-random-numbers_002c-seed-of): [Numeric Functions](#Numeric-Functions)[range expressions (regexps)](#index-range-expressions-_0028regexps_0029): [Bracket Expressions](#Bracket-Expressions)[range patterns](#index-range-patterns): [Ranges](#Ranges)[range patterns, line continuation and](#index-range-patterns_002c-line-continuation-and): [Ranges](#Ranges)[Rankin, Pat](#index-Rankin_002c-Pat): [Acknowledgments](#Acknowledgments)[Rankin, Pat](#index-Rankin_002c-Pat-1): [Assignment Ops](#Assignment-Ops)[Rankin, Pat](#index-Rankin_002c-Pat-2): [Contributors](#Contributors)[RapidJson JSON parser library](#index-RapidJson-JSON-parser-library): [gawkextlib](#gawkextlib)[`reada()` extension function](#index-reada_0028_0029-extension-function): [Extension Sample Read write array](#Extension-Sample-Read-write-array)[readable data files, checking](#index-readable-data-files_002c-checking): [File Checking](#File-Checking)[`readable.awk` program](#index-readable_002eawk-program): [File Checking](#File-Checking)[`readdir` extension](#index-readdir-extension): [Extension Sample Readdir](#Extension-Sample-Readdir)[`readfile()` extension function](#index-readfile_0028_0029-extension-function): [Extension Sample Readfile](#Extension-Sample-Readfile)[`readfile()` user-defined function](#index-readfile_0028_0029-user_002ddefined-function): [Readfile Function](#Readfile-Function)[reading input files](#index-reading-input-files): [Reading Files](#Reading-Files)[recipe for a programming language](#index-recipe-for-a-programming-language): [History](#History)[record separators](#index-record-separators): [awk split records](#awk-split-records)[record separators](#index-record-separators-1): [User-modified](#User_002dmodified)[record separators, changing](#index-record-separators_002c-changing): [awk split records](#awk-split-records)[record separators, regular expressions as](#index-record-separators_002c-regular-expressions-as): [awk split records](#awk-split-records)[record separators, with multiline records](#index-record-separators_002c-with-multiline-records): [Multiple Line](#Multiple-Line)[records](#index-records): [Reading Files](#Reading-Files)[records](#index-records-1): [Basic High Level](#Basic-High-Level)[records, multiline](#index-records_002c-multiline): [Multiple Line](#Multiple-Line)[records, printing](#index-records_002c-printing): [Print](#Print)[records, splitting input into](#index-records_002c-splitting-input-into): [Records](#Records)[records, terminating](#index-records_002c-terminating): [awk split records](#awk-split-records)[records, treating files as](#index-records_002c-treating-files-as): [gawk split records](#gawk-split-records)[recursive functions](#index-recursive-functions): [Definition Syntax](#Definition-Syntax)[redirect `gawk` output, in debugger](#index-redirect-gawk-output_002c-in-debugger): [Debugger Info](#Debugger-Info)[redirection of input](#index-redirection-of-input): [Getline/File](#Getline_002fFile)[redirection of output](#index-redirection-of-output): [Redirection](#Redirection)[redirection on VMS](#index-redirection-on-VMS): [VMS Running](#VMS-Running)[reference counting, sorting arrays](#index-reference-counting_002c-sorting-arrays): [Array Sorting Functions](#Array-Sorting-Functions)[regexp](#index-regexp): [Regexp](#Regexp)[regexp constants](#index-regexp-constants): [Regexp Usage](#Regexp-Usage)[regexp constants](#index-regexp-constants-1): [Regexp Constants](#Regexp-Constants)[regexp constants](#index-regexp-constants-2): [Comparison Operators](#Comparison-Operators)[regexp constants, `/=&hellip;/`, `/=` operator and](#index-regexp-constants_002c-_002f_003d_2026_002f_002c-_002f_003d-operator-and): [Assignment Ops](#Assignment-Ops)[regexp constants, as patterns](#index-regexp-constants_002c-as-patterns): [Expression Patterns](#Expression-Patterns)[regexp constants, in `gawk`](#index-regexp-constants_002c-in-gawk): [Standard Regexp Constants](#Standard-Regexp-Constants)[regexp constants, slashes vs. quotes](#index-regexp-constants_002c-slashes-vs_002e-quotes): [Computed Regexps](#Computed-Regexps)[regexp constants, vs. string constants](#index-regexp-constants_002c-vs_002e-string-constants): [Computed Regexps](#Computed-Regexps)[register extension](#index-register-extension): [Registration Functions](#Registration-Functions)[regular expressions](#index-regular-expressions): [Regexp](#Regexp)[regular expressions as field separators](#index-regular-expressions-as-field-separators): [Field Separators](#Field-Separators)[regular expressions, anchors in](#index-regular-expressions_002c-anchors-in): [Regexp Operators](#Regexp-Operators)[regular expressions, as field separators](#index-regular-expressions_002c-as-field-separators): [Regexp Field Splitting](#Regexp-Field-Splitting)[regular expressions, as patterns](#index-regular-expressions_002c-as-patterns): [Regexp Usage](#Regexp-Usage)[regular expressions, as patterns](#index-regular-expressions_002c-as-patterns-1): [Regexp Patterns](#Regexp-Patterns)[regular expressions, as record separators](#index-regular-expressions_002c-as-record-separators): [awk split records](#awk-split-records)[regular expressions, case sensitivity](#index-regular-expressions_002c-case-sensitivity): [Case-sensitivity](#Case_002dsensitivity)[regular expressions, case sensitivity](#index-regular-expressions_002c-case-sensitivity-1): [User-modified](#User_002dmodified)[regular expressions, computed](#index-regular-expressions_002c-computed): [Computed Regexps](#Computed-Regexps)[regular expressions, constants, See regexp constants](#index-regular-expressions_002c-constants_002c-See-regexp-constants): [Regexp Usage](#Regexp-Usage)[regular expressions, dynamic](#index-regular-expressions_002c-dynamic): [Computed Regexps](#Computed-Regexps)[regular expressions, dynamic, with embedded newlines](#index-regular-expressions_002c-dynamic_002c-with-embedded-newlines): [Computed Regexps](#Computed-Regexps)[regular expressions, `gawk`, command-line options](#index-regular-expressions_002c-gawk_002c-command_002dline-options): [GNU Regexp Operators](#GNU-Regexp-Operators)[regular expressions, interval expressions and](#index-regular-expressions_002c-interval-expressions-and): [Options](#Options)[regular expressions, leftmost longest match](#index-regular-expressions_002c-leftmost-longest-match): [Leftmost Longest](#Leftmost-Longest)[regular expressions, operators](#index-regular-expressions_002c-operators): [Regexp Usage](#Regexp-Usage)[regular expressions, operators](#index-regular-expressions_002c-operators-1): [Regexp Operators](#Regexp-Operators)[regular expressions, operators, for buffers](#index-regular-expressions_002c-operators_002c-for-buffers): [GNU Regexp Operators](#GNU-Regexp-Operators)[regular expressions, operators, for words](#index-regular-expressions_002c-operators_002c-for-words): [GNU Regexp Operators](#GNU-Regexp-Operators)[regular expressions, operators, `gawk`](#index-regular-expressions_002c-operators_002c-gawk): [GNU Regexp Operators](#GNU-Regexp-Operators)[regular expressions, operators, precedence of](#index-regular-expressions_002c-operators_002c-precedence-of): [Regexp Operators](#Regexp-Operators)[regular expressions, searching for](#index-regular-expressions_002c-searching-for): [Egrep Program](#Egrep-Program)[relational operators, See comparison operators](#index-relational-operators_002c-See-comparison-operators): [Typing and Comparison](#Typing-and-Comparison)[replace in string](#index-replace-in-string): [String Functions](#String-Functions)[retrying input](#index-retrying-input): [Retrying Input](#Retrying-Input)[`return` debugger command](#index-return-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`return` statement, user-defined functions](#index-return-statement_002c-user_002ddefined-functions): [Return Statement](#Return-Statement)[return value, `close()` function](#index-return-value_002c-close_0028_0029-function): [Close Files And Pipes](#Close-Files-And-Pipes)[`rev()` user-defined function](#index-rev_0028_0029-user_002ddefined-function): [Function Example](#Function-Example)[`revoutput` extension](#index-revoutput-extension): [Extension Sample Revout](#Extension-Sample-Revout)[`revtwoway` extension](#index-revtwoway-extension): [Extension Sample Rev2way](#Extension-Sample-Rev2way)[`rewind()` user-defined function](#index-rewind_0028_0029-user_002ddefined-function): [Rewind Function](#Rewind-Function)[right angle bracket (`>`), `>` operator](#index-right-angle-bracket-_0028_003e_0029_002c-_003e-operator): [Comparison Operators](#Comparison-Operators)[right angle bracket (`>`), `>` operator](#index-right-angle-bracket-_0028_003e_0029_002c-_003e-operator-1): [Precedence](#Precedence)[right angle bracket (`>`), `>` operator (I/O)](#index-right-angle-bracket-_0028_003e_0029_002c-_003e-operator-_0028I_002fO_0029): [Redirection](#Redirection)[right angle bracket (`>`), `>=` operator](#index-right-angle-bracket-_0028_003e_0029_002c-_003e_003d-operator): [Comparison Operators](#Comparison-Operators)[right angle bracket (`>`), `>=` operator](#index-right-angle-bracket-_0028_003e_0029_002c-_003e_003d-operator-1): [Precedence](#Precedence)[right angle bracket (`>`), `>>` operator (I/O)](#index-right-angle-bracket-_0028_003e_0029_002c-_003e_003e-operator-_0028I_002fO_0029): [Redirection](#Redirection)[right angle bracket (`>`), `>>` operator (I/O)](#index-right-angle-bracket-_0028_003e_0029_002c-_003e_003e-operator-_0028I_002fO_0029-1): [Precedence](#Precedence)[right shift](#index-right-shift): [Bitwise Functions](#Bitwise-Functions)[right shift, bitwise](#index-right-shift_002c-bitwise): [Bitwise Functions](#Bitwise-Functions)[Ritchie, Dennis](#index-Ritchie_002c-Dennis): [Basic Data Typing](#Basic-Data-Typing)[`RLENGTH` variable](#index-RLENGTH-variable): [Auto-set](#Auto_002dset)[`RLENGTH` variable, `match()` function and](#index-RLENGTH-variable_002c-match_0028_0029-function-and): [String Functions](#String-Functions)[Robbins, Arnold](#index-Robbins_002c-Arnold): [Command Line Field Separator](#Command-Line-Field-Separator)[Robbins, Arnold](#index-Robbins_002c-Arnold-1): [Getline/Pipe](#Getline_002fPipe)[Robbins, Arnold](#index-Robbins_002c-Arnold-2): [Passwd Functions](#Passwd-Functions)[Robbins, Arnold](#index-Robbins_002c-Arnold-3): [Alarm Program](#Alarm-Program)[Robbins, Arnold](#index-Robbins_002c-Arnold-4): [General Data Types](#General-Data-Types)[Robbins, Arnold](#index-Robbins_002c-Arnold-5): [Contributors](#Contributors)[Robbins, Arnold](#index-Robbins_002c-Arnold-6): [Maintainers](#Maintainers)[Robbins, Arnold](#index-Robbins_002c-Arnold-7): [Future Extensions](#Future-Extensions)[Robbins, Bill](#index-Robbins_002c-Bill): [Getline/Pipe](#Getline_002fPipe)[Robbins, Harry](#index-Robbins_002c-Harry): [Acknowledgments](#Acknowledgments)[Robbins, Jean](#index-Robbins_002c-Jean): [Acknowledgments](#Acknowledgments)[Robbins, Miriam](#index-Robbins_002c-Miriam): [Acknowledgments](#Acknowledgments)[Robbins, Miriam](#index-Robbins_002c-Miriam-1): [Getline/Pipe](#Getline_002fPipe)[Robbins, Miriam](#index-Robbins_002c-Miriam-2): [Passwd Functions](#Passwd-Functions)[Rommel, Kai Uwe](#index-Rommel_002c-Kai-Uwe): [Contributors](#Contributors)[round to nearest integer](#index-round-to-nearest-integer): [Numeric Functions](#Numeric-Functions)[`round()` user-defined function](#index-round_0028_0029-user_002ddefined-function): [Round Function](#Round-Function)[rounding numbers](#index-rounding-numbers): [Round Function](#Round-Function)[`ROUNDMODE` variable](#index-ROUNDMODE-variable): [User-modified](#User_002dmodified)[`ROUNDMODE` variable](#index-ROUNDMODE-variable-1): [Setting the rounding mode](#Setting-the-rounding-mode)[`RS` variable](#index-RS-variable): [awk split records](#awk-split-records)[`RS` variable](#index-RS-variable-1): [User-modified](#User_002dmodified)[`RS` variable, multiline records and](#index-RS-variable_002c-multiline-records-and): [Multiple Line](#Multiple-Line)[`rshift()` function (`gawk`)](#index-rshift_0028_0029-function-_0028gawk_0029): [Bitwise Functions](#Bitwise-Functions)[`RSTART` variable](#index-RSTART-variable): [Auto-set](#Auto_002dset)[`RSTART` variable, `match()` function and](#index-RSTART-variable_002c-match_0028_0029-function-and): [String Functions](#String-Functions)[`RT` variable](#index-RT-variable): [awk split records](#awk-split-records)[`RT` variable](#index-RT-variable-1): [Multiple Line](#Multiple-Line)[`RT` variable](#index-RT-variable-2): [Auto-set](#Auto_002dset)[Rubin, Paul](#index-Rubin_002c-Paul): [History](#History)[Rubin, Paul](#index-Rubin_002c-Paul-1): [Contributors](#Contributors)[rule, definition of](#index-rule_002c-definition-of): [Getting Started](#Getting-Started)[`run` debugger command](#index-run-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[rvalues/lvalues](#index-rvalues_002flvalues): [Assignment Ops](#Assignment-Ops)
---
[S](S)[`s` debugger command (alias for `step`)](#index-s-debugger-command-_0028alias-for-step_0029): [Debugger Execution Control](#Debugger-Execution-Control)[sample debugging session](#index-sample-debugging-session): [Sample Debugging Session](#Sample-Debugging-Session)[sandbox mode](#index-sandbox-mode): [Options](#Options)[save debugger options](#index-save-debugger-options): [Debugger Info](#Debugger-Info)[scalar or array](#index-scalar-or-array): [Type Functions](#Type-Functions)[scalar values](#index-scalar-values): [Basic Data Typing](#Basic-Data-Typing)[scanning arrays](#index-scanning-arrays): [Scanning an Array](#Scanning-an-Array)[scanning multidimensional arrays](#index-scanning-multidimensional-arrays): [Multiscanning](#Multiscanning)[Schorr, Andrew](#index-Schorr_002c-Andrew): [Acknowledgments](#Acknowledgments)[Schorr, Andrew](#index-Schorr_002c-Andrew-1): [Auto-set](#Auto_002dset)[Schorr, Andrew](#index-Schorr_002c-Andrew-2): [Contributors](#Contributors)[Schreiber, Bert](#index-Schreiber_002c-Bert): [Acknowledgments](#Acknowledgments)[Schreiber, Rita](#index-Schreiber_002c-Rita): [Acknowledgments](#Acknowledgments)[search and replace in strings](#index-search-and-replace-in-strings): [String Functions](#String-Functions)[search in string](#index-search-in-string): [String Functions](#String-Functions)[search paths](#index-search-paths): [Programs Exercises](#Programs-Exercises)[search paths](#index-search-paths-1): [PC Using](#PC-Using)[search paths](#index-search-paths-2): [VMS Running](#VMS-Running)[search paths, for loadable extensions](#index-search-paths_002c-for-loadable-extensions): [AWKLIBPATH Variable](#AWKLIBPATH-Variable)[search paths, for source files](#index-search-paths_002c-for-source-files): [AWKPATH Variable](#AWKPATH-Variable)[search paths, for source files](#index-search-paths_002c-for-source-files-1): [Programs Exercises](#Programs-Exercises)[search paths, for source files](#index-search-paths_002c-for-source-files-2): [PC Using](#PC-Using)[search paths, for source files](#index-search-paths_002c-for-source-files-3): [VMS Running](#VMS-Running)[searching, files for regular expressions](#index-searching_002c-files-for-regular-expressions): [Egrep Program](#Egrep-Program)[searching, for words](#index-searching_002c-for-words): [Dupword Program](#Dupword-Program)[`sed` utility](#index-sed-utility): [Full Line Fields](#Full-Line-Fields)[`sed` utility](#index-sed-utility-1): [Simple Sed](#Simple-Sed)[`sed` utility](#index-sed-utility-2): [Glossary](#Glossary)[seeding random number generator](#index-seeding-random-number-generator): [Numeric Functions](#Numeric-Functions)[semicolon (`;`), `AWKPATH` variable and](#index-semicolon-_0028_003b_0029_002c-AWKPATH-variable-and): [PC Using](#PC-Using)[semicolon (`;`), separating statements in actions](#index-semicolon-_0028_003b_0029_002c-separating-statements-in-actions): [Statements/Lines](#Statements_002fLines)[semicolon (`;`), separating statements in actions](#index-semicolon-_0028_003b_0029_002c-separating-statements-in-actions-1): [Action Overview](#Action-Overview)[semicolon (`;`), separating statements in actions](#index-semicolon-_0028_003b_0029_002c-separating-statements-in-actions-2): [Statements](#Statements)[separators, field](#index-separators_002c-field): [User-modified](#User_002dmodified)[separators, field](#index-separators_002c-field-1): [User-modified](#User_002dmodified)[separators, field, `FIELDWIDTHS` variable and](#index-separators_002c-field_002c-FIELDWIDTHS-variable-and): [User-modified](#User_002dmodified)[separators, field, `FPAT` variable and](#index-separators_002c-field_002c-FPAT-variable-and): [User-modified](#User_002dmodified)[separators, for records](#index-separators_002c-for-records): [awk split records](#awk-split-records)[separators, for records](#index-separators_002c-for-records-1): [awk split records](#awk-split-records)[separators, for records](#index-separators_002c-for-records-2): [User-modified](#User_002dmodified)[separators, for records, regular expressions as](#index-separators_002c-for-records_002c-regular-expressions-as): [awk split records](#awk-split-records)[separators, for statements in actions](#index-separators_002c-for-statements-in-actions): [Action Overview](#Action-Overview)[separators, subscript](#index-separators_002c-subscript): [User-modified](#User_002dmodified)[set breakpoint](#index-set-breakpoint): [Breakpoint Control](#Breakpoint-Control)[`set` debugger command](#index-set-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[set directory of message catalogs](#index-set-directory-of-message-catalogs): [I18N Functions](#I18N-Functions)[set watchpoint](#index-set-watchpoint): [Viewing And Changing Data](#Viewing-And-Changing-Data)[shadowing of variable values](#index-shadowing-of-variable-values): [Definition Syntax](#Definition-Syntax)[shell quoting, rules for](#index-shell-quoting_002c-rules-for): [Quoting](#Quoting)[shells, piping commands into](#index-shells_002c-piping-commands-into): [Redirection](#Redirection)[shells, quoting](#index-shells_002c-quoting): [Using Shell Variables](#Using-Shell-Variables)[shells, quoting, rules for](#index-shells_002c-quoting_002c-rules-for): [Quoting](#Quoting)[shells, scripts](#index-shells_002c-scripts): [One-shot](#One_002dshot)[shells, sea](#index-shells_002c-sea): [Undocumented](#Undocumented)[shells, variables](#index-shells_002c-variables): [Using Shell Variables](#Using-Shell-Variables)[shift, bitwise](#index-shift_002c-bitwise): [Bitwise Functions](#Bitwise-Functions)[short-circuit operators](#index-short_002dcircuit-operators): [Boolean Ops](#Boolean-Ops)[show all source files, in debugger](#index-show-all-source-files_002c-in-debugger): [Debugger Info](#Debugger-Info)[show breakpoints](#index-show-breakpoints): [Debugger Info](#Debugger-Info)[show function arguments, in debugger](#index-show-function-arguments_002c-in-debugger): [Debugger Info](#Debugger-Info)[show local variables, in debugger](#index-show-local-variables_002c-in-debugger): [Debugger Info](#Debugger-Info)[show name of current source file, in debugger](#index-show-name-of-current-source-file_002c-in-debugger): [Debugger Info](#Debugger-Info)[show watchpoints](#index-show-watchpoints): [Debugger Info](#Debugger-Info)[`si` debugger command (alias for `stepi`)](#index-si-debugger-command-_0028alias-for-stepi_0029): [Debugger Execution Control](#Debugger-Execution-Control)[side effects](#index-side-effects): [Concatenation](#Concatenation)[side effects](#index-side-effects-1): [Increment Ops](#Increment-Ops)[side effects](#index-side-effects-2): [Increment Ops](#Increment-Ops)[side effects, array indexing](#index-side-effects_002c-array-indexing): [Reference to Elements](#Reference-to-Elements)[side effects, `asort()` function](#index-side-effects_002c-asort_0028_0029-function): [Array Sorting Functions](#Array-Sorting-Functions)[side effects, assignment expressions](#index-side-effects_002c-assignment-expressions): [Assignment Ops](#Assignment-Ops)[side effects, Boolean operators](#index-side-effects_002c-Boolean-operators): [Boolean Ops](#Boolean-Ops)[side effects, conditional expressions](#index-side-effects_002c-conditional-expressions): [Conditional Exp](#Conditional-Exp)[side effects, decrement/increment operators](#index-side-effects_002c-decrement_002fincrement-operators): [Increment Ops](#Increment-Ops)[side effects, `FILENAME` variable](#index-side-effects_002c-FILENAME-variable): [Getline Notes](#Getline-Notes)[side effects, function calls](#index-side-effects_002c-function-calls): [Function Calls](#Function-Calls)[side effects, statements](#index-side-effects_002c-statements): [Action Overview](#Action-Overview)[sidebar, A Constant&rsquo;s Base Does Not Affect Its Value](#index-sidebar_002c-A-Constant_0027s-Base-Does-Not-Affect-Its-Value): [Nondecimal-numbers](#Nondecimal_002dnumbers)[sidebar, Backslash Before Regular Characters](#index-sidebar_002c-Backslash-Before-Regular-Characters): [Escape Sequences](#Escape-Sequences)[sidebar, Beware The Smoke and Mirrors!](#index-sidebar_002c-Beware-The-Smoke-and-Mirrors_0021): [Bitwise Functions](#Bitwise-Functions)[sidebar, Changing `FS` Does Not Affect the Fields](#index-sidebar_002c-Changing-FS-Does-Not-Affect-the-Fields): [Full Line Fields](#Full-Line-Fields)[sidebar, Changing `NR` and `FNR`](#index-sidebar_002c-Changing-NR-and-FNR): [Auto-set](#Auto_002dset)[sidebar, Controlling Output Buffering with `system()`](#index-sidebar_002c-Controlling-Output-Buffering-with-system_0028_0029): [I/O Functions](#I_002fO-Functions)[sidebar, Escape Sequences for Metacharacters](#index-sidebar_002c-Escape-Sequences-for-Metacharacters): [Escape Sequences](#Escape-Sequences)[sidebar, `FS` and `IGNORECASE`](#index-sidebar_002c-FS-and-IGNORECASE): [Field Splitting Summary](#Field-Splitting-Summary)[sidebar, Interactive Versus Noninteractive Buffering](#index-sidebar_002c-Interactive-Versus-Noninteractive-Buffering): [I/O Functions](#I_002fO-Functions)[sidebar, Matching the Null String](#index-sidebar_002c-Matching-the-Null-String): [String Functions](#String-Functions)[sidebar, Operator Evaluation Order](#index-sidebar_002c-Operator-Evaluation-Order): [Increment Ops](#Increment-Ops)[sidebar, Piping into `sh`](#index-sidebar_002c-Piping-into-sh): [Redirection](#Redirection)[sidebar, Pre-POSIX `awk` Used `OFMT` for String Conversion](#index-sidebar_002c-Pre_002dPOSIX-awk-Used-OFMT-for-String-Conversion): [Strings And Numbers](#Strings-And-Numbers)[sidebar, Recipe for a Programming Language](#index-sidebar_002c-Recipe-for-a-Programming-Language): [History](#History)[sidebar, Rounding Modes and Conversion](#index-sidebar_002c-Rounding-Modes-and-Conversion): [Setting the rounding mode](#Setting-the-rounding-mode)[sidebar, `RS = "\0"` Is Not Portable](#index-sidebar_002c-RS-_003d-_0022_005c0_0022-Is-Not-Portable): [gawk split records](#gawk-split-records)[sidebar, So Why Does `gawk` Have `BEGINFILE` and `ENDFILE`?](#index-sidebar_002c-So-Why-Does-gawk-Have-BEGINFILE-and-ENDFILE_003f): [Filetrans Function](#Filetrans-Function)[sidebar, Syntactic Ambiguities Between &lsquo;/=&rsquo; and Regular Expressions](#index-sidebar_002c-Syntactic-Ambiguities-Between-_002f_003d-and-Regular-Expressions): [Assignment Ops](#Assignment-Ops)[sidebar, Understanding &lsquo;#!&rsquo;](#index-sidebar_002c-Understanding-_0023_0021): [Executable Scripts](#Executable-Scripts)[sidebar, Understanding `$0`](#index-sidebar_002c-Understanding-_00240): [Changing Fields](#Changing-Fields)[sidebar, Using `close()`&rsquo;s Return Value](#index-sidebar_002c-Using-close_0028_0029_0027s-Return-Value): [Close Files And Pipes](#Close-Files-And-Pipes)[sidebar, Using `\n` in Bracket Expressions of Dynamic Regexps](#index-sidebar_002c-Using-_005cn-in-Bracket-Expressions-of-Dynamic-Regexps): [Computed Regexps](#Computed-Regexps)[`SIGHUP` signal, for dynamic profiling](#index-SIGHUP-signal_002c-for-dynamic-profiling): [Profiling](#Profiling)[`SIGINT` signal (MS-Windows)](#index-SIGINT-signal-_0028MS_002dWindows_0029): [Profiling](#Profiling)[signals, `HUP`/`SIGHUP`, for profiling](#index-signals_002c-HUP_002fSIGHUP_002c-for-profiling): [Profiling](#Profiling)[signals, `INT`/`SIGINT` (MS-Windows)](#index-signals_002c-INT_002fSIGINT-_0028MS_002dWindows_0029): [Profiling](#Profiling)[signals, `QUIT`/`SIGQUIT` (MS-Windows)](#index-signals_002c-QUIT_002fSIGQUIT-_0028MS_002dWindows_0029): [Profiling](#Profiling)[signals, `USR1`/`SIGUSR1`, for profiling](#index-signals_002c-USR1_002fSIGUSR1_002c-for-profiling): [Profiling](#Profiling)[signature program](#index-signature-program): [Signature Program](#Signature-Program)[`SIGQUIT` signal (MS-Windows)](#index-SIGQUIT-signal-_0028MS_002dWindows_0029): [Profiling](#Profiling)[`SIGUSR1` signal, for dynamic profiling](#index-SIGUSR1-signal_002c-for-dynamic-profiling): [Profiling](#Profiling)[`silent` debugger command](#index-silent-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`sin()` function](#index-sin_0028_0029-function): [Numeric Functions](#Numeric-Functions)[sine](#index-sine): [Numeric Functions](#Numeric-Functions)[single quote (`'`)](#index-single-quote-_0028_0027_0029): [One-shot](#One_002dshot)[single quote (`'`) in `gawk` command lines](#index-single-quote-_0028_0027_0029-in-gawk-command-lines): [Long](#Long)[single quote (`'`), in shell commands](#index-single-quote-_0028_0027_0029_002c-in-shell-commands): [Quoting](#Quoting)[single quote (`'`), vs. apostrophe](#index-single-quote-_0028_0027_0029_002c-vs_002e-apostrophe): [Comments](#Comments)[single quote (`'`), with double quotes](#index-single-quote-_0028_0027_0029_002c-with-double-quotes): [Quoting](#Quoting)[single-character fields](#index-single_002dcharacter-fields): [Single Character Fields](#Single-Character-Fields)[single-precision](#index-single_002dprecision): [Computer Arithmetic](#Computer-Arithmetic)[single-step execution, in the debugger](#index-single_002dstep-execution_002c-in-the-debugger): [Debugger Execution Control](#Debugger-Execution-Control)[Skywalker, Luke](#index-Skywalker_002c-Luke): [Undocumented](#Undocumented)[`sleep` utility](#index-sleep-utility): [Alarm Program](#Alarm-Program)[`sleep()` extension function](#index-sleep_0028_0029-extension-function): [Extension Sample Time](#Extension-Sample-Time)[Solaris, POSIX-compliant `awk`](#index-Solaris_002c-POSIX_002dcompliant-awk): [Other Versions](#Other-Versions)[sort array](#index-sort-array): [String Functions](#String-Functions)[sort array indices](#index-sort-array-indices): [String Functions](#String-Functions)[sort function, arrays, sorting](#index-sort-function_002c-arrays_002c-sorting): [Array Sorting Functions](#Array-Sorting-Functions)[`sort` utility](#index-sort-utility): [Word Sorting](#Word-Sorting)[`sort` utility, coprocesses and](#index-sort-utility_002c-coprocesses-and): [Two-way I/O](#Two_002dway-I_002fO)[sorting characters in different languages](#index-sorting-characters-in-different-languages): [Explaining gettext](#Explaining-gettext)[source code, `awka`](#index-source-code_002c-awka): [Other Versions](#Other-Versions)[source code, Brian Kernighan&rsquo;s `awk`](#index-source-code_002c-Brian-Kernighan_0027s-awk): [Other Versions](#Other-Versions)[source code, BusyBox Awk](#index-source-code_002c-BusyBox-Awk): [Other Versions](#Other-Versions)[source code, `gawk`](#index-source-code_002c-gawk): [Gawk Distribution](#Gawk-Distribution)[source code, Illumos `awk`](#index-source-code_002c-Illumos-awk): [Other Versions](#Other-Versions)[source code, `jawk`](#index-source-code_002c-jawk): [Other Versions](#Other-Versions)[source code, libmawk](#index-source-code_002c-libmawk): [Other Versions](#Other-Versions)[source code, `mawk`](#index-source-code_002c-mawk): [Other Versions](#Other-Versions)[source code, mixing](#index-source-code_002c-mixing): [Options](#Options)[source code, `pawk`](#index-source-code_002c-pawk): [Other Versions](#Other-Versions)[source code, `pawk` (Python version)](#index-source-code_002c-pawk-_0028Python-version_0029): [Other Versions](#Other-Versions)[source code, QSE `awk`](#index-source-code_002c-QSE-awk): [Other Versions](#Other-Versions)[source code, QuikTrim Awk](#index-source-code_002c-QuikTrim-Awk): [Other Versions](#Other-Versions)[source code, Solaris `awk`](#index-source-code_002c-Solaris-awk): [Other Versions](#Other-Versions)[source files, search path for](#index-source-files_002c-search-path-for): [Programs Exercises](#Programs-Exercises)[sparse arrays](#index-sparse-arrays): [Array Intro](#Array-Intro)[Spencer, Henry](#index-Spencer_002c-Henry): [Glossary](#Glossary)[split string into array](#index-split-string-into-array): [String Functions](#String-Functions)[`split` utility](#index-split-utility): [Split Program](#Split-Program)[`split()` function](#index-split_0028_0029-function): [String Functions](#String-Functions)[`split()` function, array elements, deleting](#index-split_0028_0029-function_002c-array-elements_002c-deleting): [Delete](#Delete)[`split.awk` program](#index-split_002eawk-program): [Split Program](#Split-Program)[`sprintf()` function](#index-sprintf_0028_0029-function): [OFMT](#OFMT)[`sprintf()` function](#index-sprintf_0028_0029-function-1): [String Functions](#String-Functions)[`sprintf()` function, `OFMT` variable and](#index-sprintf_0028_0029-function_002c-OFMT-variable-and): [User-modified](#User_002dmodified)[`sprintf()` function, `print`/`printf` statements and](#index-sprintf_0028_0029-function_002c-print_002fprintf-statements-and): [Round Function](#Round-Function)[`sqrt()` function](#index-sqrt_0028_0029-function): [Numeric Functions](#Numeric-Functions)[square brackets (`[]`), regexp operator](#index-square-brackets-_0028_005b_005d_0029_002c-regexp-operator): [Regexp Operators](#Regexp-Operators)[square root](#index-square-root): [Numeric Functions](#Numeric-Functions)[`srand()` function](#index-srand_0028_0029-function): [Numeric Functions](#Numeric-Functions)[stack frame](#index-stack-frame): [Debugging Terms](#Debugging-Terms)[Stallman, Richard](#index-Stallman_002c-Richard): [Manual History](#Manual-History)[Stallman, Richard](#index-Stallman_002c-Richard-1): [Acknowledgments](#Acknowledgments)[Stallman, Richard](#index-Stallman_002c-Richard-2): [Contributors](#Contributors)[Stallman, Richard](#index-Stallman_002c-Richard-3): [Glossary](#Glossary)[standard error](#index-standard-error): [Special FD](#Special-FD)[standard input](#index-standard-input): [Read Terminal](#Read-Terminal)[standard input](#index-standard-input-1): [Special FD](#Special-FD)[standard output](#index-standard-output): [Special FD](#Special-FD)[starting the debugger](#index-starting-the-debugger): [Debugger Invocation](#Debugger-Invocation)[`stat()` extension function](#index-stat_0028_0029-extension-function): [Extension Sample File Functions](#Extension-Sample-File-Functions)[statements, compound, control statements and](#index-statements_002c-compound_002c-control-statements-and): [Statements](#Statements)[statements, control, in actions](#index-statements_002c-control_002c-in-actions): [Statements](#Statements)[statements, multiple](#index-statements_002c-multiple): [Statements/Lines](#Statements_002fLines)[`step` debugger command](#index-step-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`stepi` debugger command](#index-stepi-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[stop automatic display, in debugger](#index-stop-automatic-display_002c-in-debugger): [Viewing And Changing Data](#Viewing-And-Changing-Data)[stream editors](#index-stream-editors): [Full Line Fields](#Full-Line-Fields)[stream editors](#index-stream-editors-1): [Simple Sed](#Simple-Sed)[`strftime()` function (`gawk`)](#index-strftime_0028_0029-function-_0028gawk_0029): [Time Functions](#Time-Functions)[string constants](#index-string-constants): [Scalar Constants](#Scalar-Constants)[string constants, vs. regexp constants](#index-string-constants_002c-vs_002e-regexp-constants): [Computed Regexps](#Computed-Regexps)[string extraction (internationalization)](#index-string-extraction-_0028internationalization_0029): [String Extraction](#String-Extraction)[string length](#index-string-length): [String Functions](#String-Functions)[string operators](#index-string-operators): [Concatenation](#Concatenation)[string, regular expression match](#index-string_002c-regular-expression-match): [String Functions](#String-Functions)[string-manipulation functions](#index-string_002dmanipulation-functions): [String Functions](#String-Functions)[string-matching operators](#index-string_002dmatching-operators): [Regexp Usage](#Regexp-Usage)[string-translation functions](#index-string_002dtranslation-functions): [I18N Functions](#I18N-Functions)[strings splitting, example](#index-strings-splitting_002c-example): [String Functions](#String-Functions)[strings, converting](#index-strings_002c-converting): [Strings And Numbers](#Strings-And-Numbers)[strings, converting](#index-strings_002c-converting-1): [Bitwise Functions](#Bitwise-Functions)[strings, converting letter case](#index-strings_002c-converting-letter-case): [String Functions](#String-Functions)[strings, converting, numbers to](#index-strings_002c-converting_002c-numbers-to): [User-modified](#User_002dmodified)[strings, converting, numbers to](#index-strings_002c-converting_002c-numbers-to-1): [User-modified](#User_002dmodified)[strings, empty, See null strings](#index-strings_002c-empty_002c-See-null-strings): [awk split records](#awk-split-records)[strings, extracting](#index-strings_002c-extracting): [String Extraction](#String-Extraction)[strings, for localization](#index-strings_002c-for-localization): [Programmer i18n](#Programmer-i18n)[strings, length limitations](#index-strings_002c-length-limitations): [Scalar Constants](#Scalar-Constants)[strings, merging arrays into](#index-strings_002c-merging-arrays-into): [Join Function](#Join-Function)[strings, null](#index-strings_002c-null): [Regexp Field Splitting](#Regexp-Field-Splitting)[strings, numeric](#index-strings_002c-numeric): [Variable Typing](#Variable-Typing)[`strtonum()` function (`gawk`)](#index-strtonum_0028_0029-function-_0028gawk_0029): [String Functions](#String-Functions)[`strtonum()` function (`gawk`), `--non-decimal-data` option and](#index-strtonum_0028_0029-function-_0028gawk_0029_002c-_002d_002dnon_002ddecimal_002ddata-option-and): [Nondecimal Data](#Nondecimal-Data)[`sub()` function](#index-sub_0028_0029-function): [Standard Regexp Constants](#Standard-Regexp-Constants)[`sub()` function](#index-sub_0028_0029-function-1): [String Functions](#String-Functions)[`sub()` function, arguments of](#index-sub_0028_0029-function_002c-arguments-of): [String Functions](#String-Functions)[`sub()` function, escape processing](#index-sub_0028_0029-function_002c-escape-processing): [Gory Details](#Gory-Details)[subscript separators](#index-subscript-separators): [User-modified](#User_002dmodified)[subscripts in arrays, multidimensional](#index-subscripts-in-arrays_002c-multidimensional): [Multidimensional](#Multidimensional)[subscripts in arrays, multidimensional, scanning](#index-subscripts-in-arrays_002c-multidimensional_002c-scanning): [Multiscanning](#Multiscanning)[subscripts in arrays, numbers as](#index-subscripts-in-arrays_002c-numbers-as): [Numeric Array Subscripts](#Numeric-Array-Subscripts)[subscripts in arrays, uninitialized variables as](#index-subscripts-in-arrays_002c-uninitialized-variables-as): [Uninitialized Subscripts](#Uninitialized-Subscripts)[`SUBSEP` variable](#index-SUBSEP-variable): [User-modified](#User_002dmodified)[`SUBSEP` variable, and multidimensional arrays](#index-SUBSEP-variable_002c-and-multidimensional-arrays): [Multidimensional](#Multidimensional)[substitute in string](#index-substitute-in-string): [String Functions](#String-Functions)[`substr()` function](#index-substr_0028_0029-function): [String Functions](#String-Functions)[substring](#index-substring): [String Functions](#String-Functions)[Sumner, Andrew](#index-Sumner_002c-Andrew): [Other Versions](#Other-Versions)[supplementary groups of `gawk` process](#index-supplementary-groups-of-gawk-process): [Auto-set](#Auto_002dset)[`switch` statement](#index-switch-statement): [Switch Statement](#Switch-Statement)[`SYMTAB` array](#index-SYMTAB-array): [Auto-set](#Auto_002dset)[syntactic ambiguity: `/=` operator vs. `/=&hellip;/` regexp constant](#index-syntactic-ambiguity_003a-_002f_003d-operator-vs_002e-_002f_003d_2026_002f-regexp-constant): [Assignment Ops](#Assignment-Ops)[`system()` function](#index-system_0028_0029-function): [I/O Functions](#I_002fO-Functions)[`systime()` function (`gawk`)](#index-systime_0028_0029-function-_0028gawk_0029): [Time Functions](#Time-Functions)
---
[T](T)[`t` debugger command (alias for `tbreak`)](#index-t-debugger-command-_0028alias-for-tbreak_0029): [Breakpoint Control](#Breakpoint-Control)[`tbreak` debugger command](#index-tbreak-debugger-command): [Breakpoint Control](#Breakpoint-Control)[Tcl](#index-Tcl): [Library Names](#Library-Names)[TCP/IP](#index-TCP_002fIP): [TCP/IP Networking](#TCP_002fIP-Networking)[TCP/IP, support for](#index-TCP_002fIP_002c-support-for): [Special Network](#Special-Network)[`tee` utility](#index-tee-utility): [Tee Program](#Tee-Program)[`tee.awk` program](#index-tee_002eawk-program): [Tee Program](#Tee-Program)[temporary breakpoint](#index-temporary-breakpoint): [Breakpoint Control](#Breakpoint-Control)[terminating records](#index-terminating-records): [awk split records](#awk-split-records)[`testbits.awk` program](#index-testbits_002eawk-program): [Bitwise Functions](#Bitwise-Functions)[`testext` extension](#index-testext-extension): [Extension Sample API Tests](#Extension-Sample-API-Tests)[Texinfo](#index-Texinfo): [Conventions](#Conventions)[Texinfo](#index-Texinfo-1): [Library Functions](#Library-Functions)[Texinfo](#index-Texinfo-2): [Dupword Program](#Dupword-Program)[Texinfo](#index-Texinfo-3): [Extract Program](#Extract-Program)[Texinfo](#index-Texinfo-4): [Distribution contents](#Distribution-contents)[Texinfo](#index-Texinfo-5): [Adding Code](#Adding-Code)[Texinfo, chapter beginnings in files](#index-Texinfo_002c-chapter-beginnings-in-files): [Regexp Operators](#Regexp-Operators)[Texinfo, extracting programs from source files](#index-Texinfo_002c-extracting-programs-from-source-files): [Extract Program](#Extract-Program)[text, printing](#index-text_002c-printing): [Print](#Print)[text, printing, unduplicated lines of](#index-text_002c-printing_002c-unduplicated-lines-of): [Uniq Program](#Uniq-Program)[`TEXTDOMAIN` variable](#index-TEXTDOMAIN-variable): [User-modified](#User_002dmodified)[`TEXTDOMAIN` variable](#index-TEXTDOMAIN-variable-1): [Programmer i18n](#Programmer-i18n)[`TEXTDOMAIN` variable, `BEGIN` pattern and](#index-TEXTDOMAIN-variable_002c-BEGIN-pattern-and): [Programmer i18n](#Programmer-i18n)[`TEXTDOMAIN` variable, portability and](#index-TEXTDOMAIN-variable_002c-portability-and): [I18N Portability](#I18N-Portability)[`textdomain()` function (C library)](#index-textdomain_0028_0029-function-_0028C-library_0029): [Explaining gettext](#Explaining-gettext)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator): [Regexp Usage](#Regexp-Usage)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-1): [Computed Regexps](#Computed-Regexps)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-2): [Case-sensitivity](#Case_002dsensitivity)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-3): [Regexp Constants](#Regexp-Constants)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-4): [Comparison Operators](#Comparison-Operators)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-5): [Comparison Operators](#Comparison-Operators)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-6): [Precedence](#Precedence)[tilde (`~`), `~` operator](#index-tilde-_0028_007e_0029_002c-_007e-operator-7): [Expression Patterns](#Expression-Patterns)[time functions](#index-time-functions): [Time Functions](#Time-Functions)[time, alarm clock example program](#index-time_002c-alarm-clock-example-program): [Alarm Program](#Alarm-Program)[time, localization and](#index-time_002c-localization-and): [Explaining gettext](#Explaining-gettext)[time, managing](#index-time_002c-managing): [Getlocaltime Function](#Getlocaltime-Function)[time, retrieving](#index-time_002c-retrieving): [Time Functions](#Time-Functions)[timeout, reading input](#index-timeout_002c-reading-input): [Read Timeout](#Read-Timeout)[timestamps](#index-timestamps): [Time Functions](#Time-Functions)[timestamps](#index-timestamps-1): [Time Functions](#Time-Functions)[timestamps, converting dates to](#index-timestamps_002c-converting-dates-to): [Time Functions](#Time-Functions)[timestamps, formatted](#index-timestamps_002c-formatted): [Getlocaltime Function](#Getlocaltime-Function)[`tolower()` function](#index-tolower_0028_0029-function): [String Functions](#String-Functions)[`toupper()` function](#index-toupper_0028_0029-function): [String Functions](#String-Functions)[`tr` utility](#index-tr-utility): [Translate Program](#Translate-Program)[`trace` debugger command](#index-trace-debugger-command): [Miscellaneous Debugger Commands](#Miscellaneous-Debugger-Commands)[traceback, display in debugger](#index-traceback_002c-display-in-debugger): [Execution Stack](#Execution-Stack)[translate string](#index-translate-string): [I18N Functions](#I18N-Functions)[`translate.awk` program](#index-translate_002eawk-program): [Translate Program](#Translate-Program)[treating files, as single records](#index-treating-files_002c-as-single-records): [gawk split records](#gawk-split-records)[troubleshooting, `--non-decimal-data` option](#index-troubleshooting_002c-_002d_002dnon_002ddecimal_002ddata-option): [Options](#Options)[troubleshooting, `==` operator](#index-troubleshooting_002c-_003d_003d-operator): [Comparison Operators](#Comparison-Operators)[troubleshooting, `awk` uses `FS` not `IFS`](#index-troubleshooting_002c-awk-uses-FS-not-IFS): [Field Separators](#Field-Separators)[troubleshooting, backslash before nonspecial character](#index-troubleshooting_002c-backslash-before-nonspecial-character): [Escape Sequences](#Escape-Sequences)[troubleshooting, division](#index-troubleshooting_002c-division): [Arithmetic Ops](#Arithmetic-Ops)[troubleshooting, fatal errors, field widths, specifying](#index-troubleshooting_002c-fatal-errors_002c-field-widths_002c-specifying): [Fixed width data](#Fixed-width-data)[troubleshooting, fatal errors, `printf` format strings](#index-troubleshooting_002c-fatal-errors_002c-printf-format-strings): [Format Modifiers](#Format-Modifiers)[troubleshooting, `fflush()` function](#index-troubleshooting_002c-fflush_0028_0029-function): [I/O Functions](#I_002fO-Functions)[troubleshooting, function call syntax](#index-troubleshooting_002c-function-call-syntax): [Function Calls](#Function-Calls)[troubleshooting, `gawk`](#index-troubleshooting_002c-gawk): [Compatibility Mode](#Compatibility-Mode)[troubleshooting, `gawk`, bug reports](#index-troubleshooting_002c-gawk_002c-bug-reports): [Bugs](#Bugs)[troubleshooting, `gawk`, fatal errors, function arguments](#index-troubleshooting_002c-gawk_002c-fatal-errors_002c-function-arguments): [Calling Built-in](#Calling-Built_002din)[troubleshooting, `getline` function](#index-troubleshooting_002c-getline-function): [File Checking](#File-Checking)[troubleshooting, `gsub()`/`sub()` functions](#index-troubleshooting_002c-gsub_0028_0029_002fsub_0028_0029-functions): [String Functions](#String-Functions)[troubleshooting, `match()` function](#index-troubleshooting_002c-match_0028_0029-function): [String Functions](#String-Functions)[troubleshooting, `print` statement, omitting commas](#index-troubleshooting_002c-print-statement_002c-omitting-commas): [Print Examples](#Print-Examples)[troubleshooting, printing](#index-troubleshooting_002c-printing): [Redirection](#Redirection)[troubleshooting, quotes with file names](#index-troubleshooting_002c-quotes-with-file-names): [Special FD](#Special-FD)[troubleshooting, readable data files](#index-troubleshooting_002c-readable-data-files): [File Checking](#File-Checking)[troubleshooting, regexp constants vs. string constants](#index-troubleshooting_002c-regexp-constants-vs_002e-string-constants): [Computed Regexps](#Computed-Regexps)[troubleshooting, string concatenation](#index-troubleshooting_002c-string-concatenation): [Concatenation](#Concatenation)[troubleshooting, `substr()` function](#index-troubleshooting_002c-substr_0028_0029-function): [String Functions](#String-Functions)[troubleshooting, `system()` function](#index-troubleshooting_002c-system_0028_0029-function): [I/O Functions](#I_002fO-Functions)[troubleshooting, typographical errors, global variables](#index-troubleshooting_002c-typographical-errors_002c-global-variables): [Options](#Options)[true, logical](#index-true_002c-logical): [Truth Values](#Truth-Values)[Trueman, David](#index-Trueman_002c-David): [History](#History)[Trueman, David](#index-Trueman_002c-David-1): [Acknowledgments](#Acknowledgments)[Trueman, David](#index-Trueman_002c-David-2): [Contributors](#Contributors)[trunc-mod operation](#index-trunc_002dmod-operation): [Arithmetic Ops](#Arithmetic-Ops)[truth values](#index-truth-values): [Truth Values](#Truth-Values)[type conversion](#index-type-conversion): [Strings And Numbers](#Strings-And-Numbers)[type, of variable](#index-type_002c-of-variable): [Type Functions](#Type-Functions)[`typeof()` function (`gawk`)](#index-typeof_0028_0029-function-_0028gawk_0029): [Type Functions](#Type-Functions)
---
[U](U)[`u` debugger command (alias for `until`)](#index-u-debugger-command-_0028alias-for-until_0029): [Debugger Execution Control](#Debugger-Execution-Control)[unassigned array elements](#index-unassigned-array-elements): [Reference to Elements](#Reference-to-Elements)[undefined functions](#index-undefined-functions): [Pass By Value/Reference](#Pass-By-Value_002fReference)[underscore (`_`), C macro](#index-underscore-_0028_005f_0029_002c-C-macro): [Explaining gettext](#Explaining-gettext)[underscore (`_`), in names of private variables](#index-underscore-_0028_005f_0029_002c-in-names-of-private-variables): [Library Names](#Library-Names)[underscore (`_`), translatable string](#index-underscore-_0028_005f_0029_002c-translatable-string): [Programmer i18n](#Programmer-i18n)[`undisplay` debugger command](#index-undisplay-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[undocumented features](#index-undocumented-features): [Undocumented](#Undocumented)[Unicode](#index-Unicode): [Ordinal Functions](#Ordinal-Functions)[Unicode](#index-Unicode-1): [Ranges and Locales](#Ranges-and-Locales)[Unicode](#index-Unicode-2): [Glossary](#Glossary)[uninitialized variables, as array subscripts](#index-uninitialized-variables_002c-as-array-subscripts): [Uninitialized Subscripts](#Uninitialized-Subscripts)[`uniq` utility](#index-uniq-utility): [Uniq Program](#Uniq-Program)[`uniq.awk` program](#index-uniq_002eawk-program): [Uniq Program](#Uniq-Program)[Unix](#index-Unix): [Glossary](#Glossary)[Unix `awk`, backslashes in escape sequences](#index-Unix-awk_002c-backslashes-in-escape-sequences): [Escape Sequences](#Escape-Sequences)[Unix `awk`, `close()` function and](#index-Unix-awk_002c-close_0028_0029-function-and): [Close Files And Pipes](#Close-Files-And-Pipes)[Unix `awk`, password files, field separators and](#index-Unix-awk_002c-password-files_002c-field-separators-and): [Command Line Field Separator](#Command-Line-Field-Separator)[Unix, `awk` scripts and](#index-Unix_002c-awk-scripts-and): [Executable Scripts](#Executable-Scripts)[unsigned integers](#index-unsigned-integers): [Computer Arithmetic](#Computer-Arithmetic)[`until` debugger command](#index-until-debugger-command): [Debugger Execution Control](#Debugger-Execution-Control)[`unwatch` debugger command](#index-unwatch-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[`up` debugger command](#index-up-debugger-command): [Execution Stack](#Execution-Stack)[user database, reading](#index-user-database_002c-reading): [Passwd Functions](#Passwd-Functions)[user-defined functions](#index-user_002ddefined-functions): [User-defined](#User_002ddefined)[user-defined, functions, counts, in a profile](#index-user_002ddefined_002c-functions_002c-counts_002c-in-a-profile): [Profiling](#Profiling)[user-defined, variables](#index-user_002ddefined_002c-variables): [Variables](#Variables)[user-modifiable variables](#index-user_002dmodifiable-variables): [User-modified](#User_002dmodified)[users, information about, printing](#index-users_002c-information-about_002c-printing): [Id Program](#Id-Program)[users, information about, retrieving](#index-users_002c-information-about_002c-retrieving): [Passwd Functions](#Passwd-Functions)[`USR1` signal, for dynamic profiling](#index-USR1-signal_002c-for-dynamic-profiling): [Profiling](#Profiling)
---
[V](V)[values, numeric](#index-values_002c-numeric): [Basic Data Typing](#Basic-Data-Typing)[values, string](#index-values_002c-string): [Basic Data Typing](#Basic-Data-Typing)[variable assignments and input files](#index-variable-assignments-and-input-files): [Other Arguments](#Other-Arguments)[variable type](#index-variable-type): [Type Functions](#Type-Functions)[variable typing](#index-variable-typing): [Typing and Comparison](#Typing-and-Comparison)[variables](#index-variables): [Other Features](#Other-Features)[variables](#index-variables-1): [Basic Data Typing](#Basic-Data-Typing)[variables, assigning on command line](#index-variables_002c-assigning-on-command-line): [Assignment Options](#Assignment-Options)[variables, built-in](#index-variables_002c-built_002din): [Using Variables](#Using-Variables)[variables, flag](#index-variables_002c-flag): [Boolean Ops](#Boolean-Ops)[variables, `getline` command into, using](#index-variables_002c-getline-command-into_002c-using): [Getline/Variable](#Getline_002fVariable)[variables, `getline` command into, using](#index-variables_002c-getline-command-into_002c-using-1): [Getline/Variable/File](#Getline_002fVariable_002fFile)[variables, `getline` command into, using](#index-variables_002c-getline-command-into_002c-using-2): [Getline/Variable/Pipe](#Getline_002fVariable_002fPipe)[variables, `getline` command into, using](#index-variables_002c-getline-command-into_002c-using-3): [Getline/Variable/Coprocess](#Getline_002fVariable_002fCoprocess)[variables, global, for library functions](#index-variables_002c-global_002c-for-library-functions): [Library Names](#Library-Names)[variables, global, printing list of](#index-variables_002c-global_002c-printing-list-of): [Options](#Options)[variables, initializing](#index-variables_002c-initializing): [Using Variables](#Using-Variables)[variables, local to a function](#index-variables_002c-local-to-a-function): [Variable Scope](#Variable-Scope)[variables, predefined](#index-variables_002c-predefined): [Built-in Variables](#Built_002din-Variables)[variables, predefined `-v` option, setting with](#index-variables_002c-predefined-_002dv-option_002c-setting-with): [Options](#Options)[variables, predefined conveying information](#index-variables_002c-predefined-conveying-information): [Auto-set](#Auto_002dset)[variables, private](#index-variables_002c-private): [Library Names](#Library-Names)[variables, setting](#index-variables_002c-setting): [Options](#Options)[variables, shadowing](#index-variables_002c-shadowing): [Definition Syntax](#Definition-Syntax)[variables, types of](#index-variables_002c-types-of): [Assignment Ops](#Assignment-Ops)[variables, types of, comparison expressions and](#index-variables_002c-types-of_002c-comparison-expressions-and): [Typing and Comparison](#Typing-and-Comparison)[variables, uninitialized, as array subscripts](#index-variables_002c-uninitialized_002c-as-array-subscripts): [Uninitialized Subscripts](#Uninitialized-Subscripts)[variables, user-defined](#index-variables_002c-user_002ddefined): [Variables](#Variables)[version of `gawk`](#index-version-of-gawk): [Auto-set](#Auto_002dset)[version of `gawk` extension API](#index-version-of-gawk-extension-API): [Auto-set](#Auto_002dset)[version of GNU MP library](#index-version-of-GNU-MP-library): [Auto-set](#Auto_002dset)[version of GNU MPFR library](#index-version-of-GNU-MPFR-library): [Auto-set](#Auto_002dset)[vertical bar (`|`)](#index-vertical-bar-_0028_007c_0029): [Regexp Operators](#Regexp-Operators)[vertical bar (`|`), `|` operator (I/O)](#index-vertical-bar-_0028_007c_0029_002c-_007c-operator-_0028I_002fO_0029): [Getline/Pipe](#Getline_002fPipe)[vertical bar (`|`), `|` operator (I/O)](#index-vertical-bar-_0028_007c_0029_002c-_007c-operator-_0028I_002fO_0029-1): [Precedence](#Precedence)[vertical bar (`|`), `|&` operator (I/O)](#index-vertical-bar-_0028_007c_0029_002c-_007c_0026-operator-_0028I_002fO_0029): [Getline/Coprocess](#Getline_002fCoprocess)[vertical bar (`|`), `|&` operator (I/O)](#index-vertical-bar-_0028_007c_0029_002c-_007c_0026-operator-_0028I_002fO_0029-1): [Precedence](#Precedence)[vertical bar (`|`), `|&` operator (I/O)](#index-vertical-bar-_0028_007c_0029_002c-_007c_0026-operator-_0028I_002fO_0029-2): [Two-way I/O](#Two_002dway-I_002fO)[vertical bar (`|`), `||` operator](#index-vertical-bar-_0028_007c_0029_002c-_007c_007c-operator): [Boolean Ops](#Boolean-Ops)[vertical bar (`|`), `||` operator](#index-vertical-bar-_0028_007c_0029_002c-_007c_007c-operator-1): [Precedence](#Precedence)[Vinschen, Corinna](#index-Vinschen_002c-Corinna): [Acknowledgments](#Acknowledgments)
---
[W](W)[`w` debugger command (alias for `watch`)](#index-w-debugger-command-_0028alias-for-watch_0029): [Viewing And Changing Data](#Viewing-And-Changing-Data)[`w` utility](#index-w-utility): [Fixed width data](#Fixed-width-data)[`wait()` extension function](#index-wait_0028_0029-extension-function): [Extension Sample Fork](#Extension-Sample-Fork)[`waitpid()` extension function](#index-waitpid_0028_0029-extension-function): [Extension Sample Fork](#Extension-Sample-Fork)[`walk_array()` user-defined function](#index-walk_005farray_0028_0029-user_002ddefined-function): [Walking Arrays](#Walking-Arrays)[Wall, Larry](#index-Wall_002c-Larry): [Array Intro](#Array-Intro)[Wall, Larry](#index-Wall_002c-Larry-1): [Future Extensions](#Future-Extensions)[Wallin, Anders](#index-Wallin_002c-Anders): [Contributors](#Contributors)[warnings, issuing](#index-warnings_002c-issuing): [Options](#Options)[`watch` debugger command](#index-watch-debugger-command): [Viewing And Changing Data](#Viewing-And-Changing-Data)[watchpoint](#index-watchpoint): [Debugging Terms](#Debugging-Terms)[`wc` utility](#index-wc-utility): [Wc Program](#Wc-Program)[`wc.awk` program](#index-wc_002eawk-program): [Wc Program](#Wc-Program)[Weinberger, Peter](#index-Weinberger_002c-Peter): [History](#History)[Weinberger, Peter](#index-Weinberger_002c-Peter-1): [Contributors](#Contributors)[`where` debugger command](#index-where-debugger-command): [Execution Stack](#Execution-Stack)[`where` debugger command (alias for `backtrace`)](#index-where-debugger-command-_0028alias-for-backtrace_0029): [Execution Stack](#Execution-Stack)[`while` statement](#index-while-statement): [While Statement](#While-Statement)[`while` statement, use of regexps in](#index-while-statement_002c-use-of-regexps-in): [Regexp Usage](#Regexp-Usage)[whitespace, as field separators](#index-whitespace_002c-as-field-separators): [Default Field Splitting](#Default-Field-Splitting)[whitespace, functions, calling](#index-whitespace_002c-functions_002c-calling): [Calling Built-in](#Calling-Built_002din)[whitespace, newlines as](#index-whitespace_002c-newlines-as): [Options](#Options)[Williams, Kent](#index-Williams_002c-Kent): [Contributors](#Contributors)[Woehlke, Matthew](#index-Woehlke_002c-Matthew): [Contributors](#Contributors)[Woods, John](#index-Woods_002c-John): [Contributors](#Contributors)[word boundaries, matching](#index-word-boundaries_002c-matching): [GNU Regexp Operators](#GNU-Regexp-Operators)[word, regexp definition of](#index-word_002c-regexp-definition-of): [GNU Regexp Operators](#GNU-Regexp-Operators)[word-boundary operator (`gawk`)](#index-word_002dboundary-operator-_0028gawk_0029): [GNU Regexp Operators](#GNU-Regexp-Operators)[`wordfreq.awk` program](#index-wordfreq_002eawk-program): [Word Sorting](#Word-Sorting)[words, counting](#index-words_002c-counting): [Wc Program](#Wc-Program)[words, duplicate, searching for](#index-words_002c-duplicate_002c-searching-for): [Dupword Program](#Dupword-Program)[words, usage counts, generating](#index-words_002c-usage-counts_002c-generating): [Word Sorting](#Word-Sorting)[`writea()` extension function](#index-writea_0028_0029-extension-function): [Extension Sample Read write array](#Extension-Sample-Read-write-array)
---
[X](X)[`xgettext` utility](#index-xgettext-utility): [String Extraction](#String-Extraction)[XOR bitwise operation](#index-XOR-bitwise-operation): [Bitwise Functions](#Bitwise-Functions)[`xor()` function (`gawk`)](#index-xor_0028_0029-function-_0028gawk_0029): [Bitwise Functions](#Bitwise-Functions)
---
[Y](Y)[Yawitz, Efraim](#index-Yawitz_002c-Efraim): [Contributors](#Contributors)
---
[Z](Z)[Zaretskii, Eli](#index-Zaretskii_002c-Eli): [Acknowledgments](#Acknowledgments)[Zaretskii, Eli](#index-Zaretskii_002c-Eli-1): [Contributors](#Contributors)[Zaretskii, Eli](#index-Zaretskii_002c-Eli-2): [Maintainers](#Maintainers)[`zerofile.awk` program](#index-zerofile_002eawk-program): [Empty Files](#Empty-Files)[Zoulas, Christos](#index-Zoulas_002c-Christos): [Contributors](#Contributors)
---
Jump to:   [!](#Index_cp_symbol-1)
   
["](#Index_cp_symbol-2)
   
[#](#Index_cp_symbol-3)
   
[$](#Index_cp_symbol-4)
   
[%](#Index_cp_symbol-5)
   
[&](#Index_cp_symbol-6)
   
['](#Index_cp_symbol-7)
   
[(](#Index_cp_symbol-8)
   
[*](#Index_cp_symbol-9)
   
[+](#Index_cp_symbol-10)
   
[,](#Index_cp_symbol-11)
   
[-](#Index_cp_symbol-12)
   
[.](#Index_cp_symbol-13)
   
[/](#Index_cp_symbol-14)
   
[;](#Index_cp_symbol-15)
   
[<](#Index_cp_symbol-16)
   
[=](#Index_cp_symbol-17)
   
[>](#Index_cp_symbol-18)
   
[?](#Index_cp_symbol-19)
   
[@](#Index_cp_symbol-20)
   
[[](#Index_cp_symbol-21)
   
[\](#Index_cp_symbol-22)
   
[^](#Index_cp_symbol-23)
   
[_](#Index_cp_symbol-24)
   
[{](#Index_cp_symbol-25)
   
[|](#Index_cp_symbol-26)
   
[~](#Index_cp_symbol-27)
   

[A](#Index_cp_letter-A)
   
[B](#Index_cp_letter-B)
   
[C](#Index_cp_letter-C)
   
[D](#Index_cp_letter-D)
   
[E](#Index_cp_letter-E)
   
[F](#Index_cp_letter-F)
   
[G](#Index_cp_letter-G)
   
[H](#Index_cp_letter-H)
   
[I](#Index_cp_letter-I)
   
[J](#Index_cp_letter-J)
   
[K](#Index_cp_letter-K)
   
[L](#Index_cp_letter-L)
   
[M](#Index_cp_letter-M)
   
[N](#Index_cp_letter-N)
   
[O](#Index_cp_letter-O)
   
[P](#Index_cp_letter-P)
   
[Q](#Index_cp_letter-Q)
   
[R](#Index_cp_letter-R)
   
[S](#Index_cp_letter-S)
   
[T](#Index_cp_letter-T)
   
[U](#Index_cp_letter-U)
   
[V](#Index_cp_letter-V)
   
[W](#Index_cp_letter-W)
   
[X](#Index_cp_letter-X)
   
[Y](#Index_cp_letter-Y)
   
[Z](#Index_cp_letter-Z)
   

---

#### Footnotes

### [(1)](#DOCF1)

The 2008 POSIX standard is accessible online at
[http://www.opengroup.org/onlinepubs/9699919799/](http://www.opengroup.org/onlinepubs/9699919799/).

### [(2)](#DOCF2)

These utilities
are available on POSIX-compliant systems, as well as on traditional
Unix-based systems. If you are using some other operating system, you still need to
be familiar with the ideas of I/O redirection and pipes.

### [(3)](#DOCF3)

Some other, obsolete systems to which `gawk`
was once ported are no longer supported and the code for those systems
has been removed.

### [(4)](#DOCF4)

Only
Solaris systems still use an old `awk` for the
default `awk` utility. A more modern `awk` lives in
/usr/xpg6/bin on these systems.

### [(5)](#DOCF5)

All such differences
appear in the index under the
entry &ldquo;differences in `awk` and `gawk`.&rdquo;

### [(6)](#DOCF6)

GNU stands for &ldquo;GNU&rsquo;s Not Unix.&rdquo;

### [(7)](#DOCF7)

The terminology &ldquo;GNU/Linux&rdquo; is explained
in the [Glossary](#Glossary).

### [(8)](#DOCF8)

The &lsquo;#!&rsquo; mechanism works on
GNU/Linux systems, BSD-based systems, and commercial Unix systems.

### [(9)](#DOCF9)

The &lsquo;?&rsquo; and &lsquo;:&rsquo; referred to here is the
three-operand conditional expression described in
[Conditional Exp](#Conditional-Exp).
Splitting lines after &lsquo;?&rsquo; and &lsquo;:&rsquo; is a minor `gawk`
extension; if --posix is specified
(see [Options](#Options)), then this extension is disabled.

### [(10)](#DOCF10)

Other popular scripting languages include Ruby
and Perl.

### [(11)](#DOCF11)

For more detail,
please see Section 4.4 of [RFC 3875](http://www.ietf.org/rfc/rfc3875). Also see the
[explanatory note sent to the `gawk` bug
mailing list](https://lists.gnu.org/archive/html/bug-gawk/2014-11/msg00022.html).

### [(12)](#DOCF12)

Not recommended.

### [(13)](#DOCF13)

Semicolons on MS-Windows.

### [(14)](#DOCF14)

Your version of `gawk`
may use a different directory; it
will depend upon how `gawk` was built and installed. The actual
directory is the value of `$(datadir)` generated when
`gawk` was configured.  You probably don&rsquo;t need to worry about this,
though.

### [(15)](#DOCF15)

In other literature,
you may see a bracket expression referred to as either a
*character set*, a *character class*, or a *character list*.

### [(16)](#DOCF16)

Use two backslashes if you&rsquo;re
using a string constant with a regexp operator or function.

### [(17)](#DOCF17)

Experienced C and C++ programmers will note
that it is possible, using something like
&lsquo;IGNORECASE = 1 && /foObAr/ { &hellip; }&rsquo;
and
&lsquo;IGNORECASE = 0 || /foobar/ { &hellip; }&rsquo;.
However, this is somewhat obscure and we don&rsquo;t recommend it.

### [(18)](#DOCF18)

If you don&rsquo;t understand this,
don&rsquo;t worry about it; it just means that `gawk` does
the right thing.

### [(19)](#DOCF19)

At least that we know
about.

### [(20)](#DOCF20)

A *binary operator*, such as &lsquo;*&rsquo; for
multiplication, is one that takes two operands. The distinction
is required because `awk` also has unary (one-operand)
and ternary (three-operand) operators.

### [(21)](#DOCF21)

Thanks to
Andrew Schorr for this tip.

### [(22)](#DOCF22)

The `sed` utility is a &ldquo;stream editor.&rdquo;
Its behavior is also defined by the POSIX standard.

### [(23)](#DOCF23)

The
CSV format lacked a formal standard definition for many years.
[RFC 4180](http://www.ietf.org/rfc/rfc4180.txt)
standardizes the most common practices.

### [(24)](#DOCF24)

When `FS` is the null string (`""`)
or a regexp, this special feature of `RS` does not apply.
It does apply to the default field separator of a single space:
&lsquo;FS = " "&rsquo;.

### [(25)](#DOCF25)

This is not quite true. `RT` could
be changed if `RS` is a regular expression.

### [(26)](#DOCF26)

This assumes
that standard input is the keyboard.

### [(27)](#DOCF27)

The &ldquo;tty&rdquo; in /dev/tty stands for
&ldquo;Teletype,&rdquo; a serial terminal.

### [(28)](#DOCF28)

The technical terminology is rather morbid.
The finished child is called a &ldquo;zombie,&rdquo; and cleaning up after
it is referred to as &ldquo;reaping.&rdquo;

### [(29)](#DOCF29)

Prior
to version 4.2, the return value from closing a pipe or co-process
was the full 16-bit exit value as defined by the `wait()` system
call.

### [(30)](#DOCF30)

The internal representation of all numbers,
including integers, uses double-precision floating-point numbers.
On most modern systems, these are in IEEE 754 standard format.
See [Arbitrary Precision Arithmetic](#Arbitrary-Precision-Arithmetic), for much more information.

### [(31)](#DOCF31)

Pathological cases can require up to
752 digits (!), but we doubt that you need to worry about this.

### [(32)](#DOCF32)

It happens that BWK
`awk`, `gawk`, and `mawk` all &ldquo;get it right,&rdquo;
but you should not rely on this.

### [(33)](#DOCF33)

`gawk`
calls this *unassigned*, as the following example shows.

### [(34)](#DOCF34)

Thus, a POSIX
numeric string and `gawk`&rsquo;s strnum are the same thing.

### [(35)](#DOCF35)

Technically, string comparison is supposed to behave
the same way as if the strings were compared with the C `strcoll()`
function.

### [(36)](#DOCF36)

See [the Austin Group website](http://austingroupbugs.net/view.php?id=1070).

### [(37)](#DOCF37)

This
program has a bug; it prints lines starting with &lsquo;END&rsquo;. How
would you fix it?

### [(38)](#DOCF38)

The original version of `awk` kept
reading and ignoring input until the end of the file was seen.

### [(39)](#DOCF39)

Some early implementations of Unix
`awk` initialized `FILENAME` to `"-"`, even if there
were data files to be processed. This behavior was incorrect and should
not be relied upon in your programs.

### [(40)](#DOCF40)

Not to mention
difficult implementation issues.

### [(41)](#DOCF41)

The ordering will vary among `awk`
implementations, which typically use hash tables to store array elements
and values.

### [(42)](#DOCF42)

When two elements
compare as equal, the C `qsort()` function does not guarantee
that they will maintain their original relative order after sorting.
Using the string value to provide a unique ordering when the numeric
values are equal ensures that `gawk` behaves consistently
across different environments.

### [(43)](#DOCF43)

Thanks to Michael Brennan for pointing this out.

### [(44)](#DOCF44)

The C version of `rand()`
on many Unix systems
is known to produce fairly poor sequences of random numbers.
However, nothing requires that an `awk` implementation use the C
`rand()` to implement the `awk` version of `rand()`.
In fact, `gawk` uses the BSD `random()` function, which is
considerably better than `rand()`, to produce random numbers.

### [(45)](#DOCF45)

`mawk`
uses a different seed each time.

### [(46)](#DOCF46)

Computer-generated random numbers really are not truly
random.  They are technically known as *pseudorandom*.  This means
that although the numbers in a sequence appear to be random, you can in
fact generate the same sequence of random numbers over and over again.

### [(47)](#DOCF47)

Unless
you use the --non-decimal-data option, which isn&rsquo;t recommended.
See [Nondecimal Data](#Nondecimal-Data) for more information.

### [(48)](#DOCF48)

Note that this means
that the record will first be regenerated using the value of `OFS` if
any fields have been changed, and that the fields will be updated
after the substitution, even if the operation is a &ldquo;no-op&rdquo; such
as &lsquo;sub(/^/, "")&rsquo;.

### [(49)](#DOCF49)

This is different from
C and C++, in which the first character is number zero.

### [(50)](#DOCF50)

This was rather naive of him, despite
there being a note in this section indicating that the next major version
would move to the POSIX rules.

### [(51)](#DOCF51)

A program is interactive
if the standard output is connected to a terminal device. On modern
systems, this means your keyboard and screen.

### [(52)](#DOCF52)

In private correspondence,
Dr. Kernighan has indicated to me that the way this was done
was probably a mistake.

### [(53)](#DOCF53)

See [Glossary](#Glossary), especially the entries &ldquo;Epoch&rdquo; and &ldquo;UTC.&rdquo;

### [(54)](#DOCF54)

The GNU `date` utility can
also do many of the things described here.  Its use may be preferable
for simple time-related operations in shell scripts.

### [(55)](#DOCF55)

Occasionally there are
minutes in a year with a leap second, which is why the
seconds can go up to 60.

### [(56)](#DOCF56)

Unfortunately,
not every system&rsquo;s `strftime()` necessarily
supports all of the conversions listed here.

### [(57)](#DOCF57)

If you don&rsquo;t understand any of this, don&rsquo;t worry about
it; these facilities are meant to make it easier to &ldquo;internationalize&rdquo;
programs.
Other internationalization features are described in
[Internationalization](#Internationalization).

### [(58)](#DOCF58)

This is because ISO C leaves the
behavior of the C version of `strftime()` undefined and `gawk`
uses the system&rsquo;s version of `strftime()` if it&rsquo;s there.
Typically, the conversion specifier either does not appear in the
returned string or appears literally.

### [(59)](#DOCF59)

This example
shows that zeros come in on the left side. For `gawk`, this is
always true, but in some languages, it&rsquo;s possible to have the left side
fill with ones.

### [(60)](#DOCF60)

If you don&rsquo;t
understand this paragraph, the upshot is that `gawk` can only
store a particular range of integer values; numbers outside that range
are reduced to fit within the range.

### [(61)](#DOCF61)

This program won&rsquo;t actually run,
because `foo()` is undefined.

### [(62)](#DOCF62)

Late in 2012.

### [(63)](#DOCF63)

This may change in a future
version; recheck the documentation that comes with your version of
`gawk` to see if it has.

### [(64)](#DOCF64)

Sadly, over 35
years later, many of the lessons taught by this book have yet to be
learned by a vast number of practicing programmers.

### [(65)](#DOCF65)

The effects are
not identical.  Output of the transformed
record will be in all lowercase, while `IGNORECASE` preserves the original
contents of the input record.

### [(66)](#DOCF66)

Although all the library routines could have
been rewritten to use this convention, this was not done, in order to
show how our own `awk` programming style has evolved and to
provide some basis for this discussion.

### [(67)](#DOCF67)

`gawk`&rsquo;s --dump-variables command-line
option is useful for verifying this.

### [(68)](#DOCF68)

This
is changing; many systems use Unicode, a very large character set
that includes ASCII as a subset.  On systems with full Unicode support,
a character can occupy up to 32 bits, making simple tests such as
used here prohibitively expensive.

### [(69)](#DOCF69)

ASCII
has been extended in many countries to use the values from 128 to 255
for country-specific characters.  If your  system uses these extensions,
you can simplify `_ord_init()` to loop from 0 to 255.

### [(70)](#DOCF70)

It would
be nice if `awk` had an assignment operator for concatenation.
The lack of an explicit operator for concatenation makes string operations
more difficult than they really need to be.

### [(71)](#DOCF71)

The `BEGINFILE`
special pattern (see [BEGINFILE/ENDFILE](#BEGINFILE_002fENDFILE)) provides an alternative
mechanism for dealing with files that can&rsquo;t be opened.  However, the
code here provides a portable solution.

### [(72)](#DOCF72)

This
function was written before `gawk` acquired the ability to
split strings into single characters using `""` as the separator.
We have left it alone, as using `substr()` is more portable.

### [(73)](#DOCF73)

It is often the case that password
information is stored in a network database.

### [(74)](#DOCF74)

There is a
subtle problem with the code just presented.  Suppose that
the first time there were no names. This code adds the names with
a leading comma. It also doesn&rsquo;t check that there is a `$4`.

### [(75)](#DOCF75)

It
also introduces a subtle bug;
if a match happens, we output the translated line, not the original.

### [(76)](#DOCF76)

This is the traditional usage. The
POSIX usage is different, but not relevant for what the program
aims to demonstrate.

### [(77)](#DOCF77)

Because `gawk`
understands multibyte locales, this code counts characters, not bytes.

### [(78)](#DOCF78)

On some older
systems, including Solaris, the system version of `tr` may require
that the lists be written as range expressions enclosed in square brackets
(&lsquo;[a-z]&rsquo;) and quoted, to prevent the shell from attempting a
file name expansion.  This is not a feature.

### [(79)](#DOCF79)

&ldquo;Real world&rdquo; is defined as
&ldquo;a program actually used to get something done.&rdquo;

### [(80)](#DOCF80)

Fully explaining the `sh` language is beyond
the scope of this book. We provide some minimal explanations, but see
a good shell programming book if you wish to understand things in more
depth.

### [(81)](#DOCF81)

On some very old versions of `awk`, the test
&lsquo;getline junk < t&rsquo; can loop forever if the file exists but is empty.

### [(82)](#DOCF82)

`gawk`
does `@include` processing itself in order to support the use
of `awk` programs as Web CGI scripts.

### [(83)](#DOCF83)

This is the definition returned from entering
`define: state machine` into Google.

### [(84)](#DOCF84)

This is why the predefined sorting orders
start with an &lsquo;@&rsquo; character, which cannot be part of an identifier.

### [(85)](#DOCF85)

This
is true because locale-based comparison occurs only when in
POSIX-compatibility mode, and because `asort()` and `asorti()` are
`gawk` extensions, they are not available in that case.

### [(86)](#DOCF86)

Michael
Brennan suggests the use of `rand()` to generate unique
file names. This is a valid point; nevertheless, temporary files
remain more difficult to use than two-way pipes.

### [(87)](#DOCF87)

This is very
different from the same operator in the C shell and in Bash.

### [(88)](#DOCF88)

`gawk` does the best it can to preserve
the distinction between comments at the end of a statement and comments
on lines by themselves. Due to implementation constraints, it does not
always do so correctly, particularly for `switch` statements. The
`gawk` maintainers hope to improve this in a subsequent
release.

### [(89)](#DOCF89)

For some operating systems, the `gawk`
port doesn&rsquo;t support GNU `gettext`.
Therefore, these features are not available
if you are using one of those operating systems. Sorry.

### [(90)](#DOCF90)

Americans
use a comma every three decimal places and a period for the decimal
point, while many Europeans do exactly the opposite:
1,234.56 versus 1.234,56.

### [(91)](#DOCF91)

Thanks to Bruno Haible for this
example.

### [(92)](#DOCF92)

The
`xgettext` utility that comes with GNU
`gettext` can handle .awk files.

### [(93)](#DOCF93)

This example is borrowed
from the GNU `gettext` manual.

### [(94)](#DOCF94)

This is good fodder for an &ldquo;Obfuscated
`awk`&rdquo; contest.

### [(95)](#DOCF95)

Perhaps it would be better if it were
called &ldquo;Hippy.&rdquo; Ah, well.

### [(96)](#DOCF96)

We don&rsquo;t know why they expect this, but
they do.

### [(97)](#DOCF97)

Thanks to Michael Brennan for this description,
which we have paraphrased, and for the examples.

### [(98)](#DOCF98)

There
is a very nice [paper on floating-point arithmetic](http://www.validlab.com/goldberg/paper.pdf) by David Goldberg, &ldquo;What Every
Computer Scientist Should Know About Floating-Point Arithmetic,&rdquo;
ACM Computing Surveys**23**, 1 (1991-03): 5-48.  This is
worth reading if you are interested in the details, but it does require
a background in computer science.

### [(99)](#DOCF99)

It
is possible for the output to be completely different if the
C library in your system does not use the IEEE 754 even-rounding
rule to round halfway cases for `printf`.

### [(100)](#DOCF100)

Weisstein, Eric W.
Sylvester&rsquo;s Sequence. From MathWorld&mdash;A Wolfram Web Resource
([http://mathworld.wolfram.com/SylvestersSequence.html](http://mathworld.wolfram.com/SylvestersSequence.html)).

### [(101)](#DOCF101)

You asked for it, you got it.

### [(102)](#DOCF102)

See
[the &ldquo;cookie&rdquo; entry in the Jargon file](http://catb.org/jargon/html/C/cookie.html) for a
definition of *cookie*, and [the &ldquo;magic cookie&rdquo; entry in the Jargon file](http://catb.org/jargon/html/M/magic-cookie.html) for a nice example.
See also the entry for &ldquo;Cookie&rdquo; in the [Glossary](#Glossary).

### [(103)](#DOCF103)

This is more common on MS-Windows systems, but it
can happen on Unix-like systems as well.

### [(104)](#DOCF104)

Because the API uses only ISO C 90
features, it cannot make use of the ISO C 99 variadic macro feature to hide
that parameter. More&rsquo;s the pity.

### [(105)](#DOCF105)

The
difference is measurable and quite real. Trust us.

### [(106)](#DOCF106)

Numeric values
are clearly less problematic, requiring only a C `double` to store.
But of course, GMP and MPFR values *do* take up more memory.

### [(107)](#DOCF107)

OK, the only data structure.

### [(108)](#DOCF108)

It is also
a &ldquo;cookie,&rdquo; but the `gawk` developers did not wish to overuse this
term.

### [(109)](#DOCF109)

This version is
edited slightly for presentation.  See extension/filefuncs.c
in the `gawk` distribution for the complete version.

### [(110)](#DOCF110)

In practice, you would probably want to
use the GNU Autotools (Automake, Autoconf, Libtool, and `gettext`) to
configure and build your libraries. Instructions for doing so are beyond
the scope of this Web page. See [gawkextlib](#gawkextlib) for Internet links to
the tools.

### [(111)](#DOCF111)

And Life was good.

### [(112)](#DOCF112)

And
thus was born the Campaign for Rational Range Interpretation (or
RRI). A number of GNU tools have already implemented this change,
or will soon.  Thanks to Karl Berry for coining the phrase &ldquo;Rational
Range Interpretation.&rdquo;

### [(113)](#DOCF113)

See
[the standard](http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap09.html#tag_09_03_05)
and
[its rationale](http://pubs.opengroup.org/onlinepubs/9699919799/xrat/V4_xbd_chap09.html#tag_21_09_03_05).

### [(114)](#DOCF114)

The IA64 architecture
is also known as &ldquo;Itanium.&rdquo;

### [(115)](#DOCF115)

We tried. It was painful.

### [(116)](#DOCF116)

There is one GNU program that is (in our opinion)
severely difficult to bootstrap from the Git repository. For
example, on the author&rsquo;s old (but still working) PowerPC Macintosh with
Mac OS X 10.5, it was necessary to bootstrap a ton of software, starting
with Git itself, in order to try to work with the latest code.
It&rsquo;s not pleasant, and especially on older systems, it&rsquo;s a big waste
of time.

Starting with the latest tarball was no picnic either. The maintainers
had dropped .gz and .bz2 files and only distribute
.tar.xz files.  It was necessary to bootstrap `xz` first!

### [(117)](#DOCF117)

A branch (since removed) created by one of the other
developers that did not include the generated files.

### [(118)](#DOCF118)

A critical central data structure
inside `gawk`.

### [(119)](#DOCF119)

The *symbols* are the variables and functions
defined inside `gawk`.  Access to these symbols by code
external to `gawk` loaded dynamically at runtime is
problematic on MS-Windows.

### [(120)](#DOCF120)

Compiled programs are typically written
in lower-level languages such as C, C++, or Ada,
and then translated, or *compiled*, into a form that
the computer can execute directly.

---
