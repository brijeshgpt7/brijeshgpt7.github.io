---
layout: post
title: Observers in Rails
date: 2014-07-30 21:31:05
image: '/assets/img/'
description: Observer is a great way to reduce the clutter that normally comes when the model class is burdened with functionality that doesn't pertain to the core responsibility of the class.
main-class: ruby
color: '#ff0000'
tags:
- Ruby
- Ruby on Rails
twitter_text: DO ONE THING, DO IT WELL.
introduction: "Observer is a great way to reduce the clutter that normally comes when the model class is burdened with functionality that doesn't pertain to the core responsibility of the class."
---


{% highlight ruby %}
## Life without Observers
{% endhighlight %}

Let's say you need to listen to changes in a `model's attributes` and trigger some events based on those changes. If you look at the following example, I've done exaclty that. If the status of application is changed an email is sent.

{% highlight ruby %}
class User < ActiveRecord::Base
  after_save :email_confirmation
  def open?
    status == 'Open'
  end
  def closed?
    status == 'Closed'
  end
  def pending?
    status == 'Pending'
  end
  def email_confirmation
    if status_changed?
      ApplicationMailer.closed_notification(self)  if closed?
      ApplicationMailer.open_notification(self)    if open?
      ApplicationMailer.pending_notification(self) if pending?
    end
  end
end
{% endhighlight %}

The above piece of code __violates__ the __Single Responsibility Principle__, which states that each class should have "only one responsiblity". The `User` model should only deal with saving and manipulating the application object. Sending emails is not part of it's responsiblity.

{% highlight ruby %}
## ActiveRecord::Observers
{% endhighlight %}

We can delegate the responsibility of observing changes in model attributes and responding to those changes, to a separate `Observer class`. Model specific event handling is much cleaner this way. This way, we can refrain from polluting the model by adding not un-necessary methods.

{% highlight ruby %}
class UserObserver < ActiveRecord::Observer
  def after_save(appl)
    if status_changed?
      ApplicationMailer.closed_notification(appl)  if appl.closed?
      ApplicationMailer.open_notification(appl)    if appl.open?
      ApplicationMailer.pending_notification(appl) if appl.pending?
    end
  end
end
{% endhighlight %}

You can read more about the [Observer Design Patterns](observer_design_patterns).
![](/assets/img/reading.gif)

----

### Note

>**1.** Observer names are inferred form the model names. So if you name your observer as `ApplicationObserver`, rails knows that it's observing the `Application` model.
>
>**2.** Your observers need not be model specific. SRP states that "Responsibility should be entirely encapsulated by the context". So by this definition you can have a single observer which listens to changes in multiple models and perfom a single function say, sending an email or publishing a notification. To acomplish this you can use the `ActiveRecord::Observer.observe` method.
>
>**3.** Place your observers in `app/models` or `app/models/observers`.
>
>**4.** Finally, Observers have been __Removed__ form the rails core after `Rails 3.2`. You need to include the `rails-observers gem` to use `observers` in later `rails` versions.

[observer_design_patterns]:  http://sourcemaking.com/design_patterns/observer