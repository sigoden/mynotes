# vim hotkeys

## switch mode

| key      | description          |
| -------- | -------------------- |
| `i`      | insert before cursor |
| `I`      | insert at line start |
| `a`      | append after cursor  |
| `A`      | append at line end   |
| `o`      | new line below       |
| `O`      | new line above       |
| `v`      | visual mode          |
| `V`      | visual line          |
| `Ctrl-v` | visual block         |
| `gv`     | reselect last visual |
| `R`      | replace mode         |
| `Esc`    | normal mode          |

## move

| key         | description               |
| ----------- | ------------------------- |
| `h j k l`   | left down up right        |
| `w` / `W`   | next word / WORD          |
| `b` / `B`   | previous word / WORD      |
| `e` / `E`   | next word / WORD end      |
| `ge` / `gE` | prev word / WORD end      |
| `0`         | first column              |
| `^`         | first non-blank           |
| `$`         | end of line               |
| `gg`        | top of file               |
| `G`         | bottom of file            |
| `%`         | matching pair             |
| `{` / `}`   | previous / next paragraph |


## search & replace

| key            | description                     |
| -------------- | ------------------------------- |
| `/pat`         | forward search                  |
| `?pat`         | backward search                 |
| `n N`          | next/prev result                |
| `* #`          | search word under cursor        |
| `g* g#`        | partial word search             |
| `f` / `F`      | find char forward/backward      |
| `t` / `T`      | until char forward/backward     |
| `;` / `,`      | f/t repeat / reverse            |
| `:s/a/b/g`     | replace in current line         |
| `:%s/a/b/gc`   | replace in whole file (confirm) |
| `:%s/\Ca/b/gc` | case-sensitive match            |
| `:%s/\ca/b/gc` | case-insensitive match          |

## operate

| key         | description          |
| ----------- | -------------------- |
| `d`         | delete               |
| `c`         | change               |
| `y`         | yank                 |
| `>` / `<`   | indent / outdent     |
| `=`         | format               |
| `gu` / `gU` | lowercase /uppercase |

examples：

| key   | description       |
| ----- | ----------------- |
| `dw`  | delete word       |
| `ciw` | change inner word |
| `yy`  | yank line         |
| `dd`  | delete line       |
| `cc`  | change line       |
| `d$`  | delete to EOL     |


## text-object

| key       | description  |
| --------- | ------------ |
| `iw` `aw` | inner/a word |
| `i"` `a"` | quote        |
| `i'` `a'` | single quote |
| `i(` `a(` | paren        |
| `i{` `a{` | braces       |
| `i[` `a[` | bracket      |
| `it` `at` | tag          |
| `ip` `ap` | paragraph    |

例如：

| key   | description        |
| ----- | ------------------ |
| `ciw` | change word        |
| `di(` | delete inside ()   |
| `ya"` | yank around quotes |


## normal mode


| key       | description                           |
| --------- | ------------------------------------- |
| `x` / `X` | delete character (forward / backward) |
| `r`       | replace single char                   |
| `dd`      | delete line                           |
| `cc`      | change line                           |
| `C`       | change to end of line                 |
| `yy`      | yank line                             |
| `p` / `P` | paste (after / before cursor)         |
| `~`       | toggle case                           |
| `J`       | join lines                            |
| `.`       | repeat last change                    |


## insert mode

| key      | description                |
| -------- | -------------------------- |
| `<C-w>`  | delete previous word       |
| `<C-u>`  | delete to line start       |
| `<C-r>"` | insert from register       |
| `<C-o>`  | execute one normal command |

## visual mode

| key       | description                           |
| --------- | ------------------------------------- |
| `d`       | delete                                |
| `c`       | change selection (insert mode on all) |
| `y`       | yank                                  |
| `>` / `<` | indent / outdent                      |
| `=`       | auto-indent                           |
| `r{char}` | replace all selected with char        |
| `I` / `A` | block insert at start/end of lines    |
| `:`       | auto `:'<,'>`                         |
| `o`       | swap visual endpoint                  |

## jump list / change list

| key      | description        |
| -------- | ------------------ |
| `Ctrl-o` | jump back          |
| `Ctrl-i` | jump forward       |
| `gd`     | jump to definition |
| `gi`     | jump last insert   |
| `g;`     | older change       |
| `g,`     | newer change       |
| `` `. `` | jump last edit     |


## mark

| key      | description         |
| -------- | ------------------- |
| `ma`     | set mark            |
| `` `a `` | jump exact          |
| `'a`     | jump line           |
| `` `` `` | previous jump exact |
| `''`     | previous jump line  |


## register

| key    | description      |
| ------ | ---------------- |
| `"ayy` | yank to register |
| `"ap`  | paste register   |
| `:reg` | show registers   |


## undo redo

| key      | description |
| -------- | ----------- |
| `u`      | undo        |
| `Ctrl-r` | redo        |


## macro

| key    | description   |
| ------ | ------------- |
| `qa`   | record macro  |
| `q`    | stop          |
| `@a`   | play macro    |
| `@@`   | repeat        |
| `10@a` | play 10 times |


## folding / z

| key         | description                             |
| :---------- | :-------------------------------------- |
| `za`        | toggle fold                             |
| `zo` / `zc` | open / close fold                       |
| `zO` / `zC` | recursive open / close all nested folds |
| `zR` / `zM` | open / close **all** folds              |
| `zz`        | scroll to center screen                 |
| `zt`        | scroll to top (top)                     |
| `zb`        | scroll to bottom (bottom)               |


## window / tab


| key            | description          |
| -------------- | -------------------- |
| `<C-w>h/j/k/l` | split nav            |
| `<C-w>v`       | vsplit               |
| `<C-w>s`       | split                |
| `<C-w>c`       | close  windows       |
| `<C-w>w`       | cycle to next window |
| `gt`           | next tab             |
| `gT`           | prev tab             |


## command mode

## files

| command   | description       |
| --------- | ----------------- |
| `:w`      | save              |
| `:x`      | save and quit     |
| `:q!`     | force quit        |
| `:wa`     | save all          |
| `:xa`     | save all and quit |
| `:e file` | open file         |

## global

| command          | description                                |
| ---------------- | ------------------------------------------ |
| `:g/pat/cmd`     | execute cmd on matching lines              |
| `:v/pat/cmd`     | execute cmd on non-matching lines          |
| `:%norm cmd`     | execute normal command on all lines        |
| `:%!cmd`         | modify with shell command                  |
| `:!cmd`          | run shell command                          |
| `:r !cmd`        | insert shell output                        |
| `:w !sudo tee %` | save current file as root (classic rescue) |
