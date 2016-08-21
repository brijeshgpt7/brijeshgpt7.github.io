---
layout: post
title: Ruby Splat
date: 2015-04-28 08:39:47
image: '/assets/img/'
introduction: Understanding the basic of Ruby Splat
main-class: ruby
color: '#ff0000'
tags:
- Ruby
- Ruby On Rails
image: "/assets/img/ruby.jpg"
categories:
twitter_text: DO ONE THING, DO IT WELL.
description: "You may use an operator, but do a little digging and you'll find that you've only been scratching the surface of what it's capable of. The humble splat operator (* and **) is a great example."
---

Ruby is more dynamic in nature. So Let's see ...

{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]  #=> [:first, :second, :third, :fourth]
a1 = :first, :second, :third, :fourth    #=> [:first, :second, :third, :fourth]
{% endhighlight %}


{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
a2 = [:before, a1, :after]
a2  #=> [:before, [:first, :second, :third, :fourth], :after]
a2.flatten  #=> [:before, :first, :second, :third, :fourth, :after]
{% endhighlight %}

Nice Ahh!!! ...

{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
a2 = [:before, *a1, :after]
 #=> [:before, :first, :second, :third, :fourth, :after]
{% endhighlight %}


{% highlight ruby %}
x, y, z = 1, 2, 3
p x #=> 1 
p y #=> 2
p z #=> 3
{% endhighlight %}

Wooo Wooo....

{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
x, y, z = *a1
p x #=> :first
p y #=> :second
p z #=> :third
{% endhighlight %}

                                                                       
{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
x, y, z = :before, *a1
p x #=> :before 
p y #=> :first
p z #=> :second 
{% endhighlight %}

But wait, there's more.... 
       
{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
*x, y, z =  *a1
p *x #=> [:first, :second]
p y  #=> :third
p z  #=> :fourth
{% endhighlight %}

{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
x, *y, z =  *a1
p x  #=> :first
p *y #=> [:second, :third]
p z  #=> :fourth
{% endhighlight %}

This is the common practice that we used with methods parameter.

{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
x, y, *z =  *a1
p x  #=> :first 
p y  #=> :second
p *z #=> [:third, :fourth]
{% endhighlight %}

{% highlight ruby %}
a1 = [:first, :second, :third, :fourth]
first, *rest = *a1
p first #=> :first
p *rest #=> [:second, :third, :fourth]    
{% endhighlight %}

Where am I going with this? Stay with me here.
{% highlight ruby %}
def sum3(x, y, z)
  x + y + z
end

triangle = [1,2,3]
p sum3(*triangle) #=> 6

{% endhighlight %}


{% highlight ruby %}
def greet(greeting, *names)
  names.each do |name|
    p "#{greeting}! #{name}"
  end
end
greet('Good Morning', 'Vinay', 'John', 'Shane')

#=> "Good Morning! Vinay"
#=> "Good Morning! John"
#=> "Good Morning! Shane"
{% endhighlight %}

{% highlight ruby %}
def randon_draw(num_times, num_draws)
  num_times.times do
    draws = num_draws.times.map{ rand(10)}
    yield(*draws)
  end
end

randon_draw(5, 3) do |first, *rest|
  p "#{first} #{rest}"
end
#=> "1 [7, 3]"
#=> "4 [5, 2]"
#=> "5 [1, 7]"
#=> "2 [9, 2]"
#=> "8 [9, 1]"

{% endhighlight %}
