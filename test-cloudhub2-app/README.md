# ra-test-app
This app is for testing CI/CD in Anypoint Platform.
The app is supposed to be deployed into Cloudhub 2.0.

Full development flow:

Desing API with RAML -> Publish API in Exchange -> Mule Gateway via API Manager using this API -> Create implementation app -> Deploy app into Cloudhub 2

## Additional features

In API Manager we can configure policies for our API


## Maven commands for CI/CD
Configure maven (CI/CD) as a `Connected App` in `Access Management`. Id and Secret of this `Connected App` must be used as `connectedAppClientId`, 
`connectedAppClientSecret` inside `cloudhub2Deployment` section in pom.xml 
For the `Connected App` we need to add necessary scopes to grant permissions required for the deployment:

```
Exchange
Exchange Contributor
Exchange Creator
Exchange Viewer

General
View All Environments' Client Management Providers
View Environment
View Environments in a particular organization
View Organization

Runtime Manager
Create Applications
Read Applications
```

Inside `cloudhub2Deployment` section in pom.xml we need to define properties:

    <properties>
        <anypoint.platform.client_id>${CLIENT_ID_OF_CURRENT_BUSINESS_GROUP}</anypoint.platform.client_id>
    </properties>
    <secureProperties>
       <anypoint.platform.client_secret>${CLIENT_SECRET_OF_CURRENT_BUSINESS_GROUP}</anypoint.platform.client_secret>
    </secureProperties> 

First it must be deployed into Exchange with the command:

    mvn deploy -nsu -DskipMunitTests

The next step is deployment into Cloudhub 2.0

    mvn deploy -nsu -DskipMunitTests -DmuleDeploy -X

If something wrong then activate debug messages for maven - `mvn -X`
It will show HTTP API calls with detailed json response. Otherwise, it is not clear


Example of debug log:

