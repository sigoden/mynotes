# Shell

## Variables

```sh
# Default value
echo ${value:-xa} # xa
value=xyz/z-a/abc
# Head
echo ${value%/*} # xyz/z-a
echo ${value%%/*} # xyz
# Tail
echo ${value#*/} # z-a/abc
echo ${value##*/} # abc
# Truncate
echo ${value:7} # /abc
echo ${value:7:4} # /abc
echo ${value:(-4):4} # /abc
# Replace
echo ${value/a/A} # xyz/z-A/abc
echo ${value//a/A} # xyz/z-A/Abc

str="Hello World"
echo ${str,,}   # hello world (Lowercase)
echo ${str^^}   # HELLO WORLD (Uppercase)
echo ${str^}    # Hello World (Only first char)
```

## Shell Variables

- `$@`: args list (recommended)
- `$*`: spconcatenate into a string (easy to mess up)
- `$#`: count args
- `$n`: nth arg
- `$?`: last exit code
- `$!`: last pid


## Array

```sh
fruits=(Apple Banana Orange Pear)

echo "${fruits[0]}"           # Element #0
echo "${fruits[-1]}"          # Last element
echo "${fruits[@]}"           # All elements, space-separated
echo "${#fruits[@]}"          # Number of elements
echo "${#fruits}"             # String length of the 1st element
echo "${#fruits[3]}"          # String length of the Nth element
echo "${fruits[@]:1:2}"       # Range (from index 1 (second element), length 2)
echo "${!fruits[@]}"          # All keys: 0 1 ...

fruits=("${fruits[@]}" Grape)          # Push
fruits+=(Watermelon)                   # Also Push
unset fruits[2]                         # Remove one item
fruits=("${fruits[@]}")                 # Duplicate
fruits=("${fruits[@]}" "${Veggies[@]}") # Concatenate

for i in "${fruits[@]}"; do
  echo "$i"
done

if [[ " ${fruits[*]} " =~ " Banana " ]]; then
  echo "Banana in fruits"
fi
```

## Dictionary

```sh
declare -A sounds

sounds[dog]="bark"
sounds[cow]="moo"
sounds[bird]="tweet"
sounds[wolf]="howl"

echo "${sounds[dog]}" # Dog's sound
echo "${sounds[@]}"   # All values
echo "${!sounds[@]}"  # All keys
echo "${#sounds[@]}"  # Number of elements
unset sounds[dog]     # Delete dog

# Value loop
for val in "${sounds[@]}"; do
  echo "$val"
done

# Key loop
for key in "${!sounds[@]}"; do
  echo "$key"
done
```

## Condition

### Test

| Code                    | Explain               |
| ----------------------- | --------------------- |
| `[[ -z $v ]]`           | Empty string          |
| `[[ -n $v ]]`           | Not empty string      |
| `[[ $v == abc ]]`       | Equal                 |
| `[[ $v != abc ]]`       | Not equal             |
| `[[ $v =~ ^[0-9]+$ ]]`  | Regexp                |
| -                       | -                     |
| `[[ -e FILE ]]`         | Exists                |
| `[[ -d FILE ]]`         | Directory             |
| `[[ -f FILE ]]`         | File                  |
| `[[ -x FILE ]]`         | Executable            |
| `[[ FILE1 -ef FILE2 ]]` | Same files            |
| -                       | -                     |
| `[[ ! EXPR ]]`          | Not                   |
| `[[ X ]] && [[ Y ]]`    | And                   |
| `[[ X ]] \|\| [[ Y ]]`  | Or                    |
| -                       | -                     |
| `[[ NUM -eq NUM ]]`     | Equal                 |
| `[[ NUM -ne NUM ]]`     | Not equal             |
| `[[ NUM -lt NUM ]]`     | Less than             |
| `[[ NUM -le NUM ]]`     | Less than or equal    |
| `[[ NUM -gt NUM ]]`     | Greater than          |
| `[[ NUM -ge NUM ]]`     | Greater than or equal |
| `(( NUM < NUM ))`       | Numeric conditions    |


#### Test equal with glob

- `*`: Any length of any character, e.g. `[[ "$v" == *abc*  ]]`
- `?`: any single character, e.g. `[[ "$v" == rc? ]] `
- `[]` any character in set, e.g. `[[ "$v" == [a-z] ]]`
- `[!]`: any character not in set, e.g. `[[ "$v" == [!0-9] ]]`

#### Test equal with extglob

```
shopt -s extglob
```

- `?(pattern)`：0 or 1
- `*(pattern)`：0 or many
- `+(pattern)`：1 or many, e.g. `[[ "$v" == +([0-9]) ]]`
- `@(pattern)`：exact, e.g. `[[ "$v" == @(apple|banana|orange) ]]`
- `!(pattern)`: exclude, e.g. `[[ "$v" == !(tmp|cache) ]]`

