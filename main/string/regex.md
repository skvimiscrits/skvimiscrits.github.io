---
layout: page
title: Regular expressions
parent: Strings
math: true
nav_order: 6
---

Regular Expression (or RegEx, or simply regex) is a string which describes a pattern.
A string may *match* that pattern.
As a simple example, String `abc` matches the pattern `a.c`.

## Pattern syntax

### Literal matching

An expression formed of *non-reserved* characters is a literal pattern
and will be matched exactly. These have unique matches.

For example, `abc` is a valid pattern and only one string `abc` will match
it.

> Reserved characters hold a special meaning in a regex.
> All common implementations of regex usually follow the same constructs.
> In order to use reserved characters as literals, they need to be escaped or quoted.
> Commonly reserved characters are:
> `<>[](){}.\$!|?^*+-=`

### Wildcard

Character `.` denotes wildcard - meaning that any character will match it.

From the initial example: `abc` matches `a.c` because `b` is accepted in place of `.`

To literally match a `.`, it has to be escaped or quoted. For example,
string `a.b` will be the only one matching regex `a\.b`. Same holds
for regex `a\Q.\Eb` (quoting is possible in some implementations like Java).

### Quantifiers

There are multiple ways of specifying *how* many times should a character
(or a *group*) occur.
- Exactly once: this is the default behaviour
- One or more times: Use `+` on the character or group. For example,
  strings `a`, `aa`, `aaa` match regex `a+`.
- Zero or more times: Use `*`. For example, string `""` (empty string)
  matches regex `a*`
- Zero or once: Use `?`. For example, strings `a` and `""` match
  regex `a?`
- Custom range: Use `{n,m}` (between `n` and `m` times inclusive) or `{,m}` (not greater than `m` times)
  or `{n,}` (not less than `n` times). For example, regex `a{0,1}` is equivalent
  to `a?`.

An important thing to note is that counts can be used not only on a character
but on *any valid regex* (in a group form). For example regex `(a.c)+` means that `a.c` should
occur one or more times. String `abcadc` matches this pattern.
Note that `(...)` denotes a group. Regex `(a.c)+` is not same as `a.c+`.

### Union

Character `|` is used to denote union. For example regex `a|b` means
`a` or `b`. This can be used on groups too - `(ab)+|c` means that either
`ab` occurring more than once or just `c`. It can be extended to denote
union of more than two groups - like `a|b|c`.

### Character classes

Character classes allow definining a regex (or part of it), especially when
dealing with ranges, in a convenient way. A character class is defined
as `[...]`. A character class **always matches a single character**.

For example, regex `a|b|c|d|e|f|g` can be defined as `[a-g]`.

> We can use the counts here: `[a-z]+` or `[a-z]{1,2}` will hold their usual meaning.

Multiple ranges can be specified in the same block. For example `[a-zA-Z0-9]`
is a valid regex.

Ranges need not cover the whole domain: `[a-c]` is a valid regex.

There is no compulsion on using ranges: `[aceg]` is equivalent to `a|c|e|g`.

Negation (`^`) can be used to define *all except* notion. `[^aceg]` means
any character except `a,c,e,g`.

> Some implementations also support *intersection* using `&&`
> - `[a-z&&[xyz]]`: `a` to `z` and one of `x,y,z`. Equivalent to `xyz`.
> - `[a-z&&[^xy]]`: Set subtraction. `[a-wz]` is equivalent.
> - `[a-z&&[^m-p]]`: Set subtraction. `[a-lq-z]` is equivalent.

Usually, implementations provide predefined character classes
- `\d`: digit (equivalent to `[0-9]`)
-	`\D`: not digit (equivalent to `[^0-9]`)
-	`\s`: whitespace character(s) (equivalent to `[ \t\n\x0B\f\r]`)
-	`\S`: non whitespace character (negation of the above)
-	`\w`: word (equivalent to `[a-zA-Z0-9_]`)
-	`\w`: non word (negation of the above)

For example, regex `\d+(\.\d+)?` matches integers or decimal numbers.

## Usage examples

### Python

```py
import re
p = re.compile('\d+(\.\d+)?')
m = p.search(input())
# p.search to find match anywhere in the string
# p.match to find match at beginning
# p.fullmatch for matching whole input
if m != None:
    print('Matched: ', m.span()) # 0,7
else:
    print('No match')

```

To find all matches `findall` can be used.
The output list is based on capturing groups used (if any) in the regex.
```py
import re
p = re.compile('\d+(\.\d+)?')
m = p.findall(input())
print(m)

# Input: 1.23 34.5 3.2
# Output: ['.23', '.5', '.2'] because of capture group (\.\d+)
```

***