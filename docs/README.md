# cics-java-liberty-link Documentation

[![Build](https://github.com/cicsdev/cics-java-liberty-link/actions/workflows/java.yaml/badge.svg)](https://github.com/cicsdev/cics-java-liberty-link/actions/workflows/java.yaml)

A comprehensive guide for building, deploying, and running a CICS Java application that uses the [`@CICSProgram`](https://www.ibm.com/docs/en/cics-ts/6.x?topic=SSJL4D_6.x/reference-javadocs/jcics-javadoc/com/ibm/cics/server/invocation/CICSProgram.html) annotation to link to Enterprise Java programs running in a Liberty JVM server.

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Downloading](#downloading)
  - [Verify CICS Dependencies](#verify-cics-dependencies)
- [Building](#building)
  - [Build Methods](#build-methods)
  - [Resolving Build Path Issues](#resolving-build-path-issues)
  - [Option 1: Building with Eclipse](#option-1-building-with-eclipse)
  - [Option 2: Building with Gradle](#option-2-building-with-gradle)
  - [Option 3: Building with Apache Maven](#option-3-building-with-apache-maven)
- [Deploying to a Liberty JVM server](#deploying-to-a-liberty-jvm-server)
  - [Liberty Configuration Requirements](#liberty-configuration-requirements)
  - [Deployment Options](#deployment-options)
- [Running](#running)
  - [Method 1: Using the Provided Transaction](#method-1-using-the-provided-transaction)
  - [Method 2: Using CECI to Link Directly](#method-2-using-ceci-to-link-directly)
  - [Method 3: Passing Data via Channels and Containers](#method-3-passing-data-via-channels-and-containers)
  - [Viewing Output](#viewing-output)
- [Additional Resources](#additional-resources)
- [License](#license)

## Overview

This sample demonstrates how to annotate a POJO packaged in a WAR (Web application), deploy it to a Liberty JVM server, and then LINK to (or call) that POJO from a CICS program or CICS transaction.

**Key capabilities:**
- Expose Java methods as CICS programs using `@CICSProgram` annotation
- Link to Java applications from CICS programs using standard EXEC CICS LINK
- Pass data between CICS and Java using channels and containers
- Deploy using CICS bundles or Liberty application configuration

## Project Structure

| Component | Description |
|-----------|-------------|
| **cics-java-liberty-link** | Top-level project containing all modules |
| **cics-java-liberty-link-app** | Web application project with annotated POJO |
| **cics-java-liberty-link-bundle** | CICS bundle project for Gradle/Maven builds |
| **etc/eclipse_projects/com.ibm.cics.server.examples.wlp.link.bundle** | CICS Explorer bundle project for 'Export to zFS' deployment |
| **etc/config/liberty/server.xml** | Template Liberty configuration with minimum required features |

## Prerequisites
- CICS TS V5.5 or later
- Java SE 1.8 or later on the workstation
- One of the following on your workstation:
    - Eclipse with the IBM CICS SDK for Java EE, Jakarta EE and Liberty
    - An IDE of your choice that supports Gradle or Maven (or can run the Wrappers)
    - A command line, to run the Wrappers or to invoke a locally installed version of Gradle or Maven

## Downloading

- Clone the repository using your IDEs support, such as the Eclipse Git plugin
- **or**, download the sample as a [ZIP](https://github.com/cicsdev/cics-java-liberty-link/archive/main.zip) and unzip onto the workstation

> [!TIP]
> Eclipse Git provides an 'Import existing Projects' check-box when cloning a repository.

### Verify CICS Dependencies

When building with Gradle or Maven, verify that the correct CICS TS bill of materials (BOM) is specified for your target CICS release.

**Why use the BOM?**
- Ensures consistent artifact versions
- Provides proper dependency scoping
- Guarantees compatibility with your CICS TS version

The example below uses a BOM compatible with **CICS TS V5.5 with JCICS APAR PH25409 or newer**. Java bytecode compiled against this version will be compatible with later CICS TS versions and subsequent JCICS APARs.

Browse available versions at [Maven Central](https://mvnrepository.com/artifact/com.ibm.cics/com.ibm.cics.ts.bom).

**Gradle** (`build.gradle`):
```gradle
compileOnly enforcedPlatform("com.ibm.cics:com.ibm.cics.ts.bom:5.5-20200519131930-PH25409")
```

**Maven** (`pom.xml`):
```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>com.ibm.cics</groupId>
      <artifactId>com.ibm.cics.ts.bom</artifactId>
      <version>5.5-20200519131930-PH25409</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

## Building

### Build Methods

You can build the sample using any of these methods:

1. **Eclipse CICS Explorer SDK** - Automatic compile/build
2. **IDE with Gradle/Maven support** - Use built-in tooling (e.g., *buildship* or *m2e* in Eclipse)
3. **Gradle/Maven Wrappers** - No IDE or local installation required
4. **Command line** - Using locally installed Gradle or Maven

### Resolving Build Path Issues

> [!IMPORTANT]
> The sample is pre-configured for **JDK 1.8** and **CICS TS V5.5 Libraries for Java EE 6/7**. When importing the project, you may encounter compile errors if your IDE isn't configured for JDK 1.8 or doesn't have CICS Explorer SDK installed.

**To resolve build path issues:**

**For CICS Explorer SDK deployment:**
1. Right-click project → Properties → Java Build Path
2. Select Libraries tab → Classpath → Add Library
3. Choose "CICS with Enterprise Java and Liberty" Library
4. Select appropriate CICS and Enterprise Java versions

**For Gradle/Maven deployment:**
- Errors may not affect the build, but to resolve them:
  - **Gradle**: Right-click project → Gradle → Refresh Gradle Project
  - **Maven**: Right-click project → Maven → Update Project...

> [!TIP]
> **Eclipse IDE Note:** Gradle (buildship) can fully refresh the classpath even after Maven updates. However, Maven (m2e) cannot reciprocate. If you previously used Gradle refresh, manually remove the 'Project Dependencies' entry from Java Build Path before performing a Maven Project Update to avoid duplication errors.


### Option 1: Building with Eclipse

**Import the project:**
- **Using EGit**: When cloning, tick "Import existing Projects" checkbox
- **Manual import**: File → Import → General → Existing projects into workspace

Then follow the [build path resolution advice](#resolving-build-path-issues) above if needed.

### Option 2: Building with Gradle

Run the top-level `settings.gradle` file to build all projects.

**Build outputs:**
- WAR file: `cics-java-liberty-link-app/build/libs/`
- CICS bundle ZIP: `cics-java-liberty-link-bundle/build/distributions/`

> [!NOTE]
> In Eclipse, the 'build' directory is often hidden. To view it: Package Explorer → ⋮ menu → Filters → uncheck "Gradle build folder"

**JVM Server Configuration:**
The target JVM server is controlled by the `cics.jvmserver` property in [`cics-java-liberty-link-bundle/build.gradle`](../cics-java-liberty-link-bundle/build.gradle), or can be set via command line.

**Build commands:**

| Platform | Command |
|----------|---------|
| **Linux/Mac (Wrapper)** | `./gradlew clean build` |
| **Windows (Wrapper)** | `gradlew.bat clean build` |
| **Command line** | `gradle clean build` |
| **With JVM server override** | `gradle clean build -Pcics.jvmserver=MYJVM` |

### Option 3: Building with Apache Maven

Run the top-level `pom.xml` file to build all projects.

**Build outputs:**
- WAR file: `cics-java-liberty-link-app/target/`
- CICS bundle ZIP: `cics-java-liberty-link-bundle/target/`

**JVM Server Configuration:**
The target JVM server is controlled by the `cics.jvmserver` property in [`cics-java-liberty-link-bundle/pom.xml`](../cics-java-liberty-link-bundle/pom.xml) under the `defaultjvmserver` configuration, or can be set via command line.

**Build commands:**

| Platform | Command |
|----------|---------|
| **Linux/Mac (Wrapper)** | `./mvnw clean verify` |
| **Windows (Wrapper)** | `mvnw.cmd clean verify` |
| **Command line** | `mvn clean verify` |
| **With JVM server override** | `mvn clean verify -Dcics.jvmserver=MYJVM` |

## Deploying to a Liberty JVM server

### Liberty Configuration Requirements

Ensure you have the following feature defined in your Liberty `server.xml`:
```xml
<feature>cicsts:link-1.0</feature>
```

A complete template `server.xml` is provided [here](../etc/config/liberty/server.xml).

### Deployment Options

You have three deployment options:

#### Option 1: Deploying CICS Bundles with CICS Explorer

**Best for:** Eclipse users with CICS Explorer SDK

1. _(Optional)_ Update JVM server name in `.warbundle` file
   - Default: `DFHWLP`
   - Change to match your CICS JVMSERVER resource name
2. Export the bundle project to zFS
   - Right-click bundle project → 'Export Bundle project to z/OS Unix File System'
3. Create and install a CICS BUNDLE resource
   - Set bundle directory to the zFS export location
   - Install the bundle
4. Verify the deployment
   - Check for PROGRAM resource `HELLOWLP` in CICS Explorer Programs view
   - Or use: `CEMT INQUIRE PROGRAM(HELLOWLP)`

#### Option 2: Deploying CICS Bundles from Gradle or Maven

**Best for:** Command-line builds and CI/CD pipelines

1. Upload the bundle ZIP to zFS
   - **Gradle**: `cics-java-liberty-link-bundle/build/distributions/*.zip`
   - **Maven**: `cics-java-liberty-link-bundle/target/*.zip`
2. Extract the bundle on zFS
   ```bash
   ${JAVA_HOME}/bin/jar xf /path/to/bundle.zip
   ```
3. Create and install a CICS BUNDLE resource
   - Set bundle directory to the extracted location
   - Install the bundle into the CICS region
4. Verify the deployment
   - Check for PROGRAM resource `HELLOWLP` in CICS Explorer Programs view
   - Or use: `CEMT INQUIRE PROGRAM(HELLOWLP)`

#### Option 3: Deploying directly with Liberty's application configuration

**Best for:** Manual WAR deployment without CICS bundles

1. Upload the WAR file to zFS
   - **Gradle**: `cics-java-liberty-link-app/build/libs/*.war`
   - **Maven**: `cics-java-liberty-link-app/target/*.war`
2. Configure Liberty `server.xml`
   ```xml
   <application id="cics-java-liberty-link-app"
                location="/path/to/cics-java-liberty-link-app.war"
                name="cics-java-liberty-link-app"
                type="war"/>
   ```
3. Verify deployment
   - Check for PROGRAM resource `HELLOWLP` in CICS Explorer Programs view
   - Or use: `CEMT INQUIRE PROGRAM(HELLOWLP)`


## Running

### Method 1: Using the Provided Transaction

If you deployed via CICS Explorer or CICS bundle, use the pre-configured `WLPH` transaction:

```
WLPH
```

**What happens:**
- Invokes the `HELLOWLP` program
- Prints: `Hello <CICSUSER> from Liberty server <JVMSERVER_NAME>`
- Output appears in output of the terminal (or the Liberty JVM server STDOUT if the transaction was not called from a terminal).

### Method 2: Using CECI to Link Directly

Link directly to the program using CECI for testing:

```
CECI LINK PROGRAM(HELLOWLP)
```

**What happens:**
- Links to the `HELLOWLP` program
- Prints: `Hello <CICSUSER> from Liberty server <JVMSERVER_NAME>`
- Output appears in Liberty JVM server STDOUT

### Method 3: Passing Data via Channels and Containers

Pass custom data to the program using CICS channels and containers:

```
CECI PUT CONTAINER(NAME) CHAR FROM(MATTHEW) CHANNEL(CHAN)
CECI LINK PROG(HELLOWLP) CHANNEL(CHAN)
```

> [!IMPORTANT]
> Both commands must be entered in the **same CECI session** to share the channel.

**What happens:**
- Creates a container named `NAME` with value `MATTHEW` on channel `CHAN`
- Links to `HELLOWLP` passing the channel
- Program reads the container and uses the value in the greeting

**Expected Output:**
```
Hello MATTHEW from Liberty server <JVMSERVER_NAME>
```

### Viewing Output

The program output is written to the Liberty JVM server's STDOUT. To view it:

1. **CICS Explorer**: JVM Servers view → Right-click server → View STDOUT
2. **z/OS Unix**: Check the JVM server's stdout file in the Liberty server directory

## Additional Resources

- [Linking to Java applications in a Liberty JVM server by using the @CICSProgram annotation](https://www.ibm.com/docs/en/cics-ts/latest?topic=djariljs-linking-java-applications-in-liberty-jvm-server-by-using-cicsprogram-annotation)
- [CICS TS Documentation](https://www.ibm.com/docs/en/cics-ts/)
- [CICS BOM on Maven Central](https://mvnrepository.com/artifact/com.ibm.cics/com.ibm.cics.ts.bom)

## License

This project is licensed under the [Apache-2.0 License](../LICENSE).

---

**Copyright IBM Corp. 2016, 2026**


