# Shell

## Variables

```sh
# Default value
echo ${value:-xa} # xa
value=xyz/ijk/abc
# Head
echo ${value%/*} # xyz/ijk
echo ${value%%/*} # xyz
# Tail
echo ${value#*/} # ijk/abc
echo ${value##*/} # abc
# Truncate
echo ${value:7} # /abc
echo ${value:7:4} # /abc
echo ${value:(-4):4} # /abc
# Replace
echo ${value/abc/cba} # xyz/cba
```

## Shell Variables

- `$#`: count args
- `$n`: nth arg
- `$?`: last exit code
- `$!`: last pid


## Array

```sh
Fruits=('Apple' 'Banana' 'Orange' 'Pear')

echo ${Fruits[0]}           # Element #0
echo ${Fruits[-1]}          # Last element
echo ${Fruits[@]}           # All elements, space-separated
echo ${#Fruits[@]}          # Number of elements
echo ${#Fruits}             # String length of the 1st element
echo ${#Fruits[3]}          # String length of the Nth element
echo ${Fruits[@]:3:2}       # Range (from position 3, length 2)
echo ${!Fruits[@]}          # Keys of all elements, space-separated



Fruits=("${Fruits[@]}" "Grape")         # Push
Fruits+=('Watermelon')                  # Also Push
Fruits=( ${Fruits[@]/Ap*/} )            # Remove by regex match
unset Fruits[2]                         # Remove one item
Fruits=("${Fruits[@]}")                 # Duplicate
Fruits=("${Fruits[@]}" "${Veggies[@]}") # Concatenate

TempFiles=($(ls /tmp))

for i in "${Fruits[@]}"; do
  echo $i
done

if [[ " ${Fruits[*]} " =~ " Banana " ]]; then
  echo "Apple in Fruits"
fi
```

## Dictionary

```sh
declare -A sounds

sounds[dog]="bark"
sounds[cow]="moo"
sounds[bird]="tweet"
sounds[wolf]="howl"

echo ${sounds[dog]} # Dog's sound
echo ${sounds[@]}   # All values
echo ${!sounds[@]}  # All keys
echo ${#sounds[@]}  # Number of elements
unset sounds[dog]   # Delete dog

# Value loop
for val in "${sounds[@]}"; do
  echo $val
done

# Key loop
for key in "${!sounds[@]}"; do
  echo $key
done
```

## Condition

### test

| Code                     | Explain          |
| ------------------------ | ---------------- |
| `[[ -z STRING ]]`        | Empty string     |
| `[[ -n STRING ]]`        | Not empty string |
| `[[ STRING == STRING ]]` | Equal            |
| `[[ STRING != STRING ]]` | Not equal        |
| `[[ STRING =~ STRING ]]` | Regexp           |
| -                        | -                |
| `[[ -e FILE ]]`          | Exists           |
| `[[ -d FILE ]]`          | Directory        |
| `[[ -f FILE ]]`          | File             |
| `[[ -x FILE ]]`          | Executable       |
| `[[ FILE1 -ef FILE2 ]]`  | Same files       |
| -                        | -                |
| `[[ ! EXPR ]]`           | Not              |
| `[[ X && Y ]]`           | And              |
| `[[ X \|\| Y ]]`         | Or               |


### if

```sh
if [[ "foo" == "$1" ]]; then
  echo foo
elif [[ "bar" == "$1" ]]; then
  echo bar
else
  echo "not found"
  exit 1
fi
```

### case
```sh
case "$1" in
  start | up)
    vagrant up
    ;;
  stop | down)
    vagrant up
    ;;

  *)
    echo "Usage: $0 {start|stop}"
    ;;
esac
```

### exitcode
```sh
if grep -q 'alice' /etc/passwd; then
  echo "User alice exists"
fi
```

## Loop

```sh

# index
for i in `seq 1 10`; do
  echo $i 
done

for i in `seq 1 2 10`; do
  echo $i
done

# fs
for f in ./*; do
  echo $f
done

# array
arr=( a b c )
for v in ${arr[@]}; do
  echo $v
done

# lines
cat file.txt | while read l; do
  echo $l
done

# forever
while true; do
  sleep 1
done
```

## Function

Returns value
```sh
foo() {
    local msg='world'
    echo "$1 $msg"
}
result="$(foo hello)"
echo $result
```

Returns boolean
```sh
_is_win() {
    if [[ "$(uname)" =~ "_NT" ]]; then
        return 0
    else
        return 1
    fi
}

if _is_win; then
  echo 'windows'
fi
```

## Value

###  Condition

| Code                | Explain               |
| ------------------- | --------------------- |
| `[[ NUM -eq NUM ]]` | Equal                 |
| `[[ NUM -ne NUM ]]` | Not equal             |
| `[[ NUM -lt NUM ]]` | Less than             |
| `[[ NUM -le NUM ]]` | Less than or equal    |
| `[[ NUM -gt NUM ]]` | Greater than          |
| `[[ NUM -ge NUM ]]` | Greater than or equal |
| `(( NUM < NUM ))`   | Numeric conditions    |

### Math

```sh
a=$(($RANDOM%200))  # Random number 0..199
$((a + 200))        # Add 200 to $a
```

## Redirect

- `>`: equal to  `1>`
- `1>&2`： redirect stdout to stderr
- `2>&1`： redirect stderr to stdout
- `>& file`: redirect stdout/stderr to file

## Heredoc

```sh
cat << EOF | sudo tee -a /etc/sudoers.d/$USER
$USER ALL=(ALL) NOPASSWD:ALL
EOF 
```

- `<<EOF`: will replace variable
- `<<'EOF'`: not replace variable
- `<<-EOF`: will replace variable trim `\t`
- `<<-'EOF'`: not replace variable，trim `\t`

## Yes/No

```sh
read -p "Are you sure (y/n)? " choice
if [ "$choice" = "y" ]; then
  echo choice yes
fi
```

## Common Tools

- Character related: find/sed/awk
- Network related: curl/wget
- Argument parser related: getopt/[argc](https://github.com/sigoden/argc)
- JSON/YAML/TOML related: [jq](https://github.com/jqlang/jq)/[yq](https://github.com/mikefarah/yq)
- Interative related: [gum](https://github.com/charmbracelet/gum)