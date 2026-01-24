# Workflow Execution — Linux Endpoint Triage and Script-Based Validation Logic Using Bash

---

## Overview

This section establishes the analytical goals of the workflow, the operational mindset applied during execution, and how the techniques practiced align with real-world host investigation and response scenarios.

> **Workflow vs Execution vs Writeup (Terminology Used Here)**  
> - **Workflows** refer to operational tasks such as onboarding telemetry and validating parsing behavior.  
> - **Executions** refer to hands-on configuration and validation using real data and Splunk services.  
> - **Writeups** document configuration decisions, troubleshooting steps, and validation results.

### Purpose and Analyst Focus

This workflow execution focuses on building foundational Linux command-line and Bash scripting capabilities that directly support host triage, system investigation, and lightweight automation tasks commonly performed in SOC and IT operations environments.

The primary emphasis is not only on learning what individual commands and scripts do, but on understanding:

- How the shell functions as the interface between the user and the operating system
- How commands are interpreted and executed through the kernel
- How scripting logic in Bash parallels programming concepts already familiar from Python

This approach ensures that command usage is not treated as isolated syntax memorization, but instead as part of a broader operational and investigative workflow where analysts must interpret system behavior, validate host state, and automate repetitive checks.

> **Learning Context:**  
> Prior to execution, time was spent reviewing how Linux shells interpret commands, how scripts are executed, and how iterative and conditional logic translates from Python into Bash. This preparation ensured that each command and script could be reasoned about rather than executed mechanically, reinforcing long-term operational understanding.

### What This Workflow Demonstrates

This workflow execution write-up documents direct interaction with a Linux virtual machine using Bash to perform:

- System enumeration
- Filesystem navigation and inspection
- Shell environment analysis
- Script development using variables, loops, and conditional branching

Rather than treating each task as a standalone command demonstration, the workflow execution progresses through increasingly complex operational phases, each building on the previous one. This mirrors how analysts move from basic situational awareness into structured automation and validation tasks during host investigations.

Several scripting scenarios are intentionally designed to resemble operational patterns such as:

- User input collection
- Conditional access checks
- Repeated validation logic
- Execution permission control

These are fundamental constructs that later translate into more advanced automation for log parsing, system validation, and response workflows.

### Investigation and Detection Relevance

The techniques demonstrated throughout this workflow execution map directly to host-based investigative tasks, including:

- Establishing baseline system context (kernel, memory, disk, network)
- Inspecting files and permissions for potential misconfigurations
- Enumerating running processes and login activity
- Understanding shell environments that may influence script execution or attacker tooling
- Building small automation utilities to reduce repetitive manual checks

Understanding how Bash scripts interact with system state is particularly relevant for:

- Incident response automation
- Live host containment procedures
- Forensic triage scripts
- Scheduled monitoring tasks (cron-based workflows)

Additionally, familiarity with shell environments and permission models supports better detection of persistence techniques, privilege escalation attempts, and unauthorized script execution on compromised hosts.

---

## Environment and Execution Context

This section documents the technical environment, access method, and operational constraints under which the workflow was executed, providing important context for how commands and scripts behaved during analysis.

### Execution Platform

All analysis was performed on a remote Linux virtual machine accessed via terminal-based SSH sessions. The environment was provisioned dynamically and did not persist across sessions, requiring repeated re-establishment of context and recreation of scripts.

This constraint reflects realistic scenarios where analysts may:

- Access short-lived cloud instances
- Work in sandboxed investigation environments
- Need to quickly rebuild tooling during response operations

All activity was performed on a remote Linux virtual machine accessed through terminal-based shell sessions.

Environment characteristics:

- **Operating System:** Ubuntu 20.04.6 LTS (x86_64)  
- **Kernel Version:** Linux 5.15.0-1068-aws  
- **Shell:** Bash (`/bin/bash`)  
- **Access Method:** SSH  
- **Editor:** `nano`  
- **Network Interface:** `ens5`  
- **Execution Context:** Ephemeral virtual machine instance  

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-01.png?raw=true&v=2"
       style="border: 2px solid #444; border-radius: 6px;"
       width="1000"><br>
  <em>Figure 1</em>
</p>

Because the environment reset between sessions, internal IP addresses changed frequently (typically within the `10.201.x.x` range), and all scripts had to be recreated when reconnecting. This unintentionally reinforced familiarity with setup procedures and script reconstruction, which is common in incident response environments.

### Tooling and Constraints

All tasks were performed using native Linux utilities and Bash without external scripting frameworks or automation platforms.

This constraint ensures that:

- All workflows remain portable across environments
- Scripts can execute on minimal server installations
- Analysts do not rely on non-standard dependencies

Primary tooling:

- **Shell:** Bash (interactive and scripting)  
- **Editor:** `nano`  
- **Utilities:** `ls`, `cd`, `pwd`, `grep`, `cat`, `less`, `head`, `tail`, `ps`, `df`, `free`, `ip`, `whoami`, `hostname`, `chmod`, `uname`, `last`  

Scripts were executed directly from the shell after setting appropriate execution permissions using `chmod +x`, reinforcing Linux execution control concepts that are not present in interpreted scripting environments like Python by default.

