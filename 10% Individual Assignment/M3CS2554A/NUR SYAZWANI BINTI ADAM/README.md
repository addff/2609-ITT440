# NUR SYAZWANI BINTI ADAM

**Student ID:** 2025508225 | **Group:** M3CS2554A 

# Subprocess with fork()


## What is a Process?

Every program that is running on a Unix-like system is a **process**. To keep track of them, the system gives each process a **process ID (PID)**, a positive integer that uniquely identifies it. Think of it as the process's identity card.

- `getpid()` returns the PID of the calling process.
- `getppid()` returns the PID of its **parent**, the process that created it.
- `pid_t` is the data type used to store a PID. It is an integer type.

## What is fork()?

`fork()` is the system call that creates a **new process** by copying the one that is currently running. The original is called the **parent** and the new copy is the **child**. The child receives its own copy of the parent's stack, data, heap and program code, so it starts out as an almost exact duplicate. After the call, both processes continue running **at the same time** from the line right after `fork()`.

This is needed because it is how a system runs many things at once. A shell uses it to start every command, and a server uses it to look after many clients together.

<p align="center">
  <img src="https://github.com/syazwanihub/assigment-image/blob/main/fork_diagram.png?raw=true" alt="A parent process calls fork(); the parent continues and a child process is created" width="340"><br>
  <em>Figure 1: The parent calls fork(), then both the parent and the new child carry on.</em>
</p>

## What does fork() return?

| Return value | Received by | Meaning |
|---|---|---|
| **Negative (-1)** | Parent | Creation of a child process was unsuccessful. |
| **Zero (0)** | Child |  In the newly created child. Use `getpid()` for its own PID and `getppid()` for its parent's PID. |
| **Positive** | Parent | In the parent. The value is the **PID of the child**, which the parent can keep to track it or `wait()` for it. |

> **Note:** If `fork()` is called without an `if` to separate parent and child, every existing process forks again, so the number doubles each time. After **n** calls there are **2ⁿ** processes in total.

---

## How to Create and Terminate a Child Process?

### Step 1: How to call fork()?

```c
pid_t pid;
int rv;

switch (pid = fork()) {
```

The `fork()` system call duplicates the running process. The value it returns is stored in `pid` and checked by a `switch` statement, because the parent and the child receive different values.

### Step 2: How to handle errors?

```c
case -1:
    perror("fork");
    exit(1);
```

If `fork()` returns -1, no child was made. The program reports the reason with `perror()` and exits with status 1.

### Step 3: What does the child process do?

```c
case 0:
    printf(" CHILD: This is the child process!\n");
    printf(" CHILD: My PID is %d\n", getpid());
    printf(" CHILD: My parent's PID is %d\n", getppid());
    printf(" CHILD: Enter my exit status (make it small): ");
    scanf(" %d", &rv);
    printf(" CHILD: I'm outta here!\n");
    exit(rv);
```

If `fork()` returns 0, the program is running inside the child. The child:

- prints a message saying it is the child process,
- shows its own PID with `getpid()` and its parent's PID with `getppid()`,
- asks the user for a small number and stores it in `rv`,
- ends with `exit(rv)`, which is how the child **terminates** and passes that number back as its exit status.

### Step 4: What does the parent process do?

```c
default:
    printf("PARENT: This is the parent process!\n");
    printf("PARENT: My PID is %d\n", getpid());
    printf("PARENT: My child's PID is %d\n", pid);
    printf("PARENT: I'm now waiting for my child to exit()...\n");
    wait(&rv);
    printf("PARENT: My child's exit status is: %d\n", WEXITSTATUS(rv));
    printf("PARENT: I'm outta here!\n");
```

If `fork()` returns a positive number, the program is running inside the parent, and that number is the child's PID. `wait(&rv)` **pauses the parent** until the child terminates. `WEXITSTATUS(rv)` then reads the exit status that the child passed to `exit()`.

### Full Code

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main(void)
{
    pid_t pid;
    int rv;

    switch (pid = fork()) {
    case -1:
        perror("fork");
        exit(1);
    case 0:
        printf(" CHILD: This is the child process!\n");
        printf(" CHILD: My PID is %d\n", getpid());
        printf(" CHILD: My parent's PID is %d\n", getppid());
        printf(" CHILD: Enter my exit status (make it small): ");
        scanf(" %d", &rv);
        printf(" CHILD: I'm outta here!\n");
        exit(rv);
    default:
        printf("PARENT: This is the parent process!\n");
        printf("PARENT: My PID is %d\n", getpid());
        printf("PARENT: My child's PID is %d\n", pid);
        printf("PARENT: I'm now waiting for my child to exit()...\n");
        wait(&rv);
        printf("PARENT: My child's exit status is: %d\n", WEXITSTATUS(rv));
        printf("PARENT: I'm outta here!\n");
    }
    return 0;
}
```

### Output

Command to compile and execute:

```bash
$ gcc forkchild.c -o forkchild
$ ./forkchild
```

<p align="center">
  <img src="https://github.com/syazwanihub/assigment-image/blob/main/forkchild_output.png?raw=true" alt="Terminal output of forkchild" width="460"><br>
  <em>Figure 2: Output of forkchild.c, with the parent waiting for the child to exit.</em>
</p>

The child's parent PID (16131) matches the parent's own PID, which confirms they are related. The number 12 entered in the child was passed to `exit(12)`, and the parent received the same value through `wait()` and printed it as the child's exit status.

---

## Example 1: How does one parent create two children?

When a parent creates more than one child, it must call `wait()` once for each child it wants to collect.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/wait.h>

int main(void)
{
    pid_t pid1, pid2;
    int rv;

    pid1 = fork();
    if (pid1 < 0) {
        perror("fork");
        exit(1);
    }
    if (pid1 == 0) {
        printf(" CHILD 1: PID = %d\n", getpid());
        exit(11);
    }

    pid2 = fork();
    if (pid2 < 0) {
        perror("fork");
        exit(1);
    }
    if (pid2 == 0) {
        printf(" CHILD 2: PID = %d\n", getpid());
        exit(22);
    }

    wait(&rv);
    printf("PARENT: exit status of first child = %d\n", WEXITSTATUS(rv));
    wait(&rv);
    printf("PARENT: exit status of second child = %d\n", WEXITSTATUS(rv));

    return 0;
}
```

