---
layout: default
title: Deploying to Heroku
permalink: /heroku/
redirect_from:
  - /heroku.html
sitemap:
    priority: 0.7
    lastmod: 2023-12-19T00:00:00-00:00
---

# Deploying to Heroku

This sub-generator allows deployment of your JHipster application to the [Heroku cloud](https://www.heroku.com/){:target="_blank" rel="noopener"}.

[![]({{ site.url }}/images/logo/logo-heroku.png)](https://www.heroku.com/){:target="_blank" rel="noopener"}


## Running the sub-generator

Before running the sub-generator, you must install the [Heroku CLI](https://cli.heroku.com/){:target="_blank" rel="noopener"}.

You must also [create a Heroku account](http://signup.heroku.com/){:target="_blank" rel="noopener"} and log in with the CLI by running the following command:

<pre>**$ heroku login**
Enter your Heroku credentials.
Email: YOUR_EMAIL
Password (typing will be hidden): YOUR_PASSWORD
Authentication successful.
</pre>

<div class="alert alert-warning"><i class="fa fa-money" aria-hidden="true"></i>
As of <a href="https://blog.heroku.com/next-chapter" target="_blank" rel="noopener">>November 2022 Heroku does not offer a completely free tier</a> anymore.
This means you will need a properly <a href="https://devcenter.heroku.com/articles/account-verification" target="_blank" rel="noopener">verified Heroku account</a>
and deploying an application using the smallest dyno options and the smallest Postgres size will cost you around <b>$12 per month</b>.
</div>

The Heroku sub-generator creates an application using [free dynos](https://devcenter.heroku.com/articles/dyno-types){:target="_blank" rel="noopener"} with add-ons matching your selected configuration.

We support the following addons:

* [Heroku Postgres](https://www.heroku.com/postgres){:target="_blank" rel="noopener"} when using PostgreSQL
* [JawsDB](https://elements.heroku.com/addons/jawsdb){:target="_blank" rel="noopener"} when using MySQL or MariaDB
* [Heroku Redis](https://elements.heroku.com/addons/heroku-redis){:target="_blank" rel="noopener"} when [using Redis](/using-cache/#caching-with-redis)
* [MemCachier](https://elements.heroku.com/addons/memcachier){:target="_blank" rel="noopener"} when [using Memcached](/using-cache/#caching-with-memcached)
* [Bonsai Elasticsearch](https://elements.heroku.com/addons/bonsai){:target="_blank" rel="noopener"} when [using Elasticsearch](/using-elasticsearch/)
* [Okta](https://elements.heroku.com/addons/okta){:target="_blank" rel="noopener"} when [using OAuth2/OIDC (optional)](/security/#oauth2)


To deploy your application to Heroku, run this command:

`jhipster heroku`

This should package your application in "production" mode, create an Heroku application with a database, upload your code, and start the application.

<div class="alert alert-info"><i class="fa fa-info-circle" aria-hidden="true"></i>
Note that if your application is a microservice, you will be prompted to provide a registry URL. Scroll down to learn how to do this.
</div>

<div class="alert alert-warning"><i class="fa fa-exclamation-circle" aria-hidden="true"></i>
Please be aware that your application must start under 90 seconds, or it will be shutdown.
Depending on the platform load, starting under 90 seconds is not guaranteed!
</div>

## Changing the Java version

You can select the Java version when executing the Heroku sub-generator.
By default this will be Java 11.
You can find all on Heroku [supported Java version in the official documentation](https://devcenter.heroku.com/articles/java-support#supported-java-versions){:target="_blank" rel="noopener"}.

If you want to change the Java version e.g. from `11` to `14` later you need to change it in `system.properties` in your projects root folder:

```
java.runtime.version=14
```

When you redeploy your application it will use Java 14.

## Deploying your application

By default the application will be [deployed via git](https://devcenter.heroku.com/articles/git){:target="_blank" rel="noopener"}.
This means you push your code and Heroku will build and deploy it on their servers.
If you can't or don't want to push code to someone else's server you can use the jar option and [deploy an executable jar](https://devcenter.heroku.com/articles/deploying-executable-jar-files){:target="_blank" rel="noopener"}.
Heroku also supports [deploying a docker image](https://devcenter.heroku.com/articles/container-registry-and-runtime){:target="_blank" rel="noopener"}, but the sub-generator does not support this option yet.

### Updating your deployed application

#### Using git option

When deploying via git a new remote has been created called heroku.
To deploy new code you need to push the changes to the heroku remote:

`git push heroku master`

<div class="alert alert-info"><i class="fa fa-info-circle" aria-hidden="true"></i>
This assumes you have run the generator on the machine you are executing this command from.
If you have not, you will need to follow the <a href="https://devcenter.heroku.com/articles/git#for-an-existing-heroku-app" target="_blank" rel="noopener">instructions for creating a Heroku remote</a>.
</div>

#### Using jar option

When you selected to deploy an executable jar you need to create the updated jar and deploy the new file to Heroku.

##### Preparing a new jar

When your application is already deployed, you can prepare a new deployment with:

`./mvnw package -Pprod -DskipTests`

Or when using gradle:

`./gradlew -Pprod bootJar -x test`

##### Pushing to production

<div class="alert alert-info"><i class="fa fa-info-circle" aria-hidden="true"></i>
This assumes you have run the generator on the machine you are executing this command from.
If you have not, you will need to follow the instructions for installing the <a href="https://devcenter.heroku.com/articles/deploying-executable-jar-files" target="_blank" rel="noopener">Heroku Java CLI</a>.
</div>

To push to production, type:

`heroku deploy:jar target/*.jar`

Or when using gradle:

`heroku deploy:jar build/libs/*jar`

## Deploying Docker to Heroku

You can deploy your app as a Docker container to Heroku too. While this works, there's no Heroku setup and configuration that happens, so you have to do that manually. This documentation assumes you've already run `jhipster heroku` to deploy your app and therefore leverages the integration and add-on provisioning that this process performs.

**NOTE**: If you're using a version of JHipster that's prior to v6.10.2, you'll need to add the following to `src/main/resources/config/application-heroku.yml`:

```yaml
server:
  port: ${PORT:8080}
```

Build your Docker image:

```
./mvnw package -Pprod verify jib:dockerBuild
```

If you're using Gradle:

```
./gradlew -Pprod bootJar jibDockerBuild
```

You can test it out locally using Docker Compose.

```shell
docker-compose -f src/main/docker/app.yml up
```

Once you've confirmed everything works, create a new app on Heroku, and add it as a remote.

```shell
heroku apps:create
git remote add docker https://git.heroku.com/<your-new-app>.git
```

Then run the commands below to deploy your JHipster app as a Docker image. Be sure to replace the `<...>` placeholders with your Heroku app name. If you don't know your app name, run `heroku apps`.

```shell
heroku container:login
docker tag space registry.heroku.com/<heroku-app>/web
docker push registry.heroku.com/<heroku-app>/web
```

For example:

```shell
heroku container:login
docker tag space registry.heroku.com/fast-peak-70014/web
docker push registry.heroku.com/fast-peak-70014/web
```

At this point, you can use the PostgreSQL and Okta add-ons you've already configured. Run the following command to get the identifiers of the add-ons from the `heroku` remote that you first deployed to.

```shell
heroku addons --remote heroku
```

Then you can attach these instances to your new application.

```shell
heroku addons:attach <postgresql-addon-name> --remote docker
heroku addons:attach <okta-addon-name> --remote docker
```

When you use `jhipster heroku` to deploy your application, it properly configures the database for you. However, when deploying it as a Docker container, none of that happens. Therefore, you need to set a few configuration variables so your Docker container can talk to PostgreSQL. First, run the following command to get the PostgreSQL URL.

```
heroku config:get DATABASE_URL --remote docker
```

This command will retrieve a value with the following syntax:

```
postgres://username:password@address
```

Then, set the database environment variables to match the keys that are in `application-heroku.yml`:

```shell
heroku config:set JDBC_DATABASE_URL=jdbc:postgresql://<address> --remote docker
heroku config:set JDBC_DATABASE_USERNAME=<username> --remote docker
heroku config:set JDBC_DATABASE_PASSWORD=<password> --remote docker
```

Set the max amount of Java memory to use and specify the Spring profiles.

```shell
heroku config:set JAVA_OPTS=-Xmx256m
heroku config:set SPRING_PROFILES_ACTIVE=prod,heroku
```

Run the command below to open your browser and navigate to your app.

```
heroku open --remote docker
```

Copy the URL of your app and log in to your Okta developer account. Go to **Applications** > **Web** > **General** and add the URL to Login and Logout redirect URIs. Make sure the login redirect URI ends with `/login/oauth2/code/oidc`.

Now you should be able to release your container and start the app.

```
heroku container:release web --remote docker
```

You can watch the logs to see if your container started successfully.

```
heroku logs --tail --remote docker
```

Now you should be able to open your app, click the **sign in** link, and authenticate!

```
heroku open --remote docker
```

**NOTE**: You will **NOT** be able to login to your JHipster app using the admin account the Okta add-on provisions. To make sure you're not logged in with that account, we suggest you use a new private window to log in.

If you test your Dockerized JHipster app on [securityheaders.com](https://securityheaders.com), you'll see it scores an **A**!

## Deploying Microservices

JHipster microservices require Consul or JHipster Registry as described in the [Doing microservices with JHipster](/microservices-architecture/) documentation. You can deploy a JHipster registry to Heroku by clicking this button:

[![Deploy to Heroku](https://camo.githubusercontent.com/c0824806f5221ebb7d25e559568582dd39dd1170/68747470733a2f2f7777772e6865726f6b7563646e2e636f6d2f6465706c6f792f627574746f6e2e706e67)](https://dashboard.heroku.com/new?&template=https%3A%2F%2Fgithub.com%2Fjhipster%2Fjhipster-registry)

Once the registry is deployed, you can run the `jhipster heroku` command against your microservice or gateway. The Heroku sub-generator will prompt you for the URL of your registry, which will be in the form `https://[appname].herokuapp.com`.

A registry running on Heroku has a few limitations, including:

*   The registry will only work with native configuration (and not Git config).
*   The registry service cannot be scaled up to multiple dynos to provide redundancy. You must deploy multiple applications (i.e. click the button more than once). This is because Eureka requires distinct URLs to synchronize in-memory state between instances.

### Using security with your JHipster Registry on Heroku

To get the automatically-generated admin password on the JHipster Registry, type:

`heroku config:get JHIPSTER_PASSWORD`

To use this password, update all of your microservices and your gateway to use the credentials for the registry by running this command:

`heroku config:set JHIPSTER_REGISTRY_URL="https://admin:[password]@[appname].herokuapp.com"`

## Troubleshooting

If your application is stopped by Heroku when your Liquibase changelog is being applied, your database will be marked as "locked" by Liquibase. You will need to manually clean the lock table. On Postgres, you make sure you have a [local Postgres client installed](https://devcenter.heroku.com/articles/heroku-postgresql#local-setup){:target="_blank" rel="noopener"} and run the following command:

`heroku pg:psql -c "update databasechangeloglock set locked=false;"`

Heroku has a default boot-timeout limit of 90 seconds. If your app takes longer than this, Heroku will stop the process, which may leave the database in a locked state. If the problem is persistent, try contacting [Heroku Support](http://help.heroku.com){:target="_blank" rel="noopener"} to request a longer boot limit for your app.

### Using Elasticsearch

The Bonsai used addon with the free sandbox plan does [only support Elasticsearch 7.10.x](https://docs.bonsai.io/article/139-which-versions-bonsai-supports){:target="_blank" rel="noopener"}.
This might lead to some [incompatibilities](https://github.com/jhipster/generator-jhipster/issues/10003){:target="_blank" rel="noopener"} depending in the Spring Data and [JHipster versions you are using](https://github.com/jhipster/generator-jhipster/issues/18650){:target="_blank" rel="noopener"}. 
JHipster [enforces bonsai compatible Elasticsearch](https://github.com/jhipster/generator-jhipster/pull/18774){:target="_blank" rel="noopener"} dependencies (e.g. clients) when deploying to Heroku. 

<div class="alert alert-warning"><i class="fa fa-money" aria-hidden="true"></i>
If you are willing to use a <b>paid</b> addon you can use the <a href="https://devcenter.heroku.com/articles/foundelasticsearch" target="_blank" rel="noopener">official Elastic Cloud integration</a> to get access to the latest Elasticsearch version and features.
</div>

## More information

*   [Example Application](https://github.com/kissaten/jhipster-example){:target="_blank" rel="noopener"}
*   [Spring Boot Heroku documentation](https://docs.spring.io/spring-boot/docs/current/reference/html/deployment.html#cloud-deployment-heroku){:target="_blank" rel="noopener"}
*   [Heroku free dyno documentation](https://devcenter.heroku.com/articles/free-dyno-hours){:target="_blank" rel="noopener"}
*   [Heroku Java support documentation](https://devcenter.heroku.com/articles/java-support#supported-java-versions){:target="_blank" rel="noopener"}