### Use if expr

```sh
if [[ "foo" == "$1" ]]; then
  echo foo
elif [[ "bar" == "$1" ]]; then
  echo bar
else
  echo "not found"
  exit 1
fi

if grep -q 'alice' /etc/passwd; then
  echo "User alice exists"
fi

read -r -p "Are you sure (y/n)? " choice
if [ "$choice" = "y" ]; then
  echo choice yes
fi
```

### Use case expr
```sh
case "$1" in
  start | up)
    echo start
    ;;
  stop | down)
    echo stop
    ;;

  *)
    echo "Usage: $0 {start|stop}"
    ;;
esac
```

## Loop

```sh

# index
for i in {1..10}; do
  echo "$i"
done

for ((i=1; i<=10; i+=2)); do
  echo "$i"
done

# fs
for f in ./*; do
  echo "$f"
done

# array
IFS=, read -r -a arr <<< "a,b,c"
for v in "${arr[@]}"; do
  echo "$v"
done

# lines
while IFS= read -r l; do
  echo "$l"
done < file.txt

# forever
while true; do
  sleep 1
done
```

## Function

Shell function has no real return value

- `return` can only return an exit code (0-255)
- `echo` is the actual way to return data

```sh
foo() {
    local msg='world'
    echo "$1 $msg"
}
result="$(foo hello)"
echo "$result"
```

```sh
_is_win() {
    [[ "$(uname)" =~ "_NT" ]]
}

if _is_win; then
    echo 'windows'
fi
```

## Math

```sh
$((1 + 2))
$((10 - 3))
$((4 * 5))
$((20 / 3))   # Integer division => 6
$((RANDOM % 100))  # Random number 0..99

$((a = 5 + 3))
$((a++))
$((a--))
$((a += 10))
$((a *= 2))

((a == b))
((a != b))
((a < b))
((a <= b))
((a > b))
((a >= b))
```

## Redirect

- `<`: stdin
- `>`: stdout
- `>>`: stdout in append mode
- `2>`: stderr
- `1>&2`： redirect stdout to stderr
- `2>&1`： redirect stderr to stdout
- `>file 2>&1`: redirect stdout/stderr to file

## Here String

```sh
grep foo <<<"$HOME"
```

## Here Document

```sh
cat << EOF | sudo tee -a /etc/sudoers.d/$USER
$USER ALL=(ALL) NOPASSWD:ALL
EOF 
```

- `<<EOF`: will replace variable
- `<<'EOF'`: not replace variable
- `<<-EOF`: will replace variable trim `\t` only
- `<<-'EOF'`: not replace variable，trim `\t` only

## Process Substitution

- `<(cmd)`：Use command stdout as a file, e.g. `diff <(sort a.txt) <(sort b.txt)`
- `>(cmd)`: Write to the command stdin, e.g. `tar cf >(gzip > out.tar.gz) dir/`

## Subshell 

Modification of internal variables does not affect external ones

```sh
(cd /tmp && touch test.txt)
```

## Command group

Executes in the current shell, used only for logical grouping

```sh
{ echo "header"; cat data.txt; echo "footer"; } > output.txt
```

> There must be a space after `{`, and a semicolon or newline before `}`

## Set Options

| Option / Syntax     | Description                                               |
| ------------------- | --------------------------------------------------------- |
| `set -e`            | Exit immediately if any command returns a non-zero status |
| `set -u`            | Treat unset variables as an error and exit                |
| `set -x`            | Print each command before executing it (debug mode)       |
| `set -o noclobber`  | Prevent `>` from overwriting existing files               |
| `set -o pipefail`   | Pipeline fails if any command in the pipeline fails       |
| `set -euo pipefail` | Common “strict mode” for safer scripts                    |

## Misc

- `source`: Used for **Running script files in the current Shell environment**

```sh
source file.sh
. file.sh
```

- `trap`: Used to capture signals (such as EXIT, INT, TERM) and automatically execute specified cleanup commands when the script exits or is interrupted.

```sh
cleanup() {
    rm -f "$tmpfile"
    echo "Cleaned up on exit"
}
trap cleanup EXIT       # Execute when the script exits (including normal and error exits)
trap 'echo Ctrl+C' INT  # Capture SIGINT
```

- Export all environments/variables/functions/options of the current shell

```sh
( set -o posix; set )
```

- Load dotenv file

```sh
[ -f .env ] && { set -a; source .env; set +a; }
```

## Common Tools

- Character related: find/sed/awk
- Network related: curl/wget
- Argument parser related: getopt/[argc](https://github.com/sigoden/argc)
- JSON/YAML/TOML related: [jq](https://github.com/jqlang/jq)/[yq](https://github.com/mikefarah/yq)
- Interative related: [gum](https://github.com/charmbracelet/gum)
