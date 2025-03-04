# yourmd-gradle-scripts

This repository contains common gradle scripts that can be used
in real-projects to reduce build boilerplate.

# Included scripts

| script                                                            | description                                                                          |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------|
| [*/gradle/common.gradle*](scripts/common.gradle) | common build.gradle settings, **required**                                           |
| [*/gradle/java.gradle*](scripts/java.gradle)     | common build.gradle settings for java projects, requires inclusion of `common.gradle` |
| [*/gradle/docker.gradle*](scripts/docker.gradle) | docker image tagging support, requires inclusion of `common.gradle`                  |
| [*/gradle/jib.gradle*](scripts/jib.gradle)       | docker `jib` support, requires inclusion of `common.gradle`               |

See [scripts](scripts) directory for details.

# Versions

This repository has gone through a shift from version 0.4.0 in the way it is used. 
In general, aim to use version >=0.4.0, as it is suitable for Gradle 8.10.
If for any reason an older version of Gradle is needed, use <=0.3.6.

# Using version >=0.4.0

New versions of these scripts will need to be used as git submodules in each project. 

Before committing, make sure the submodule is pinned to a tag and NOT a branch.

```
git submodule add https://github.com/YourMD/yourmd-gradle-scripts.git gradle/common-scripts
cd gradle/common-scripts
git checkout 0.4.0
```

### How to use (version >=0.4.0)

To import a common script in Gradle after adding the submodule, use the following import method
in your Gradle project:


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
}

plugins {
    id "net.researchgate.release"           version  "3.1.0" // gradle release plugin (required)
    id "io.spring.dependency-management"    version  "1.1.0" // dependency management plugin (required)
}


// apply default settings from jar
apply from: "$rootDir/gradle/common-scripts/scripts/common.gradle"
apply from: "$rootDir/gradle/common-scripts/scripts/java.gradle"

// DONE!
//
// This is enough to build/test/publish almost all java/groovy projects :)
// You should probably add your custom dependencies, though. 
// 

```

### Updating the common scripts 

For updating this repository, merge the changes into the main branch and run the release command
locally from the main branch `./gradlew release`. Go through the steps and ensure a new git tag is created. 

Then, in the main project, checkout the new git tag in the submodule and push your changes.

```
cd gradle/common-scripts
git checkout {your.new.tag}
```

### Removing a git submodule in a dependant project

If for any reason you wish to remove this or any other submodule from a project, run the following:

```
git submodule deinit -f gradle/common-scripts
rm -rf .git/modules/gradle/common-scripts
git rm -f gradle/common-scripts
```

### Pipeline requirements in sub projects

After checking out the code on the pipeline, ensure the submodules are also pulled. 

```
git submodule update --init --recursive
```

# Using version <=0.3.6 

These versions were published as a jar on Sonatype and need to be used accordingly.

### How to "compile"

Clone this project and install artifact jar into local maven repository:

```
./gradlew clean publishToMavenLocal
```

### How to use (version <=0.3.6)

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
    id "net.researchgate.release"           version  "3.0.2" // gradle release plugin (required)
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