### Data Sources Analyzed

This workflow execution primarily relies on:

- Live system state obtained via shell commands
- Local filesystem structures
- Environment variables
- User-provided terminal input during script execution

Rather than analyzing imported log datasets, the workflow execution emphasizes real-time host interrogation, which is a critical skill during:

- Incident response triage
- Initial compromise validation
- Live containment actions

Authentication logs and login activity are reviewed through system context rather than full log ingestion, reflecting quick situational checks analysts often perform before moving to deeper forensic analysis.

### Workflow Execution Map (High-Level)

Across all execution phases, the workflow follows a consistent analytical rhythm:

1. Establish baseline system context  
2. Review shell and kernel interaction model  
3. Navigate filesystem and inspect files  
4. Enumerate available shell environments  
5. Develop scripts using variables, loops, and conditionals  
6. Implement multi-input validation logic resembling authentication workflows  

Each phase is grouped by analytical objective rather than by individual command usage. This reflects how real investigations prioritize investigative goals over tool mechanics, even though multiple commands may be required to achieve each objective.

---


## Step-by-Step Execution

### Step 1 — Establishing System Context and Shell Fundamentals

**Objective:**  
Understand how the shell acts as the interface between the user and the operating system and confirm baseline system information through command-line enumeration.

Before running commands, I reviewed the conceptual relationship between the operating system, kernel, and shell:

- Operating System — full runtime environment
- Kernel — hardware and resource management
- Shell — user-facing command interpreter

Keeping this model in mind helped me better understand what was actually happening when I typed commands into the terminal.

> **Personal Context:**  
> This connected well with what I learned while studying for the CompTIA Security+ exam, where system administration and command-line usage are heavily emphasized in security tasks.

**Steps I followed:**
- Observed how the GUI and CLI offer different levels of control.
- Learned that the shell interprets user commands and relays them to the OS.
- Understood how CLI access is foundational in system administration and automation.

> **System Concepts Review**
> I took time to review and reinforce the relationship between the operating system, the kernel, and the shell. I confirmed that the operating system provides the full environment the machine runs on, while the kernel is the core component that directly manages hardware resources such as memory, CPU, storage, and devices. The shell serves as the user-facing interface that accepts commands and passes them to the kernel for execution. I also refreshed the differences between common shells. In this environment, I used Bash, which is the default shell on most Linux systems and supports both interactive commands and shell scripting. I compared this to PowerShell on Windows, which serves a similar purpose as a command interface but also functions as a scripting language and works with objects rather than plain text. I also noted the distinction from the older Windows Command Prompt (cmd.exe), which is more limited in functionality compared to PowerShell.

#### Step 1A — SSH into the Virtual Machine and Observe System Banner

I connected to the virtual machine using SSH. Upon login, the system displayed a Message of the Day (MOTD) banner showing operating system version, kernel information, and basic system statistics.

I then ran the following commands to establish baseline context:

- `uname -a` to confirm kernel version and CPU architecture
- `lsb_release -a` to confirm the Ubuntu distribution and version
- `ip -4 addr show ens5` to verify IPv4 configuration on the primary interface
- `free -h` to inspect memory and swap usage
- `df -h /` to inspect root filesystem usage
- `whoami` to confirm the current user context
- `last -a | head` to view recent login activity
- `sudo apt update && apt list --upgradable` to check for available system updates

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-02.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 2</em>
</p>

Running this collection of commands allowed me to quickly validate that I was operating on the expected host image, confirm that networking was functional, and check that disk and memory usage were normal before continuing.

> **Personal Learning Note:**  
> I deliberately reviewed standard enumeration commands to reinforce consistent syntax usage and interpretation of outputs, ensuring I was not relying on memory alone but fully understanding what each command revealed about system state.

#### Step 1B — Review GUI vs CLI Operational Differences

I reviewed why command-line interaction is heavily used in security and operations contexts compared to graphical interfaces.

> **Personal Learning Note:**
> The shell provides direct control and visibility into system operations that graphical interfaces often abstract away. When commands are entered, the shell interprets instructions and passes them to the kernel for execution.

From a practical standpoint:

- The CLI allows me to execute compound operations quickly using pipes and scripts.
- Many system logs and configuration files are accessed and filtered most efficiently through text-based tools.
- Automation is built around shell and scripting rather than GUI workflows.

This reinforced why comfort with the shell is essential for investigations, incident response, and systems administration.

#### Step 1 Findings

- CLI access enables faster and more precise system interrogation.
- Enumeration commands provide immediate situational awareness.
- Shell-based workflows are foundational to security operations.

---

### Step 2: Filesystem Navigation and File Inspection

**Objective:**  
Develop proficiency navigating directories and inspecting file contents using common Linux commands.

> **Personal Learning Note:**  
> I took time to independently review and refresh several Linux command-line fundamentals, including which commands to use for system enumeration, how to interpret their output, and the correct syntax for writing them. This helped reinforce consistent command usage, improved clarity in my workflow execution, and ensured that I fully understood what each command was doing rather than running them by memory alone. I revisited directory navigation to refresh my memory on the filesystem context. I used `cd` to move between folders and `cd ..` to step one directory up. The `..` operator reminded me of directory traversal concepts attackers abuse when manipulating relative paths. While performing these checks I captured the outputs and noted the paths I visited for evidence.