`fork()` is called twice. The first child prints its PID and calls `exit(11)`. The second child prints its PID and calls `exit(22)`. Each child exits immediately so that it does not create further processes. The parent calls `wait()` twice, and each call collects one child.

<p align="center">
  <img src="https://github.com/syazwanihub/assigment-image/blob/main/example1_output.png?raw=true" alt="Output showing the two child PIDs and their exit statuses 11 and 22" width="440"><br>
  <em>Figure 3: Output of Example 1, showing both children and their exit statuses (11 and 22).</em>
</p>

---

## Example 2: What is an orphan process?

An **orphan** is a child that is still running after its parent has terminated. It is adopted by the `init` process (PID 1), which later collects its exit status.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void)
{
    pid_t pid = fork();

    if (pid < 0) {
        perror("fork");
        exit(1);
    }

    if (pid == 0) {
        printf("child pid=%d, ppid before=%d\n", getpid(), getppid());
        sleep(30);
        printf("child: parent PID now=%d\n", getppid());
        printf("child: exiting...\n");
    } else {
        printf("parent pid=%d\n", getpid());
        printf("parent exiting...\n");
        exit(0);
    }
    return 0;
}
```

The parent (PID 14315) exited first, while the child (PID 14316) kept running. The child's parent PID was already 1, which means it had been adopted by `init` after its parent terminated.

<p align="center">
  <img src="https://github.com/syazwanihub/assigment-image/blob/main/orphan_output.png?raw=true" alt="Output of the orphan program showing parent PID 1" width="360"><br>
  <em>Figure 4: Output of the orphan program, where the child's parent PID becomes 1.</em>
</p>

---

## Example 3: What is a zombie process?

A **zombie** is a process that has terminated but still has an entry in the process table, because its parent has not collected its exit status. In the program below, the parent sleeps and never calls `wait()`, so the child stays a zombie.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(void)
{
    pid_t p = fork();

    if (p == 0) {
        exit(0);          /* child ends immediately */
    }

    sleep(60);            /* parent does NOT call wait() */
    return 0;
}
```

To observe it, run the program in the background and list the processes:

```bash
$ ./zombie &
$ ps -eo pid,ppid,stat,cmd | grep -E "zombie|defunct"
```

The `ps` output shows the child process (PID 10184) with status `Z` and the name `[zombie] <defunct>`. Its parent (PID 10181) is sleeping and has not called `wait()`, so the child's exit status was never collected and its entry remains in the process table.

<p align="center">
  <img src="https://github.com/syazwanihub/assigment-image/blob/main/zombie_ps.png?raw=true" alt="ps output showing a defunct zombie process" width="560"><br>
  <em>Figure 5: The ps output showing the zombie child (status Z, defunct).</em>
</p>

### How to prevent a zombie?

The parent calls `wait()` for each child. `wait()` pauses the parent until a child finishes, so the child's entry is removed straight away. In a server, `signal(SIGCHLD, SIG_IGN);` lets the kernel clean up finished children automatically.

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>

int main(void)
{
    pid_t p = fork();

    if (p == 0) {
        exit(0);          /* child ends immediately */
    }

    wait(NULL);           /* parent waits, so no zombie */
    sleep(60);
    return 0;
}
```

After adding `wait(NULL)`, the `ps` output shows only the sleeping parent (PID 25045). There is no `<defunct>` entry, which means the child's exit status was collected immediately and no zombie process was created.

<p align="center">
  <img src="https://github.com/syazwanihub/assigment-image/blob/main/no_zombie_ps.png?raw=true" alt="ps output showing no zombie process" width="560"><br>
  <em>Figure 6: The ps output after adding wait(NULL), with no zombie process.</em>
</p>

---

## Conclusion

`fork()` creates a child that runs alongside its parent. If the parent ends first, the child becomes an orphan and is adopted by `init`. If the child ends first and the parent does not call `wait()`, it becomes a zombie. Using `wait()` prevents this.

## References

- Kerrisk, M. *The Linux Programming Interface*. <https://broman.dev/download/The%20Linux%20Programming%20Interface.pdf>
- Understanding Unix Processes in C: a practical introduction to fork and wait. <https://medium.com/@chelbiahmed501/understanding-unix-processes-in-c-a-practical-introduction-to-fork-and-wait-4c437d80daff>
- GeeksforGeeks. Using fork() to produce 1 parent and 3 child processes. <https://www.geeksforgeeks.org/operating-systems/using-fork-produce-1-parent-3-child-processes/>

## Demonstration Video

<https://youtu.be/ni416g2Cdzo>
