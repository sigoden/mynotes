# JQ

## Basic Filters

```
.              Identity (root)
.foo           Object field access
.foo.bar       Nested object field access
."wired-key"   Key with special chars (quoted)
.foo?          Safe access (null if missing)
.[]            Iterate array elements
.[]?           Safe iterate
.[0]           Index by number (also .[0] = .[0] |= ...)
.[-1]          Last element (negative index)
.[1:5]         Array slice from index 1 (inclusive) to index 5 (exclusive)
.[1:]
.[:5]
```

## Array & Object Construction

```
[1, 2, .a]       Build array
{key: .name}     Build object
{($k): .v}         Computed key
{name}           Shorthand for {name: .name}
```

## Operators

### `|`

Pipe the output of a filter as input to the next filter.

```
. | .name           Feed current value into field access
. | map(. + 1)      Feed array into map
keys | .[]          Chain filters — keys then iterate
```

The fundamental composition primitive: everything left of `|` is evaluated, and its output becomes `.` on the right.

```
jq -n '1 | . + 2'                   # 3
jq -n '[1,2,3] | length'            # 3
jq -n '{a:1,b:2} | keys | .[]'      # "a" "b"
```

### `//`

Alternative operator — returns left operand if it is truthy (not `null` or `false`), otherwise returns right operand. Useful for defaults.

```
.a // "default"      Use .a if present, else "default"
null // 42           → 42
false // 42          → 42 (false is falsy too)
"" // "fallback"     → "" (empty string is truthy)
```

### `?`

`?` suppresses errors from a filter (returns nothing on error).  Commonly paired with `//` for fallback defaults.

```
.foo?                 null instead of error if key missing
.[]?                  skip if not an array
.foo?.bar? // "n/a"   chain safe accesses with default
```

```
jq -n '{} | .foo? // "no"'       # "no"
jq -n '5 | .[]? // "empty"'      # "empty"
jq -n 'null | .a?.b? // 0'       # 0
```

### as

Bind the current value (or destructured parts) to a variable for later use.

```
. as $x | $x + 1
.a as $val | {$val}
{a, b} as {$x, $y} | $x + $y
.[] as $item | {item: $item}
```

Variables are prefixed with `$` and stay in scope for the rest of the pipeline.

```
jq -n '1 as $x | $x + 2'            # 3
jq -n '[1,2,3] | . as $a | $a | add' # 6
jq -n '{a:1,b:2} | {a} as {$a} | $a' # 1
```

### `,`

Produce multiple values from a single input as a stream.
Each expression after `,` generates a separate output.

```
.name, .age        Output name and age as separate values
1, 2, 3            Stream of three numbers
[.x, .x * 2]       Build array from multiple expressions
```

```
jq -n '1, 2, 3'                 # 1 2 3 (three lines)
jq -n '{a:1,b:2} | .a, .b'      # 1 2
jq -nc '[1,2,3] | .[], .[]'     # 1 1 2 2 3 3
```

## Arithmetic

```
.a + .b     Add (numbers) or concat (strings/arrays)
.a - .b     Subtract (or set diff: [1,2] - [2,3] → [1])
.a * .b     Multiply (or repeat string: "a" * 3 → "aaa")
.a / .b     Divide (or split string: "a,b" / "," → ["a","b"])
.a % .b     Modulo
```

```
jq -n '3 + 4 * 2'                 # 11  (* before +)
jq -n '[1,2] + [3,4]'             # [1,2,3,4]
jq -n '{"a":1} + {"b":2}'         # {"a":1,"b":2}
jq -n '{"a":1} * {"a":2,"b":3}'   # {"a":2,"b":3}  (merge, rhs wins)
jq -n '"a" * 3'                   # "aaa"
jq -n '"a,b" / ","'               # ["a","b"]
```

## Comparison

```
.a == .b     Equal (type-sensitive, no coercion)
.a != .b     Not equal
.a > .b      Greater than
.a >= .b     Greater or equal
.a < .b      Less than
.a <= .b     Less or equal
```

