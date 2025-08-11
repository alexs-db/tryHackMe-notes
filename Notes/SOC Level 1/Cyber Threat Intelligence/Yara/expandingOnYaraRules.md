# Yara Conditions Continued

Checking whether or not a file exists isn't all that helpful. After all, we can figure that out for ourselves using much better tools for the job.

Yara has a few conditions, which I encourage you to read about at your own leisure. However, I'll detail a few below and explain their purpose.

## Yara Rule Sections

| Keyword   | Description |
|-----------|-------------|
| Meta      | Descriptive information about the rule |
| Strings   | Text or hexadecimal patterns to search for |
| Conditions| Logic that determines when the rule matches |
| Weight    | (Not detailed here) |

---

### Meta

This section of a Yara rule is reserved for descriptive information by the author. For example, you can use `desc` (short for description) to summarise what your rule checks for. Anything within this section does **not** influence the rule itself. Similar to commenting code, it is useful to summarise your rule.

---

### Strings

You can use strings to search for specific text or hexadecimal in files or programs. For example, to search a directory for all files containing "Hello World!", you would create a rule like:

```yara
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"
}
```

We define the keyword `strings` where the string we want to search for ("Hello World!") is stored in the variable `$hello_world`.

Of course, we need a condition to make the rule valid. To use this string as the condition, reference the variable's name:

```yara
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"

    condition:
        $hello_world
}
```

Essentially, if any file has the string "Hello World!", the rule will match. However, this will **only** match if "Hello World!" is found, and will not match "hello world" or "HELLO WORLD".

To solve this, the condition `any of them` allows multiple strings to be searched for:

```yara
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"
        $hello_world_lowercase = "hello world"
        $hello_world_uppercase = "HELLO WORLD"

    condition:
        any of them
}
```

Now, any file with the strings:
1. Hello World!
2. hello world
3. HELLO WORLD

will trigger the rule.

---

### Conditions

We have already used the `true` and `any of them` conditions. Much like regular programming, you can use operators such as:

- `<=` (less than or equal to)
- `>=` (greater than or equal to)
- `!=` (not equal to)

For example, the rule below does the following:

```yara
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!"

    condition:
        #hello_world <= 10
}
```

The rule will:
1. Look for the "Hello World!" string
2. Only match if there are **less than or equal to ten** occurrences of "Hello World!"

---

### Combining Keywords

You can use keywords such as:

- `and`
- `not`
- `or`

to combine multiple conditions. For example, to check if a file has a string and is of a certain size (less than 10 KB and contains "Hello World!"):

```yara
rule helloworld_checker {
    strings:
        $hello_world = "Hello World!" 
        
    condition:
        $hello_world and filesize < 10KB 
}
```

The rule will only match if **both** conditions are true.

#### Example Output

- If the file has "Hello World!" but is larger than 10KB, the rule does **not** match.
- If the file has "Hello World!" and is less than 10KB, the rule **matches**:

```shell
cmnatic@thm:~$ yara myfirstrule.yar mytextfile.txt
helloworld_textfile_checker mytextfile.txt
```

The text within the red box is the name of your rule, and the text within the green box is the matched file.

---

## Anatomy of a Yara Rule

> *A picture showing a Yara rule cheatsheet developed by fr0gger_*

Information security researcher "fr0gger_" has created a handy cheatsheet that breaks down and visualises the elements of a YARA rule (image credits to him). It's a great reference point for getting started!
