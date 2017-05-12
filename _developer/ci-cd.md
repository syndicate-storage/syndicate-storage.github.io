---
layout: post
title: A Syndicate CI/CD Strategy
---

### Syndicate Continuous Integration and Continuous Delivery Strategy

---
Our software development strategy is based on concepts established under the [“Continuous Integration”](https://en.wikipedia.org/wiki/Continuous_integration) and [“Continuous Delivery”](https://en.wikipedia.org/wiki/Continuous_delivery) _(CI/CD)_ models.  This approach is based on the practice that developers integrate code into a shared repository as frequently as possible.  The regular and short integration cycles generally lead to improved quality of software and a significantly reduced time to deliver it.  Small and frequent changes enable us to quickly identify and make corrections or improvements.  And with automation, the process of rebuilding, deploying, testing, and analyzing can begin almost instantly.


These are some of the key components of our CI/CD environment, which will be described in more detail below:

* **[Jenkins](https://jenkins.io)** 
* **[Docker](https://www.docker.com)**
* **[Ansible](https://www.ansible.com/it-automation)**
* Syndicate automated testing framework
* Analysis and debugging tools
* Collaboration Tools (**[Slack](https://get.slack.help/hc/en-us/categories/202622877-Slack-Guides)**)

<br>

<div align="center"><img src="http://tanmaysarkar.com/wp-content/uploads/2015/03/jen.jpg" alt="DockerLogo" style="width: 60px;"/></div>

## Integration with Jenkins

We utilize a [build server running Jenkins](https://butler.opencloud.cs.arizona.edu/jenkins/job/syndicate-tests/) to automate the build/delivery process.  The Jenkins server is also integrated with our testing and analysis framework.  When a code change is merged, the build server detects the change and automatically rebuilds the associated Syndicate binaries and packages.  Within a few moments of a change, users can begin installing the newest Syndicate packages and the automated testing begins!  Literally thousands of tests are performed and – when complete – the results of the tests are posted to the Jenkins dashboard and developers are also instantly notified via “Slack”.  To assist with the analysis process, tests also include valgrind/memcheck reports which are integrated directly with Jenkins and are visible from the Jenkins dashboard.  We include profiling data as part of the analysis output, thus making it possible for developers to not only quickly find errors or potential memory leaks, but to also find potential timing related issues or see basic flow diagrams / call graphs associated with each test!

<br>

<div align="center"><img src="https://www.docker.com/sites/default/files/moby_48%402x.png" alt="DockerLogo" style="width: 70px;"/> + <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcS4kbEocBYvX-orDbMBUtTKk9pMJcbZ0A4ojPUoLqOglGPAJH9_gQ" alt="DockerLogo" style="width: 60px;"/></div>

## Docker and Ansible

Docker is an essential part of our CI/CD development environment.  It provides the ability for developers to run Syndicate in light-weight, isolated “containers” that are identical between each developer.
We also use Ansible in conjunction with Docker, thus providing the ability to quickly install/rebuild fresh testing and development environments on our test/demo servers - these are automatically configured to be identical to the docker containers.  In our environment, the [syndicate-ci](https://github.com/syndicate-storage/syndicate-ci) repository is responsible for creating a Docker based test environment for the Jenkins server, whereas our [syndicate-deploy](https://github.com/syndicate-storage/syndicate-deploy) repository can be used to deploy the same test environment in either Docker containers or actual hardware, for the purpose of testing or development.  Using this strategy provides the ability for developers to begin developing or testing in literally minutes.

<br>

<div align="center"><img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcSA9Q5l1U0U4pG4qH6ENqAI44Zjb94hpd0gDQk3U4KBKZNWdbjO" alt="DockerLogo" style="width: 60px;"/></div>
## Automated testing framework with integrated analysis/debugging

This environment also includes a unique testing framework consisting of thousands of tests, all of which are tracked and reported to [our Jenkins server](https://butler.opencloud.cs.arizona.edu/jenkins/job/syndicate-tests/).  Although we do not develop strictly under a “[test-driven](https://en.wikipedia.org/wiki/Test-driven_development)” environment, our philosophy is to create tests for each feature, risk, and requirement of Syndicate.  For this reason, our testing framework ([syndicate-tests](https://github.com/syndicate-storage/syndicate-tests)) was designed to be versatile, extensible, and easy to use, thus allowing developers to quickly create new tests that meet our CI testing standards.  The testing framework uses YAML based test files which further simplifies the creation and design of each test.  Integrated within the framework, each test can optionally be run with various profiling tools (valgrind/memcheck, callgrind, sprof, oprofile, etc).   The test framework knows how to process the resulting files by organizing them, reformatting them, or even producing helpful plots.
It is standard practice to run tests locally prior to merging code changes to the mainline.  For this reason, the test framework is available and installed from the [syndicate-deploy](https://github.com/syndicate-storage/syndicate-tests) repository, allowing developers to test or make changes directly from within docker containers.  Furthermore, analysis reports are not only integrated within Jenkins but they are optionally available during standalone testing.  Another feature that is quite unique to our development environment is the ability to develop and debug directly from the test framework.  For example, imagine a developer encounters an issue corresponding to a specific test.  And according to the debug logs, the line numbers corresponding to the issue may also be available.  With this information, the developer can easily instruct the specific test to launch a debugger and break in the exact area(s) of interest.

<br>

<div align="center"><img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTPX93UjS4rdFEK3q6VZKz_sX8E3KU9qMfhHA5jAyCHwWOzhLMY" alt="DockerLogo" style="width: 100px;"/></div>
## Collaboration Tools

A vital part of continuous integration comes from the developers ability to work together, share information, to collaborate and to receive notifications about the state of the system.  We use “[Slack](https://slack.com)” for our collaborative needs.  Not only is much of our communication done exclusively through slack, which is helpful for maintaining records and a history of technical conversations; but our Jenkins server also posts statuses regarding the state of each repository along with links to test results/artifacts.  

<br>
<div align="center">
<a href="https://www.youtube.com/watch?v=ktNPYyWsUak&t=2s">Watch the Syndicate CI/CD demonstration video</a>
</div>
<br>
---


## Current and Future CI/CD Efforts

In the near future, we plan to expand support in the following areas:

* package support (distribution media) for additional architectures and operating systems
* additional profiling data and statistics
* automated generation of code documentation using Doxygen
* reduced times for test and analysis runs