#### Step 2A — Verify Working Directory and Navigate Filesystem

Commands practiced:
- `pwd` — showed my current directory.
- `ls` — listed files and directories.
- `cd <directory>` — moved into another directory.

I opened the terminal and began by verifying my working directory in the system with `pwd` (print working directory):

```bash
pwd
```

I used `cd` to move between directories using and used `ls` to view the contents of directories

```bash
cd <directory>
cd ..
```

Relative path traversal reinforces concepts commonly abused during directory traversal attacks and is also essential for routine administration.

> **Security Context:**  
> Directory traversal concepts are relevant to both normal administration and exploitation techniques involving relative paths.

#### Step 2B — Inspecting Files and Searching Content

I revisited the `grep` command, which allows searching for specific keywords or patterns within files. This is especially helpful when dealing with large files, such as logs, where I only need lines that match a certain term. Running `grep` with the desired pattern returned only the lines in the file that contained that keyword.

Commands practiced:
- `cat <file>` — display full file contents
- `less`, `head`, `tail` — view partial file content
- `grep <pattern> <file>` — search for matching lines

To search within files, I used:

```bash
grep "hello" examplefile.txt
```

This filtered output to only show lines containing the target keyword. This type of filtering mirrors how I would search for indicators or specific values within large log files during investigations.


#### Step 2C — Reviewing File Permissions

Commands practiced:
- `ls` — list directory contents
- `ls -l` — long format with permissions
- `ls -la` — include hidden files

I listed files with permission details using:

```bash
ls -l
ls -la
```

I reviewed how permissions are displayed:
- The first character indicates file type (`d` for directory, `-` for regular file).
- The next three sets of characters represent read, write, and execute permissions for the owner, group, and others.

Permission interpretation:
- First character indicates file type (`d` = directory, `-` = file)
- Followed by read (`r`), write (`w`), execute (`x`) permissions for:
  - owner
  - group
  - others

Understanding these permission strings is important for identifying misconfigurations that could allow unauthorized access or execution.

I reviewed the `ls` command to refresh how to list the contents of a directory. This included recalling how ls displays files, subdirectories, and sometimes hidden items depending on the options used. 

I also revisited several common flags that provide additional detail or change the output format (such as showing file permissions, sizes, and timestamps). For example, I could run `ls -la`, which would list all files (including hidden ones) in long format, which displays file permissions, owner, group, size, and modification dates in a structured way. This is especially helpful when determining file visibility and understanding permission settings within a directory. 

I also reviewed how to use `ls -l` to display file and directory permissions in long format. The output includes several fields, such as the file type, permission bits, owner, group, file size, and the last modification date. This helped reinforce how Linux controls access to files and directories through permission settings. For example, running `ls -l` returned entries such as `drwxr-xr-x` for directories or `-rw-r--r--` for files.

Understanding these permission strings is important for identifying misconfigurations that could allow unauthorized access or execution.


> **Personal Learning Note:**
> I took time to refamiliarize myself with how to interpret these permission strings, noting that the first character indicates the file type (for example, `d` for directory), followed by three sets of read (`r`), write (`w`), and execute (`e`) permissions for the owner, group, and others. Reviewing this helped reinforce how Linux controls access to files and directories and reminded me that understanding permissions is useful both for routine system interaction and when identifying misconfigurations that could be leveraged in an attack or privilege escalation scenario.

#### Step 2D — Additional Enumeration Commands

I also reviewed the following commands again to reinforce situational awareness techniques:

- `hostname`
- `ip a`
- `df -h`
- `free -h`
- `ps aux`
- `touch`

While I did not capture detailed output for each command, the goal was to build familiarity with their syntax and typical output structures rather than perform forensic-level analysis.

During this session, I also revisited several additional Linux commands that I commonly use for basic enumeration and situational awareness. These included commands such as `whoami` to confirm my current user context, uname `-a` to review kernel and system information, hostname to identify the system name, and `ip a` to inspect network interfaces and assigned IP addresses. 

I also used `df -h` and `free -h` to check disk and memory usage respectively, along with `ps aux` to view active running processes. 

> **Personal Learning Note:**
I additionally tested directory navigation and file interaction commands such as `touch`, `cat`, `less`, `head`, and `tail` to ensure I remained familiar with reading, creating, and examining files directly from the command line. Since these were primarily refreshers and foundational operations that I am already familiar with, I did not document each command output in detail here. The purpose of this review was to re-establish confidence in these core commands so that my focus can remain on analysis and interpretation rather than recalling syntax.

#### Step 2 Findings

- CLI navigation enables efficient log and file inspection
- Text searching tools such as `grep` are essential for investigations
- File permissions provide critical security context

Navigating Linux via the CLI provides fast access to system information. The ability to search text efficiently with commands like `grep` is particularly useful for log and forensic work.

> **Personal Learning Note:**
> I reinforced core navigation and file interaction commands in Linux. These are essential commands that I will use frequently as I continue building administrative and security skills.

---

### Step 3: Enumerating and Evaluating Shell Environments

