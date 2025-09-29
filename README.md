# Linux-IPC-Shared-memory
Ex06-Linux IPC-Shared-memory

# AIM:
To Write a C program that illustrates two processes communicating using shared memory.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Shared Memory

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that illustrates two processes communicating using shared memory.
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/types.h>
#include <unistd.h>

#define SHM_SIZE 1024  // Size of shared memory

int main() {
    int shmid;
    char *shared_mem;
    pid_t pid;

    // Generate a unique key
    key_t key = ftok("shmfile",65);

    // Create shared memory segment
    shmid = shmget(key, SHM_SIZE, 0666 | IPC_CREAT);
    if (shmid == -1) {
        perror("shmget");
        exit(1);
    }

    // Fork process: writer (parent) and reader (child)
    pid = fork();

    if (pid < 0) {
        perror("fork");
        exit(1);
    }

    // ----------------- Writer Process -----------------
    if (pid > 0) {
        // Attach to shared memory
        shared_mem = (char *) shmat(shmid, NULL, 0);

        if (shared_mem == (char *) -1) {
            perror("shmat");
            exit(1);
        }

        printf("Writer: Enter a message: ");
        fgets(shared_mem, SHM_SIZE, stdin);

        printf("Writer: Message written to shared memory.\n");

        // Detach shared memory
        shmdt(shared_mem);

        // Wait for child process to finish
        wait(NULL);

        // Delete shared memory
        shmctl(shmid, IPC_RMID, NULL);
    }

    // ----------------- Reader Process -----------------
    else {
        sleep(2); // Give writer time to write

        // Attach to shared memory
        shared_mem = (char *) shmat(shmid, NULL, 0);

        if (shared_mem == (char *) -1) {
            perror("shmat");
            exit(1);
        }

        printf("Reader: Message read from shared memory: %s\n", shared_mem);

        // Detach shared memory
        shmdt(shared_mem);
    }

    return 0;
}

```
## OUTPUT

<img width="303" height="49" alt="Screenshot 2025-09-29 105850" src="https://github.com/user-attachments/assets/9b64eb08-e3c3-4f15-80e2-d04a65508db1" />




<img width="674" height="92" alt="Screenshot 2025-09-29 105843" src="https://github.com/user-attachments/assets/58f29d85-43d8-470b-99f7-e4d04a2dc29c" />


# RESULT:
The program is executed successfully.