```
[DEBUG] HTTP Request
GET https://anypoint.mulesoft.com/amc/application-manager/api/v2/organizations/acd0233f-3db8-4edb-aae5-914767ffe274/environments/586454f3-1c10-48d1-9034-4f53c07b56df/deployments/2aed7adb-ad6b-490c-baed-f27a365db847
Accept: application/json
User-Agent: mule-deployer/4.3.0
Authorization: bearer xxx
x-anypoint-session-extend: true
X-ANYPNT-ENV-ID: 586454f3-1c10-48d1-9034-4f53c07b56df
X-ANYPNT-ORG-ID: acd0233f-3db8-4edb-aae5-914767ffe274


[DEBUG] HTTP response
200 OK
date: Mon, 17 Mar 2025 02:27:25 GMT
server: nginx
content-length: 1680
content-type: application/json
connection: keep-alive
strict-transport-security: max-age=31536000; includeSubDomains
x-anypnt-trx-id: 1eb39

{"id":"2aed7adb-ad6b-490c-baed-f27a365db847","name":"ra-test-app","creationDate":1742178438512,"lastModifiedDate":1742178438512,"target":{"provider":"MC","targetId":"6484a003-1012-426e-8749-13cf6b429290","deploymentSettings":{"clustered":false,"enforceDeployingReplicasAcrossNodes":false,"http":{"inbound":{"publicUrl":"ra-test-app.e0t2qn.aus-s1.cloudhub.io"}},"runtimeVersion":"4.9.2:6e-java17","runtimeReleaseChannel":"EDGE","updateStrategy":"rolling","lastMileSecurity":false,"disableAmLogForwarding":false,"persistentObjectStore":false,"anypointMonitoringScope":"app","sidecars":{"anypoint-monitoring":{"image":"auto","resources":{"cpu":{"limit":"50m","reserved":"0m"},"memory":{"limit":"50Mi","reserved":"50Mi"}}}},"forwardSslSession":false,"generateDefaultPublicUrl":false,"runtime":{"version":"4.9.2:6e-java17","releaseChannel":"EDGE","java":"17"}},"replicas":1},"status":"FAILED","application":{"status":"NOT_RUNNING","desiredState":"STARTED","ref":{"groupId":"acd0233f-3db8-4edb-aae5-914767ffe274","artifactId":"ra-test-app","version":"1.0.0-SNAPSHOT","packaging":"jar"},"configuration":{"mule.agent.application.properties.service":{"applicationName":"ra-test-app","properties":{"anypoint.platform.client_id":"9fe7076ec805464e9350273019ba4e5d"},"secureProperties":{"anypoint.platform.client_secret":"******"}}},"vCores":0.1},"desiredVersion":"efe7bb5e-75c0-4b8f-838b-8483a7157c0a","replicas":[{"state":"FAILED","deploymentLocation":"6484a003-1012-426e-8749-13cf6b429290","currentDeploymentVersion":"efe7bb5e-75c0-4b8f-838b-8483a7157c0a","reason":"Usage exceeds limits of vCores allocation. Please check with your admin to increase quotas."}],"lastSuccessfulVersion":null}

[ERROR] Failed to deploy ra-test-app: Deployment has failed
org.mule.tools.client.core.exception.DeploymentException: Deployment has failed
    at org.mule.tools.verification.DefaultDeploymentVerification.assertDeployment (DefaultDeploymentVerification.java:49)
    at org.mule.tools.verification.fabric.RuntimeFabricDeploymentVerification.assertDeployment (RuntimeFabricDeploymentVerification.java:42)
    at org.mule.tools.deployment.fabric.RuntimeFabricArtifactDeployer.checkApplicationHasStarted (RuntimeFabricArtifactDeployer.java:130)
    at org.mule.tools.deployment.fabric.RuntimeFabricArtifactDeployer.deployApplication (RuntimeFabricArtifactDeployer.java:72)
    at org.mule.tools.deployment.cloudhub2.Cloudhub2ApplicationDeployer.deploy (Cloudhub2ApplicationDeployer.java:36)
    at org.mule.tools.deployment.DefaultDeployer.deploy (DefaultDeployer.java:50)
    at org.mule.tools.maven.mojo.deploy.DeployMojo.doExecute (DeployMojo.java:45)
    at org.mule.tools.maven.mojo.deploy.AbstractMuleDeployerMojo.execute (AbstractMuleDeployerMojo.java:91)
    at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:126)
    at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute2 (MojoExecutor.java:328)
    at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute (MojoExecutor.java:316)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:212)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:174)
    at org.apache.maven.lifecycle.internal.MojoExecutor.access$000 (MojoExecutor.java:75)
    at org.apache.maven.lifecycle.internal.MojoExecutor$1.run (MojoExecutor.java:162)
    at org.apache.maven.plugin.DefaultMojosExecutionStrategy.execute (DefaultMojosExecutionStrategy.java:39)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:159)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:105)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:73)
    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:53)
    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:118)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:261)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:173)
    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:101)
    at org.apache.maven.cli.MavenCli.execute (MavenCli.java:906)
    at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:283)
    at org.apache.maven.cli.MavenCli.main (MavenCli.java:206)
    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:77)
    at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke (Method.java:569)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:255)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:201)
    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:361)
    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:314)
Caused by: java.lang.IllegalStateException: Deployment failed
    at org.mule.tools.verification.fabric.RuntimeFabricDeploymentVerification$RuntimeFabricDeploymentVerificationStrategy.lambda$isDeployed$0 (RuntimeFabricDeploymentVerification.java:69)
    at org.mule.tools.verification.DefaultDeploymentVerification.lambda$assertDeployment$0 (DefaultDeploymentVerification.java:42)
    at org.mule.tools.client.OperationRetrier.retry (OperationRetrier.java:73)
    at org.mule.tools.verification.DefaultDeploymentVerification.assertDeployment (DefaultDeploymentVerification.java:42)
    at org.mule.tools.verification.fabric.RuntimeFabricDeploymentVerification.assertDeployment (RuntimeFabricDeploymentVerification.java:42)
    at org.mule.tools.deployment.fabric.RuntimeFabricArtifactDeployer.checkApplicationHasStarted (RuntimeFabricArtifactDeployer.java:130)
    at org.mule.tools.deployment.fabric.RuntimeFabricArtifactDeployer.deployApplication (RuntimeFabricArtifactDeployer.java:72)
    at org.mule.tools.deployment.cloudhub2.Cloudhub2ApplicationDeployer.deploy (Cloudhub2ApplicationDeployer.java:36)
    at org.mule.tools.deployment.DefaultDeployer.deploy (DefaultDeployer.java:50)
    at org.mule.tools.maven.mojo.deploy.DeployMojo.doExecute (DeployMojo.java:45)
    at org.mule.tools.maven.mojo.deploy.AbstractMuleDeployerMojo.execute (AbstractMuleDeployerMojo.java:91)
    at org.apache.maven.plugin.DefaultBuildPluginManager.executeMojo (DefaultBuildPluginManager.java:126)
    at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute2 (MojoExecutor.java:328)
    at org.apache.maven.lifecycle.internal.MojoExecutor.doExecute (MojoExecutor.java:316)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:212)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:174)
    at org.apache.maven.lifecycle.internal.MojoExecutor.access$000 (MojoExecutor.java:75)
    at org.apache.maven.lifecycle.internal.MojoExecutor$1.run (MojoExecutor.java:162)
    at org.apache.maven.plugin.DefaultMojosExecutionStrategy.execute (DefaultMojosExecutionStrategy.java:39)
    at org.apache.maven.lifecycle.internal.MojoExecutor.execute (MojoExecutor.java:159)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:105)
    at org.apache.maven.lifecycle.internal.LifecycleModuleBuilder.buildProject (LifecycleModuleBuilder.java:73)
    at org.apache.maven.lifecycle.internal.builder.singlethreaded.SingleThreadedBuilder.build (SingleThreadedBuilder.java:53)
    at org.apache.maven.lifecycle.internal.LifecycleStarter.execute (LifecycleStarter.java:118)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:261)
    at org.apache.maven.DefaultMaven.doExecute (DefaultMaven.java:173)
    at org.apache.maven.DefaultMaven.execute (DefaultMaven.java:101)
    at org.apache.maven.cli.MavenCli.execute (MavenCli.java:906)
    at org.apache.maven.cli.MavenCli.doMain (MavenCli.java:283)
    at org.apache.maven.cli.MavenCli.main (MavenCli.java:206)
    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke0 (Native Method)
    at jdk.internal.reflect.NativeMethodAccessorImpl.invoke (NativeMethodAccessorImpl.java:77)
    at jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke (DelegatingMethodAccessorImpl.java:43)
    at java.lang.reflect.Method.invoke (Method.java:569)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launchEnhanced (Launcher.java:255)
    at org.codehaus.plexus.classworlds.launcher.Launcher.launch (Launcher.java:201)
    at org.codehaus.plexus.classworlds.launcher.Launcher.mainWithExitCode (Launcher.java:361)
    at org.codehaus.plexus.classworlds.launcher.Launcher.main (Launcher.java:314)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------

```