**Objective:**  
Identify which shell is currently active and review alternative shells available on the system.

> ** Personal Learning Note:**  
> I took time to independently review and refresh several Linux command-line fundamentals, including which commands to use for system enumeration, how to interpret their output, and the correct syntax for writing them. This helped reinforce consistent command usage, improved clarity in my workflow execution, and ensured that I fully understood what each command was doing rather than running them by memory alone.

> **Note:**
> To learn about different shell types and how they influence command behavior and scripting style. In this part of the workflow execution write-up, I stepped back from hands-on exploitation and did more of a structured review.  
The goal was to look at the different Linux shells available on the system, see which one I was actually using, and compare a few popular shells (Bash, Fish, and Zsh) in terms of features and usability. Even though this section was mostly “review,” I treated it like a small reconnaissance exercise on my own environment.

By the end of this step, the following was executed:

- Verified which shell my user account was currently using.
- Enumerated all login shells available on the system.
- Reviewed how to switch between shells temporarily and permanently.
- Compared Bash, Fish, and Zsh from a day-to-day usability perspective:
  - scripting support  
  - tab completion  
  - customization  
  - user friendliness  
  - syntax highlighting

#### Step 3A — Identifying the Active Shell

I checked the active shell by printing the `SHELL` environment variable:

 `echo $SHELL`. 

To do so, I started by confirming which shell my session was actually running. In the terminal, I echoed the `$SHELL` environment variable. To do so, I ran the following command: `echo $SHELL`, which identified the active shell I was using.

The output showed: 

```
/bin/bash
```

This confirmed that my session was running in Bash, which is the default shell on many Linux systems and is widely supported for scripting and automation.

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-03.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 3</em>
</p>


#### Step 3B — Listing Available Login Shells

To see which shells were available and permitted for login, I ran:

```bash
cat /etc/shells
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-04.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 4</em>
</p>

This file lists shells that users are allowed to log into, which is relevant when configuring accounts or restricting shell access for security purposes.

#### Step 3C — Switching Between Shells Interactively

I temporarily switched to `Zsh` to see how it behaves differently from `Bash`. To achieve this, I first needed to see what other shells were installed and allowed for login on this system. Linux stores this information in `/etc/shells`. Without changing system defaults, I tested switching shells inside the session.

To start Z shell:

```bash
zsh
```
<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-05.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 5</em>
</p>

After exiting the Z shell (zsh) setup menu, I manually switched my shell back. To do this, I simply typed `bash` and pressed [Enter]. This immediately returned me to the standard Bash prompt, confirming that I was now back in the Bash environment.

To return to Bash:

```bash
bash
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-06.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 6</em>
</p>

I did not permanently change the default shell for the user account. The goal was only to observe differences in interactive behavior and command completion.

#### Step 3D — Reviewing Switching Shells Permanently

I also reviewed how to permanently change the default shell for a user. The command pattern looks like this: `chsh -s /usr/bin/zsh`. 

For this review portion I did not actually change my default shell; instead I focused on understanding the mechanism and when it would be appropriate to use it (for example, if I decided to fully migrate from Bash to Zsh).


#### Step 3E — Reviewing Shell Characteristics

I shifted into a more conceptual comparison of shells, starting with Bash, then Fish, then Zsh.

Key points I reviewed about Bash:
- It is the default shell on many Linux distributions.
- It supports scripting, and most basic shell scripts in the wild are written for Bash or compatible shells.
- Bash has history and tab completion, which make it easier to recall and complete commands.
- It provides decent flexibility but not as much “out-of-the-box” user friendliness or visual niceties as newer shells.

From a security and operations perspective, Bash is still important because a lot of automation, tooling, and older documentation assume Bash or sh compatibility.

Next, I went over Fish (Friendly Interactive Shell). This was also a more conceptual comparison of shells, but now with Fish. 

Highlights I noted:
- Fish focuses heavily on user friendliness:
  - Very clean, beginner-friendly prompt.
  - Auto-suggestions based on history and completions.
- It supports scripting, but its scripting syntax differs from POSIX/Bash, which can be a drawback for portability.
- It offers:
  - powerful tab completion,
  - built-in syntax highlighting,
  - and helpful prompts that flag errors as you type.

The main trade-off I took away: Fish is great for interactive use, but not ideal for writing scripts that need to run everywhere.

Finally, I reviewed Zsh, which is kind of a hybrid between a traditional Unix shell and a more modern, highly customizable environment.

Key points I reviewed:
- Zsh is often described as a “modern shell” that blends features from other shells.
- It supports advanced tab completion and powerful customization through frameworks and plugins (e.g., oh-my-zsh).
- Like Fish, it offers:
  - syntax highlighting,
  - better auto-completion,
  - and a lot of room for theming and prompt customization.
- It remains quite compatible with Bash-style scripting, which makes it more attractive than Fish for people who want both scripting and a nice interactive experience.

I came away with the impression that Zsh is a strong “daily-driver” candidate for power users, especially when combined with plugins.

#### Step 3 Findings

I did personal research and reviewed a comparison table for Bash, Fish, and Zsh across several categories. I summarized it for myself like this:

