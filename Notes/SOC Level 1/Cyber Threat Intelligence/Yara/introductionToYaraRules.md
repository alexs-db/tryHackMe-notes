# Your First Yara Rule

Yara uses a proprietary rule language that is easy to learn but challenging to master. The effectiveness of your rule depends on your understanding of the patterns you want to search for.

## Using Yara Rules

Every Yara command requires two arguments:
1. The rule file you create
2. The file, directory, or process ID to scan

**Example command:**
```bash
yara myrule.yar somedirectory
```
`.yar` is the standard file extension for Yara rules.

## Creating a Basic Yara Rule

Let's create a simple rule:

1. **Create a file named `somefile`:**
    ```bash
    touch somefile
    ```

2. **Create a new rule file named `myfirstrule.yar`:**
    ```bash
    touch myfirstrule.yar
    ```

3. **Edit `myfirstrule.yar` and add the following content:**
    ```yara
    rule examplerule {
        condition: true
    }
    ```

The rule is named `examplerule` and has a single condition: `true`. Every rule must have a name and a condition.

## Running Your Rule

To scan `somefile` with your rule:
```bash
yara myfirstrule.yar somefile
```

**Expected output if `somefile` exists:**
```
examplerule somefile
```

**If the file does not exist, Yara will output an error:**
```
error scanning sometextfile: could not open file
```

---

Congratulations! You've created and tested your first Yara rule.