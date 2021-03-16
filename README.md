# ALT Rails

*Research project around best practices, tooling and approaches to maintan and develop big Ruby on Rails applications*

## Introduction

[ALT Rails](https://github.com/cored/alt-rails) is inspired by the [ALT .NET](https://docs.microsoft.com/en-us/archive/msdn-magazine/2008/march/%7b-end-bracket-%7d-what-is-alt-net) community. That community started as an answer to growing frustration with the tools and guidance suggested by [Microsoft](https://microsoft.com) and the larger .NET Framework community.

In the Ruby on Rails community, there's not that level of frustration (That I know of). Still, in recent years more companies are showing limitations either with the tools or the overall structure suggested by the framework.

- [RubyHack 2019: Taming Monoliths Without Microservices](https://www.youtube.com/watch?v=uBSIKLgOz_o)
- [Under Deconstruction: The State of Shopify's Monolith](https://shopify.engineering/shopify-monolith)
- [RailsConf 2019: The Past, Present and Future of Rails at GitHub](https://www.youtube.com/watch?v=vIScxVu00bs)

ALT Rails, as the name implies, is about alternative ways of doing things with Ruby on Rails. It's a research project to identify common patterns, tools, and practices to use with the framework in order to support a specific set of principles. 

## ALT Rails Developers Principles 

1. Pragmatic developers who uses whatever works always looking for a better way
> Solutions to problems hardly appear, fighting the tools that we use to deliver the solutions.
> If Ruby on Rails is not serving us at any point in time, we have the right to swap it whenever we see fit, without a lot of hassle. Our applications shouldn't depend on any infrastructure details and a web-framework is infrastructure. 
  
2. Look outside of the Ruby on Rails community to adopt best practices from everywhere
> Ideas are discoveries not inventions. We need to expose ourselves to different perspectives to identify what works and what doesn't.
> Looking outside of the Ruby on Rails community is done to identify not just better tools to replicate within the framework but also better ways to enforce Software Engineering principles.

3. Understand the value of simplification and keep looking ways to improve the code-base

4. Realize that tools are great but what really matter are principles and knowledge
> The best tools are those that encourage the adoption of good Software Engineering principles.
> For instance the [dry-rb](https://dry-rb.org/) project embraces Functional Programming principles like [Immutability](https://en.wikipedia.org/wiki/Immutable_object).

> Immutability promotes stability through the prevention of side effects







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

[ActiveRecord](https://github.com/cored/rails-apps-patterns/blob/master/active_record.md)

## TODO

* RESTful controllers guide
* Service Objects guide
* TDD Workflow guide

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
- [Domain Driven Rails](http://blog.arkency.com/domain-driven-rails/)

### Others

- [Housetrip Engineering guidelines](https://github.com/HouseTrip/guidelines)
- [Hanami Framework](http://hanamirb.org/)

This is not the entire list but I think this is a good start.
