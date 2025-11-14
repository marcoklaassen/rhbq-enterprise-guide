# Red Hat Build of Quarkus (RHBQ) Enterprise Guide

The purpose of this document is to answer frequently asked questions about Red Hat Build of Quarkus. 
It should help users to use Quarkus supported and enterprise ready. So you'll find information about how to integrate Quarkus 
in your enterprise ready application development lifecycle and handle differences between Red Hat Build of Quarkus (RHBQ) and Quarkus upstream. 

## How to get notified about new Red Hat Build of Quarkus Releases

### Manual Approach

In [the Red Hat Product Errata Page](https://access.redhat.com/errata-search/?q=&p=1&sort=portal_update_date+desc&rows=10&portal_product=Red%5C+Hat%5C+build%5C+of%5C+Quarkus&portal_advisory_type=Product+Enhancement+Advisory) you'll find the latest Product Enhancement Advisory for Red Hat Build of Quarkus

### Pull based API approach

In the [Red Hat Maven Repositorie's maven-metadata.xml](https://maven.repository.redhat.com/ga/com/redhat/quarkus/platform/quarkus-bom/maven-metadata.xml) you'll always find a list of all versions. 

### Notifications - Red Hat Console

In the [Notification Preferences -> Errata | Subscription Services Area of the Red Hat Hybrid Cloud Console](https://console.redhat.com/settings/notifications/user-preferences?bundle=subscription-services&app=errata-notifications) you can configure notifications. Activate Subscription Enhancements (Instant Notification or Daily Digest) to get Notifications about the `RHEAs` you've also found in [the Red Hat Product Errata Page](https://access.redhat.com/errata-search/?q=&p=1&sort=portal_update_date+desc&rows=10&portal_product=Red%5C+Hat%5C+build%5C+of%5C+Quarkus&portal_advisory_type=Product+Enhancement+Advisory).

You can also configure [Integrations in the Red Hat Hybrid Cloud Console](https://console.redhat.com/settings/integrations?category=overview). 
So you can configure the way you'll get notified (e.g. E-Mail, Microsoft Teams, Slack Integrations). You can add integrations like `Webhook integrations`, too. This way you are able to integrate notifications in pipeline systems like GitLab, Jenkins or Tekton. 
It's e.g. good practice to notifiy Java community Teams channels about new RHBQ Releases and trigger test pipelines automatically to make sure every application is able to check their code against the new release. 

How to setup and configure Notifications is documented in the [Configuring notifications on the Red Hat Hybrid Cloud Console](https://docs.redhat.com/en/documentation/red_hat_hybrid_cloud_console/1-latest/html/configuring_notifications_on_the_red_hat_hybrid_cloud_console/index) part of the Red Hat Hybrid Cloud Console Documentation. 

## There is a new Quarkus Release: How to get it? Where should I have a look at? 

Via https://docs.redhat.com/en/documentation/red_hat_build_of_quarkus/ you'll find always the latest RHBQ documentation. 

Let's take the example of RHBQ 3.27: 
You'll find the new Maven Configuration for the updated release in [Reconfiguring your Maven project to Red Hat build of Quarkus](https://docs.redhat.com/en/documentation/red_hat_build_of_quarkus/3.27/html/getting_started_with_red_hat_build_of_quarkus/assembly_quarkus-getting-started#con_quarkus).

IMHO a simpler way to get information about the current Maven Configuration is to go via [Quarkus Documentation](https://docs.redhat.com/en/documentation/red_hat_build_of_quarkus/) -> `Migrating applications to Red Hat build of Quarkus $version`. There is a maven comment to upgrade your application directly and automatically. 

Sometimes after a new release we experienced that the documentation isn't up to date even when the latest release was announced or already available in the maven repository. That shouldn't be the case and please [create a support case](https://access.redhat.com/support/) in this case. 
But for me the single Source of Truth is [The Red Hat Maven Repository](https://maven.repository.redhat.com/ga/com/redhat/quarkus/platform/quarkus-bom/maven-metadata.xml).

And for pipeline integrations you can simply use 
```bash
curl -s https://maven.repository.redhat.com/ga/com/redhat/quarkus/platform/quarkus-bom/maven-metadata.xml | \
xmllint --xpath "string(/metadata/versioning/latest)" -
```
to simply find the latest RHBQ release version (incl. Red Hat Build number).

## Using RHBQ BOM with Quarkiverse Extensions

Q: Is it possible to use red hat build of quarkus (with the maven bom configuration) and also use quarkiverse extensions? how does the maven pom.xml does look like in these cases?

A: Yes, it is absolutely possible to use Red Hat build of Quarkus (RHBQ) for the main platform BOM while also adding extensions from the community-driven Quarkiverse.

```xml
<project>
    <properties>
        <quarkus.platform.group-id>com.redhat.quarkus.platform</quarkus.platform.group-id>
        <quarkus.platform.version>3.27.0.redhat-00002</quarkus.platform.version>
        <my-quarkiverse-ext.version>1.2.3</my-quarkiverse-ext.version> 
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>${quarkus.platform.group-id}</groupId>
                <artifactId>quarkus-bom</artifactId>
                <version>${quarkus.platform.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-resteasy-reactive</artifactId>
        </dependency>

        <dependency>
            <groupId>io.quarkiverse.myext</groupId>
            <artifactId>quarkus-myext</artifactId>
            <version>${my-quarkiverse-ext.version}</version> 
        </dependency>
        
        </dependencies>

    </project>
```

### Important Considerations
Version Management is Manual for Quarkiverse: You must manually specify the `<version>` for any Quarkiverse dependency since it is not included in the Red Hat BOM.

Support Implications: Quarkiverse extensions are community-driven and are not supported by Red Hat. This creates a hybrid build where core components are supported, but community extensions are not.

## quarkus-logging-json (io.quarkus compared with io.quarkiverse.loggingjson)

### The Core io.quarkus Extension

`io.quarkus:quarkus-logging-json`

Comes from the core Quarkus repository and is automatically included in the supported Red Hat Build of Quarkus (RHBQ) Bill of Materials (BOM).

It provides a basic, functional implementation of JSON formatting for the JBoss Log Manager, which Quarkus uses by default.

### The Quarkiverse io.quarkiverse.loggingjson Extension

`io.quarkiverse.loggingjson:quarkus-logging-json`

Created and maintained within the community-driven Quarkiverse.

It was developed to add missing features, most importantly the ability to easily output logs in the Elastic Common Schema (ECS) format. 
ECS: naming convention that makes logs from different applications easier to ingest and analyze in platforms like the Elastic Stack (Elasticsearch, Logstash, Kibana).

This version is more feature-rich. 

## Quarkus Maven Version for RHBQ (e.g. 3.27.0.redhat-00002)

The trailing number in the Red Hat build of Quarkus (RHBQ) version, like the 00002 in 3.27.0.redhat-00002, is part of Red Hat's build and release sequence numbering scheme. It has a specific technical purpose related to maintenance and support.

The RHBQ version typically follows this format:

* `{Community_Version}.{redhat}-{BBBBB}`
* `3.27.0` (Community Version): The version number of the upstream Quarkus community project.
* `.redhat` (Product Identifier): Identifies this as the certified Red Hat product build.
* `00002` (Build Sequence/Release Number): This represents the specific Red Hat build iteration of that particular community version.

Example: 

If a critical issue is found in the certified 3.27.0 build, Red Hat will create a new build with the fix without changing the community base version.

