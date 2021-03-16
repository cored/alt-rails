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
