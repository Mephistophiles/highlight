
HIGHLIGHT TESTCASES MANUAL - Version 3.45                       September 2018 
==============================================================================

CONTENT
-------

1. ABOUT
2. TEST CASE NOTATION


### 1. ABOUT

Input files whose filenames start with syntax_test_ can contain column and state
indicators in comment sections to test the highlight syntax recognition of the
previous line. If such a test fails, highlight will print an error message and
exit with FAILURE return value.

See the feature discussion here: https://gitlab.com/saalen/highlight/issues/80

NOTE: This feature is considered experimental. Please report problems and bugs
      like false positives.

NOTE: Only the last 100 states of each input line are being tracked.

### 2. TEST CASE NOTATION

A test case is defined by two entities: column and expected state.

The column is defined by ^ ("here") or < ("comment start / first column").

The state is defined by one of the following identifiers:

std: standard / no recognition
str: string
num: number
slc: single line comment
com: multiline comment
esc: escape character
ppc: preprocessor
pps: preprocessor string
opt: operator
ipl: interpolation
ws: whitespace
kwX: keyword group X (X: a..z)

The state identifiers match the correspomnding HTML output CSS class names.


#### Example:

The following C file syntax_test_1.c contains various state indicators:

```c
#include <iostream>
#include "myheader"
// ^ ppc ^^^^^^^^^^ pps       1)  

int main() {
//  ^^^^ kwd                  2)  
/* comment comment comment  
 * <  com ^ ws     ^ com      3)
 */

    int var =   0x1234;    
/*      ^^^ std ^    ^ num */
//          ^ opt             4)
    std::cout << "whatever: " << var <<   "\n";
//               ^          ^ str    ^^opt ^^ esc
    return 0;
    /*  <   kwa               5) */ 
}
```

1. this line contains a test for preprocessor and a preprocessor string.
   The ^ indicators point at the tested string sections of the previous line.
   
2. the keyword group kwd is checked (functions are highlighted as kwd in C syntax)

3. The < points to column 0 as the comment starts there. ws tests for whitespace.

4. A source code line can be tested with various test comments.
   Here the opt test looks for the operator two lines before.

5. < points at column 4, the beginning of the comment.


Running highlight with this file will not produce any additional output, as all 
tests pass.

If you change the kwa in 5) to kwb (or any other state), highlight will print an error
like this:

```
highlight: Could not validate file:
syntax_test_1.c line 17, column 4: got kwa instead of kwb
```