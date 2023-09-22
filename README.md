# Don’t Wire your Application More than Once for Testing

<figure>
<img
src="https://www.pexels.com/photo/woman-in-white-long-sleeve-shirt-using-silver-laptop-computer-3784324"
alt="woman in white long sleeve shirt using silver laptop computer 3784324" />
</figure>

## Do you Wire your Application More than Once?

Do you use `@SpringBootTest`? How about `@ContextConfiguration`? Then,
yes - you are wiring your Application for testing. Remember the amount
of time and effort it took to get the application wired in the first
place? Now imagine doing it again. And again. Having potentially many
differently wired results, each focused on a specific test scenario.

What if I told you there’s an easier and better way?

## DO Black Box Automation Testing at Build Time

Black-box testing of the entire application, also referred to as System
Integration & Test once-upon-a-time, has the benefit of running the full
application as it will be deployed and run in production, with external
dependencies (databases, messaging systems, rest services, etc.) being
satisfied with test instances. If you are thinking of "TEST ENVIRONMENT
12," let’s talk. There’s a better way.

Imagine the following in your toolbelt:

- Docker

- Tooling to Spin Up, Shut Down, and Clean up Docker Containers during
  Integration Testing

  - Test Containers, OR

  - docker-maven-plugin (io.fabric8)

- Docker Images

  - Application

  - Database

    - Test Init SQL File

  - Messaging Middleware

  - WireMock Rest Services

So how do you run it?

    $ mvn clean install

## Why?

Grey-box tests, which involve some amount of exposed internals of a
system, and some amount of testing through the integrated layers pieced
together, lead to the following challenges:

- *Hard to Maintain* - focusing tests on an unclear Subsection of the
  Application leads to confusing, hard-to-maintain test code. You know
  this one when looking at a test and having trouble figuring out just
  what it is testing, and/or how it is testing it.

- *Flaky Tests*

  - Often the internals that the test depends on are unclear, and over
    time workarounds can lead to integration tests breaking in
    non-obvious ways

  - *False Results* - in addition these tests sometimes rely on internal
    timings that lead to false-positives and false-negatives; if you
    find yourself wanting to write `sleep()` in a test, …​

- Slow Integration Tests - as developer after developer writes
  additional tests, the startup and shutdown time for each grows the
  overall time to test the entire application

  - ActiveMQ has suffered from this problem and is a key motivation for
    this Author to avoid the same fate

## Isn’t it Slow?

Relative to a local build without integration tests, yes. Relative to a
local build with a large number of grey-box tests, no - this is much
faster because there is no need to repeatedly spin up and shut down the
application. Also, comparing build time to external tests, these tests
are much faster because the focus isn’t a full regression suite.
Instead, the focus is on functional testing of the system, and ensuring
that obvious impediments to running the application are not introduced.

One example of a big win for this type of testing - new, required
configuration settings for the application. The intergation tests will
quickly fail with an error such as:

    Caused by: java.lang.IllegalArgumentException: Could not resolve placeholder 'activemq.username' in value "${activemq.username}"

Here are tips to get the most value:

- Enable these tests to be bypassed easily via mvn profile or property
  so developers can cycle through multiple builds without the full suite
  of tests

- Run these tests in the build pipeline that automatically executes on
  commit

- Encourage developers to run the full suite of tests immediately before
  pushing commits.

- Focus the tests on System Integration Tests - making sure the
  fully-integrated system is functional, and no critical, obvious parts
  were broken.

- Maintain a separate set of end-to-end tests integrating this
  System/Service/Application with others.

## Added Benefit - Tests Double as a Reference Use-Case

Anyone wanting to understand better how the System is used, such as what
settings are needed, and how they can be configured correctly, can refer
to these tests for a working example. In addition, reproducing failures
is simple because the test environment is cleanly packaged in docker
containers.

## Additional Tips

### Cucumber

Use Cucumber to write and maintain the tests. The added readability of
the tests and reports, as well as the reuse of step definitions, is
valuable.

### Lightweight Tests

Keep the test code lightweight - the tests don’t need to cover all the
possible failure conditions gracefully because developers will be
looking at the failures and diagnosing them. This helps greatly reduce
the time and effort to write and maintain the tests while only very
slightly reducing the ease with which a failure can be diagnosed.

## See it in Action

At OpenNMS, we built this test flow in the Lokahi (aka Horizon Stream)
project. The entire project is opensource, and available on GithHub
here: <https://github.com/OpenNMS-Cloud/lokahi>.

Here is a direct link to one of the `docker-it` modules that runs the
black-box tests:
<https://github.com/OpenNMS-Cloud/lokahi/tree/develop/minion-gateway/docker-it>.

## Who is the Author

Arthur Naseef.

Oh. Details. Right…​

Arthur started programming at a very early age on a TRS-80 with 64KB of
RAM. In his early carrier, starting before Linux was available, he
focused on Unix, C, and database work. Over the years, reaching into
embedded systems, Linux kernel drivers, performance tuning, concurrency,
and much, much more.

After switching to the Java world, fighting with ActiveMQ, reading
through the broker code, and finding a couple of bugs in the broker
internals, Arthur was offered Apache committer status on the project and
eventually PMC membership. He continues to consult, primarily focused on
Java-based Service Oriented Architected (aka "Microservices"), and
asserts expertise in Java, the JVM, and more.

Arthur is not shy to dig deep into internals - of third-party packages,
O/S operation, Kernel internals - in order to solve problems. Some of
this can be seen in recent commits to the Ignite project, including a
commit for this issue:
<https://issues.apache.org/jira/browse/IGNITE-17274>.

Besides consulting work, you can see some of Arthur’s efforts at work at
Playful Digital Learning where he builds the technology that powers the
business, including PDLCode, which enables early coders to learn by
coding Minecraft in-game interactions.

### My Philosophy

Wiring cool code was fun in the past. But now, I strive to only write
code that actually provides value - automating business-needs,
automating the development process to speed development, applying
best-practices, working tech debt out of solutions, and so on.

Coupled together with an internal need to "Solve Problems," this has
resulted in many philosophies that guide my development toward
high-value activities and away from low-value ones. I spend a lot of
time trying to share my learning with others. And, of course, I’m always
looking to improve further.

## Reaching Out

Please do not hesitate to reach out with questions and comments, here on
the Blog, or through the Savoir Technologies website at
<https://www.savoirtech.com>.

## With Thanks

Thank you to Andrea Piacquadio for the awesome stock photo:
<https://www.pexels.com/photo/woman-in-white-long-sleeve-shirt-using-silver-laptop-computer-3784324/>
