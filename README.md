# ALT Rails

## Introduction

> So what is ALT .NET? And how does it differ from the .NET that we already know and love? What are these values that many of us think are missing? What are these alternative tools, techniques, and practices that ALT .NET'ers are espousing? Let's first examine the original tenets of being an ALT .NET developer.

**ALT Rails** tries to answer the same questions for Ruby on Rails. There's an increase of interest on this topic as some of the big companies using the framework share: 

- [RubyHack 2019: Taming Monoliths Without Microservices](https://www.youtube.com/watch?v=uBSIKLgOz_o)
- [Under Deconstruction: The State of Shopify's Monolith](https://shopify.engineering/shopify-monolith)
- [RailsConf 2019: The Past, Present and Future of Rails at GitHub](https://www.youtube.com/watch?v=vIScxVu00bs)


## ALT Rails Developers Principles 

1. Pragmatic developers who use whatever works are always looking for a better way
> Solutions to problems hardly appear, fighting the tools we use to deliver the solutions.
> If Ruby on Rails is not serving us at any moment, we have the right to swap it whenever we see fit, without a lot of hassle. Business rules shouldn't be couple to any infrastructure details.  A web framework is part of the infrastructure. 
  
2. Look outside of the Ruby on Rails community to adopt best practices from everywhere
> Ideas are discoveries, not inventions. 

3. Understand the value of simplification and keep looking for ways to improve the code-base


4. Realize that tools are great but what matters are principles and knowledge
> The best tools encourage the adoption of good Software Engineering principles.

### Ruby on Rails Components

Ruby on Rails promotes a structure around the [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) pattern.

The **Ruby on Rails** definition of boundary resides in three buckets: a *Model*, a *Controller*, and a *View*.

When describing complex problems is essential to use rich and expressive language to do so. The language used to communicate changes in a Ruby on Rails application is not expressive because it uses those three buckets. 

The language used to express a solution should include concepts from the problem's domain.

It is not cost-effective to share a mental model with businesspeople describing technical details. 

There is a big gap between *MVC*, which describes a web-application structure, and the application's core domain. The persistence abstraction in *Ruby on Rails* application often accrues a high level of complexity. 

#### ActiveRecord: Ruby on Rails Persistence Layer 

*ActiveRecord* models characterize themselves by mapping the database structure and application code. 
  
*ActiveRecord* models include persistence logic to manage the database and the application mapping. The more logic an *ActiveRecord* model has, the easier it is to introduce a *God Object* to the application. 

> In object-oriented programming, a God object (sometimes also called an Omniscient or All-knowing object) is an object that references a large number of distinct types, has too many unrelated or uncategorized methods, or some combination of both. 

*God objects* are hard to evolve because they are hard to test.

> Another item that comes up is that they're hard to evolve because of all these complex interactions between different methods inside of these God objects [^2]

### Evolving a Software Architecture

> The architecture of a software system is the shape given to that system by those who build it[^1]

Developers drive the shape of a system while introducing new features to a plan.

The final structure, thus, supports operational business processes. In other words, it is not possible to suggest a pre-defined design that will fit every project.

**ALT Rails Way** goal is to identify solutions for everyday problems with big *Ruby on Rails* applications. The following recipes and patterns are more of reference material than a prescription. 


## References

[^1]: [Robert C. Martin] (2018): *Clean architecture: a craftsman's guide to software structure and design*(https://www.amazon.com/Clean-Architecture-Craftsmans-Software-Structure-ebook-dp-B075LRM681/dp/B075LRM681/ref=mt_other?_encoding=UTF8&me=&qid=)
[^2]: [Anthony Eden] (2021): *10 Years In - The Realities of Running a Rails App Long Term - Anthony Eden*(https://youtube.com/watch?v=Xd41xwQNYbY&t=289)




