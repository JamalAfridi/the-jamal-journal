---
title: Removing dependencies with the Publish-Subscribe pattern
description: This is a post on Publish-Subscribe pattern in a rails application
date: 2020-12-17
tags:
  - Ruby
  - Rails
  - Software-Architecture
layout: layouts/post.njk
---

![Publish-Subscribe](/img/pub-sub.png)

At [Hiring Hub](http://www.hiring-hub.com/) we tend to discuss our application architecture from time to time, always looking to improve and evolve it. One of these talks spawned the idea of implementing event-driven programming with the Publish-Subscribe pattern.

## Publish-Subscribe pattern

In software architecture, publish-subscribe is a messaging pattern where senders of messages (publishers) announce events to multiple interested receivers (subscribers) without coupling the senders to the receivers.

In other words, publish-subscribe is a pattern used to communicate messages between different system components without those components knowing anything about each other.

Imagine a radio station (publisher). You (subscriber) tune in to the frequency of this radio station (subscribe) and start to sing along when you hear a song (react to the event).

This is a very convenient pattern when you have many parts of your program that must respond differently to a specific event.

## Reducing model dependencies

Let’s use a real-world example of when a job is posted onto Hiring Hub’s marketplace. In this case, when a job is posted to the marketplace, an email should be sent to notify recruiters of the job so they can begin to engage with that role.

Doing all of this in one place will result in high coupling between different components, leading to a program that is harder to maintain.

Below is a code example of the scenario mentioned above, where we are sending an email from the job model using a callback. Here the job model knows more than it should and has more reasons to change. It is aware of some important domain concepts, like sending a notification that there is a new job on the platform. This violates the single responsibility principle in object-oriented design.

```ruby/
class Job < ApplicationRecord

	after_commit -> { NewJobNotification.perform_later(id) },
             on: :update,
             if: -> { saved_change_to_published_status? && live? }
end
```

Using the Publish-Subscribe pattern, we can refactor this to the below images. For this, we would have a service that posts a job and broadcasts a message to everyone interested in that job being posted. All other services isolated from each other will perform the necessary work as soon as they receive the event they need.

```ruby/
class Employer::JobsController < JobsController
  include Wisper::Publisher

  def create
    @job = Job.new(job_params)

    if @job.save
      publish(:job_posted, @job.id)
      redirect_employer
    end
  end
end
```

```ruby/
class JobListener
  def self.job_posted(job_id)
    job = Job.find(job_id)
    NewJobNotificiation.perform_later(job_id) if job.live?
  end
end
```

```ruby/
class Job < ApplicationRecord
end
```

In this example, the Publish-Subscribe pattern eliminates the callback in the job model, removing the direct dependency to the email service and ensuring loose coupling. Expanding the behaviour to additional actions is just a matter of hooking to the desired event.

## Advantages/disadvantages of Publish-Subscribe
  * 👍 Less coupling between classes
  * 👍 High level of scalability
  * 👍 Simple to test events that are published/received
  * 👍 Performance gains with async event handling
  * 👎 Dealing with asynchronous data flows
  * 👎 Need full integration tests to check the system as a whole

## Wisper Gem

There are a lot of solutions for providing Publish-Subscribe functionality in your rails application. At Hiring Hub, we decided to go with a small library called [Wisper](https://github.com/krisleech/wisper).

## Final Thoughts

Using an event-messaging pattern can be powerful in decoupling responsibilities between different contexts in your code; handling events asynchronously can lead to a more responsive application. However, this approach is not perfect; there is no right or wrong way to get started. Take an incremental approach and see how things go. It’s a step forward towards improving the design of your application.