**Scripting**
- Bash: Widely used and highly compatible. Most scripts target Bash or POSIX sh.
- Fish: Supports scripting but uses its own syntax; less portable.
- Zsh: Very capable scripting support and largely compatible with existing shell scripts.

**Tab Completion**
- Bash: Basic completion; can be extended with extra configuration.
- Fish: Very advanced completion with intelligent suggestions.
- Zsh: Extremely powerful completion, especially with plugin frameworks.

**Customization**
- Bash: Customizable via .bashrc and other config files, but mostly manual.
- Fish: Interactive tools make customization easier, especially for prompts.
- Zsh: Extremely customizable; plugin managers and frameworks make it easy to build a very tailored shell.

**User Friendliness**
- Bash: Familiar and traditional; many users are comfortable with it.
- Fish: Probably the most user-friendly out of the box.
- Zsh: Can be very friendly, especially after some initial setup.

**Syntax Highlighting**
- Bash: Not built in; requires third-party tools or custom configuration.
- Fish: Built-in syntax highlighting by default.
- Zsh: Syntax highlighting is typically enabled via plugins, but works very well.

This reinforced the idea that there’s no “one best shell”; it really depends on whether I care more about compatibility, interactivity, or customization.

Bash is widely used and is a stable, script-friendly option. Shells like Zsh and Fish offer more interactive features such as improved tab completion and syntax highlighting.

I learned that different shells can improve workflow depending on preference and environment. This helped me understand why administrators may choose one shell over another.

Even though this part of the analysis step was mostly review, it helped me organize what I already knew about Linux shells into something more systematic:

- I confirmed how to identify and enumerate shells on a Linux system using $SHELL and /etc/shells.
- I saw how easy it is to test different shells interactively without changing system defaults.
- I revisited the strengths and weaknesses of Bash, Fish, and Zsh, especially in terms of scripting vs. interactive use.
- From a security/ops perspective, I’m reminded that:
  - Bash remains a baseline skill because of its ubiquity in scripts and tooling.
  - Fish and Zsh can dramatically improve productivity and reduce typing mistakes in day-to-day terminal work.

If I were to change my default shell in the future, I’d likely experiment with Zsh plus a plugin framework, while still keeping Bash skills sharp for scripts and servers that only offer the standard tools.

---

## Step 4 — Writing Bash Scripts with Iterative and Conditional Logic

### Objective
To begin writing shell scripts and understand how variables, loops, and conditional statements support automation.

> **Personal Learning Note:**  
> I took time to independently review and refresh several Linux command-line fundamentals, including which commands to use for system enumeration, how to interpret their output, and the correct syntax for writing them. This helped reinforce consistent command usage, improved clarity in my workflow understanding, and ensured that I fully understood what each command was doing rather than running them by memory alone.

> **Personal Context:**  
> Although I previously studied scripting in college, primarily using Python, including work with iterative and recursive functions, this task sequence served as a refresher on how similar logic structures translate into Bash. The goal was not to build a complex automation pipeline, but to reinforce core scripting mechanics that will support future SOC automation, tooling, and forensic workflows.

#### Step 4A — Creating an Initial Interactive Script

I created the initial Bash script using `nano`. 

This mirrored the same process as simple Python programs, except here the file used a `.sh` extension and required a shebang to define the interpreter. 

I created a script named `peters_script.sh` with the following contents:

```bash
#!/bin/bash
echo "Please enter your name"
read name
echo "Hi there, $name"
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-07.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 7</em>
</p>

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-08.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 8</em>
</p>

The shebang (#!/bin/bash) is similar to how Python scripts use `#!/usr/bin/env python3` to specify the interpreter. This seems to be the same concept, just different ecosystem. Basically, the shebang (`#!/bin/bash`) specifies that the script should be interpreted using Bash when executed directly.

> **Personal Context & Note:** 
> I learned that a shebang is the very first line of a script that tells the operating system which interpreter should be used to execute the file. It begins with the characters `#!` followed by the full path to the interpreter, such as `#!/bin/bash` for a Bash script or `#!/usr/bin/env python3` for a Python script. This line is important because, without it, the system won’t know which language to use unless you explicitly run the script by calling the interpreter manually. With a proper shebang, you can execute the script directly after giving it execution permissions, and the operating system will automatically invoke the correct interpreter. It also improves portability and readability since anyone opening the script can immediately see which language and interpreter it relies on.


#### Step 4B — Granting Execute Permissions

After writing the script, I saved it ([CTRL+X] > then [Y], then [ENTER]). Before executing the script directly, I applied execute permissions:

```bash
chmod +x peters_script.sh
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-09.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 9</em>
</p>

This step enforces the idea of execution-level control that doesn’t exist in Python by default but is crucial in Linux environments.

#### Step 4C — Executing the Script

Now that the script had execution rights, I ran the script using:

```bash
./peters_script.sh
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-10.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 10</em>
</p>

To run the script, I used the `./` prefix. This explicitly told the shell to execute the file located in the current directory, because the current directory wasn’t included in the system’s PATH. Without `./`, the shell would look only in the directories listed in PATH, fail to find the script, and return a “command not found” error.

The script accepted user input, assigned it to a variable, and displayed a greeting, exactly the same flow I practiced in Python when handling raw input.


