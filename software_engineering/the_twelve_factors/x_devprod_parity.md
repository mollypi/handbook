# X. Dev\/prod parity

## Keep development, staging, and production as similar as possible

Historically, there have been substantial gaps between development \(a developer making live edits to a local [deploy](https://github.com/mollypi/site-reliability-engineer-handbook/tree/ba847bfb6a351eac14a0bf1f23b13fa51652f83b/codebase/README.md) of the app\) and production \(a running deploy of the app accessed by end users\). These gaps manifest in three areas:

* **The time gap:** A developer may work on code that takes days, weeks, or even months to go into production.
* **The personnel gap**: Developers write code, ops engineers deploy it.
* **The tools gap**: Developers may be using a stack like Nginx, SQLite, and OS X, while the production deploy uses Apache, MySQL, and Linux.

**The twelve-factor app is designed for** [**continuous deployment**](http://www.avc.com/a_vc/2011/02/continuous-deployment.html) **by keeping the gap between development and production small.** Looking at the three gaps described above:

* Make the time gap small: a developer may write code and have it deployed hours or even just minutes later.
* Make the personnel gap small: developers who wrote code are closely involved in deploying it and watching its behavior in production.
* Make the tools gap small: keep development and production as similar as possible.

Summarizing the above into a table:

|  | Traditional app | Twelve-factor app |
| :--- | :--- | :--- |
| Time between deploys | Weeks | Hours |
| Code authors vs code deployers | Different people | Same people |
| Dev vs production environments | Divergent | As similar as possible |

[Backing services](https://github.com/mollypi/site-reliability-engineer-handbook/tree/ba847bfb6a351eac14a0bf1f23b13fa51652f83b/backing-services/README.md), such as the app's database, queueing system, or cache, is one area where dev/prod parity is important. Many languages offer libraries which simplify access to the backing service, including _adapters_ to different types of services. Some examples are in the table below.

| Type | Language | Library | Adapters |
| :--- | :--- | :--- | :--- |
| Database | Ruby/Rails | ActiveRecord | MySQL, PostgreSQL, SQLite |
| Queue | Python/Django | Celery | RabbitMQ, Beanstalkd, Redis |
| Cache | Ruby/Rails | ActiveSupport::Cache | Memory, filesystem, Memcached |

Developers sometimes find great appeal in using a lightweight backing service in their local environments, while a more serious and robust backing service will be used in production. For example, using SQLite locally and PostgreSQL in production; or local process memory for caching in development and Memcached in production.

**The twelve-factor developer resists the urge to use different backing services between development and production**, even when adapters theoretically abstract away any differences in backing services. Differences between backing services mean that tiny incompatibilities crop up, causing code that worked and passed tests in development or staging to fail in production. These types of errors create friction that disincentivizes continuous deployment. The cost of this friction and the subsequent dampening of continuous deployment is extremely high when considered in aggregate over the lifetime of an application.

Lightweight local services are less compelling than they once were. Modern backing services such as Memcached, PostgreSQL, and RabbitMQ are not difficult to install and run thanks to modern packaging systems, such as [Homebrew](http://mxcl.github.com/homebrew/) and [apt-get](https://help.ubuntu.com/community/AptGet/Howto). Alternatively, declarative provisioning tools such as [Chef](http://www.opscode.com/chef/) and [Puppet](http://docs.puppetlabs.com/) combined with light-weight virtual environments such as [Vagrant](http://vagrantup.com/) allow developers to run local environments which closely approximate production environments. The cost of installing and using these systems is low compared to the benefit of dev/prod parity and continuous deployment.

Adapters to different backing services are still useful, because they make porting to new backing services relatively painless. But all deploys of the app \(developer environments, staging, production\) should be using the same type and version of each of the backing services.

