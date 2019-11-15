---
title: "Calculating the Mandelbrot Set"
date: 2019-11-14
tags: [operating systems, multithreading, multiprocessing, Unix]
excerpt: "Mandelbrot set"
---

# Mandelbrot Set 
[Link to the github repository](https://github.com/MarcoBrian/MandelbrotSet)


<figure>
  <img src="/images/mandelbrot/mandelbrot.png" alt="mandelbrot set">
  <figcaption><i>The Mandelbrot Set</i></figcaption>
</figure>

Here is a [youtube video](https://www.youtube.com/watch?v=NGMRB4O922I) discussing what the mandelbrot set is.


The purpose of this project is to demonstrate how a computationally intensive application could be executed **faster** by dividing the compuational workload into smaller tasks, and then execute them in parallel. Here in this project we are making use of multiprocessing and multithreading (one process) tecniques in order to do that. 

We are not going to dive deep into the theory behind the Mandelbrot set but here is a good a [youtube video](https://www.youtube.com/watch?v=NGMRB4O922I) that gives a good idea of what the Mandelbrot Set is.

The program is trying to compute and draw an 800x800 pixel image. Where each pixel is defined as a point in the complex plane, we can perform the calculation with a single process and calculate the image from pixel by pixel from top to bottom row by row. But this would take a much longer execution time because we are not making good use of parallel/concurrent execution. 

We can make use of a multi-processing Boss and Worker model.  The parent process (Boss) will create new child processes (Workers) and assign rows of pixels for the child processes to calculate.  The child processes will calculate the rows assigned and return the data back to the parent process. These child processes would run parallel (if multiple cores available) and also be run concurrently. The way that the parent and the child processes communicate is that we are going to use Unix pipes for sending the data (we will discuss the structure soon) and using Signals for interprocess communications (reports to Boss that work is done so that Boss can assign more tasks to Worker if there are still tasks left)


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

