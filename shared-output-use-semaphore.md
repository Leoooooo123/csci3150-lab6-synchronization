# Shared Output: Use semaphore {#shared-output-use-semaphore}

We use semaphore to provide mutual exclusion to the standard error. If the process is using, the another process will wait until the semaphore is unlocked.

After we use semaphore

```
$ gcc process-1.c -pthread -o process-1
$ gcc process-2.c -pthread -o process-2
$ ./process-1 & ./process-2 &
```

```c
/*process-1.c*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <semaphore.h>
#include <unistd.h>
int main(int argc, char * argv[]) {
       sem_t * mutex;
       char * c = "This is CSCI3150--An operating system course.\n";
    // specify no buffering for stderr
    setbuf(stderr, NULL);
       mutex = sem_open("mutex_for_stderr", O_CREAT, 0666, 1);
       sem_wait(mutex);
       while (* c != '\0') {
            fputc(* c, stderr);
            c++;
            sleep(1);
         }
    sem_post(mutex);
    sem_close(mutex);
    sem_unlink("mutex_for_stderr");
    return 0;
}
```

```c
/*process-2.c*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <semaphore.h>
#include <unistd.h>
int main(int argc, char * argv[]) {
    sem_t * mutex;
    char * c = "This is CSCI3150--An operating system course.\n";
    // specify no buffering for stderr
    setbuf(stderr, NULL);  
    mutex = sem_open("mutex_for_stderr", O_CREAT, 0666, 1);
    sem_wait(mutex);
    while (* c != '\0') {
        fputc(* c, stderr);
        c++;
        sleep(rand()%2+1);
    }
    sem_post(mutex);
    sem_close(mutex);
    sem_unlink("mutex_for_stderr");
    return 0;
}
```