## Assignment

```
=        Assign
|=       Update value (e.g. .arr |= sort)
+= -= *= /= %=    Arithmetic update-assignment
```

```
jq -n '[1,2,3] | .[0] = 99'       # [99,2,3]
jq -n '{a:1} | .a |= . + 1'       # {"a":2}
jq -n '{a:1} | .a = (.a | . + 1)' # {"a":2}
jq -n '{a:1} | .a += 1'           # {"a":2}
jq -n '{a:10} | .a /= 2'          # {"a":5}
```

## Condition

```
.x > 1                  Keep item if condition true (filters out falsy)
.name == "foo"          String equality
has("foo")              Object has key "foo"
has(0)                  Array has element
.foo != null            Exists check (works for arrays too)
isempty(.[])            Empty array

.a > 1 and .b < 5       Both true
.a > 1 or  .b < 5       Either true
.flag | not             Negate via pipe (preferred idiom)
.a > 1 and (.b | not)   Group with parens
```

```
select(has("foo"))
if .x > 1 then "big" else "small" end
if .x > 1 then "big" elif .x > 0 then "mid" else "small" end
```

## Built-in Functions

### length

Length of string, array, or object.

```
jq -n '"abc" | length'
```

### empty

Return nothing (zero outputs). Useful as a no-op or to suppress output in conditional branches.

```
jq -n 'empty'                           # (no output)
jq -n '[1, empty, 2]'                   # [1, 2]
jq -n 'if true then 42 else empty end'  # 42
```

### type

Return the type of a value as a string.

```
$ jq -nc '[0, false, [], {}, null, "hello"] | map(type)'
["number","boolean","array","object","null","string"]
```

### numbers / strings / booleans / arrays / objects / nulls

Type filter functions — return only values of matching type.

```
jq -n '[1, "a", true, null] | map(numbers)'
jq -n '[1, "a", true, null] | map(strings)'    # [1], ["a"]
```

### tonumber / toboolean / tostring

Convert between string and number.

```
jq -n '"42" | tonumber'
jq -n '"true" | toboolean'
jq -n '42 | tostring'
```

### fromjson / tojson

Parse a JSON string or serialize to JSON string.

```
jq -n '"{\"a\":1}" | fromjson'      # {"a":1}
jq -n '{a:1} | tojson'              # "{\"a\":1}"
```

### pick

Select only specified keys from an object.

```
jq -n '{a:1,b:2,c:3} | pick(.a, .c)'    # {"a":1,"c":3}
```

### del

Delete a key from an object.

```
jq -n '{a:1,b:2} | del(.b)'
```

### paths / setpath / getpath / delpaths

Inspect and manipulate nested paths.

```
jq -n '{a:{b:1},c:2} | paths'
jq -n '{} | setpath(["a","b"]; 1)'                  # {"a":{"b":1}}
jq -n '{"a":{"b":1}} | getpath(["a","b"])'          # 1
jq -n '{"a":{"b":1,"c":2}} | delpaths([["a","b"]])' # {"a":{"c":2}}
```

### add

Sum array elements.

```
jq -n '[1,2,3] | add'                # 6
jq -n '[] | add'                     # null
```

### keys / keys_unsorted

Return the keys of an object (sorted/insertion-order).

```
jq -n '{b:1,a:2} | keys'           # ["a","b"]
jq -n '{b:1,a:2} | keys_unsorted'  # ["b","a"]
```

### to_entries / from_entries / with_entries

Convert between objects and arrays of key-value entries.

```
jq -n '{a:1} | to_entries'
jq -n '[{"key":"a","value":1}] | from_entries'
jq -n '{a:1,b:2} | with_entries(.value *= 2)' 
```

### map / map_values

Apply a filter to each array element / object value.

```
jq -n '[1,2,3] | map(. * 2)'
jq -n '{a:1,b:2} | map_values(. + 1)'
```

### select

Filter items by condition.

