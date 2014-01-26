# Overview

Welcome to the Zend Server PHP buildpack! This buildpack allows you to deploy your PHP apps on Cloud Foundry using Zend Server 6.2. 
Zend Server's integration with Cloud Foundry allows you to quickly get your PHP applications up and running on a highly available PHP production environment which includes, amongst other features, a highly reliable PHP stack, application monitoring, troubleshooting, and more.

# Buildpack Components

* Zend Server 6.2 Enterprise edition
* Zend Server 6.2 configuration files
* PHP 5.4
* Nginx web server
 

# Usage
1. Download and install Cloud Foundry's 'cf' CLI.
2. Create a new folder on your workstation, and access it
3. In the new folder, create an empty file called `zend_server_php_app`. 
4. If you have additional application files and resources you would like to deploy, copy them to the new folder.
5. Create a new 'index.php' file, and paste the following code (if you already have an 'index.php' file, skip to the next step):
```
<?php
echo "Hello world!;
?>
```
6. Enter the following command:
`cf push --buildpack=https://github.com/davidl-zend/zend-server-mysql-buildpack-dev` 
7. Name your application.
8. Select the number of instances you would like to use for your application.
9. Allocate memory for you application (at least 512M).
10. Enter a sub-domain for your application.
11. Enter a domain for your application.
12. Create a MySQL database service for your application (if you already have a database service, skip to step 13). To create a custom service (recommended for production, large clusters):
a. Enter 'y'
b. As the service type, select 'user-provided'.
c. Name the service.
d. Enter a comma separated list of the service parameters.
The required parameters are 'hostname', 'port', 'username' , 'password', 'name' (where 'name' is the database Zend Server will use for its internal functions).
e. Enter the values for each parameter in order. The service is created and bound to the application.
To create a clearDB service:
a. Enter 'y.
b. As the service type, select 'cleardb'.
c. Name the service.
d. Select the clearDB service plan you wish to use (Do NOT select the Spark plan).The service is created and bound to the application.
13. Bind an existing database service to your application, or any additional services you may wish to use (If you already added a database service in the previous step, skip to step 14): a. Enter 'y'. b. Enter the service you wish to bind to the application. The service is bound to the application.
14. To save the configuration, enter 'y'. Your configurations area saved in a 'manifest.yml' file, and your application is deployed using the Zend Server buildpack. This may take a few minutes.
15. Once successfully initialized and deployed, a success message with the URL at which your application is available at is displayed.
16. To access the application, enter the supplied URL in your Web browser.
17. To access Zend Server, enter add 'ZendServer' to the supplied URL. For example:`http://<application URL>/ZendServer` The Zend Server Login page is displayed.
18. To access the Zend Server UI, enter the following credentials: Username - admin, Password - changeme.
19. To change the Zend Server UI password, or in case you misplace your password, enter the following command:
`cf set-env <application name> ZS_ADMIN_PASSWORD <new password>`

# Binding an External MySQL Database Service
You can bind an MySQL external database to the Zend Server app as a "user-provided" service. Doing so will enable persistence, session clustering, and more (if you already bound a database service when creating the application, this process is unnecessary). 
Note:
If you bind more than one database service to the application, specify which service Zend Server should use by setting the 'ZS_ DB' env variable to the correct service. Otherwise, Zend Server will use the first database available: 
`cf set-env <app_name> ZS_DB <db-service-name>`

1. Open your CLI.
2. Run `cf create-service`.
2. As a service type, select "user-provided".
3. Name the service.
4. Enter a comma separated list of the MySQL service parameters.
The required parameters are 'hostname', 'port', 'username' , 'password', 'name' (where 'name' is the database Zend Server will use for its internal functions).
5. Enter the values for each parameter in order.
6. Run the following command to bind the MySQL service to your application: `cf bind-service [service-name] [app-name]`.
7. The service will be auto-detected upon push. Zend Server will create the MySQL database schema and enable clustering features.


# Known Issues
* Zend Server Code Tracing may not work properly in this version.
* Several issues might be encountered if you do not bind MySQL providing service to the app (cleardb/mysql/MaraiaDB):
 * You can change settings using the Zend Server UI and apply them - but they will not survive application pushes and restarts, nor will they be propagated to new application instances.
 * Application packages deployed using Zend Server's deployment mechanism (.zpk packages) will not be propagated to new app instances.
 * Zend Server will not operate in cluster mode.
* Application generated data is not persistent (this is a limitation of Cloud Foundry) unless saved to a third party storage provider (like S3). 
* MySQL is not used automatically - If you require MySQL then you will have to setup your own server and configure your app to use it.
* If the application does not contain an 'index.php' file you will most likely encounter a "403 permission denied error".

# Local Installation
You can optionally install the cartridge in yout local Cloud Foundry environment if you want it to be available as a system buildpack. This enables cartridge "auto detection" and adds it to the menu of cartridges presented by the cf utility. If you do not wish to install, skip this stage and go directly to the usage section.

# Requirements
* Working Cloud Foundry v2 environment with dea_ng and gorouter
* The lucid64 Cloud Foundry stack should be installed and enabled - check settings in /vagrant/dea_ng/config/dea.yml
* xz compression utility - it is installed automatically by vagrant if you follow the guide below

The buildpack is tested against a system generated by the vagrant installer: http://blog.cloudfoundry.com/2013/06/27/installing-cloud-foundry-on-vagrant/

# Instructions
Clone the git repo into the buildpack directory on your dea_ng node using the command:
`git clone https://github.com/davidl-zend/zend-server-mysql-buildpack-dev`

Alternatively you can clone the repo into a Web server in your environment and specify the buildpack to the cf client. 
i.e.  `cf push --buildpack=http://url-to-cloned-repo` or   (you can save this value in the app's manifest.yml).

# Additional Resources
The following resources will help you understand Cloud Foundry concepts and workflows:
* For more info on getting started with Cloud Foundry: http://docs.cloudfoundry.com/docs/dotcom/getting-started.html
* How to add a service in Cloud Foundry: http://docs.cloudfoundry.com/docs/dotcom/adding-a-service.html
* How to design apps for the cloud: http://docs.cloudfoundry.com/docs/using/app-arch/index.html
* Cloud Foundry documentation: http://docs.cloudfoundry.com/
