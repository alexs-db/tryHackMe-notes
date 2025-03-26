# Offline Attacks

This section discusses offline attacks, including dictionary, brute-force, and rule-based attacks.

## Dictionary Attack

A dictionary attack is a technique used to guess passwords by using well-known words or phrases. The dictionary attack relies entirely on pre-gathered wordlists that were previously generated or found. It is important to choose or create the best candidate wordlist for your target in order to succeed in this attack.

### Example: Performing a Dictionary Attack with Hashcat

Let's say we obtain the following hash `f806fc5a2a0d5ba2471600758452799c` and want to perform a dictionary attack to crack it. First, we need to know the following:

1. **What type of hash is this?**
2. **What wordlist will we be using? Or what type of attack mode could we use?**

To identify the type of hash, we could use tools such as `hashid` or `hash-identifier`. For this example, `hash-identifier` suggests the possible hashing method is **MD5**. Note that the time to crack a hash will depend on the hardware you're using (CPU and/or GPU).

#### Running the Dictionary Attack

```bash
hashcat -a 0 -m 0 f806fc5a2a0d5ba2471600758452799c /usr/share/wordlists/rockyou.txt
```

**Output:**

```
hashcat (v6.1.1) starting...
f806fc5a2a0d5ba2471600758452799c:rockyou

Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: f806fc5a2a0d5ba2471600758452799c
Time.Started.....: Mon Oct 11 08:20:50 2021 (0 secs)
Time.Estimated...: Mon Oct 11 08:20:50 2021 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   114.1 kH/s (0.02ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 40/40 (100.00%)
Rejected.........: 0/40 (0.00%)
Restore.Point....: 0/40 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456 -> 123123

Started: Mon Oct 11 08:20:49 2021
Stopped: Mon Oct 11 08:20:52 2021
```

- `-a 0`: Sets the attack mode to a dictionary attack.
- `-m 0`: Sets the hash mode for cracking MD5 hashes. For other types, run `hashcat -h` for a list of supported hashes.
- `f806fc5a2a0d5ba2471600758452799c`: This option could be a single hash like our example or a file containing multiple hashes.
- `/usr/share/wordlists/rockyou.txt`: The wordlist/dictionary file for our attack.

To show the cracked value if the hash has been cracked, run:

```bash
hashcat -a 0 -m 0 f806fc5a2a0d5ba2471600758452799c /usr/share/wordlists/rockyou.txt --show
```

**Output:**

```
f806fc5a2a0d5ba2471600758452799c:rockyou
```

As a result, the cracked value is `rockyou`.

---

## Brute-Force Attack

Brute-forcing is a common attack used by attackers to gain unauthorized access to accounts. This method guesses the victim's password by trying all possible combinations of characters.

### Difference Between Dictionary and Brute-Force Attacks

- **Dictionary Attack**: Uses a predefined wordlist containing possible passwords.
- **Brute-Force Attack**: Tries all combinations of characters.

For example, if we know a PIN contains 4 digits, a brute-force attack would try all combinations from `0000` to `9999`. In other cases, sequences of numbers or letters can be appended to existing words, such as `admin0`, `admin1`, ..., `admin9999`.

#### Hashcat Charset Options

Hashcat provides charset options to generate custom combinations:

```
 ? | Charset
 ===+=========
    l | abcdefghijklmnopqrstuvwxyz
    u | ABCDEFGHIJKLMNOPQRSTUVWXYZ
    d | 0123456789
    h | 0123456789abcdef
    H | 0123456789ABCDEF
    s |  !"#$%&'()*+,-./:;<=>?@[\]^_`{|}~
    a | ?l?u?d?s
    b | 0x00 - 0xff
```

#### Example: Brute-Force Attack with Hashcat

To generate all 4-digit combinations:

```bash
hashcat -a 3 ?d?d?d?d --stdout
```

**Output (Partial):**

```
1234
0234
2234
3234
9234
4234
5234
8234
7234
6234
...
```

- `-a 3`: Sets the attack mode to brute-force.
- `?d?d?d?d`: Specifies 4 digits (`?d` represents a digit).
- `--stdout`: Prints the result to the terminal.

#### Cracking an MD5 Hash with a 4-Digit PIN

Given the hash `05A5CF06982BA7892ED2A6D38FE832D6`, we know it corresponds to a 4-digit PIN. Run:

```bash
hashcat -a 3 -m 0 05A5CF06982BA7892ED2A6D38FE832D6 ?d?d?d?d
```

**Output:**

```
05a5cf06982ba7892ed2a6d38fe832d6:2021

Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: 05a5cf06982ba7892ed2a6d38fe832d6
Time.Started.....: Mon Oct 11 10:54:06 2021 (0 secs)
Time.Estimated...: Mon Oct 11 10:54:06 2021 (0 secs)
Guess.Mask.......: ?d?d?d?d [4]
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........: 16253.6 kH/s (0.10ms) @ Accel:1024 Loops:10 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 10000/10000 (100.00%)
Rejected.........: 0/10000 (0.00%)
Restore.Point....: 0/1000 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-10 Iteration:0-10
Candidates.#1....: 1234 -> 6764

Started: Mon Oct 11 10:54:05 2021
Stopped: Mon Oct 11 10:54:08 2021
```

The cracked value is `2021`.