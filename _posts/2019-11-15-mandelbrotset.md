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

The program is trying to compute the and draw a 800x800 pixels image. Where each pixel is defined as a point in the complex plane. We can perform the calculation with a single process and calculate the image from top to bottom row by row. Pixel by pixel. 






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

