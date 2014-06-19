---
layout: post
title:  "A thread-safe singleton in C++11"
date:   2014-06-09 19:00:00
categories: testpost, coding
tags: cpp c++
---

During my time at university (relevant classes were in 2013), the C++11 standard was not mentioned at all. What I got to learn in depth were the problems one could face in C++ trying to make things thread-safe, even though a lot of those problems have been dealt with within the new standard.

For example, I was taught about the problems of making the Singleton-pattern thread-safe: I learnt about "double-checked locking" and then got to read why that might not help[^1][^2]. But, lo and behold, all that is obsolete in C++11:

"If control enters the declaration concurrently while the variable is being initialized, the concurrent execution shall wait for completion of the initialization."[^3]

Hence, this simple and lock-free "Meyers-Singleton" is now thread-safe:
{% highlight c++ %}
static Singleton& getInstance()
{
     static Singleton s;
     return s;
}
{% endhighlight %}

I have to admit that this is all common [wikipedia knowledge](http://en.wikipedia.org/wiki/Double-checked_locking#Usage_in_C.2B.2B11) by now -  but I'm still [testing Jekyll]({% post_url 2014-06-08-testing-jekyll %}) and this was a decent way to test syntax highlighting, footnotes and post-linking without posting total nonsense.


[^1]: [Meyers, S., & Alexandrescu, A. (2004). C++ and the perils of double-checked locking: Dr. Dobb’s Journal](http://erdani.com/publications/DDJ_Jul_Aug_2004.pdf)
[^2]: *Addendum: reading this again, I feel I make my university look worse than it was. Most courses were up-to-date, often adopting new standards in their beta phases. But C++ was an exception, certainly due to the fact that it was not a big part of the curriculum anyway.*
[^3]: §6.7 [stmt.dcl] p4 of "ISO International Standard ISO/IEC 14882:2011(E) – Programming Language C++"