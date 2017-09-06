# Bedtime Stories

Guides for developing web applications using Ruby on Rails.

## Introduction

This document outlines several patterns that could aid developing Rails applications.
This is not by any means the true and only way to properly factor web applications built with Rails.
There are several other good documents on the Internet talking about the same topics and the reader is encourage to research and compare.

## The problem

The goal is to come to an agreement as a team and consistently use the same patterns and/or coding style across Rails applications.

Benefits for developers:

* Improve testing of isolated components to have more accurate results for stake holders
* To enhance maintainability and extensibility of our applications by improving the underlying architecture.
* To enable developers to work faster and more efficiently by freeing up our mental energy to focus on the business logic we are working to implement instead of _how_ to write the code.

Benefits for the business:

* Software Quality
* Faster feature development
* Better estimation of features
* Less bugs

Of course the document is not a guide on how to build any type of web applications.
There are reasons to use _The Rails Way_ sometimes and the reader must be the decider on which approach to follow.

Using these concepts in most applications will lead us to think more in terms of the actual business domain and not about
the implementation details of how we are delivering data to an end user.
Remember that Rails is just a delivery mechanism - it doesn't come with any details about the business that we are trying to provide a solution for.

## Guides

[ActiveRecord](https://github.com/CasperSleep/BedtimeStories/blob/master/active_record.md)

## Inspiration

### Talks

- [Hexagonal Rails](https://www.youtube.com/watch?v=CGN4RFkhH2M)
- [Domain Driven Rails](https://vimeo.com/106759024)
- [Decoupling from Rails](https://www.youtube.com/watch?v=tg5RFeSfBM4)
- [Architecture the lost years](https://www.youtube.com/watch?v=WpkDN78P884)
- [Built to last: A domain-driven approach to beautiful systems](https://www.youtube.com/watch?v=52qChRS4M0Y&t=1840s&list=PL0C95hfAJNs_5KT7u2aGpZhDfwoWGGvEu&index=3)
- [The design of tests](https://www.youtube.com/watch?v=qT5iriwidRg&list=PL0C95hfAJNs_6BXVI2rTYPUtRHd1pveMc&index=2)

### Articles

- [Seven Patterns to Refactor Fat ActiveRecord Models](http://blog.codeclimate.com/blog/2012/10/17/7-ways-to-decompose-fat-activerecord-models/)
- [Hexagonal Architecture](http://alistair.cockburn.us/Hexagonal+architecture)

- [Screaming architecture](https://8thlight.com/blog/uncle-bob/2011/09/30/Screaming-Architecture.html)

### Books

- [Domain Driven Design by Eric Evans](http://www.amazon.com/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)
- [Implementing domain driven design](https://www.amazon.com/Implementing-Domain-Driven-Design-Vaughn-Vernon/dp/0321834577/ref=sr_1_1?ie=UTF8&qid=1498666274&sr=8-1&keywords=implementing+domain+driven+design)
- [Growing Rails applications in practice](https://pragprog.com/book/d-kegrap/growing-rails-applications-in-practice)
- [Fearless refactoring Rails controllers](http://rails-refactoring.com/)

### Others

- [Housetrip Engineering guidelines](https://github.com/HouseTrip/guidelines)
- [Hanami Framework](http://hanamirb.org/)

This is not the entire list but I think this is a good start.
