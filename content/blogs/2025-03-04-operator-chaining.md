+++
title = 'Python Operator Chaining… and why you still can’t trust ChatGPT'
date = 2025-03-04T10:43:30+02:00
draft = false
+++

A colleague of mine recently posted the following on a Teams channel which we’ve appropriately named “Something cool I learned today!”:

> `"a" in "abc" is True` will evaluate to `False` (!)
>
> This is because the [Python] interpreter reads this as `"a" in ("abc" is True)`, which is the same as `"a" in False`.

I was about to post [a link giving more details about operator precedence in Python](https://www.geeksforgeeks.org/precedence-and-associativity-of-operators-in-python/), until I looked more closely at the explanation there and noticed something odd: _`in` and `is` have the same precedence_ and have left-to-right associativity (they get processed left-to-right). Following that, I would expect the example above to evaluate to `True`:

```python
"a" in "abc" is True # -> ("a" in "abc") is True
"a" in "abc" # -> True
True is True # -> True
```

But when I actually ran the code, it returned `False`.

Something wasn't lining up, so I decided to do some more digging. I started with a conversation with ChatGPT that went something like this (full thread here - https://chatgpt.com/share/67c629cb-38e8-8013-8daf-290abec2600a):

> Me: Hey, ChatGPT, what's going on here?
>
> ChatGPT: Simple. It’s operator precedence.
>
> Me: But don’t `is` and `in` have the same precedence?
>
> ChatGPT: Good point. This should evaluate to `True`.
>
> Me: But it doesn’t.
>
> ChatGPT: Oooohhh. Riiiighht. It’s _operator chaining_.

That wasn't the _most_ confidence-building conversation I’ve had in my life, so I went to Google next. I found a Stack Overflow post with a similar question and the same explanation that ChatGPT eventually gave: https://stackoverflow.com/questions/31487806/a-in-abc-true-evaluates-to-false

Essentially, because of "operator chaining" the example above, `"a" in "abc" is True`, translates to the following:

```python
'a' in 'abc' and 'abc' is True
```

Since `"abc"` is not `True`, the right half of the expression, and thereby all of it, evaluates to `False`.

This is similar to how Python evaluates `1 < 2 < 3` as `(1 < 2) and (2 < 3)`. Alternatively, using the example from the Python docs:

> `x < y <= z` is equivalent to `x < y and y <= z`

I had known about this rule generally, but did not realize it applied in a case like this.

**Lessons learned:**

1. If two or more comparison operators (`<`, `<=`, `>`, `>=`, `==`, `!=`, `is`, `is not`, `in`, `not in`) are "chained" together, Python inserts `and`s between them to interpret what's going on.
2. ChatGPT is not a Python expert... yet.

For more on this, check out these links:

- GeeksForGeeks on operator precedence: https://www.geeksforgeeks.org/precedence-and-associativity-of-operators-in-python/
- Stack Overflow on the example covered here: https://stackoverflow.com/questions/31487806/a-in-abc-true-evaluates-to-false
- Python docs on operators: https://docs.python.org/3/reference/expressions.html#comparisons
- PEP 535 – Rich comparison chaining: https://peps.python.org/pep-0535/
