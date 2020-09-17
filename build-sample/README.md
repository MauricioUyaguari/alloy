[![Maven Central](https://img.shields.io/maven-central/v/org.finos.alloy/alloy.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22alloy%22)
![Build](https://github.com/finos/alloy/workflows/alloy-build/badge.svg)
![Docs](https://github.com/finos/alloy/workflows/Docusaurus-website-build/badge.svg)
[![FINOS - Incubating](https://cdn.jsdelivr.net/gh/finos/contrib-toolbox@master/images/badge-incubating.svg)](https://finosfoundation.atlassian.net/wiki/display/FINOS/Archived)

# Alloy Build (SAMPLE)

*TODO: move this content to main readme, when completed*

The alloy project builds with Apache Maven, which takes care of:
- Invoking sub-module build systems, like `npm`
- Manage project version lifecycle
- Creating uber JARs
- Run unit and integration testing
- Send testing metrics to https://sonarcloud.io/code?id=org.finos.alloy%3Aalloy-parent
- Deploy snapshot JARs to https://oss.sonatype.org/#nexus-search;quick~org.finos.alloy
- Release Docker image to https://hub.docker.com/u/finos
- Deploy released JARs to Maven Central
- Run a release process that invoke all steps above in a transactional fashion

## Requisites
1. Please follow https://finosfoundation.atlassian.net/wiki/spaces/FDX/pages/75530342/Continuous+Integration#ContinuousIntegration-Continuous(artifact)deploymentinJava to get credentials for oss.sonatype.org and consequentially Maven Central.
2. Apache Maven 3.x - `mvn -v`
3. JRE 8+ (to confirm)

## Local run

### Build the project and run tests
```
mvn package
```
JAR file is built into `build-sample/server/target/alloy-server-0.1-SNAPSHOT.jar`.

### Submit metrics to SonarCube
```
mvn install -Psonar
```
You must have `SONAR_TOKEN` environment variable set

### Deploy JARs to oss.sonatype.org
```
mvn deploy
```
You must have `CI_DEPLOY_USERNAME` and `CI_DEPLOY_PASSWORD` environment variables set

### Push image to Docker Hub
```
mvn deploy -Pdocker
```
You must have `DOCKER_USERNAME` and `DOCKER_PASSWORD` environment variables set

### Release
Make sure to:
- Perform a successful `mvn deploy` from your workstation prior to a release
  - Check your `settings.xml` and make sure that it [complies with this template](https://github.com/finos/finos-parent-pom/blob/master/settings.xml)
- Have `gpg2` installed
- Have the certificate passphrase at hand

To release:
1. Run `mvn release:prepare release:perform -Prelease`
  - If the command fails, run `mvn release:rollback`
2. Access https://oss.sonatype.org/#stagingRepositories
3. Select the repository and click on `Close`
4. Check the `Activity` tab to check that all validations pass
5. Wait 2 minutes
6. Click on the `Refresh` button
7. Click on the `Release` button

For more info, visit [FINOS Wiki](https://finosfoundation.atlassian.net/wiki/spaces/FDX/pages/75530322/Java#Java-Release) and [Sonatype Wiki](https://central.sonatype.org/pages/releasing-the-deployment.html).

## Continuous Integration (CI)
CI is delivered by the [build.yml](.github/build.yml) GitHub Action, which sequentially...
- downloads all project dependencies and build plugins
- runs the maven build and test
- submits project quality metrics to SonarCube
- creates the Docker images
- deploys JARs to OSS Sonatype repo (if branch is `dev`)
- deploys JARs to Maven Central (if branch is `master`)
- runs Docker image push (if branch is `master`)

# Links
- https://gist.github.com/faph/20331648cdc0b492eba0f4d83f69eaa5
- https://github.com/spotify/dockerfile-maven
- https://github.com/actions/setup-java
