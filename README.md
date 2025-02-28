# yourmd-gradle-scripts

This repository contains common gradle scripts that can be used
in real-projects to reduce build boilerplate.

# Included scripts

| script                                                             | description                                                                          |
|--------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| [*/gradle/common.gradle*](src/main/resources/gradle/common.gradle) | common build.gradle settings, **required**                                           |
| [*/gradle/java.gradle*](src/main/resources/gradle/java.gradle)     | common build.gradle settings for java projects, requires inclusion of `common.gradle` |
| [*/gradle/docker.gradle*](src/main/resources/gradle/docker.gradle) | docker image tagging support, requires inclusion of `common.gradle`                  |
| [*/gradle/jib.gradle*](src/main/resources/gradle/jib.gradle)       | docker `jib` support, requires inclusion of `common.gradle`               |

See [src/main/resources/gradle](src/main/resources/gradle) directory for details.

# How to "compile"

Clone this project and install artifact jar into local maven repository:

```
./gradlew clean publishToMavenLocal
```

# How to use

```gradle
//
// build.gradle
//

buildscript {
    // required to apply gradle snippets stored in md.your.gradle:common-settings jar
    repositories {
      mavenLocal()
      mavenCentral()
    }
    dependencies {
      classpath "md.your.gradle:common-settings:<version>"
    }
}

plugins {
    id "net.researchgate.release"           version  "3.1.0" // gradle release plugin (required)
    id "io.spring.dependency-management"    version  "1.1.0" // dependency management plugin (required)
}


// apply default settings from jar
apply from: getClass().getResource("/gradle/common.gradle")
apply from: getClass().getResource("/gradle/java.gradle")

// DONE!
//
// This is enough to build/test/publish almost all java/groovy projects :)
// You should probably add your custom dependencies, though. 
// 

```

# Batteries included

This project reduces gradle build script boilerplate, so it configures
most annoying stuff for your out-of-the-box.

## Configured repositories

* *mavenLocal*
* *mavenCentral*
* *ymdreleases* (S3)
* *ymdsnapshots* (S3)

## Automatically enabled plugins

* *java*
* *groovy*
* *idea*
* *eclipse*
* *maven*
* *maven-publish*
* [*net.researchgate.release*](https://github.com/researchgate/gradle-release)
* [*io.spring.dependency-management*](https://github.com/spring-gradle-plugins/dependency-management-plugin)

## Gradle tasks

| task | description |
|---|---|
|*awsCredentials*| displays AWS credentials |
|*repositories*| displays all configured repositories|
|*downloadDeps*| downloads all required dependencies|
|*wrapper*| install/configure gradle wrapper|

## Dependency management

All dependencies are managed, so you don't need to provide dependency versions,
so everybody using this project ends up with the same dependency set.

### Automatically enabled dependencies

| dependency scope | enabled dependencies|
|---|---|
|compileOnly|<ul><li></li></ul>|
|testCompile|<ul><li>org.slf4j:slf4j-api</li><li>org.codehaus.groovy:groovy-all</li><li>org.spockframework:spock-core</li><li>org.assertj:assertj-core</li></ul>|

# How to deploy to maven OSSRH

## via gradle.properties

Add the following to your ```~/.gradle/gradle.properties```:

```
osshr.user=<osshr_jira_username>
osshr.pass=<osshr_jira_password>

signing.gpg.key.content=<base64 encoded GPG private key>
signing.gpg.key.password=<GPG private key password>
```

## via env variables

set up the following env vars:

```
OSSHR_USER=<osshr_jira_username>
OSSHR_PASS=<osshr_jira_password>

SIGNING_GPG_KEY_CONTENT=<base64 encoded GPG private key>
SIGNING_GPG_KEY_PASSWORD=<GPG private key password>
```

Then upload Sonatype OSSHR using:

```
./gradlew publish
```