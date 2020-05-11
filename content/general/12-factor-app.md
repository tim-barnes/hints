---
title: "12 Factor App"
date: 2020-05-09T08:12:01+01:00
draft: true
pre: "<b>3. </b>"
tags: ["general", "quality"]
weight: 30
---

### Notes

All quotations from https://12factor.net/.  Additional notes are my own.

### Overview

The 12-Factor App is a set of relatively simple rules for building a modern, cloud deployable application or service.  Quoting the source:


> Use declarative formats for setup automation, to minimize time and cost for new developers joining the project;
> Have a clean contract with the underlying operating system, offering maximum portability between execution environments;
> Are suitable for deployment on modern cloud platforms, obviating the need for servers and systems administration;
> Minimize divergence between development and production, enabling continuous deployment for maximum agility;
> And can scale up without significant changes to tooling, architecture, or development practices.
> The twelve-factor methodology can be applied to apps written in any programming language, and which use any combination of backing services (database, queue, memory cache, etc).


### Personal Observations

Delivery of a fully complying app can be hard, particularly in a start-up context or where more traditional s/w engineering practices hold sway.  Often, baby steps, letting practices settle before applying more techniques is required.

In general, using Docker makes building a 12-factor app much easier.


### The Twelve Factors


#### I. Codebase

> One codebase tracked in revision control, many deploys

Notes:

* Use git (most likely with a common centralised server like GitLab or Github)
* Use configuration (eg Feature Flags) to manage different functionality sets between deployments
* Avoid divergence between development, staging and production
* Avoid long lived branches/Pull Requests.  Anything older than a few weeks is unlikely to be merged.

https://12factor.net/codebase


#### II. Dependencies

> Explicitly declare and isolate dependencies

Notes:

* Don't rely on the existence of system dependencies
* Declare a manifest of dependencies (eg requirements.txt, CMake files, etc)
* Docker makes this **much** easier - dependencies are installed as part of the build process.

https://12factor.net/dependencies


#### III. Config

> Store config in the environment

Notes:

* Config is everything that varies between different deployment targets.  **Not stored in code**  If you manually change a constant in the build process, its broken.
* Generally best to store config in environment variables - these are language and OS agnostic, and therefore visible and understandable to everyone.
* For Kubernetes deployments, Helm can effectively surface these values into a config DSL values.yaml for the service.
* Credentials must be supplied through config, but never checked into the codebase.

https://12factor.net/config


#### IV. Backing services

> Treat backing services as attached resources

Notes:

* No distinction between local and remote backing services.
* Try to avoid directly using APIs, use client libraries instead.
* Most API client libraries require a lot of workarounds to break this rule!

https://12factor.net/backing-services


#### V. Build, release, run

> Strictly separate build and run stages

Notes:
* Each stage is a step in a process, with defined output artefacts.
* It should never be possible to change the code at runtime.
* Docker makes this easy!

https://12factor.net/build-release-run


#### VI. Processes
> Execute the app as one or more stateless processes

Notes:
* Processes are stateless and share nothing between them.  State is persisted in a stateful backing service.
* Processes should be idempotent.

https://12factor.net/processes


#### VII. Port binding
> Export services via port binding

Notes:
* App is exposed using a user-space process on a network port.
* Routing layer manages access to the app.
* Apps can be composed into more complex systems.

https://12factor.net/port-binding


#### VIII. Concurrency
> Scale out via the process model

Notes:
* Share nothing, idempotent processes can scale horizontally very simply.
* Processes can do internal multiplexing (eg using threads), however this often causes more problems than it solves.

https://12factor.net/concurrency


#### IX. Disposability
> Maximize robustness with fast startup and graceful shutdown

Notes:
* Processes are disposable - can be started and stopped at a moments notice.
* Minimise start-up time - processes should be ready to accept requests within a few seconds.
* Gracefully shutdown on receipt of SIGTERM.  Message consumers should NACK or release any current jobs so the broker can deliver to other instances.
* Be robust to sudden death, ie if the process must crash, do it fast.  Don't nail the corpse in the upright position.

https://12factor.net/disposability


#### X. Dev/prod parity
> Keep development, staging, and production as similar as possible

Notes:
* Use continuous deployment.
* Minimise time and personnel gaps between development and production.  If you developed it, you should release it.
* Avoid using different backing services between development and production.

https://12factor.net/dev-prod-parity


#### XI. Logs
> Treat logs as event streams

Notes:
* Write one event per line.
* Write to `stdout` - implement log aggregators (eg ELK stack) as part of infrastructure.
* Log sufficient context to infer app behaviour, eg, correlation ID, request ID or message ID allows you to quickly search for related messages between different apps, and state in backing store.

https://12factor.net/logs


#### XII. Admin processes

> Run admin/management tasks as one-off processes

Notes:
* Admin/management processes run in an identical environment to the app.
* Admin/management processes (like database migration) run against a release.
* Never place admin processes in an app's start up code.


https://12factor.net/admin-processes
