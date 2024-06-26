# JQ

## Common Options

```
-r       Output without quotes and escapes
-c       Compact output
--arg    Set $name to the string value;
```

## Multi Branches

```
  [
      { condition: (.type == "string"), result: { $key: "foo" }},
      { condition: (.type == "boolean"), result: { $key: false }},
      { condition: (.type == "number"), result: { $key: 3.14 }},
      ...
      { condition: true, result: { $key: null } }
  ] | map(select(.condition) | .result) | first;
```

## Use `,` as stream constructor

```
$ echo '{"a":3,"b":4}' | jq '.a,.b'
3
4
```

## User defined functions

```
```