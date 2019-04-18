# AMPLIFY Flow Central Docker

AMPLIFY Flow Central Docker

## Content

- Docker-Compose: Orchestration infrastructure.

## Before you begin

This document assumes a basic understanding of core Docker concepts such as containers, container images, and basic Docker commands.
If needed, see [Get started with Docker](https://docs.docker.com/get-started/) for a primer on container basics.

## Prerequisites

- Git
- Docker version 17.09 or higher
- Docker-Compose version 1.17.0 or higher
- Create the directories where you wish to bind your volumes
- License and certificates
-	Optional: MongoDB instance

*Note* You may use an existing MongoDB instance or our bundled up MongoDB service which will be deployed by default when running with our docker-compose.

#### Installing the necessary prerequisites  (Git, Docker Engine and Docker Compose)

Add local registry to docker installation daemon.conf file:

For unix /etc/docker/daemon.json
{
"insecure-registries": ["cgqajenkins.lab.buch.axway.int:8083"]
}
!!! The docker service needs to be restarted in order to apply the changes.


Deploy
In the directory containing this readme file (flowcentral-docker-master\fc-1.0.0) run the following commands:
  docker login cgqajenkins.lab.buch.axway.int:8083 -u admin -p admin123
  docker-compose up --build
The environment deployment using docker-compose up --build command takes around 7-12 minutes.
This includes: start containers, register products ( CFT, ST ) in Flow Central and create predefined objects (depending on the configuration of you machine).


##### Installing Git

*Note* Debian/Ubuntu
```console
sudo apt install -y git
```

*Note* Centos/RHEL
```console
sudo yum install -y git
```

 ***(More details on Git installation can be found at the following url: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git )***

##### Installing the Docker Engine and Docker Compose

*Note* Details on installing Docker Engine for Linux based systems can be found here: https://docs.docker.com/cs-engine/1.12/.

*Note* Details on installing Docker Compose can be found here : https://docs.docker.com/compose/install/.

 ***(More details on Docker and Docker Compose can be found here: https://docs.docker.com/ )***

##### In order to obtain the necessary license files please contact your local Axway vendor

## Clone the Flow Central repository from GitHub

```console
git clone https://git.ecd.axway.int/cloud/docker-flowcentral.git
cd docker-flowcentral
```

#### Create volumes for persistence data

 - mkdir -p /home/$USER/volume_mount/fc_logs         # All the application logs will be stored in here.
 - mkdir -p /home/$USER/volume_mount/fc_keystore     # This folder has to contain either the java keystore (i.e. files with the `.jks` extension) certificates or `.p12` keys.
 - mkdir -p /home/$USER/volume_mount/fc_license      # This folder will contain the Flow Central license under the name **license.xml**.
 - mkdir -p /home/$USER/volume_mount/fc_api_keys     # This folder will contain the private and public keys required to connect to API Central.
 - mkdir -p /home/$USER/volume_mount/fc_keys         # This folder contains keys generated by the Flow Central service for encrypting entries in the database. **WARINING** do not modify these key files as this will result in both loss of data and the inability to apply any updates.

*Note* In order to persist data from the MongoDB container we create volumes where the Mongo data and Mongo configuration files will be stored. **TAKE HEED**, you may use any MongoDB image you desire as Axway does not offer support and/or maintenance for the MongoDB image bundled alongside Flow Central.

 - mkdir -p /home/$USER/volume_mount/mongo_data      # Mongo Database files.
 - mkdir -p /home/$USER/volume_mount/mongo_conf      # Mongo Database configuration folder.

*Note* In order for the Flow Central and MongoDB services to be able to operate you have to give the appropriate permissions to folder where the volumes will be mounted.

Example : chmod -R 777 /home/$USER/volume_mount

*Note* For additional details regarding the keys used for connecting and authenticating to the Axway services refer to the section **Service authentication and authorization** at the following URL: https://techweb.axway.com/confluence/display/DOCFC/Docker+compose+API+Central+parameters.

*Note*  The Flow Central `docker-compose.yml` file defines a volume as a mechanism for persisting data generated by and used by the MongoDB service required by Flow Central.
The MongoDB data is placed in this volume so it can be reused when creating and starting a new Flow Central container.

## Customizing the Flow Central service

The docker-compose file describes the Flow Central service along with the MongoDB service it requires. Furthermore it allows the management and configuration of the aforementioned services parameters.

Before you start, you must specify the Flow Central image you want to use. For that you must edit the **image** entry under the **fchost** service and specify the image name using the following naming convention "< repository > : < tag >".

Example:
 ```console
  image: axway/FlowCentral:latest
```
Moreover please make sure that the folders created at the previous step (i.e. **Create volumes for persistence data** ) correspond with those specified in the docker-compose file.

Finally please ensure that the entry `hostname` under the service `fchost` has the same value as the parameters `FQDN` and `HOSTNAME` listed under the `environment` entry and that they correspond to the actual hostname of the machine running the Flow Central services.

#### Using a custom MongoDB

In order to use a custom MongoDB service you must first comment/remove the entries under the `fcmongo` service located in the docker-compose file.

Furthermore you must edit the `fchost` service located in the same file. You must fill out the MongoDB environment variables with the appropriate values specific to your custom solution. Finally you must comment/remove the entry `depends_on` from the `fchost`.

#### Docker-compose parameters

The following tables illustrate a list of available parameters from the docker-compose file. With these parameters you can fine tune Flow Central's configuration in order for it to better fit your use case.

### Docker-compose **editable** parameters (these parameters come with a default value, but they can be customized for your use case)

#### Flow Central

 **Parameter**               |  **Values**  |  **Description**
 --------------------------- | :----------: | ---------------
ACCEPT_EULA                  |  \<string>   |  Parameter which indicates your acceptance of the End User License Agreement (i.e. EULA).
FQDN                         |  \<string>   |  Host address of the Flow Central instance.
HOSTNAME                     |  \<string>   |  Host name of the Flow Central instance.
CG_SHARED_SECRET             |  \<string>   |  Flow Central's shared secret.
ENCRYPTION_KEY               |  \<string>   |  Flow Central's encryption key.
MONGODB_HOST                 |  \<string>   |  MongoDB host name.
MONGODB_PORT                 |  \<number>   |  MongoDB port.
MONGODB_ROOT_NAME            |  \<string>   |  MongoDB root user name.
MONGODB_USER_NAME            |  \<string>   |  Flow Central's user MongoDB user.
MONGODB_USER_PASSWORD        |  \<string>   |  Flow Central's user MongoDB user's password.
UME_APIC_PUBLICKEY           |  \<string>   |  ApiCatalog Public Key.
UME_APIC_PRIVATEKEY          |  \<string>   |  ApiCatalog Private Key.
UME_APIC_HOST                |  \<string>   |  ApiCatalog Host.
UME_APIC_CLIENTID            |  \<string>   |  ApiCatalog Client ID.
UME_APIC_USE_CATALOG         |  \<string>   |  Enable or disable connection to ApiCatalog.
UME_APIC_TOKENURL            |  \<string>   |  API Catalog connection URL.
BUSINESS_CA_FILE_CHOSEN      |  \<string>   |  File name of the custom business certificate.
GOVERNANCE_CA_FILE_CHOSEN    |  \<string>   |  File name of the custom governance certificate.
SECURITY_CG_UI_FILE_CHOSEN   |  \<string>   |  File name of the UI certificate.


#### Flow Central docker-compose **uneditable** parameters

**Parameter**                       |  **Values**  |  **Description**
------------------------------------| :----------: | ---------------
IDP_CONFIGURATION                   |  \<string>   |  Flow Central's internal configurations type.
BUSINESS_CA_CERTIF_ALIAS            |  \<string>   |  Custom business certificate's internal alias.
BUSINESS_CA_PASSWORD                |  \<string>   |  Custom business certificate's password.
BUSINESS_USE_CUSTOM_JKS             |  \<string>   |  Flag which tells Flow Central to use a custom certificate file (\*.jks).
BUSINESS_CA_JKS                     |  \<string>   |  Path towards the BUSINESS certificate file (java keystore).
GOVERNANCE_CA_CERTIF_ALIAS          |  \<string>   |  Custom governance certificate internal alias.
GOVERNANCE_CA_PASSWORD              |  \<string>   |  Custom governance certificate password.
GOVERNANCE_CA_JKS                   |  \<string>   |  Path towards the GOVERNANCE certificate file (java keystore).
SECURITY_CG_UI_ALIAS                |  \<string>   |  UI's certificate alias.
SECURITY_CG_UI_PASSWORD             |  \<string>   |  UI's s certificate password.
SECURITY_CG_UI_JKS                  |  \<string>   |  Path towards the UI certificate file (java keystore).
CERTIFICATE_EXPIRATION_NOTIFICATION |  \<number>   |  Certificate expiration notification
GENERAL_CUSTOM_LOCATION_ENABLED     |  \<string>   |  Flag which indicates the use of a custom log location.
GENERAL_CUSTOM_LOCATION_PATH        |  \<string>   |  Path towards the Flow Central's log directory.
MONGODB_ADMIN_USER                  |  \<string>   |  MongoDB's admin user.
MONGODB_ADMIN_PASS                  |  \<string>   |  MongoDB's admin user password.
MONGODB_APPLICATION_DATABASE        |  \<string>   |  MongoDB's application database.
MONGODB_APPLICATION_USER            |  \<string>   |  MongoDB's application user.
MONGODB_APPLICATION_PASS            |  \<string>   |  MongoDB's application user's password.


## Flow Central service operations

#### 1. Create and start the Flow Central service

From the folder where the `docker-compose.yml` file is located, run the command:

```console
docker-compose up
```

The `up` command pulls the Flow Central image from DockerHub, recreates, starts, and attaches to a container for services.
Unless they are already running, this command also starts any linked services.

You can use the `-d` option to run containers in the background.

```console
docker-compose up -d
```

You can use the `-V` option to recreate anonymous volumes instead of retrieving data from the previous containers.

```console
docker-compose up -V
```

Run the docker `ps` command to see the running containers.

```console
docker ps
```

#### 2. Stop and remove the Flow Central service

From the folder where the `docker-compose.yml` file is located, you can stop the containers using the command:

```console
docker-compose down
```

The `down` command stops containers, and removes containers, networks, anonymous volumes, and images created by `up`.
You can use the `-v` option to remove named volumes declared in the `volumes` section of the Compose file, and anonymous volumes attached to containers.

***BE CAREFULL*** When using the Flow Central Docker image in production, the following command `docker-compose down -v` will remove any persistent data associated with your Flow Central containers.
***It will essentially result in a clean slate for your Flow Central services.***

#### 3. Start the Flow Central service

From the folder where the `docker-compose.yml` file is located, you can start the Flow Central service using `start` if it was stopped using `stop`.

```console
docker-compose start
```

#### 4. Stop Flow Central service

From the folder where the `docker-compose.yml` file is located, you can stop the containers using the command:

```console
docker-compose stop
```

## Security notices

`*Note*` The image axway/mongodb is using the official mongo:3.4 image on top of it we added some scripts specific to the application `we are not managing the mongo:3.4 image`

`*Note*` The connection between Flow Central and MongoDB is plain text and does not support `SSL` at the moment but this is mitigated by running both images on the same host.