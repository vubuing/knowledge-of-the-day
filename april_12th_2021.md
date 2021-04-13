##### The knowledge that I learned for April 12th, 2021.

This is inherit from [The twelve-factor app](https://12factor.net/)

# The twelve-factor application

---

This is the list of methodologies that should be applied to software which serves as service:

- To minimize time and cost for new developers joining the project -> Use **declarative** format for setup automation (Well documented, one line build);
- **Maximum portability** between execution environments -> Compatible with Operating Systems;
- Suitable for **deployment** on **cloud platforms**;
- Enable **continuous deployment** -> **Minimize divergence** between development and production;
- Able to **scale up** without significant changes to tooling, architecture, or development practices -> Using abstraction in OOP to separate the business code with those tools' code.
  > The twelve-factor methodology can be applied to apps written in any programming languages, and which use any combination of backing services (db, queue, memory cache, etc.).

1. Codebase:
   > One codebase tracked in revision control, many deploy

There is only one codebase per app, but app will have many deploys. Usually, we have one deploy for the: local, development, staging and production environment.

The most stable release should be deployed on staging and production environment.

2. Dependencies:
   > Explicitly decare and isolate dependencies

**A 12-factor app never relies on implicit existence of system-wide packages**. It declares all dependencies via a dependency declaration manifest. Furthermore, it uses a dependency isolation tool during execution to ensure that no implicit dependencies "leak in" from the surrounding system. The full and explicit dependency specification is applied uniformly to both production and development.

- For Golang code, we use [go dep](https://github.com/golang/dep) and [go module](https://github.com/golang/go/wiki/Modules) to manage dependencies.
- For JS code, we use [npm](https://www.npmjs.com/).
- For iOS/macOS app, we use [Carthage](https://github.com/Carthage/Carthage).
- For Android, we use Gradle.

Every dependency should be pin a clear version for stability and should be cloned into the code repo.

3. Configuration:
   > Store config in the environment

Use a file to store all environment variables of the app. Example in Go, the binary file could be run in:

- Load config from .env files,
- Load config from environment variables,
- Load default config.

4. Backing services:

Backing services (or infrastructure services) are datastores (such as MySQL or PostgreSQL), messaging/queueing systems (such as RabbitMQ or Beanstalkd), SMTP services for outbound email (such as Postfix), and caching systems (such as Memcached).

**Backing services should be treated as attached resources**. The code for a 12-factor app makes no distinction between local and third party services. Both are attached resources, accessed via a URL or other locator/credentials stored in the config. We use environment variables to configure backing services, so we can easily change them.

5. Build, Release and Run:

You must strictly separate the Build (binary), Release (binary + env config) and Run (exec runtime) stages.

We have [five separate environments](https://github.com/dwarvesf/playbook/blob/master/engineering/environment.md), any of them is isolated with each other. We take advantage of Docker and Kubernetes for this factor. For Golang code, we use the binary from **build** stage in docker image for **release** stage.

6. Processes:

**Execute the app as one or more stateless processes**. 12-factor processes are stateless and share-nothing. Any data that needs to persist must be stored in a stateful backing service, because resources in cloud environment are ephemeral and should also be immutable.

We have a perfect match with containers because they are designed to run with just one scope, and they are ephemeral.

7. Port Binding:

Exporting services via port binding will allow us to become a backing service for another app via URL.

We define ports for all services in Dockerfile.

8. Concurrency:

In the 12-factor app, processes are first class citizen. Processes in the 12-factor app take strong cues from the unix process model for running service daemons. Using this model, the developer can architect their app to handle diverse workloads by assigning each type of work to a corresponding process type.

The cloud support horizontal scale by allowing us to create replicas of our application on-demand.

9. Disposability:

12-factor apps processes can be started and stopped at any time. Because of that, we have to strive to **minimize the startup time** and **shut down them gracefully**.

For instance, we shouldn't stop an app when it's writing to a backing service. To do so, our app must be able to capture signals, ensure that we finish calls, and then stop the app.

10. Dev/Prod parity:
    > Keep development, staging, and production as similar as possible.

12-factor app is designed for Continuous Deployment (CD) by keeping the gap between dev and prod small:

- Make the time gap small: dev may write code and have it deployed hours or even just minutes later;
- Make the personnel gap small: dev who wrote code closely involved in deploying it & watching its behavior in production;
- Make the tools gap small: keep dev & prod as similar as possible

We achieve this using CI/CD flow with Docker containers. Every time dev merge to develop/master branch; this will be auto deployed to dev/prod env. Stacks between local/dev/prod/staging env must be the same.

11. Logs:

- Treat logs as event streams
- Provide visibility into the behavior of a running app. In server-based environments, they are written to a file on disk (a "logfile"); but this is only an output format
- Logs are the stream of aggregated, time-ordered events collected from the output streams of all running processes & backing services
- Put logs in stdout
- In development, the dev will view this stream in the foreground of their terminal -> observe the app's behaviors
- In staging/prod deploys, each process' stream will be captured by the exec env, collated together with all other streams from the app, and routed to 1 or more final destinations for viewing and long-term archival
- The event stream for an app can be routed to a file, or watched via realtime tail in a term
- Most significantly, the stream can be sent to a log indexing & analysis system such as Splunk, or a general-purpose data warehousing system such as Hadoop/Hive. These systems allow great power & flexibility for introspecting an app's behavior over time, including:
  - Finding specific events in the past
  - Large-scale graphing of trends (e.g. requests per minute)
  - Active alerting according to user-defined heuristics (such as an alert when the quantity of errors per minute exceeds certain threshold)
- Time + Logging label + Message
- Best practices:
  - Make log entries meaningful with context: Add as detailed enough context as possible to log messages
    -> Easy to examine problem, also make message different from others
  - Use standard date & time format -> format ISO-8601
  - Use local time + offset for timestamp
  - Tag logging label correctly
  - Split logging to different targets based on their level/granularity
    - When debug -> log almost everything to nail down the cause of whatever bug you're trying to fix
    - In production -> log actions of the user that are relevant to business logic
  - Include stack trace when logging an exception
  - Include name of the thread when logging from a multi-threaded app

12. Admin Process:

- 12-factor strongly favors languages provide a REPL shell out of the box, make it easy to run one-off scripts
- In local deploy -> dev invoke one-off admin processes by direct shell command inside the app's checkout directory
- In production deploy -> dev use ssh or others that provided by that deployment's exec env to run such a process
