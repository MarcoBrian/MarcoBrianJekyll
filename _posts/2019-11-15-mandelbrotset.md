---
title: "Calculating the Mandelbrot Set"
date: 2019-11-14
tags: [operating systems, multithreading, multiprocessing, Unix]
excerpt: "Mandelbrot set"
toc: true
toc_label: "Contents"
toc_sticky: false
---


*Disclaimer: This post is for revision and educational purposes only. Anything related to the post that is used to commit potential misuse is not the intention of this post*

*This is a good reference and the one used*
[Operating Systems Textbook reference](http://pages.cs.wisc.edu/~remzi/OSTEP/)

# Mandelbrot Set 

[Link to the Github repository](https://github.com/MarcoBrian/MandelbrotSet)


<figure>
  <img src="/images/mandelbrot/mandelbrot.png" alt="mandelbrot set">
  <figcaption><i>The Mandelbrot Set</i></figcaption>
</figure>

## Introduction

The purpose of this project is to demonstrate how a computationally intensive application could be executed **faster** by dividing the compuational workload into smaller tasks, and then execute them in parallel. Here in this project we are making use of multi-processing and multi-threading (using only one process) techniques in order to do that. 

*Note: Multiprocessing and multithreading are two different concepts.*


We are not going to dive deep into the theory behind the Mandelbrot set but here is a good a [youtube video](https://www.youtube.com/watch?v=NGMRB4O922I) that gives a good idea of what the Mandelbrot Set is.
 
 
## The Crux

### Multi-processing program
 
The program is trying to compute and draw an 800x800 pixel image. Where each pixel is defined as a point in the complex plane, we  can perform the calculation with a single process and calculate the image from pixel by pixel from top to bottom row by row. But this would take a much longer execution time because we are not making good use of parallel/concurrent execution. Therefore we can try to use multi-processing/multi-threading to perform this task.

We can make use of a multi-processing Boss and Worker model. The parent process (Boss) will create new child processes (Workers) and assign rows of pixels for the child processes to calculate. The child processes will calculate the rows assigned and return the data back to the parent process. These child processes would run parallel (if multiple cores available) and also be run concurrently. The way that the parent and the child processes communicate is that we are going to use Unix pipes for sending the data (we will discuss the structure soon) and using Signals for interprocess communications (reports to Boss that work is done so that Boss can assign more tasks to Worker if there are still tasks left)

We will also discuss how we will be able to perform the task by using multi-threading later on.

## Pipes

We are going to use two different pipes for the communication of data between the Boss and the Worker. The Task pipe and the Data pipe. The task pipe is used to send the tasks from the Boss to the worker. The task pipe will send the following data structure :

{% highlight c %}
typedef struct task {
int start_row;
int num_of_rows;
} TASK;
{% endhighlight %}
In the Task pipe, the Boss will be the writer and all the Workers would be readers.


The Data pipe is used to send the computed results from the worker to the boss process. The following data structure would be used:  
{% highlight c %}
typedef struct message {
int row_index;
pid_t child_pid;
float rowdata[IMAGE_WIDTH];
} MSG;
{% endhighlight %}

While in the Data pipe, the Boss will be reader and all the Workers would be writers.

## Signals

As we would like to distribute the tasks dynamically, the boss needs to find out which workers are idle, then it assigns a new task to the worker. The trick is that when a worker returns the computation results to the boss, it includes its identity (using the PID) in the message that carries the computation results of the last row of the just completed task. Once the boss notices that, it can place a new task in the Task pipe and uses the SIGUSR1 signal (which we will design the handler) to inform the worker to work on the new task.

A special requirement of this part is to use signals to synchronize between the boss and workers. We use a user defined signal – SIGUSR1 to inform a worker that a new task has been placed in the Task pipe. Thus, a worker only gets a task to work on when it receives the SIGUSER1 signal. To terminate child processes, the boss sends SIGINT signals to its child processes. We know that the calculation have ended if we have received all the 800 rows of the image. 

We will have to define the signal handlers for both SIGUSR1 and SIGINT in the beginning of the program.

## Why do we return the computed results row by row?

All of the child processes made use of the Data pipe to return the results of the blocks of rows. The contents returned from multiple child processes would jumble up together if we return the data in blocks. To prevent this, we have to make use of **atomic guarantee** feature of the pipe. Any data written to the pipe with less than or equal to PIPE_BUF will not get mixed up. In Linux, this value (PIPE_BUF) is 4096 bytes. In our program, each row has 800 pixels, and each pixel is a floating-point number of 4 bytes. So each row is 3200 bytes < 4096 bytes. Therefore the data will not get mixed up.  

## Pseudocode for multi-process program

### Boss process logic

<ul>
    <li> Install signal handlers (SIGUSR1,SIGINT) </li>
    <li> Create pipes (Task,Data) </li>
    <li> Create N workers </li>
    <li> Distribute initial tasks among workers and send SIGUSR1 signal </li>
    <ul>
        <li>while not all results have completed: </li>
        <ul>
        <li> read result from Data pipe and store data</li>
        <li> if result includes worker id and tasks still available</li>
        <ul>
            <li>place tasks in Task pipe and send SIGUSR1</li>
        </ul>
        </ul>
    </ul>
    <li>Terminate all workers</li>
    <li>Draw Image</li>
</ul>


### Worker process logic

<ul>
    <li>While True </li>
    <ul>
        <li>wait for SIGUSR1 </li>
        <li>Get task from Task pipe </li>
        <li>Compute tasks</li>
        <li>Write results to data pipe</li>
    </ul>
</ul>



## Running the program

Compile the C program 
{% highlight console %}
gcc multiprocess.c -o multiprocess -lSDL2 -lm
{% endhighlight %}

If you don't have the SDL2 graphics library you can install it by running: 
{% highlight console %}
sudo apt-get install libsdl2-dev
{% endhighlight %}

The program takes in two parameters: 
1. The number of child processes
2. The number of rows to compute for 1 tasks. 

{% highlight console %}
./multiprocess (number-of-child processes) (number-of-rows-in-a-task)
{% endhighlight %}





## Multi-threaded Process (Part 2)

Now we are going to perform the task using multithreading concept. We are only using one process but we will create multiple threads to perform the computation. So if the concept of threads are a bit hazy you can look at the chapters on Mutex, Conditional Variables and Semaphores chapters from the [textbook reference](http://pages.cs.wisc.edu/~remzi/OSTEP/).


## Producer-Consumer Problem (Bound-buffer problem)

We are still going to have a similar design as the multiprocess program. We are having a Boss and Worker model too. The main thread is the Boss and the newly created threads are Workers. The Boss thread will be the one who will assign the tasks and the Worker threads will be taking in the tasks and perform the following computations. 

So the question arises how do we allow the Boss and Worker threads to communicate?

Previously we have used a Linux Pipe for communication in multi-process program, but now we can take advantage of the fact that threads of a process share the same address space. So we can use a dynamically allocated memory (stored in the Heap) which all of the threads can have access to. We will allocate memory call it "task_buffer" where we can have the Boss thread (Producer) store the tasks in that buffer and then the Worker (Consumer) threads will read the task from that buffer. Now because there are multiple threads accessing this shared resource there will be a need for synchronisation between these threads to prevent Race Condition from happening. 

In this example we are using a Producer-Consumer model to solve the synchronisation problem. This is a pretty famous and classic model and you can read up more on the reference provided. Because I think the explanation in the book is very good. So hopefully you've got to understand the model. In my program I used Semaphores because i think it was simple to understand and makes the code more readable. But you can also use Mutex locks and Conditional Variables to achieve the same effect.

## Semaphores

First off we need to set up the initial values of the Semaphores. We have two semaphores, one for the Producer and one for the Consumers. One is named "Empty" and the other is named "Full". The Empty semaphore is used to signal the Producer that there is an empty spot in the buffer to fill and the Full semaphore is used to signal the Consumer that there is a task ready to be consumed. 

So we first initialize the values of the Semaphore. 

### Initialize Semaphore valus

What would be the values of the semaphores?

The Empty semaphore would be initialized to the size of the buffer. Why? Because initially the buffer is empty and therefore the Producer could do sem_wait(&empty), decrement the value of the semaphore and be allowed to put tasks into the buffer (As long as value of Empty is > 0 and after it has acquired the Lock for accessing the Buffer). Afterwards the Producer perform a sem_post(&full) to signal the Consumer there is task ready to be consumed.

The Full semaphore would be initilized to 0. This is because initially before the Producer performs a sem_post(&full) the Consumer should not be allowed to access the Buffer or else there would be an error trying to read off empty tasks. 

## Termination of Threads

After we have all the tasks being produced and consumed. We will finally reach an end and the Producer will have no more tasks to give. At this point we need to tell the threads to stop working while also finish off their last tasks. How will we do this? Again you can try to think of multiple solutions. 

In the program, I made use of the task_buffer as a communication tool to stop the threads. There is an additional attribute in the Task data structure that i added for this reason. Called 'terminate'. 

{% highlight c %}
typedef struct task {
    int start_row;
    int num_of_rows;
    int terminate; 
} TASK;
{% endhighlight %}

After all tasks have been placed into the buffer by the Producer. We assign new additional N tasks - (N is the number of workers). In these N tasks we assign the terminate value to 1. The worker threads will have to check whether the terminate value is set to 1. If it is set to 1 , then the worker will stop and terminate. 



## Compile the C program

{% highlight console %}
gcc multithread.c -o multithread -lSDL2 -lm -pthread
{% endhighlight %}

## Run the executable
The program takes in three parameters: 
1. The number of worker threads
2. The number of rows to compute for 1 tasks. 
3. Size of buffer

{% highlight console %}
./multithread (number-of-worker threads) (number-of-rows-in-a-task) (buffer size)
{% endhighlight %}