#### Step 4D — Creating a Script With a Loop

Next, I created a script that used a loop (named the script file `peters_loop_script.sh`). Similar to iterative (`for`) loops in Python (`for i in range(1, 11):`), this felt very familiar even though the syntax looked different.

I created `peters_loop_script.sh`:

```bash
#!/bin/bash
for i in {1..10}
do
    echo $i
done
```
The iterative logic mirrored Python: looped from 1 to 10, print each number. 

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-11.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 11</em>
</p>

After writing the script, I saved it ([CTRL+X] > then [Y], then [ENTER]), I applied execute permissions and ran the script:

```bash
chmod +x peters_loop_script.sh
./peters_loop_script.sh
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-12.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 12</em>
</p>

This script demonstrated how loops in Bash mirror iteration constructs used in Python and other languages.

---

#### Step 4E — Creating a Script With Conditional Logic

This section introduced conditional logic (`if`/`elif`/`else`). I then created `peters_conditional_script.sh`:

```
nano peters_conditional_script.sh
```

Inside the file, I wrote:

```bash
#!/bin/bash
echo "First, please enter your name:"
read name

if [ "$name" = "Peter Ahn" ]; then
    echo "Welcome Peter Ahn! The secret password is 123.
fiu"
else
    echo "Sorry! You are not authorized to access the secret password!"
fi
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-13.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 13</em>
</p>

The structure is similar to Python’s conditional branching, but using `fi` to close a condition, an explicit end marker to prevent logic bleed.

I executed the script running:

```bash
./peters_conditional_script.sh
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-14.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 14</em>
</p>

Using `./peters_conditional_script.sh` only runs the file, it does not give it permission to run. If the script isn’t already executable, the command will fail. The purpose of `chmod +x` is to add the executable permission so the system can actually run it. After that, `./script.sh` works because the file is now allowed to execute.

The script checks whether the entered name matches the authorized value (“Peter”), which is my name. If the condition is met, the script displays the secret and welcomes the user. If the condition fails, the script shows an unauthorized message instead. 

The two screenshots below are simply examples showing both possible outcomes. When running the script yourself, you will only see one outcome at a time, either the authorized message if the input matches, or the unauthorized message if it does not.

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-15.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 15</em>
</p>

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-16.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 16</em>
</p>

This demonstrated how conditional logic controls execution flow based on user input.


#### Step 4F — Adding Comments for Maintainability

In this step, I updated the conditional script to include comments explaining each section: 

```
#!/bin/bash

# Asking the user to enter their name.
echo "Please enter your name first:"
read name

# Checking if the user's name matches required name.
if [ "$name" = "Peter" ]; then

    # Display the secret if the name is valid.
    echo "Welcome Peter Ahn! The secret password is 123."

else

    # Message if the condition fails. Meaning user entered wrong name.
    echo "Sorry! You are not authorized to access the secret password!"

fi
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-17.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 17</em>
</p>

Comments don't change execution, but they dramatically improve readability, just as they did in my Python coursework.

---


#### Step 4 Findings

Scripting allows repetitive tasks to be automated reliably. Conditional logic allows scripts to adapt based on input or system state.

- Bash scripting supports variables, loops, and conditional branching similar to Python.
- Execution permissions are required to run scripts directly.
- Proper formatting and spacing are critical to avoid syntax errors.
- Comments improve script readability and maintainability.

1. Bash Scripts Parallel Python Logic
Even though syntactically different, the structure of programming logic was almost identical to what I practiced in Python:
- Variables
- Input handling
- Iterative loops
- Conditionals
- Output formatting

This reinforced the idea that foundational programming concepts carry over across languages.

2. Execution Control Is Unique to Bash
One major difference from Python was the need to:
- set executable permissions (chmod +x)
- explicitly reference the script path (./filename.sh)

This is important operationally, especially in SOC automation, because permissions affect whether scripts run correctly during incident response or cron-based monitoring.

3. Syntax Is Minimalistic but Strict
Compared to Python’s whitespace-sensitive structure, Bash relies heavily on:
- explicit terminators (fi, done)
- spacing around brackets in conditionals ([ "$var" = "value" ])
- careful quoting to avoid unexpected variable expansion

Small mistakes break scripts quickly, which reminded me of debugging incorrect base cases in recursive Python functions.

4. Good Commenting Is Essential
Bash scripts quickly become unreadable without comments, especially when nested logic or loops are involved.

> **Personal Learning Note**
> Bash handles variables, loops, input, and conditional logic. Knowing Python made it easier to understand the structure of Bash scripts, though the syntax differences require attention. I gained hands-on reinforcement of:
- script creation
- execution permissions
- iterative logic
- basic user interaction
- conditional branching

> **Personal Context:**
> I was reminded of the importance of comments, just as essential in Bash as they were when writing iterative or recursive Python functions. Overall, this review section prepared me to begin writing more complex automation scripts for cybersecurity workflows.

> **Personal Context:**
> This served as a strong refresher in shell scripting fundamentals. Although most of the concepts were familiar from studying Python and algorithmic logic (recursive vs. iterative), applying them in a Linux environment gave me a deeper understanding of how Bash differs and where it excels, especially for systems-level automation.

---

