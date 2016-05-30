# yourmd-gradle-scripts

This repository contains common gradle scripts that can be used
in real-projects to reduce build boilerplate.

# Included scripts

| script | description |
|---|---|
|[*/gradle/common.gradle*](src/main/resources/gradle/common.gradle)|common build.gradle settings, **required**|

See [src/main/resources/gradle](src/main/resources/gradle) directory for details.

# How to "compile"

Clone this project and install artifact jar into local maven repository:

```
./gradlew install
```
 
# How to use

```gradle
//
// build.gradle
//

buildscript {
    ext {
       ymdGradleSettingsVersion = "0.0.1-SNAPSHOT" // make sure to use latest version
    }
}

plugins {
    id "com.orctom.applyscript" version "1.1"  // implements applyscript (required)
    id "net.researchgate.release" version  "2.4.0" // gradle release plugin (required)
    id "io.spring.dependency-management" version  "0.5.6.RELEASE" // dependency management plugin (required)
}

repositories {
    mavenLocal()
    mavenCentral()
}

dependencies {
    // jar artifacts with shared gradle scripts
    scripts "md.your.gradle:common-settings:${ymdGradleSettingsVersion}"
}

// apply scripts from jar
applyscript 'common-settings-${ymdGradleSettingsVersion}"/gradle/common.gradle'

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
  * *jcenter*
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

Add the following to your ```~/.gradle/gradle.properties```:

```
ossrhUsername=<osshr_jira_username>
ossrhPassword=<osshr_jira_password>

signing.keyId=0B735390
signing.password=<key_password>
signing.secretKeyRingFile=<path_to_secring.gpg>
```

Then upload using:

```
./gradlew uploadArchives
```
