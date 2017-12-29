# Semaphore in C {#semaphore-in-c}

**Named Semaphore**

POSIX named semaphore APIs we use in this lab are shown in the below table. You can look up manual pages for details of these functions.`semaphore.c`shows how to use these functions to create, operate and remove named semaphore. Try it and make sure you understand it. Note that programs using the POSIX semaphores API must be compiled with -pthread to link against the real-time library. So you need compile semaphore.c like this:

```
gcc semaphore.c -pthreaad -o semaphore
```

| Function | Description |
| :--- | :--- |
| sem\_open | Opens/creates a named semaphore for use by a process |
| sem\_wait | lock a semaphore |
| sem\_post | unlock a semaphore |
| sem\_close | Deallocates the specified named semaphore |
| sem\_unlink | Removes a specified named semaphore |

```c
/*semaphore.c*/
#include <fcntl.h>           /* For O_* constants */
#include <sys/stat.h>        /* For mode constants */
#include <semaphore.h>
#include <stdio.h>

int main(int argc, char * argv[]){
  char * name = "my_semaphore";
  int VALUE = 2;
  sem_t * sema;
  //If semaphore with name does not exist, then create it with VALUE
  printf("Open or Create a named semaphore, %s, its value is %d\n", name,VALUE);
  sema = sem_open(name, O_CREAT, 0666, VALUE);
  //wait on semaphore sema and decrease it by 1
  sem_wait(sema);
  printf("Decrease semaphore by 1\n");
  //add semaphore sema by 1
  sem_post(sema);
  printf("Add semaphore by 1\n");
  //Before exit, you need to close semaphore and unlink it, when all  processes have
  //finished using the semaphore, it can be removed from the system using sem_unlink
  sem_close(sema);
  sem_unlink(name);
  return 0;
}
```

**Unnamed Semaphore**

POSIX semaphores are available in two flavors: named and unnamed. They differ in how they are created and destroyed, but otherwise work the same. Unnamed semaphores exist in memory only and require that processes have access to the memory to be able to use the semaphores. This means they can be used only by threads in the same process or threads in different processes that have mapped the same memory extent into their address spaces. Named semaphores, in contrast, are accessed by name and can be used by threads in any processes that know their names.

When we want to use POSIX semaphores within a single process, it is easier to use unnamed semaphores. This only changes the way we create and destroy the semaphore. To create an unnamed semaphore, we call the`sem_init()`function.

```
#
include
<
semaphore.h
>
int
sem_init
(sem_t *sem, 
int
 pshared, 
unsigned
int
 value)
;
```

The\_pshared\_argument indicates if we plan to use the semaphore with multiple processes. If we just want to use the semaphore in a single process, then set it to zero. The\_value\_argument specifies the initial value of the semaphore.

Instead of returning a pointer to the semaphore like`sem_open()`does, we need to declare a variable of type`sem_t`and pass its address to`sem_init()`for initialization. After initializing unnamed semaphore using`sem_init()`function, the`sem_wait()`and`sem_post()`functions can operate as usual.

When we are done using the unnamed semaphore, we can discard it by calling the`sem_destroy()`function.

```
#
include
<
semaphore.h
>
int
sem_destroy
(sem_t *sem)
;
```

After calling`sem_destroy()`, we can't use any of the semaphore functions with\_sem\_unless we reinitialize it by calling`sem_init()`again.