```
jq -n '[1,2,3] | map(select(. > 1))'
```

### first / last

Return first or last element of an array.

```
jq -n '[1,2,3] | first'             # 1
jq -n '[1,2,3] | last'              # 3
```

### reverse

Reverse an array.

```
jq -n '[1,2,3] | reverse'           # [3,2,1]
```

### sort / sort_by

Sort array; `sort_by(f)` sorts by the result of applying `f` to each element.

```
jq -n '[3,1,2] | sort'
jq -n '[{"age":2},{"age":1}] | sort_by(.age)'
```

### group_by

Group elements into array of arrays based on a key expression.

```
$ jq -nc '[{"name":"a","type":"x"},{"name":"b","type":"y"},{"name":"c","type":"x"}] | group_by(.type)'
[[{"name":"a","type":"x"},{"name":"c","type":"x"}],[{"name":"b","type":"y"}]]
```

### unique / unique_by

Remove duplicate elements. `unique` uses value equality; `unique_by(f)` uses the result of `f` as the key.

```
jq -n '[1,2,1,3] | unique'                          # [1,2,3]
jq -n '[{"x":1},{"x":2},{"x":1}] | unique_by(.x)'   # [{"x":1},{"x":2}]
```

### min / max / min_by / max_by

Find minimum or maximum values.

```
jq -n '[3,1,2] | min'              # 1
jq -n '[3,1,2] | max'              # 3
jq -n '[{"x":2},{"x":1}] | min_by(.x)'  # {"x":1}
```

### flatten

Flatten nested arrays to a specified depth (default: infinite).

```
jq -n '[[1,[2]],[3]] | flatten'    # [1,2,3]
jq -n '[[1,[2]],[3]] | flatten(1)' # [1,[2],3]
```

### indices

Find all indices where a value/pattern occurs.

```
jq -n '"abcabc" | indices("b")'     # [1,4]
jq -n '[1,2,1,2] | indices(1)'      # [0,2]
```

### inputs

Read remaining input values (useful with `-n` for streaming).

```
printf '1\n2\n3\n' | jq -n '[inputs] | add'    # 6
```

### range

Generate a sequence of numbers.

```
jq -n 'range(3)'                    # 0 1 2
jq -n 'range(1; 5)'                 # 1 2 3 4
jq -n 'range(0; 10; 3)'             # 0 3 6 9
```

### repeat

Repeatedly apply a filter, emitting each result.

```
jq -n '0 | repeat(.+1) | limit(3; .)'  # 1 2 3
```

### limit

Limit the number of outputs from a stream.

```
jq -n '[1,2,3,4,5] | limit(3; .[])'    # 1, 2, 3
```

### reduce

Accumulate array elements into a single value. Takes `(init; update)` where
`.` is the accumulator and `$item` is each element.

```
jq -n '[1,2,3] | reduce .[] as $item (0; . + $item)'      # 6
```

### foreach

Iterate with state and emit a value per element. Takes `(init; update; extract)`.

```
$ jq -nc '[1,2,3] | foreach .[] as $item (0; . + $item; {idx: ., sum: .})'     
{"idx":1,"sum":1}
{"idx":3,"sum":3}
{"idx":6,"sum":6}
```

### while / until

Loop constructs. `while(cond; update)` emits values while cond is true; `until(cond; update)` loops until cond is true then outputs result.

```
jq -n '[0, while(.<10; .+1)]'       # [0,1,2,3,4,5,6,7,8,9,10]
jq -n '0 | until(.>10; .+1)'        # 11
```

### any / all

Check if any/all elements satisfy a condition.

```
jq -n '[1,2,3] | any(. > 2)'       # true
jq -n '[1,2,3] | all(. > 0)'       # true
jq -n '[1,2,3] | any'              # true (any truthy)
jq -n '[] | any'                   # false
```

### contains / inside

Check if a value contains another (strings, arrays, objects).

```
jq -n '"foobar" | contains("foo")'  # true
jq -n '[1,2,3] | contains([1,2])'   # true
jq -n '{"a":1,"b":2} | contains({a:1})'  # true
```

