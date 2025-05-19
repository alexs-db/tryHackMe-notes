# Arbitrary Control Flow Patterns

To craft arbitrary control flow patterns, we can leverage mathematics, logic, and other complex algorithms to inject different control flows into a malicious function.

Predicates can be used to create these complex logical or mathematical algorithms. A predicate refers to the decision-making part of a function that returns `true` or `false`. At a high level, a predicate is similar to the condition in an `if` statement that determines whether a code block will execute.

## Opaque Predicates

In obfuscation, **opaque predicates** are used to control a known output and input. According to the paper *Opaque Predicate: Attack and Defense in Obfuscated Binary Code*:

> "An opaque predicate is a predicate whose value is known to the obfuscator but is difficult to deduce. It can be seamlessly applied with other obfuscation methods such as junk code to turn reverse engineering attempts into arduous work."

Opaque predicates fall under bogus control flow and probabilistic control flow methods. They can be used to arbitrarily add logic to a program or refactor the control flow of an existing function.

> **Note:** The topic of opaque predicates requires a deeper understanding of mathematics and computing principles. Here, we will observe a common example rather than cover it in-depth.

## Example: The Collatz Conjecture

The **Collatz Conjecture** is a well-known mathematical problem often used as an example of an opaque predicate. It states that if two arithmetic operations are repeated, they will return one from every positive integer. Since we know it will always output one for a positive integer input, it is a viable opaque predicate.

For more information, see the [Collatz Problem](https://en.wikipedia.org/wiki/Collatz_conjecture).

Below is an example of the Collatz conjecture applied in Python:

```python
x = 0
while x > 1:
    if x % 2 == 1:
        x = x * 3 + 1
    else:
        x = x / 2
    if x == 1:
        print("hello!")
```

In this code snippet, the Collatz conjecture will only perform its mathematical operations if `x > 1`. From the definition of the Collatz problem, it will always return one for a positive integer input, so the statement will always return `true` if `x` is a positive integer greater than one.

To prove the efficacy of this opaque predicate, we can observe its Control Flow Graph (CFG). If this is what an interpreted function looks like, imagine how complex a compiled function may appear to an analyst.

---

Using the knowledge from this task, put yourself in the shoes of an analyst and attempt to decode the original function and output of the code snippet above.

If you correctly follow the print statements, it will result in a flag you can submit.