<a id="Precompiled-Headers" href="https://gcc.gnu.org/onlinedocs/gcc/Precompiled-Headers.html">See "Precompiled Headers" on gcc.gnu.org</a>

Previous: [Environment Variables](https://gcc.gnu.org/onlinedocs/gcc/Environment-Variables.html#Environment-Variables), Up: [Invoking GCC](https://gcc.gnu.org/onlinedocs/gcc/Invoking-GCC.html#Invoking-GCC)   \[[Contents](https://gcc.gnu.org/onlinedocs/gcc/index.html#SEC_Contents "Table of contents")\]\[[Index](https://gcc.gnu.org/onlinedocs/gcc/Option-Index.html#Option-Index "Index")\]

* * *

<a id="Using-Precompiled-Headers"></a>

### 3.21 Using Precompiled Headers

<a id="index-precompiled-headers"></a><a id="index-speed-of-compilation"></a>

Often large projects have many header files that are included in every source file. The time the compiler takes to process these header files over and over again can account for nearly all of the time required to build the project. To make builds faster, GCC allows you to _precompile_ a header file.

To create a precompiled header file, simply compile it as you would any other file, if necessary using the -x option to make the driver treat it as a C or C++ header file. You may want to use a tool like `make` to keep the precompiled header up-to-date when the headers it contains change.

A precompiled header file is searched for when `#include` is seen in the compilation. As it searches for the included file (see [Search Path](http://gcc.gnu.org/onlinedocs/cpp/Search-Path.html#Search-Path) in The C Preprocessor) the compiler looks for a precompiled header in each directory just before it looks for the include file in that directory. The name searched for is the name specified in the `#include` with ‘.gch’ appended. If the precompiled header file cannot be used, it is ignored.

For instance, if you have `#include "all.h"`, and you have all.h.gch in the same directory as all.h, then the precompiled header file is used if possible, and the original header is used otherwise.

Alternatively, you might decide to put the precompiled header file in a directory and use -I to ensure that directory is searched before (or instead of) the directory containing the original header. Then, if you want to check that the precompiled header file is always used, you can put a file of the same name as the original header in this directory containing an `#error` command.

This also works with -include. So yet another way to use precompiled headers, good for projects not designed with precompiled header files in mind, is to simply take most of the header files used by a project, include them from another header file, precompile that header file, and -include the precompiled header. If the header files have guards against multiple inclusion, they are skipped because they’ve already been included (in the precompiled header).

If you need to precompile the same header file for different languages, targets, or compiler options, you can instead make a _directory_ named like all.h.gch, and put each precompiled header in the directory, perhaps using -o. It doesn’t matter what you call the files in the directory; every precompiled header in the directory is considered. The first precompiled header encountered in the directory that is valid for this compilation is used; they’re searched in no particular order.

There are many other possibilities, limited only by your imagination, good sense, and the constraints of your build system.

A precompiled header file can be used only when these conditions apply:

*   Only one precompiled header can be used in a particular compilation.
*   A precompiled header cannot be used once the first C token is seen. You can have preprocessor directives before a precompiled header; you cannot include a precompiled header from inside another header.
*   The precompiled header file must be produced for the same language as the current compilation. You cannot use a C precompiled header for a C++ compilation.
*   The precompiled header file must have been produced by the same compiler binary as the current compilation is using.
*   Any macros defined before the precompiled header is included must either be defined in the same way as when the precompiled header was generated, or must not affect the precompiled header, which usually means that they don’t appear in the precompiled header at all.
    
    The -D option is one way to define a macro before a precompiled header is included; using a `#define` can also do it. There are also some options that define macros implicitly, like -O and -Wdeprecated; the same rule applies to macros defined this way.
    
*   If debugging information is output when using the precompiled header, using -g or similar, the same kind of debugging information must have been output when building the precompiled header. However, a precompiled header built using -g can be used in a compilation when no debugging information is being output.
*   The same -m options must generally be used when building and using the precompiled header. See [Submodel Options](https://gcc.gnu.org/onlinedocs/gcc/Submodel-Options.html#Submodel-Options), for any cases where this rule is relaxed.
*   Each of the following options must be the same when building and using the precompiled header:
    
    ```
    -fexceptions
    
    ```
    
*   Some other command-line options starting with -f, -p, or -O must be defined in the same way as when the precompiled header was generated. At present, it’s not clear which options are safe to change and which are not; the safest choice is to use exactly the same options when generating and using the precompiled header. The following are known to be safe:
    
    ```
    -fmessage-length=  -fpreprocessed  -fsched-interblock 
    -fsched-spec  -fsched-spec-load  -fsched-spec-load-dangerous 
    -fsched-verbose=
    ```
    

For all of these except the last, the compiler automatically ignores the precompiled header if the conditions aren’t met. If you find an option combination that doesn’t work and doesn’t cause the precompiled header to be ignored, please consider filing a bug report, see [Bugs](https://gcc.gnu.org/onlinedocs/gcc/Bugs.html#Bugs).

If you do use differing options when generating and using the precompiled header, the actual behavior is a mixture of the behavior for the options. For instance, if you use -g to generate the precompiled header but not when using it, you may or may not get debugging information for routines in the precompiled header.

* * *

Previous: [Environment Variables](https://gcc.gnu.org/onlinedocs/gcc/Environment-Variables.html#Environment-Variables), Up: [Invoking GCC](https://gcc.gnu.org/onlinedocs/gcc/Invoking-GCC.html#Invoking-GCC)   \[[Contents](https://gcc.gnu.org/onlinedocs/gcc/index.html#SEC_Contents "Table of contents")\]\[[Index](https://gcc.gnu.org/onlinedocs/gcc/Option-Index.html#Option-Index "Index")\]
