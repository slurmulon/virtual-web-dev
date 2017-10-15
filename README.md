# Virtual Web Development

> :zap: Low friction development process for modern web applications

---

## Scenario

Allow me to paint you a picture.

You just started working on a brand new web application or a massive re-write of a legacy one.

You and your team have mapped out the domain, created diagrams, wrote planning documents, performed research on technology, spec'd out requirements, provided estimates and much more.

After you start development you soon run into a complicated problem - the front-end team needs a working API resource in order to make necessary progress. Otherwise they will be blocked from doing a bunch of other integration work. It also risks breaking the original estimation of not only this task but many others.

The front-end team could just manually stub out their methods, and many times this is appropriate, but more than often this approach involves clunky workarounds or abstractions that must be gutted out or contained before the first release can happen.

> **Note**
>
> Dependency injection patterns and tools, such as [Guice](https://github.com/google/guice), can help alleviate this problem.

Once this situation is encountered one of the teams must re-prioritize their work, most likely against their best wishes and intuition, and they inevitably experience an unnecessary amount of pressure to support the feature rapidly.

As any experienced software engineer knows this dynamic will almost certainly lead to a refactor in the not-so-distant future. It also typically involves sacrificing essential safety nets such as unit tests, documentation and DevOps due to increased pressure from management.

This painful process typically repeats itself several times over before the first iteration of a releasable product is completed (released well after the original deadline, of course).

This is all pretty depressing, right? Sure, but fortunately it doesn't have to be this way.

Really.

And the best part is, you can gain a ton of benefit by simply changing the the way you do work that you were going to do (or should do) anyways: documentation.

However, there are additional concepts which Virtual Web Development builds upon that should be adopted by any well-rounded engineering team.

## Definition

Virtual Web Development (VWD) is a development process that incorporates ideas from Test-Driven Development (TDD), Behavior-Driven Development (BDD), and Service Virtualization.

The primary goal of VWD is to reduce development friction between the various integration points of a web application's architecture and to facilitate a healthy amount of upfront design and planning.

It is intended to be complementary, minimally invasive and relatively flexible in how it may be approached. It also integrates cleanly with previously established design processes such as BDD.

While it's easy for an individual engineer have a solid natural sense for processes and techniques that accelerate productivity, it is no doubt beneficial to formally describe these processes as much as possible such that they can be easily reasoned about, refined and adopted.

### Concepts

 - Developers should **only write code in a version-controlled virtual machine or virtual environment** (e.g. Vagrant, Docker, etc.)

 - Developers across teams (such as front-end and back-end) should **always reach a consensus** on an API resource's formats and behaviors **before any actual work is started**

 - Developers should write code with a **mock-first** or **mockist** mindset. When you build around the agreed upon formats well enough, swapping out mock implementations for real ones will involve a minimal amount of work

 - Developers should utilize a **localized proxy service** that establishes a **hybrid interface** which **conditionally mocks API resources and entities** based on the working status of the desired API route

 - Mocks and fixtures should be thorough enough such that a **developer can simply swap out mock implementations for real implementations** while every feature remains functional

 - **API documentation** should be formally described using a **machine-readable specification** such as API Blueprint or OpenAPI

 - API documentation should be used to generate and **centralize your mock fixtures** as much as possible

 - API documentation should be used to **automate as many processes as possible**, such as generating code or spinning up a local instance of the hybrid interface

 - **Static fixtures** should act as a canonical source of information for both integration and unit tests

 - Static fixtures should be normalized in order to **prevent data drift inconsistencies**

 - **Parity** between mock, developer and production/production-like environments should as high as possible

 - **Replicating the state** of any entity in any environment should be trivial

 - Pointing a developer's local virtual machine to an external environment should only require changing an environment variable (i.e. `NODE_ENV='staging'`)

 - A mirror of production should be used by developers when debugging or testing production issues. This **prevents any accidental changes** from taking place on production itself.

### Entities

This follwing list describes the conceptual entities involved in VWD that allow you to properly adhere to the constraints outlined above.

Under each entity is a list of known tools that can be used to create them. The lists of tools should be considered non-exhaustive and purely suggestive. A more detailed list of available technology can be found towards the end of this document.

 - Version-controlled virtual machine image(s)

   * **Purpose**: All developers share and use this VM to develop so that environment parity is at its highest
   * **Tools**: Vagrant, Puppet, Chef, Docker, Terraform

 - Mock API server
   * **Purpose**: Provides mocked API resource fixtures as a stand-alone service
   * **Tools**: Drakov, RAML Mocking Servier, WireMock, Swagger API Auto Mocking

 - Localized hybrid proxy service.

   * **Purpose**: Creates a unifying HTTP interface between your mock API and your real API (hence a "hybrid")
   * **Tools**: NGINX, Sinatra, Express, Koa, Flask

 - Standardized and machine-readable API documentation

   * **Purpose**: Ensures consistency and allows many tasks to be automated
   * **Tools**: API Blueprint, RAML, Swagger, OpenAPI

 - API resource fixtures (typically formatted in JSON)

   * **Purpose**: Describes the various states possible in your API
   * **Tools**: JSON Schema Faker, Faker, Hazy

 - Scripts for capturing API requests and responses

   * **Purpose**: Allows for rapid debugging of production issues and eases maintenance of your documentation and fixtures
   * **Tools**: WireMock, MockServer, vcrpy

### Adherence

It's simple.

As long as the previously defined concepts are incorporated using all of the described entities, VWD zen is achieved.

## Background

For whatever reason I am usually the first person to introduce something similar to VWD to the team, except for one time - the time that I myself was introduced to some of the concepts underlying this process.

A great engineering friend of mine joined a large solar company I was working for, and one of the first tasks he was given was to do a little DevOps and give the team some quick yet much needed wins.

After he dug around the code and talked to engineering team, he soon realized that their greatest struggle was the high amount of development friction that existed between the front-end and back-end teams.

This problem was horribly exacerbated because the company was performing a massive re-write of their platform. It was so large that the client and platform API had to be re-written simultaneously (I know, a recipe for disaster).

He quickly put together a virtual developer ecosystem based on Vagrant and Chef. It offered a consistent and predictable environment for engineers to develop in (none of this "works for me" insanity).

It also included a pattern-based routing proxy server (based on Sinatra) that allowed developers to toggle mocking on the individual API routes of our applications.

Requests to mocked API routes would be redirected to a static JSON fixture while requests to real working routes would be proxied to the configured environment (typically `localhost`).

He also put in some awesome features for proxying hosts and coordinating data, configs and processes between all of our apps, but that's besides the point. In general it was perfectly robust and gave the team way more benefit than we thought it would.

> Perhaps others have utilized this technique at this level for a long time, I don't know because the term "Mock Development" isn't an established term and nobody really talks about it, online or otherwise.
>
> Even if this has been some sort of underground or private practice, I feel it's traditionally been done in an adhoc manner since there has never been any solid tooling for it, until now (more on that in a minute).

Anyways, wrapping up my story. This mock server insantly tore down the sludge-like barrier between the front-end and back-end teams and the development friction evaporated.

Finished API resources could be left untouched while incomplete resources could be easily configured to use mock API resources (i.e. JSON fixtures).

Once an API resource was complete a front-end engineer would simply disable the mock route for the resource, run some tests, perform any necessary fixes and close out the work.

90% of the time the real integration just works and no additional development is required.

But that remaining 10% means there are some situations where swapping out "mock" for "real" doesn't just magically work:

 - When you are using a "hybrid" setup (hybrid = mocks + real) and attempting to coordinate states or map information between fixtures and real data (this is the most common issue)

 - When your mocks and/or fixtures are not thorough enough or contain erroneous information

 - When you cannot encapsulate dispersed logic or states with fixtures alone

 - When your fixtures are excessively randomized (such as random UUIDs). This leads to complications around mapping or validating data. Relates to the first issue.

Either way, the amount of work this saves your software team is inevitably great and worth the initial investment. The maintenance cost is also very low.

## Benefits

Besides practically eliminating the risk of blockage between front-end and back-end work, there are numerous other benefits to utilizing VWD in your projects:

 - Produces quick results that are as close to the real implementation as possible.

 - Forces both teams to agree upon initial request and response formats for the API. This helps to ensure an adequate amount of planning and design work has been done before development work can begin.

 - Allows you to easily replicate or clone production states for debugging. Simply capture the HTTP requests and responses, save them as fixtures, point your mock proxy server at the fixtures and voila :sparkles:.

 - Enables engineers on all sides to retain a healthy flow. If they don't have to switch gears they are more likely to produce a higher quality solution in a shorter amount of time.

 - Modern tooling allows for standardized approaches to this problem. Learn it once, use it anywhere.

 - Provides complete freedom of choice as to where and how much to utilize VWD. Minimally invasive.

 - The fixtures used by the mock server/proxy can also be utilized by unit tests.

 - Allows you to perform integration and isolated testing safely.

 - Dynamic fixture tooling (hazy, hercule, etc.) allows you to keep your fixtures DRY.

 - Pairs cleanly with both Domain-Driven Design (DDD), Test-Driven Development (TDD) and Behavior-Driven Development.

 - The initial time and resource investment is almost always small, except with unusually large and complicated APIs.

 - Eases the nerves of management and stakeholders who are anxious to see progress. However, it should be made clear to them which parts of the application are mocked and which aren't.

## Tech

Over the years I've I ended up utilizing mock development successfully on multiple web application re-writes. However, I did so using different technology.

Our original mocking solution was excellent and served its purpose far beyond what was expected, but it did not utilize any standards around describing the API and its inputs and outputs.

### API Blueprint

This is where [API Blueprint](https://apiblueprint.org) comes in. API Blueprint is a high-level description language (based on Markdown) for specifying the supported behaviors of a web APIs.
It essentially becomes the **official technical documentation** of your API.

It also supports JSON Schema, which gives you even more flexibility when it comes to thoroughly mocking your APIs.

I won't go into all of the details here. There are plenty of solid examples on the API Blueprint website.

The important concept to keep in mind is that API Blueprint offers a standardized, machine-readable method of documenting your API and provides a high amount of flexibility and forgiveness since it's based on Markdown.

Most importantly it can be used to automate a wide number of tasks involved in API development.

#### Modules

There are lots of incredibly useful modules for API Blueprint.

The most relevant module to VWD is **[Drakov](https://npmjs.com/drakov)**. This module effortlessly generates a mock HTTP server based on your API Blueprint definition.

You should take it to the next level by creating a central NGINX, Express or Sinatra server that acts as your hybrid proxy service. This hybrid interface redirects certain requests to the mock server and others to your real implementation based on your needs.

This also helps work around issues related to CORS by piping every request through the same host instead of forcing your client to juggle multiple API integrations (anti-pattern). If you don't do this, you will usually have to choose between the mock and the real implementation instead of being able to freely combine them.

#### Problem

One major downside of API Blueprint is that it is completely denormalized. In other words, you have to write everything in a single and often massive Markdown file. This is quite painful because most of the time you want to share data between your fixtures
or API resources.

For instance, generally APIs will allow clients to access individual entities by unique paths but they will also allow some of the entities to be nested / denormalized into other parent entities.
Unless shared data like this is centralized, the inherent duplication causes future modifications to be verbose, prone to human error and take _way_ longer than they need to.

#### Solution

I wrote a build tool a couple years back that attempted to solve this problem. [`blot`](https://github.com/slurmulon/blot) allows you to write your API Blueprints in individual, normalized files.
It also allows you to generate random fixture data and render your API blueprint as HTML. Think of it as a swiss-army helper module for writing, building and rendering large API Blueprint definitions.

`blot` incorporates two other tools called [`hazy`](https://github.com/slurmulon/hazy) and [`hercule`](https://github.com/jamesramsay/hercule)

 - `hazy` describes a syntax for generating random data into JSON objects and also manages a pool of fixtures that may be queried using JSON Path patterns.

 - `hercule` provides a syntax that allows  generic transclusion of plaintext data. It works great for any plaintext data but was built with an emphasis on Markdown (the syntax is influenced by it).

`blot` is not a perfect or mature tool, not even close. It needs better error handling, more tests and several other things that I haven't had time to address.
I simply have too many projects I'm working on or intend to work on, but if anybody is interested in progressing it further then please don't hesitate to reach out.

### Specifications

There are several other specifications for describing your web APIs.

- [OpenAPI](https://openapis.org/)
- [Swagger](https://swagger.io/)
- [RAML](https://raml.org/)

I have a bias towards API Blueprint because it's minimally invasive, based on Markdown and provides way more flexibility than its competitors.

### Processes

Virtual Web Development shares many concepts and paradigms with [Service Virtualization](https://smartbear.com/learn/software-testing/what-is-service-virtualization/) and can be viewed as an extension of it.

However VWD is a hybrid ecosystem because it concerns itself with more than just services. It also defines guidelines for the developer environment and expects a proxy service for transparently unifying both mock and real data.

Because VWD shares so much with Service Virtualization, it naturally takes inspiration from both **Test-Driven Development (TDD)** and **Behavior-Driven Development (BDD)**, both of which are based on mockist principles.

### Mocking

- [WireMock](http://wiremock.org)
- [MockLab](http://mocklab.io)
- [Mock-Server](http://mock-server.com)
- [vcrpy](https://github.com/kevin1024/vcrpy)
- [mock5](https://github.com/rwz/mock5)
- [json-server](https://github.com/typicode/json-server)

### Servers

- [NGINX](https://nginx.org/en/)
- [Sinatra](http://www.sinatrarb.com/)
- [Express](https://expressjs.com/)

### Environments

- [Chef](https://www.chef.io/chef/)
- [Puppet](http://puppet.com/)
- [Vagrant](https://www.vagrantup.com/)
- [Docker](https://www.docker.com/)
- [Terraform](https://www.terraform.io/)

### Data

- [IPFS](https://ipfs.io)
- [Dat](https://datproject.org)

### Testing

- [Dredd](http://dredd.org/en/latest/)
- [Cucumber](https://cucumber.io/)
- [vREST](https://vrest.io/)

## Conclusion

Although I've used this development process on several re-write projects now in various forms I'm still learning more as I go.

I will continue to extend upon these concepts as I experience the novel and unique needs of the applications I develop. I'm particularly interested in how this concept can be generally applied to other integration points.

A more formal description of VWD is necessary before it can be widely adopted in its most useful form. Any help from the open source community is certainly welcome. It seems to me that some sort of hybrid between API/Service Virtualization and VWD is key.

If you have used something similar to VWD and would like to share your experiences, ideas, or opinions then please open up a Github issue. I would love to talk about this approach more with others!
