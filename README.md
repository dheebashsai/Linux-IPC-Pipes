# Linux-IPC--Pipes
Linux-IPC-Pipes

Regno: 212224040009

# Ex03-Linux IPC - Pipes

# AIM:
To write a C program that illustrate communication between two process using unnamed and named pipes

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - pipe(), fifo()

### Step 3:

Testing the C Program for the desired output. 

# PROGRAM:

## C Program that illustrate communication between two process using unnamed pipes using Linux API system calls
```
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <string.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/wait.h>
#include <stdio.h>

void server(int rfd, int wfd); 
void client(int wfd, int rfd); 

int main() {
    int p1[2], p2[2], pid;
    pid_t child_pid;
    
   
    if (pipe(p1) == -1 || pipe(p2) == -1) {
        perror("pipe");
        exit(1);
    }

   
    child_pid = fork();
    if (child_pid == -1) {
        perror("fork");
        exit(1);
    }

    if (child_pid == 0) { 
        close(p1[1]);
        close(p2[0]); 
        server(p1[0], p2[1]);
        exit(0);
    } 
    close(p1[0]); 
    close(p2[1]);  
    client(p1[1], p2[0]);
    wait(NULL);

    return 0;
}

void server(int rfd, int wfd) {
    char fname[2000];
    char buff[2000];
    int n, fd;
    n = read(rfd, fname, sizeof(fname) - 1);
    if (n <= 0) {
        write(wfd, "Failed to read filename", 22);
        return;
    }
    fname[n] = '\0';

    fd = open(fname, O_RDONLY);
    if (fd < 0) {
        write(wfd, "Can't open file", 15);
        return;
    }

    n = read(fd, buff, sizeof(buff) - 1);
    if (n < 0) {
        write(wfd, "Error reading file", 18);
    } else {
        buff[n] = '\0'; 
        write(wfd, buff, n); 
    }

    close(fd); 
}

void client(int wfd, int rfd) {
    char fname[2000];
    char buff[2000];
    int n;

    printf("ENTER THE FILE NAME: ");
    scanf("%s", fname);

    printf("CLIENT SENDING THE REQUEST... PLEASE WAIT\n");

    
    write(wfd, fname, strlen(fname) + 1);

   
    n = read(rfd, buff, sizeof(buff) - 1);
    if (n <= 0) {
        printf("Error or empty response from server.\n");
    } else {
        buff[n] = '\0';
        printf("THE RESULTS FROM THE SERVER ARE:\n%s\n", buff);
    }
}
```
## OUTPUT
![image](https://github.com/user-attachments/assets/f94c74a3-715d-49d0-9e75-96173902f1ef)


## C Program that illustrate communication between two process using named pipes using Linux API system calls
```
#include <unistd.h>
#include <stdlib.h>
#include <stdio.h>
#include <sys/types.h>
#include <sys/stat.h>
int main(){
int res = mkfifo("/tmp/my_fifo", 0777);
if (res == 0) printf("FIFO created\n");
exit(EXIT_SUCCESS);
}
```

## OUTPUT
![image](https://github.com/user-attachments/assets/3e253c09-fef7-419c-8ad0-0e084f6461a4)


# RESULT:
The program is executed successfully.
