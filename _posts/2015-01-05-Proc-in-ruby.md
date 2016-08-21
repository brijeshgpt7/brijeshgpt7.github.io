---
layout: post
title: Proc and proc in ruby
date: 2015-01-05 21:31:05
image: '/assets/img/'
description: 'Blocks are like closures, because they can refer to variables from their defining context.'
main-class: ruby
color: '#ff0000'
tags:
- Ruby
- Ruby on Rails
twitter_text: DO ONE THING, DO IT WELL.
introduction: 'Basic anonymous functions: the Proc class'
---

{% highlight ruby %}
proc_obj_without_block_not_possible = Proc.new #=> Proc class
proc_obj_without_block_not_possible = proc.new # Kernel#proc
{% endhighlight %}

{% highlight ruby %}
proc_obj_with_block = Proc.new { }
proc_obj_with_block = proc.new { }
{% endhighlight %}

{% highlight ruby %}
proc_obj_without_args = Proc.new do 'Hello'
end
{% endhighlight %}

{% highlight ruby %}
proc_obj_by_proc_class = Proc.new do |str|
  "Hello #{str}"
end
## Now will see the way of calling a proc object of Proc class

p proc_obj_by_proc_class
p proc_obj_by_proc_class.call
p proc_obj_by_proc_class.call('Foo')
p proc_obj_by_proc_class.('Foo')
p proc_obj_by_proc_class['Foo']
p proc_obj_by_proc_class.call('Foo', 'Bar')
{% endhighlight %}

{% highlight ruby %}
proc_obj_by_kernel_proc = proc do |str|
  "Hello #{str}"
end
## Now will see the way of calling a proc object of kernel module

p proc_obj_by_kernel_proc
p proc_obj_by_kernel_proc.call
p proc_obj_by_kernel_proc.call('Foo')
p proc_obj_by_kernel_proc.('Foo')
p proc_obj_by_kernel_proc['Foo']
p proc_obj_by_kernel_proc.call('Foo', 'Bar')
{% endhighlight %}


#### Now About Lambda

{% highlight ruby %}
proc_obj_by_lambda_without_block = lambda { }
proc_obj_by_lambda_without_block = -> { }
proc_obj_by_lambda = lambda do |str|
  "Hello #{str}"
end

# p proc_obj_by_lambda
# p proc_obj_by_lambda.call
p proc_obj_by_lambda.call('Foo')
p proc_obj_by_lambda.('Foo')
p proc_obj_by_lambda['Foo']
# p proc_obj_by_lambda.call('Foo', 'Bar')
{% endhighlight %}

#### First difference between proc and lambda


{% highlight ruby %}
def return_from_proc
  prok = proc { p 'I am in proc block'}
  prok.call()
  return 'I am proc return value'
end


def return_from_lambda
  lamda = proc { p 'I am in lambda block'}
  lamda.call()
  return 'I am lambda return value'
end

return_from_proc
return_from_lambda
{% endhighlight %}


#### Second difference between proc and lambda

{% highlight ruby %}
prok = proc { |a,b| "Hello #{a}===and==#{b}"}
prok.call
prok.call('Ajay')
prok.call('Ajay', 'Vijay')
prok.call('Ajay', 'Vijay', 'Sanjay')

stabby_lambda = ->(a,b) { "Hello #{a}===and==#{b}"}
stabby_lambda.call
stabby_lambda.call('Ajay')
stabby_lambda.call('Ajay', 'Vijay')
stabby_lambda.call('Ajay', 'Vijay', 'Sanjay')
{% endhighlight %}