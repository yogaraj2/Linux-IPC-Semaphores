# Linux-IPC-Semaphores
Ex05-Linux IPC-Semaphores

# AIM:
To Write a C program that implements a producer-consumer system with two processes using Semaphores.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Sempahores

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that implements a producer-consumer system with two processes using Semaphores.
```
#include <stdio.h>	 /* standard I/O routines.              */
#include <stdlib.h>      /* rand() and srand() functions        */
#include <unistd.h>	 /* fork(), etc.                        */
#include <time.h>	 /* nanosleep(), etc.                   */
#include <sys/types.h>   /* various type definitions.           */
#include <sys/ipc.h>     /* general SysV IPC structures         */
#include <sys/sem.h>	 /* semaphore functions and structs.    */
#define NUM_LOOPS	20	 /* number of loops to perform. */
#if defined(__GNU_LIBRARY__) && !defined(_SEM_SEMUN_UNDEFINED)
/* union semun is defined by including <sys/sem.h> */
#else
/* according to X/OPEN we have to define it ourselves */
union semun {
        int val;                    /* value for SETVAL */
        struct semid_ds *buf;       /* buffer for IPC_STAT, IPC_SET */
        unsigned short int *array;  /* array for GETALL, SETALL */
        struct seminfo *__buf;      /* buffer for IPC_INFO */
};
#endif
int main(int argc, char* argv[])
{
    int sem_set_id;	      /* ID of the semaphore set.       */
    union semun sem_val;      /* semaphore value, for semctl(). */
    int child_pid;	      /* PID of our child process.      */
    int i;		      /* counter for loop operation.    */
    struct sembuf sem_op;     /* structure for semaphore ops.   */
    int rc;		      /* return value of system calls.  */
    struct timespec delay;    /* used for wasting time.         */
    sem_set_id = semget(IPC_PRIVATE, 1, 0600);
    if (sem_set_id == -1) {
	perror("main: semget");
	exit(1);
    }
    printf("semaphore set created, semaphore set id '%d'.\n", sem_set_id);
    sem_val.val = 0;
    rc = semctl(sem_set_id, 0, SETVAL, sem_val);
    child_pid = fork();
    switch (child_pid) {
	case -1:	/* fork() failed */
	    perror("fork");
	    exit(1);
	case 0:		/* child process here */
	    for (i=0; i<NUM_LOOPS; i++) {
		sem_op.sem_num = 0;
		sem_op.sem_op = -1;
		sem_op.sem_flg = 0;
		semop(sem_set_id, &sem_op, 1);
		printf("consumer: '%d'\n", i);
		fflush(stdout);
	    }
	    break;
	default:	/* parent process here */
	    for (i=0; i<NUM_LOOPS; i++) {
		printf("producer: '%d'\n", i);
		fflush(stdout);
		sem_op.sem_num = 0;
					sem_op.sem_op = 1;
		sem_op.sem_flg = 0;
		semop(sem_set_id, &sem_op, 1);
		if (rand() > 3*(RAND_MAX/4)) {
	    	    delay.tv_sec = 0;
	    	    delay.tv_nsec = 10;
	    	    //nanosleep(&delay, NULL);
		                      sleep(10); }
if(NUM_LOOPS>=10)    {
	    semctl(sem_set_id, 0, IPC_RMID, sem_val) ;} // Remove the sem_set_id
	    }
    
    return 0;}
}
```

## OUTPUT
$ ./sem.o 
![Screenshot from 2024-05-07 19-36-42](https://github.com/yogaraj2/Linux-IPC-Semaphores/assets/153482637/83e6909c-2c9c-49f1-858c-310b9c55f4b9)


$ ipcs

![Screenshot from 2024-05-07 19-36-50](https://github.com/yogaraj2/Linux-IPC-Semaphores/assets/153482637/1c0ff9ad-dc48-42bc-9aa1-08a835df4fe9)


# RESULT:
The program is executed successfully.