### Step 5 — Locker Authentication Script (Multi-Input Validation)


### Objective

Create a script that validates multiple input values before granting access, simulating multi-factor-style checks. 

The objective of this task was to challenge myself by creating a small authentication program in Bash that would force me to apply multiple scripting fundamentals I’ve been reviewing variables, loops, user input handling, and conditional logic. 

I designed this locker-access scenario intentionally so I could test whether I could collect several pieces of user information and validate them correctly using Bash. The idea was to simulate a simple authentication system that checks a username, company name, and PIN, giving me a realistic way to practice tying these scripting components together in one workflow.

> **Personal Context:**
> While working through this, I reflected often on my scripting background from college, where I studied Python extensively, learning both recursive and iterative functions. Many concepts I learned there, especially iterative loops and input validation, mapped directly onto this Bash workflow, even though the syntax and structure differed.

> **Personal Learning Note:**  
> I took time to independently review and refresh several Linux command-line fundamentals, including which commands to use for system enumeration, how to interpret their output, and the correct syntax for writing them. This helped reinforce consistent command usage, improved clarity in my workflow understanding, and ensured that I fully understood what each command was doing rather than running them by memory alone.


#### Step 5A — Defining Validation Requirements

The script must validate username, company name, and PIN. The script must compare input against required values. If all three matched, access is granted; otherwise, access is denied.

I defined the following required values:

- **Username:** Peter
- **Zip Code:** 10314 
- **PIN:** 1234  

The script needed to:

1. Prompt for all three values
2. Store each value in variables
3. Validate that all values match expected inputs
4. Display success or denial message

> **Personal Learning Context:**
>This immediately reminded me of input-handling assignments from Python class, where we built iterative prompts and validated user responses using conditionals.


#### Step 5B — Creating the Script File and Variables

I created a new script using the `nano` text editor:

```bash
nano peters_locker_script.sh
```

Inside the file, I added the shebang along with placeholder variables:

```
#!/bin/bash

username=""
zipcode=""
pin=""
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-18.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 18</em>
</p>

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-19.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 19</em>
</p>

The shebang works the same way it did when writing Python scripts (#!/usr/bin/env python3), telling the system which interpreter to use.


#### Step 5C — Collecting Input Using a Loop

Instead of writing three separate prompts, I used a loop sequentially request the `username`, `zip code`, and `PIN`:

```bash
for i in {1..3}; do
    if [ "$i" -eq 1 ]; then
        echo "Enter your Username:"
        read username
    elif [ "$i" -eq 2 ]; then
        echo "Enter your Zip Code:"
        read companyname
    else
        echo "Enter your PIN:"
        read pin
    fi
done
```
This allowed all inputs to be collected using a single loop construct, reducing repeated code and centralizing input handling.

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-20.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 20</em>
</p>

Thought Process:

- `for i in {1..3}; do` was to start the for loop, where `i` will take the values `1`, `2,` and `3` in sequence. Conceptually, this is like `for i in range(1, 4):` in Python.
  - instead of writing three separate prompt blocks, I used one loop and decide what to ask based on the value of `i`.
  - When `i` is `1`, this `if` branch runs.
    
- `if [ "$i" -eq 1 ]` checks if the current loop counter is equal to `1`.
  - If true, it would:
    - print `Enter your Username:` to the screen (`echo`),
    - then waits for the user to type something and presses ENTER (`read username`), storing the input in the `username` variable.
      
- `elif [ "$i" -eq 2 ];` checks if `i` is `2`, this `elif` (else if) branch runs.
  - Same pattern, if true, it would:
    - print `Enter your company name` to the screen (`echo`),
    - then waits for the user to type something and presses ENTER (`read companyname`), storing the input in the `companyname` variable.
   
- `else` checks if `i` is `3`, and runs when neither the `if` or `elif` matches, so this `else` block runs
  - Same pattern again, if true, it would:
    - print `Enter your PIN` to the screen (`echo`)
    - then waits for the user to type something and presses ENTER (`read pin`), storing the inpurt in the `pin` variable.

> **Note:**
> I deliberately set up a small “locker access” scenario in Bash as a way to test myself on the scripting fundamentals I’ve been revisiting. I wanted something slightly more realistic than just echoing text, so I designed a script that asked for three separate pieces of information, `username`, `company name`, and `PIN`. Then, decided whether to grant or deny access.

> **Note:**
> My goal was to see if I could tie together variables, loops, user input, and conditional logic in one flow, similar to how I used to structure small projects in Python during college. Back then, I worked with both iterative and recursive functions, so here I focused on the iterative side: using a loop to progressively gather input and then a single conditional block to evaluate everything at the end. 

> **Note:**
> This was less about memorizing syntax and more about confirming that I still understand the underlying logic well enough to design a small “system” from scratch.

> **Personal Context:**
> While writing this, I noticed the loop was structured very similarly to iterative Python loops I wrote in college, where we iterated through a range of values and performed different logic within each iteration.
This specific loop doesn’t use recursion (which I learned in Python as well), but the concept of controlling flow through repeated cycles is parallel to iterative function structures.


#### Step 5D — Validating All Inputs

I validated all three values using a single compound conditional:

```bash
if [ "$username" = "Peter" ] && [ "$companyname" = "PeterAhnCompany" ] && [ "$pin" = "1234" ]; then
    echo "Authentication Successful. You can now access your locker, Peter."
