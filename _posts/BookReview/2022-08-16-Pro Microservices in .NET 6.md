---
layout: post
title:  'Pro Microservices in .NET 6: With Examples Using ASP.NET Core 6, MassTransit, and Kubernetes'
author: Faten Elhariry
categories: 'Book Review'
tags: microservice, 'Book Review'
---

this a quick review for [oreilly book](https://learning.oreilly.com/library/view/pro-microservices-in/9781484278338/)

#### chapter 3
    - this chapter illusterate the 
      - business that we are going to build it is transfering company that define and manage three parts (reserve the carrier based on a list of quotes, provide payment. Carriers can manage their fleet and base costs per mile plus extra costs based on particular needs.)

      - then general idea about what is DDD by define the main parts of it **(Domain, Ubiquitous Language, Bounded Contexts, and Aggregates with Aggregate Roots.)**

        - **Domain** : used to define the main domain for the project and define the main focus and the functionality relate to the    main domain **the main domain here is: hotshot load management** 
          - **subdomain**: 
             is the process of digging deeper into the business to define the relate business process for example (accounting, invoice system) `This grouping of focus is known as` **the Problem Space.** 
            - subdomain should also exist in groups separate from others. 
            - The groups of code that provide functionality for their subdomain are called **a bounded context**
              - **The bounded context exists in what is known as the Solution Space**
            - There are three subdomain types: Core, Supportive, and Generic.
              - **Core** Each core subdomain in an application contains one or more bounded contexts that are critical to the company.
              - **Supportive** The supportive subdomains are not deemed critical but contain code that is supportive of the business.
              - **Generic** Lastly, generic subdomains are those that are replaceable with off-the-shelf solutions.
        
        - **Ubiquitous Language**
          - One of the most important artifacts of DDD is the Ubiquitous Language (UL), is a collection of phrases and terms that helps everyone involved to have a clear and concise understanding of business processes.(unified lanaguage used by all stackholder - business, developer and testing) to avoid confuse 
          - also we should have concise names for namespaces, classes, and methods helps the code stay in line with the UL.

        - **Bounded Contexts**
            is a collection of codes that implements business processes in a subdomain distinctly different from other processes.
            - creation of a customer account, while another is notification.

        - **Aggregates and Aggregate Roots**
          Not surprisingly, there can be multiple classes in a bounded context. There is a relationship between the classes based on dependency. **This dependency may be due to inheritance or composition** 
          - Each group of these classes is an Aggregate and the top-level class in an Aggregate Root.
          <img src="/images/BookReview/1.png" /> 
          <img src="/images/BookReview/2.png" /> 