`inside` is the inverse — checks if input is inside argument.

```
jq -n '"foo" | inside("foobar")'    # true
```

### walk

Walk the entire tree and apply a filter `f` to every node (bottom-up).

```
$ jq -nc '{"a":{"b":1},"c":[2,3]} | walk(if type == "number" then . * 2 else . end)'
{"a":{"b":2},"c":[4,6]}
```

### debug

Print value to stderr, pass through unchanged. Useful for tracing in pipelines.

```
jq -n '1, 2 | debug'
```

stderr output: `["DEBUG:",1]` `["DEBUG:",2]`

### try / catch / error

Handle errors without halting the pipeline. `try expr` silently suppresses
errors; `try expr catch handler` runs `handler` with the error message as `.`.

```
try .a.b                               # null if path missing (silent)
try .a.b catch "n/a"                   # null if path missing (explicit)
try error("fail") catch "caught: \(.)" # "caught: fail"
```

## Format Strings

- `@json`: Format value as JSON string, e.g. `jq -nc '42 | @json'` → `"42"`
- `@csv`: Convert array to CSV line, e.g. `jq -nr '["a","b"] | @csv'` → `"a","b"`
- `@tsv`: Convert array to TSV line, e.g. `jq -nr '["a","b"] | @tsv'` → `a\tb`
- `@html`: Escape HTML special chars, e.g. `jq -nr '"<script>" | @html'` → `&lt;script&gt;`
- `@uri`: Percent-encode URI component, e.g. `jq -nr '"hello world" | @uri'` → `hello%20world`
- `@base64`: Base64 encode string, e.g. `jq -nr '"hello" | @base64'` → `aGVsbG8=`
- `@base64d`: Base64 decode string, e.g. `jq -nr '"aGVsbG8=" | @base64d'` → `hello`
- `@sh`: Escape for shell use (single-quote wrap), e.g. `jq -nr '"it'\''s" | @sh'` → `'it'\''s'`
- `@text`: Output as plain text, e.g. `jq -nc '42 | @text'` → `"42"`

## Regex

### test

Check if string matches regex — returns `true`/`false`.

```
jq -n '"hello 42" | test("\\d+")'
jq -n '"Hello" | test("^[a-z]+$"; "i")'
```

### match

Return match objects with details (offset, length, captures).

```
$ jq -nc '"abc123" | match("[a-z]+|\\d+"; "g")'
{"offset":0,"length":3,"string":"abc","captures":[]}
{"offset":3,"length":3,"string":"123","captures":[]}
```

### capture

Extract named groups into an object.

```
$ jq -nc '"2024-01-15" | capture("(?<y>\\d+)-(?<m>\\d+)-(?<d>\\d+)")'
{"y":"2024","m":"01","d":"15"}
```

### split / splits

Split string by regex. `split` returns array; `splits` streams results.

```
$ jq -rn '"a,b" | split(",")'
[
  "a",
  "b"
]

$ jq -rn '"a,b" | splits(",")'
a
b
```

### sub / gsub

Replace matched substrings. `sub` replaces first match, `gsub` replaces all.

```
jq -rn '"hello world" | sub("\\w+"; "***")'     # *** world 
jq -rn '"hello world" | gsub("\\w+"; "***")'    # *** ***
```

Capture groups in replacement via `$1`, `$2`, etc.:

```
jq -rn '"2024-01-15" | sub("(?<y>\\d+)-(?<m>\\d+)-(?<d>\\d+)"; "\(.y).\(.m).\(.d)")'
```

## Datetime

### now

Return the current time as Unix epoch seconds (float).

```
jq -n 'now'               # 1705314600.22693
```

### strftime 

Formats a datetime (epoch seconds) to a string using a format specifier.

```
jq -n 'now | strftime("%Y-%m-%d")'                # "2024-05-18"
```

## strptime

Parses a string to epoch seconds using a format specifier.

