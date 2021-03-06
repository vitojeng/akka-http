## Releasing

Create a new issue from the [Akka HTTP Release Train Issue Template](scripts/release-train-issue-template.md) by running `scripts/create-release-issue.sh` and follow the steps.

A few more background information about the process can be found below.

### Under the Travis hood

Here is what happens in detail when Travis CI is building a git tagged commit:

1. According to the `.travis.yml` file `deploy` section it first runs a `+publish` task:
  1. Because of the `+` a the start of the task, all of these actions are repeated for every scala version defined in `crossScalaVersions`.
  2. Because of the `bintray-sbt` plugin, `publish` builds and uploads artifacts to Bintray.
  3. By default all projects have `BintrayPlugin` enabled. Projects that have `disablePlugins(BintrayPlugin)` are not built.
  4. Artifacts are uploaded to `https://bintray.com/$bintrayOrganization/$bintrayRepository/$bintrayPackage` which in this case is [https://bintray.com/akka/maven/akka-http](https://bintray.com/akka/maven/akka-http).
  5. Credentials for the `publish` task are read from the `BINTRAY_USER` and `BINTRAY_PASS` environment variables which are stored encrypted on the `.travis.yml` file. The user under these credentials must be part of the [Akka team on Bintray](https://bintray.com/akka/).

# Pushing to Maven Central
This could be done automatically via `.travis.yml` in `deploy` by adding section is `akka-http/bintraySyncMavenCentral`, however we prefer to run this step manually after confirming a release is valid.

  1. This task syncs all of the artifacts under the [Akka Http](https://bintray.com/akka/maven/akka-http) package in Bintray to Maven Central. For the sync to be successful, the package first needs to be added to JCenter repository. This must be done through Bintray Web interface, and only once when the package is created.
  2. This task is only run for one project, because all Akka Http projects are published to a single package on Bintray.
  3. Credentials for the `bintraySyncMavenCentral` task are read from the `SONATYPE_USER` and `SONATYPE_PASS` environment variables which are stored encrypted on the `.travis.yml` file. The user under these credentials must have the rights to publish artifacts to the Maven Central under the `com.typesafe.akka` organization name.
