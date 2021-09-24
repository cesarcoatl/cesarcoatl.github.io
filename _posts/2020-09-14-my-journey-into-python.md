---
layout: post
title: My journey into Python
cover-img: /assets/img/IMG_2032.jpg
thumbnail-img: /assets/img/python-200x200.png
tags: python
date: 2020-09-14 10:58 -0700
---
At my current job, I was hired as an ASP.NET Developer to maintain and extend an application developed by another employee, but eventually, I received training in Ignition by [Inductive Automation](https://inductiveautomation.com/){:target="_blank"}, which offers the ability to create scripts using Python, or in their case, [Jython](https://docs.inductiveautomation.com/display/DOC80/Python+Scripting#PythonScripting-PythonorJython?){:target="_blank"}.

Jython, being a Java implementation of Python, gives the user access to the Java Platform. This means you get access to both Python and Java using Python 2.7 syntax. But more about Jython on a different post.

As I mention in the ["about me"]({{ site.url }}/aboutme/) section, I’ve been writing lines of code from an early age, but professionally, I started my career working at a small company building Websites with PHP. Although I did an internship where we were working on JSP (back then JavaServer Pages, now [Jakarta Server Pages](https://en.wikipedia.org/wiki/Jakarta_Server_Pages){:target="_blank"}). Oh, memories!

After being exposed to these languages, I believe that learning the syntax for any language is the most trivial task while having (or developing) a [programmer mindset](https://mitcommlab.mit.edu/broad/commkit/coding-mindset/){:target="_blank"} is more important. And the second, most important factor, is having the right tools. There's no doubt about it.

And I must agree that it is very true when people say that Python is friendly and easy to learn because it truly is. To prove this, let's consider the ["Hello, World!" program](https://en.wikipedia.org/wiki/%22Hello,_World!%22_program){:target="_blank"}, as it is the prototypical first program for most coders. I will use some of the [most popular programming languages](https://insights.stackoverflow.com/survey/2019#most-popular-technologies){:target="_blank"}, and you'll be the judge.

C:

```c
#include <stdio.h>
int main() {
   printf("Hello, World!");
   return 0;
}
```

C#:

```csharp
using System;

namespace myApp
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello, World!");
        }
    }
}
```

Java:

```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World");
    }
}
```

And finally, Python:

```python
print("Hello, World!")
```

I have used all of these languages, but I've come to fall in love with Python over the years.

When I started using Python in Ignition, I was only using it for the code behind events, such as the click of a button, or when a window was opened or closed, which is what I had learned from the training and their documentation. But after I embarked on building my first project, I realized the potential of having a tool like Python at my disposal.

Now I have created some packages shared across multiple installations and applied [OOP](https://realpython.com/python3-object-oriented-programming/){:target="_blank"} principles by creating supporting classes in my projects. And in the last four years since I started working with Python, I've learned so much and will continue learning.

I am happy to be writing in Python, and I look forward to many more years.
