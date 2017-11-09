# Getting started with Tomcat on IBM Cloud

In this getting started, we'll take you through a sample Tomcat helloWorld app.

## Prerequisites

You'll need the following:
* [IBM Cloud account](https://console.ng.bluemix.net/registration/)
* [Cloud Foundry CLI](https://github.com/cloudfoundry/cli#downloads)
* [Eclipse IDE for Java EE Developers](http://www.eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/neon2)
* [Git](https://git-scm.com/downloads)
* [Maven](https://maven.apache.org/download.cgi)
* [Apache Tomcat version 8.0.41](https://tomcat.apache.org/download-80.cgi#8.0.41 )


## 1. Clone the sample app

Now you're ready to start working with the sample Tomcat app. Clone the repository and change to the directory to where the sample app is located.
```
git clone https://github.com/IBM-Bluemix/get-started-tomcat
cd get-started-tomcat
```

Peruse the files in the *get-started-tomcat* directory to familiarize yourself with the contents.

## 2. Run the app locally

You must install the dependencies and build a .war file as defined in the pom.xml file to run the app.

Install the dependencies.

```
mvn clean install  
```

Copy GetStartedTomcat.war from the `target` directory into your `tomcat-install-dir` `webapps` directory.

Run the app.  
```
<tomcat-install-dir>/bin/startup.bat|.sh
```

View your app at: http://localhost:8080/GetStartedTomcat/

Use `shutdown.bat|.sh` to stop your app.  Note you may need to give the commands execute permission.

## 3. Prepare the app for IBM Cloud deployment

To deploy to IBM Cloud, it can be helpful to set up a manifest.yml file. One is provided for you with the sample. Take a moment to look at it.

The manifest.yml includes basic information about your app, such as the name, how much memory to allocate for each instance and the route. In this manifest.yml **random-route: true** generates a random route for your app to prevent your route from colliding with others.  You can replace **random-route: true** with **host: myChosenHostName**, supplying a host name of your choice. [Learn more...](https://console.bluemix.net/docs/manageapps/depapps.html#appmanifest)
 ```
applications:
- name: GetStartedTomcat
  random-route: true
  memory: 256M
  path: target/GetStartedTomcat.war
  buildpack: java_buildpack
 ```

## 4. Deploy the app

You can use the Cloud Foundry CLI to deploy apps.

Choose your API endpoint

```
cf api <API-endpoint>
```

Replace the *API-endpoint* in the command with an API endpoint from the following list.

|URL                             |Region          |
|:-------------------------------|:---------------|
| https://api.ng.bluemix.net     | US South       |
| https://api.eu-de.bluemix.net  | Germany        |
| https://api.eu-gb.bluemix.net  | United Kingdom |
| https://api.au-syd.bluemix.net | Sydney         |


Login to your IBM Cloud account:

```
cf login
```

From within the *get-started-tomcat* directory push your app to IBM Cloud
```
cf push
```

This can take around two minutes. If there is an error in the deployment process you can use the command `cf logs <Your-App-Name> --recent` to troubleshoot.

When deployment completes you should see a message indicating that your app is running.  View your app at the URL listed in the output of the push command.  You can also issue the
  ```
cf apps
  ```
  command to view your apps status and see the URL.

## 6. Developing in Eclipse


IBM® Eclipse Tools for IBM Cloud provides plug-ins that can be installed into an existing Eclipse environment to assist in integrating the developer's integrated development environment (IDE) with IBM Cloud.

Download and install  [IBM Eclipse Tools for IBM Cloud](https://developer.ibm.com/wasdev/downloads/#asset/tools-IBM_Eclipse_Tools_for_Bluemix).

Import this sample into Eclipse using `File` -> `Import` -> `Maven` -> `Existing Maven Projects` option.

Create a Tomcat server definition:
  - In the `Servers` view right-click -> `New` -> `Server`.
  - Select `Apache` -> `Tomcat v8.0 Server`.
  - Choose your `tomcat-install-dir`.
  - Continue the wizard with default options to Finish.

Run your application locally on the Apache server:
  - Right click on the `GetStartedTomcat` sample and select `Run As` -> `Run on Server` option.
  - Find and select the localhost Tomcat server and press Finish.
  - In a few seconds, your application should be running at http://localhost:8080/GetStartedTomcat/

Create a IBM Cloud server definition:
  - In the `Servers` view, right-click -> `New` -> `Server`.
  - Select `IBM` -> `IBM Bluemix` and follow the steps in the wizard.
  - Enter your credentials and click `Next`
  - Select your `org` and `space` and click `Finish`

Run your application on IBM Cloud:
  - Right click on the `GetStartedTomcat` sample and select `Run As` -> `Run on Server` option.
  - Find and select the `IBM Bluemix` and press Finish.
  - A wizard will guide you with the deployment options. Be sure to choose a unique `Name` for your application.
  - In a few minutes, your application should be running at the URL you chose.

Now you have run your code locally and on the cloud!

The `IBM Eclipse Tools for IBM Cloud` provides many powerful features such as incremental updates, remote debugging, pushing packaged servers, etc. [Learn more](/docs/manageapps/eclipsetools/eclipsetools.html)

## 7. Add a database

Next, we'll add a NoSQL database to this application and set up the application so that it can run locally and on IBM Cloud.

1. Log in to IBM Cloud in your Browser. Browse to the `Dashboard`. Select your application by clicking on its name in the `Name` column.
2. Click on `Connections` then `Connect new`.
2. In the `Data & Analytics` section, select `Cloudant NoSQL DB` and `Create` the service.
3. Select `Restage` when prompted. IBM Cloud will restart your application and provide the database credentials to your application using the `VCAP_SERVICES` environment variable. This environment variable is only available to the application when it is running on IBM Cloud.

Environment variables enable you to separate deployment settings from your source code. For example, instead of hardcoding a database password, you can store this in an environment variable which you reference in your source code. [Learn more...](/docs/manageapps/depapps.html#app_env)

## 8. Use the database

We're now going to update your local code to point to this database. We'll store the credentials for the services in a properties file. This file will get used ONLY when the application is running locally. When running in Bluemix, the credentials will be read from the VCAP_SERVICES environment variable.

1. Open Eclipse and open the file src/main/resources/cloudant.properties:
  ```
  cloudant_url=
  ```

2. In your browser open the IBM Cloud UI, select your App -> Connections -> Cloudant -> View Credentials

3. Copy and paste just the `url` from the credentials to the `url` field of the `cloudant.properties` file and save the changes.  The result will be something like:
  ```
  cloudant_url=https://123456789 ... bluemix.cloudant.com
  ```

4. Restart Tomcat server in Eclipse from the `Servers` view.

  Refresh your browser view at: http://localhost:8080/GetStartedTomcat/. Any names you enter into the app will now get added to the database.

  Your local app and the IBM Cloud app are sharing the database.  View your IBM Cloud app at the URL listed in the output of the push command from above.  Names you add from either app should appear in both when you refresh the browsers.
