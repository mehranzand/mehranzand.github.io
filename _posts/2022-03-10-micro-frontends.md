---
layout: post
title:  "Micro frontends"
subtitle: "The idea to breaking down front-end app into smaller"
date: 2022-03-10 00:00:00 +000
categories: architecture
tags: architecture micro-frontends
comments: true
author: "Mehran Zand"
meta: "architecture micro frontends micro-frontends"
---
Every software web development team in the world depends majorly on front-end development to develop, deploy and market products to their customers.
If you have large scale front-end app with multiple front-end teams that use different JavaScript frameworks, maybe it's time to think about switching to micro-frontends solutions, the micro-service idea for front-end.
<br />
It means breaking down large monolithic projects into smaller, more manageable pieces, which are independently developed and owned by different teams, with the power to independently build and ship products simultaneously.

<br />
![Micro frontends deployment](/assets/2022-03-10-micro-frontends/deployment.png)

<br />
## Why micro frontends?
There are some of the key benefits you can get from micro frontends are:

<br />
### Technology Independent
Micro frontend architectures resemble back-end architectures where back ends are composed from semi-independent microservices. You can use multiple frameworks in your application. However, it should be done mindfully and transparently to avoid confusion. Nevertheless, you have a choice of what framework to use for a particular task. Additonally, a development team can choose their own technology.

### Deployment Independent
You can deploy and deliver micro frontends with no affect to the entire application as easily.
The changes will affect precisely that part of the business process that it has covered.
all teams will develop, build, test, and release components in a standardized way using the same development environments and through standard build pipelines.

### Rapid and Incremental Upgrades
So that each team can constantly deliver new features, upgrades, fixes and rollbacks to their products used throughout our applications, without breaking functionality or user-experience at all.

### Easier Hiring
 First, with micro frontends, you look only for professionals to work on a specific part of an app where a particular tech stack is used. Second, since many tech stacks would be in use on the same project, it becomes easier to hire new developers. It can be most imoportant part for leaders and CTO's.

### High Resilience 
In micro frontend web apps are distributed in small chunks of services starting from the UI to database that works independently. So, even if one feature faces the issue, it doesn’t affect the rest of the web app. And this feature makes it possible to recover from the error faster.



## In conclusion
Along with several advantages of using micro frontend that mentioned this article there are some disadventages with micro frontend such as complexity in testing, routing and so on but it heavily depends on your business case, whether you should or should not adopt micro frontends. If you have a small project and team, micro frontend architecture is unjustified. At the same time, large projects with distributed teams and a massive number of requests benefit a lot from building micro frontend applications. That is why today, micro frontend architecture is already widely used by many large companies, and that is why you should opt for it too. 

