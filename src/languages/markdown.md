# Markdown

## Heading

### Heading3

#### Heading4

##### Heading5

## Text Style

**bold text**

*italicized text*

> blockquote

## Ordered List
1. First item
2. Second item
3. Third item

## Unordered List
- First item
- Second item
- Third item

## Code

`Inline code` with backticks

```rust
#[derive(Debug)]
pub enum State {
    Start,
    Transient,
    Closed,
}

impl From<&'a str> for State {
    fn from(s: &'a str) -> Self {
        match s {
            "start" => State::Start,
            "closed" => State::Closed,
            _ => unreachable!(),
        }
    }
}
```

## Link

[Link](http://a.com)

## Image

![Image](https://dummyimage.com/200x100/e0e0e0)

## Horizontal Rule
---

## Autolink literals

www.example.com, https://example.com, and contact@example.com.

## Footnote

A note[^1]

[^1]: Big note.

## Strikethrough

~one~ or ~~two~~ tildes.

## Table

| a   | b    |    c |   d   |
| --- | :--- | ---: | :---: |
| 1   | 2    |    3 |   4   |

## Tasklist

* [ ] to do
* [x] done

## Math

$E = mc^2 + 2$