else
    echo "Authentication Denied!!"
fi
```

All conditions had to evaluate to true in order for access to be granted.

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-21.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 21</em>
</p>


Thought Process:

- `if [ "$username" = "Peter" ] && [ "$companyname" = "PeterAhnCompany" ] && [ "$pin" = "1234" ];` verifies that all three variables match their expected values. The condition only passes if every comparison is true, not just one or two.
  - If true, it would print `Authentication Successful. You can now access your locker, Peter` using the `echo` command.
  - If not (`else`), it would print `Authentication Denied!!` using the `echo` command

> **Personal Context:**
> The validation logic instantly reminded me of Python’s `if x == value` and `y == value:` syntax. Bash uses square brackets and requires specific spacing, but the underlying conditional evaluation worked almost exactly the same. I recognized this structure as something similar to the “base case vs. recursive case” logic taught in Python recursion: a single exact match leads to one path, and any deviation leads to the alternative.

---

#### Step 5E — Executing and Testing the Script

Before running the script, I ensured it had the correct execution permissions:

```bash
chmod +x peters_locker_script.sh
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-22.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 22</em>
</p>

I applied execute permissions and ran the script:

```bash
./peters_locker_script.sh
```

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-23.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 23</em>
</p>

I tested both valid and invalid input scenarios.

I entered the correct details first:

- `Username` - I entered `Peter`
- `Zip Code` - I entered `10314`
- `PIN` - I entered `1234`

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-24.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 24</em>
</p>

It printed the message `Authentication Successful. You can now access your locker, Peter.` as expected.

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-25.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 25</em>
</p>

I then entered incorrect details to test what would happen if the variables contained (at least) 1 wrong input that wasn't defined in the locker script since the script was defined to only print the authentication successfull message if all variables matched:

- `Username` - I entered `John` (incorrect variable input)
- `Zip Code` - I entered `10314`
- `PIN` - I entered `1234`

It printed the message `Authentication Denied!!` as expected.

<p align="left">
  <img src="images/lab01-linux-shell-and-scripting-basics-26.png?raw=true&v=2" 
       style="border: 2px solid #444; border-radius: 6px;" 
       width="1000"><br>
  <em>Figure 26</em>
</p>



#### Step 5 Findings

This script simulated simple authentication logic. It highlighted the importance of correct condition syntax and secure handling of input.

- All validation conditions must pass before access is granted.
- Loop-based input collection reduces duplicated code.
- Compound conditionals allow multiple checks to be evaluated simultaneously.

1. Bash Follows Similar Logical Structures to Python

Even though Bash syntax is more rigid (spacing matters, brackets matter, etc.), the fundamental ideas such as loops, variable assignment, and conditionals, mirror the same patterns I practiced in Python during college.
This made the script intuitive to follow.

2. Loops in Bash Can Replace Multi-Line Prompts

Instead of writing `read username`, `read companyname`, and `read pin`, the loop made the process cleaner and scalable. This reminded me of Python’s iterative functions where loops reduce repetition and prevent duplicated logic.

3. Validation Logic Reinforces Input Security

The conditional check ensures that:
- Every field must match exactly.
- Any incorrect value denies access.

This is a simple example, but it reflects basic authentication concepts used in real-world systems.

4. Bash Requires More Attention to Syntax

Misplaced brackets or missing spaces can break a script. Python taught me structure through indentation; Bash teaches structure through spacing and delimiters.

> **Personal Note:**
This brought together several scripting fundamentals into one cohesive task. Writing the locker authentication script helped me recognize how transferable programming concepts are between languages, especially between Python (which I studied academically) and Bash (which is more common in Linux automation and SOC workflows).

---

## Results & Interpretation

By the end of this workflow execution, I was able to confidently navigate a Linux system using the shell, enumerate system state, inspect files, and write Bash scripts that included user input, looping logic, and conditional branching.

Even simple scripts demonstrated how automation can be applied to repetitive validation tasks and how scripting logic mirrors control-flow concepts found in higher-level languages such as Python.

---

## Operational & Defensive Takeaways

- Command-line proficiency is essential for host triage and investigation.
- Automation scripts reduce manual effort and human error.
- Script execution permissions must be managed to prevent unauthorized code execution.
- Input validation logic parallels authentication and authorization checks used in real systems.

---

## Reuse Pack (Quick Reference)

### Common Enumeration Commands

- `uname -a`
- `lsb_release -a`
- `ip a`
- `df -h`
- `free -h`
- `ps aux`

### Bash Script Patterns

- Shebang: `#!/bin/bash`
- Execution permission: `chmod +x script.sh`
- Loop structure: `for i in {1..N}; do ... done`
- Conditional structure: `if [ condition ]; then ... else ... fi`

---

## What I Learned (Skills Demonstrated)

- Linux system enumeration and triage techniques
- Filesystem navigation and permission interpretation
- Shell environment awareness and management
- Bash scripting using variables, loops, and conditionals
- Designing input validation logic
- Translating programming concepts across languages

---
