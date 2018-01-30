# Optum PI CDI Framework MVP

## Overview

The Optum Payment Integrity (OPI) Common Data Intake (CDI) framework is an Apache Kafka based software framework that remains congruent with the CDI Guiding Principles.
The CDI framework extends Kafka Core, Connect, and Streams APIs to realize a set of reusable, extensible, and well-engineered components to enable the creation of streaming data pipelines.
Traditionally, Kafka stremaing data pipelines are typically near real-time; however, OPI has historically processed a number of large batch import files from a variety of clients.
This poses a unique challenege to leveraging the Kafka ecosystem; name how to treat batch file processing in a similar manner as real-time data events.
To solve for this architectural divergence, the CDI framework development team (or simply "CDI team") views batch files as frozen blocks or "bundles" of potentially temporially related events.
These bundled events must undergo a lightweight "unbundling" process on the ingress side of Kafka. At this point, these events can be processed with similar fidelity as real-time events.

### CDI Principles (Conceptual Architecture) ###

Please see the following document: [CDI Principles.pptx](https://example.org)

This document is maintained by OPI IT Leadership (Jeremy Kingston, et al.).

### CDI Framework Solution Architecture ###

Please see the following document: [CDI Solution Architecture.pptx](https://example.org)

This document is maintained by and colloquially known amongst the CDI team as the "Big Animal Pictures".

## Development

Current engineering status:

##### Minimal viable product (MVP) #4 #####

*** IN PROCESS ***

- TODO

##### Minimal viable product (MVP) #3 #####

- Solution architecture creation ("big animal pictures") to align with CDI Principles
- Engineering backlog creation based on solution architecture and implementation commitments
- Resource and capacity planning; training of additional proposed resources
- Reduce friction in new developer on-boarding and memorialize tribal knowledge

##### Minimal viable product (MVP) #2 #####

- Altered custom Kafka Connect source connector and task to use new unbundling mode ("lines")
- Unbundling is simply break a fil apart on record boundaries (lines or segments) into raw topics
- Custom Kafaka Streams API processor now supports record-by-record distributed line parsing
  - Tech note: Processor::context().commit() can cause poor performance and general use case
- The "fixed" mode is still supported at the Kafka Connect connector/task level via configuration

##### Minimal viable product (MVP) #1 #####

- Batch import fixed length flat text ("fixed") files into Kafka topics using JSON metadata specifications.
- Custom Kafka Connect source connector and task using shared/reusable 'lifecycle' abstractions
- Core Java fixed length flat text parsing code (non-buffering/iterator-based model)
- Serious use of factory pattern, interfaces, iterators, et al. constructs.
- Proved that Kafka can run on Windows 10 without Cygwin/Bash emulation

## Prerequisites ##

You need to have [JetBrains IntelliJ Community](https://www.jetbrains.com/idea/download) and [Java SDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) installed.

On Windows, you will need the [Microsoft Visual C++ 2015 Update 3 runtime](https://www.microsoft.com/en-us/download/details.aspx?id=53587).
NOTE: You may encounter an installation error. Ask the CDI team for help if this occurs. 

## Java Primer

Get the latest JDK; however, Java 8 (language level 52) should be used for building. Ths is set already in the IntelliJ project.

The CDI framework is a pure core Java/JVM software solution. It does not use Scala, Python, or other languages for the binary distribution components (JARs).

## IntelliJ Primer

IntelliJ is a modern Java Integrated Development Environment (IDE) with first class support for Dev/Ops and refactoring.

If you have never used the IntelliJ IDE, and you have used Visual Studio, then the project system naming is slightly different.

- An IntelliJ "project" is loosely equivalent to a Visual Studio "solution".
- An IntelliJ "module" is loosely equivalent to a Visual Studio "project".
- IntelliJ support two project systems: file (.ipr) and directory (.idea) - CDI has chosen directory-based project artifacts.

The CDI development team uses IntelliJ as their IDE of choice. We have designed the repo and associated project files to allow contributors an
easy on-ramp to productivity: you should simply be able to clone the repo and open the relevant IntelliJ project(s). There is no
need to edit .gitignore file to exclude IntelliJ assets or create your own IntelliJ .idea project structure. If you find yourself
doig any of these; or if you find yourself doing something that feels messy to get productive with the CDI framework inside of IntelliJ; STOP.
Consult a member of the CDI team for assistance before havig to beat one's head on concrete.

## Building on Windows

The development team intentionally chose to start local development on Windows laptops instead of relying on shared infrastructure.
This allowed the team to innovate quickly, and to force ourselves to understand the deep internals of Kafka runtime components, dependencies, and headaches.
There are many Internet threads spreading misinformation about Kafka on Windows.
Kafka does indeed run on Windows; however, there are a couple known bugs in the Kafka source that causes issues that are not difficult to work around.

These known issues include:

- Non-empty directory exceptions during log directory clean up.
- Sketchy batch file support in the source distribution.
- Kafka Streams API uses RocksDB, which in turn has a dependency on the Microsoft Visual C++ 2015 Update 3 runtime.

Currently, automated build/deploy/test processes are non-existent.
Given that we started iterating quickly on prototypes, we chose initially for forgo this overhed as we build-measure-learn.
The CDI development team now recognizes the need to add these fundamental capabilities to scale forward.

### 1. First clone the repo ###
    git clone https://github.optum.com/CDI/framework-mvp

### 2. Setup local file system input/output/metadata directory tree ###
    <repo-root>\local_setup.bat

### 3. Confirm local file system input/output/metadata directory tree ###

    c:\
        kafka_2.11-1.0.0\
            (Kafka runtime - current pinned version checked into Git)
        kio\
            (This directory structure is simply an arbitrary convention)
            bcbs-la-1010\
                in\
                    (should have zero byte placeholder files - overwrite with PHI not in Git)
                meta\
                    (should have JSON metadata files)
                out\
                    (be an empty directory)
        log\
            (error logs from log4j will be put here)
        tmp\
            (this is where Kafka and ZooKeeper put data)

### 4. Open the IntelliJ CDI core project ###
    <repo-root>\src\core

This project contains the CDI framework - a set of reusable components that realize end-to-end streaming data solutions using Kafka.

Build the project and artifacts (JARs).

On first build and deploy, to copy build output artifacts:

    <repo-root>\tools\local_deploy.bat

NOTE: Kafka Server should not be running locally. Also, this script will delete the c:\tmp and c:\logs directories.

Alternatively, to copy build output artifacts (while Kafka Server is running to test the Streams API only):

    <repo-root>\tools\local_deploy_lite.bat

Th CDI team is working to provide a better experience around this as the product matures.
    
### 5. Open the IntelliJ CDI sandbox project ###

    <repo-root>\src\sandbox

This project contains prototypes for Kafka Streams API research.

Build the project and artifacts (JARs).

If you get a build error, check to see if JAR output from the core project exists in the following location:

    <repo-root>\privatelib

This location is used only by the kproc module inside the sandbox project.
This is a stop-gap until we determine the optimal project structure. 

Th CDI team is working to provide a better experience around this as the product matures.

### 6. Working with the CDI helper batch/config files ###

The Apacke Kafka distribution contains batch files and configurations to run various services locally:

    <repo-root>\tools\kafka_2.11-1.0.0\bin\windows
    <repo-root>\tools\kafka_2.11-1.0.0\config

Note, the CDI team added the following batch file:

    <repo-root>\tools\kafka_2.11-1.0.0\bin\windows\_kafka-streams-application-reset.bat

The CDI team has also added the following convenience config files useful for local testing:

    <repo-root>\tools\kafka_2.11-1.0.0\config\_CDI_*.properties

Further, CDI team has also created the following helper batch files useful for local testing:

    <repo-root>\tools\kafka_2.11-1.0.0\_.bat
    <repo-root>\tools\kafka_2.11-1.0.0\run_*.bat

Of note is the _.bat (yes, "underscore DOT bat"). The CDI team found that on Windows, when a Kafka service is started from the command line,
the implementation spawns a new console window but blocks the parent console until exit. This meant that many console windows
were need to be opened to manage local testing and execution. If you invoke the run*.bat scripts in the following manner using _.bat as
a bootstrapper, the parent console no longer blocks. Frankly, it is a useful batch hack.

    <repo-root>\tools\kafka_2.11-1.0.0\_ run_<whatever>.bat

Th CDI team is working to provide a better cross platform experience experience around this as the product matures.

### 7. Running the ZooKeeper and Kafka servers locally ###

ZooKeeper must be running before any other Kafka services can execute ggiven the tight coupling.

    <repo-root>\tools\kafka_2.11-1.0.0\_ run_zookeeper_server.bat

If the new child console start and immediately exits, an error has occured.
You should check the logs directory to determine root cause.

Next, Kafka Server need to be running.

    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_server.bat

If the new child console start and immediately exits, an error has occured.
You should check the logs directory to determine root cause.

### 8. Running a Connect or Streams process locally ###


With ZooKeeper and Kafka Servers running locally:

Run any of the batch files to source data into raw topics:

    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_claim.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_claim_network_id_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_eligibility.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_erp_product_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_lob_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_network_id_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_plan_detail_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_prov_category_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_provider.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_prov_specialty_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_source_prov_type_cd.bat

NOTE: Some of the files do not yet exist in Git and are in process of testing.


Run any of the batch files to sink data into files:

    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_claim.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_claim_network_id_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_eligibility.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_erp_product_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_lob_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_network_id_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_plan_detail_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_prov_category_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_provider.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_prov_specialty_cd.bat
    <repo-root>\tools\kafka_2.11-1.0.0\_ run_kafka_connect_sink_prov_type_cd.bat

NOTE: Some of the files do not yet exist in Git and are in process of testing.

    kproc
    kstreams

To execute Processor API or Streams API solutions, simply invoke the entry point via Java or the IDE.
If you execute from the command line using java.exe, you must ensure the class path is set accordingly.

### 9. Care and feeding of the local environment ###

To stop the ZooKeeper or Kafka Servers, you should NOT click the X in the upper right corner of the console window.
This will cause these services to ungracefully shutdown and possibly cause log corruption.
Instead, use Control+C and let the signal handling kick in to gracefully shutdown the services.

At anytime (with no services running), you can manually delete the following directories to reset your local Kafka environment: 

    c:\
        log\
        tmp\

You can also run a bacth file to clean and reploy JARs to your local environment:

    <repo-root>\tools\local_deploy.bat

## Building on MacOS

Coming soon...

## Building on Linux

Coming soon...

## Project Contact

Please reach out to [Daniel P. Bullington](daniel.bullington@optum.com) with questions regarding the CDI Framework. 

## Contribute

- Source Code: https://github.optum.com/CDI/framework-mvp
- Issue Tracker: https://github.optum.com/CDI/framework-mvp/issues

## License

Copyright 2017-2018 (c) Optum. All rights reserved.