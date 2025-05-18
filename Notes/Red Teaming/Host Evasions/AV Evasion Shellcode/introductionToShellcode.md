# Introduction to Shellcode

Shellcode is a set of crafted machine code instructions that tell a vulnerable program to run additional functions—often to provide access to a system shell or create a reverse shell.

Once injected and executed by the vulnerable software, shellcode modifies the program's flow to execute the attacker's code. It is generally written in Assembly language and translated into hexadecimal opcodes. Writing unique shellcode helps evade AV software, but requires strong Assembly skills.

---

## Skills Required for Writing Shellcode

To craft your own shellcode, you need:

- A good understanding of x86 and x64 CPU architectures
- Assembly language proficiency
- Strong knowledge of C or similar languages
- Familiarity with Linux and Windows OS internals

---

## Example: Simple Linux Shellcode

We'll create a simple shellcode for Linux that writes the string `THM, Rocks!` using two syscalls:

- `sys_write` to print a string
- `sys_exit` to terminate the program

### Syscall Register Table (Linux x64)

| rax   | System Call | rdi           | rsi                | rdx         |
|-------|-------------|---------------|--------------------|-------------|
| 0x1   | sys_write   | unsigned int fd | const char *buf   | size_t count|
| 0x3c  | sys_exit    | int error_code |                    |             |

---

### Assembly Code (`thm.asm`)

```nasm
global _start

section .text
_start:
    jmp MESSAGE      ; 1) jump to MESSAGE

GOBACK:
    mov rax, 0x1
    mov rdi, 0x1
    pop rsi          ; 3) pop address of "THM, Rocks!\r\n" into rsi
    mov rdx, 0xd
    syscall

    mov rax, 0x3c
    mov rdi, 0x0
    syscall

MESSAGE:
    call GOBACK      ; 2) call GOBACK, pushes address of message
    db "THM, Rocks!", 0dh, 0ah
```

**Explanation:**

- The message string is stored at the end of the `.text` section.
- The program jumps to the message, calls back to `GOBACK`, and uses the stack to get the message address.
- Registers are set up for `sys_write` and `sys_exit` syscalls.

---

### Compile and Run

```bash
nasm -f elf64 thm.asm
ld thm.o -o thm
./thm
# Output: THM, Rocks!
```

---

### Extracting Shellcode

1. **Dump the .text section:**

    ```bash
    objdump -d thm
    ```

2. **Extract the .text section as binary:**

    ```bash
    objcopy -j .text -O binary thm thm.text
    ```

3. **Convert to C string:**

    ```bash
    xxd -i thm.text
    ```

    Example output:

    ```c
    unsigned char new_text[] = {
      0xeb, 0x1e, 0xb8, 0x01, 0x00, 0x00, 0x00, 0xbf, 0x01, 0x00, 0x00, 0x00,
      0x5e, 0xba, 0x0d, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xb8, 0x3c, 0x00, 0x00,
      0x00, 0xbf, 0x00, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xe8, 0xdd, 0xff, 0xff,
      0xff, 0x54, 0x48, 0x4d, 0x2c, 0x20, 0x52, 0x6f, 0x63, 0x6b, 0x73, 0x21,
      0x0d, 0x0a
    };
    unsigned int new_text_len = 50;
    ```

---

### Testing the Shellcode in C

```c
#include <stdio.h>

int main(int argc, char **argv) {
    unsigned char message[] = {
        0xeb, 0x1e, 0xb8, 0x01, 0x00, 0x00, 0x00, 0xbf, 0x01, 0x00, 0x00, 0x00,
        0x5e, 0xba, 0x0d, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xb8, 0x3c, 0x00, 0x00,
        0x00, 0xbf, 0x00, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xe8, 0xdd, 0xff, 0xff,
        0xff, 0x54, 0x48, 0x4d, 0x2c, 0x20, 0x52, 0x6f, 0x63, 0x6b, 0x73, 0x21,
        0x0d, 0x0a
    };
    (*(void(*)())message)();
    return 0;
}
```

**Compile and run:**

```bash
gcc -g -Wall -z execstack thm.c -o thmx
./thmx
# Output: THM, Rocks!
```

> Note: The `-z execstack` option disables NX protection to allow code execution on the stack.

---

## Challenge

Modify your C program to execute the following shellcode. What is the flag?

```c
unsigned char message[] = {
  0xeb, 0x34, 0xb9, 0x00, 0x00, 0x00, 0x00, 0x5e, 0x48, 0x89, 0xf0, 0x80,
  0x34, 0x08, 0x01, 0x48, 0x83, 0xc1, 0x01, 0x48, 0x83, 0xf9, 0x19, 0x75,
  0xf2, 0xb8, 0x01, 0x00, 0x00, 0x00, 0xbf, 0x01, 0x00, 0x00, 0x00, 0xba,
  0x19, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xb8, 0x3c, 0x00, 0x00, 0x00, 0xbf,
  0x00, 0x00, 0x00, 0x00, 0x0f, 0x05, 0xe8, 0xc7, 0xff, 0xff, 0xff, 0x55,
  0x49, 0x4c, 0x7a, 0x78, 0x31, 0x74, 0x73, 0x2c, 0x30, 0x72, 0x36, 0x2c,
  0x34, 0x69, 0x32, 0x30, 0x30, 0x62, 0x31, 0x65, 0x32, 0x7c, 0x0d, 0x0a
};
```

---

Understanding how shellcodes are created is essential for tasks such as encrypting and encoding shellcode for evasion.