```
jq -n '"2024-01-15" | strptime("%Y-%m-%d")'       # [2024,0,15,0,0,0,1,14]
```

### fromdate

Parse an ISO 8601 datetime string to Unix epoch seconds.

```
jq -n '"2024-01-15T10:30:00Z" | fromdate'   # 1705314600
```

### todate

Convert Unix epoch seconds to an ISO 8601 datetime string.

```
jq -n 'now | todate'                        # "2024-05-18T12:34:56Z"
```

### strflocaltime

Like `strftime` but formats in local timezone.

```
jq -n 'now | strflocaltime("%Y-%m-%d %H:%M:%S")'
```

### mktime / gmtime

Convert between array representation and epoch seconds.

```
jq -n '[2024,0,15,0,0,0,1,14] | mktime'    # 1705305600 (local)
jq -n '1705305600 | gmtime'                # [2024,0,15,0,0,0,1,14]
```

## CLI Options

### `-n`

Use `null` as input — build data from scratch.

```
jq -n 'range(3)'
```

### `-R`

Read input as raw strings (not JSON). Each line becomes a string.

```
printf 'foo\nbar\n' | jq -R '.'    # "foo" "bar"
```

### `-s`

Slurp — read the entire input stream into a single array.

```
printf '{"x":1}\n{"x":2}\n' | jq -s 'map(.x) | add'    # 3
```

### `-r`

Raw output — print strings without JSON quotes/escaping.

```
$ jq -n '"hello"'
"hello"
$ jq -rn '"hello"'
hello
```

### `-b`

Brief output — omit trailing newline. (e.g. `\r` on Windows)

```
$ jq -nb '42'
```

### `-c`

Compact output — each output on a single line.

```
$ jq -n '[1,2]'
[
  1,
  2
]
$ jq -nc '[1,2]'
[1,2]
```

### `-e`

Exit with code 0 if the last output is truthy, 1 otherwise.

```
jq -e 'has("foo")' data.json
```

### `--arg`

Set a variable `$name` to a string value.

```
jq -n --arg name foo '{$name}'    # {"name":"foo"}
```

### `--argjson`

Set a variable `$name` to a JSON value.

```
jq -n --argjson n 42 '{$n}'    # {"n":42}
```

### `--args`

Pass positional arguments as `$ARGS.positional` (strings).

```
jq -n '$ARGS.positional' --args hello world    # ["hello","world"]
```

### `--jsonargs`

Pass positional arguments as JSON values.

```
jq -n '$ARGS.positional' --jsonargs 1 2 3    # [1,2,3]
```

## Misc

### `$ENV`

$ENV is an object representing the environment variables as set when the jq program started. $ENV is readonly

```
$ENV               Object of all environment variables
$ENV | keys        List all env var names
$ENV.PATH          Access env var via variable
```


### `$ARGS.named`

Access named arguments passed via `--arg` / `--argjson` programmatically.

```
jq -n --arg a 1 --argjson b 2 '$ARGS.named'    # {"a":"1","b":2}
```

### `$ARGS.positional`

Access positional arguments passed via `--args` / `--jsonargs` programmatically.

```
jq -n '$ARGS.positional' --args hello world  # ["hello","world"]
jq -n '$ARGS.positional' --jsonargs 1 2 3    # [1,2,3]
```

### Multi Branches (Conditional)

```
  [
      { cond: (.type == "string"), result: { key: "foo" }},
      { cond: (.type == "boolean"), result: { key: false }},
      { cond: (.type == "number"), result: { key: 3.14 }},
      ...
      { cond: true, result: { key: null } } # default
  ] | map(select(.cond) | .result) | first;
```

Simplify: `if .type == "string" then {key: "foo"} elif .type == "number" then {key: 3.14} else {key: null} end`

### User Defined Functions

```
def add1: . + 1;
def map(f): [.[] | f];          # f is a filter parameter (def filter)
def add(x; y): x + y;           # Multi-arg (note semicolons)
```
