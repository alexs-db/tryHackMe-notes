# All About Yara

> "The pattern matching swiss knife for malware researchers (and everyone else)"  
> — Virustotal, 2020

Yara is a powerful tool that identifies information based on both binary and textual patterns, such as hexadecimal values and strings within files.

## Yara Rules

Yara uses rules to label these patterns. For example, Yara rules are often written to determine if a file is malicious, based on the features or patterns it presents. Strings are a fundamental component of programming languages, used by applications to store data such as text.

**Example:**  
The following Python code prints "Hello World". The text `"Hello World"` is stored as a string.

```python
print("Hello World!")
```

You could write a Yara rule to search for `"Hello World"` in every program on your operating system.

## Why Does Malware Use Strings?

Malware, like any application, uses strings to store textual data. Here are some examples of data stored in strings by different malware types:

| Type        | Data                              | Description                                 |
|-------------|-----------------------------------|---------------------------------------------|
| Ransomware  | `12t9YDPgwueZ9NyMgw519p7AA8isjr6SMw` | Bitcoin wallet for ransom payments          |
| Botnet      | `12.34.56.7`                      | IP address of the Command and Control (C&C) server |

## Caveat: Malware Analysis

Explaining the full functionality of malware is beyond the scope of this room due to the size of the topic. Strings are covered in more detail in "Task 12 - Strings" of my MAL: Introductory room. I am also creating a whole Learning Path for it. If you'd like an introduction while learning the fundamentals, I recommend checking out my room.