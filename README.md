Quick Start Guide for Installing and Using ownCloud
===================================================
# Preface
This quick start guide helps you to install, perform configuration and user management tasks for ownCloud server. This includes configuring ownCloud components, creating user accounts and enabling users to connect to ownCloud server.

# Overview
ownCloud is a one stop solution for all your content collaboration needs. It greatly helps you to share and synchronize data and work on it whenever and wherever required irrespective of your device and its operating system. It is more flexible and secure making it more reliable and safe.

### Key Advantages
* Open source file synchronization and sharing
* Easily configurable and manageable
* Supports clients with wide range of operating systems
* Highly scalable
* Available in different Editions serving different purposes

|Name of the Edition       |Type|Support Options                |Purpose                                                        |
|--------------------------|----|-------------------------------|---------------------------------------------------------------|
|Free Community Edition    |Free|community support              |Core Server for all editions                                   |
|Standard Subscription     |Paid|Support for core server        |Provides support for the core Server                           |
|Enterprise Subscription   |Paid|Support for Enterprise Edition|Provides support for the core Server and enterprise applications|

# Installing ownCloud Server using the Docker Image

Docker is an application that simplifies the process of managing application processes in containers. Containers let you run your applications in resource-isolated processes. They’re similar to virtual machines, but containers are more portable, more resource-friendly, and more dependent on the host operating system.

Before installing Docker Engine on a host machine, set up the Docker prerequisite packages which let apt use packages over HTTPS using the following commands:
```bash
user@systems $ sudo apt-get install \
apt-transport-https \
ca-certificates \
curl \
gnupg \
lsb-release
```
1. Adding official Docker GPG trust key
    1. Apt-get package management uses public-key cryptography to authenticate and download packages. Public key cryptography is based on pairs of keys- a public key can be shared whereas a private key is kept a secret.  `apt-key` is a program that is used to manage a keyring of gpg key for secure apt.
       
         Add trusted key by running the following shell commands to trust the repository
       ```bash
       curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
       ```
    1. To validate the key available at `/etc/apt/trusted.gpg.d/` run the below command to check whether the Docker Key id is displayed.
       ```bash
       sudo apt-get list
       ```
1. Add Repository URL to source respository list by the following command:
    ```bash
      sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"
    ```

1. By running the command `sudo apt-get update` you can confirm that the your list of packages from all repositories and PPA's are up to date. If you don't run this command, you may be getting older versions of packages.
1. Install the latest version of Docker community edition along with its dependencies.
    ```bash
    sudo apt-get install docker-ce
    ```
   On successful execution, 5:20.10.5~3-0~ubuntu-focal package will be installed on your system.
1. Use the following command to verify whether the Docker is installed, the daemon has started, and the process is enabled to start on boot.
    ```bash
    sudo systemctl status docker
    ```
   The output should be similar to the following, showing that the service is active and running:
    ```bash
        ● docker.service - Docker Application Container Engine
        Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
        Drop-In: /etc/systemd/system/docker.service.d
        └─options.conf
        Active: active (running) since Sat 2021-03-20 14:06:27 IST; 2h 21min ago
        TriggeredBy: ● docker.socket
        Docs: https://docs.docker.com
        Main PID: 13272 (dockerd)
        Tasks: 28
        Memory: 2.0G
        CGroup: /system.slice/docker.service
        ├─13272 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
    ```
1. Docker commands can also be executed without prefixing sudo or being within the **docker** group by adding current user into docker group.
    ```bash
      sudo usermod -aG ${USER} docker
    ```
   Reactivate the profile either by substituting user with `su - ${USER}` or re-initate the profile by logging into the system.

## Installing Docker-Compose

Docker Compose is yet another useful Docker tool. It allows you to launch, execute, communicate, and close containers in a single coordinated command. Docker Compose is used for defining and running multi-container Docker applications.

1. Use curl to download the docker-compose file into the /usr/local/bin directory
    ```bash
       sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
    ```
1. Once the download is complete, apply executable permissions to the file
    ```bash
       sudo chmod +x /usr/local/bin/docker-compose
    ```
1. To verify whether the installation is successful, run the following command to print the compose version
    ```bash
      docker-compose --version
    ```
## Installing ownCloud Server by Docker

In this section, we’ll use Docker Compose to build a multi-container Own-cloud application stack.

1. The first step is to create a project directory under the user's home directory. User's home directory can be identified by bash environment variable. You can change the base directory to any path as required. Ensure you have read, write and access permissions to the directory.
    ```bash
    mkdir -p $HOME/own-cloud-server
    ```
1. Navigate to the above created directory and download the docker-compose file from owncloud-docker repositories using the following command.
    ```bash
   cd $HOME/own-cloud-server
   wget -q https://raw.githubusercontent.com/owncloud/docs/master/modules/admin_manual/examples/installation/docker/docker-compose.yml
    ```
1. Create Environment configuration file to pass variables to docker-compose file downloaded in the above step. Ensure you have provided appropriate file permissions for the file created below. This file will store administrative login credentials for the Owncloud server.
   ```bash
   cat << EOF > .env
   OWNCLOUD_VERSION=10.6
   OWNCLOUD_DOMAIN=localhost
   ADMIN_USERNAME=admin
   ADMIN_PASSWORD=admin
   HTTP_PORT=8080
   EOF
   ```
   Ensure you are pointing to current OWNCLOUD_VERSION before updating the configuration file by referring to [Owncloud Docker Hub Repository](https://hub.docker.com/r/owncloud/server).
   
   * Assuming 8080 port is allowed in the given network range and also at the host operating system's firewall, verify the created environment file and validate whether `HTTP_PORT` is configured to 8080.
   * If you wish to change it to any other port, update accordingly in the environment file and re-provision the application.
   * To check whether the port is exposed when the application is running, navigate to the location where the docker-compose file is downloaded, for example - `$HOME/owncloud-server` and execute the command `docker-compose ps` from the directory. 

1. From the project directory, start the Owncloud application by running the following command:
   ```bash
      docker-compose up -d
   ```
   Compose will pull the images and start the containers. You can also start the docker compose in a detached mode by passing the -d option
    1. To Stop containers if not detached from the console click `Ctrl+C`.
    1. If containers are running in detached mode, go to the directory where your compose file is present and execute the command `docker-compose stop`

1. In a web browser type-in the url `http://localhost:8080/` and click Enter. The OwnCloud server login screen appears.

## Accessing ownCloud server UI from Client Machine
1. In a web browser that has network access to the ownCloud Server, type in `<ip address of the ownCloud Server>:8080` in the toolbar and click **Enter**.
   You are navigated to the **ownCloud ** application login page.
1. In the **Username or email** and **Password** fields, provide the user login credentials and click Enter.

You’re now successfully logged in to the ownCloud server’s web UI and can perform the required operations.
# Managing User Accounts in ownCloud Server
In the ownCloud server UI,
1. Navigate to the **Personal settings** menu on the top right corner of the screen beside to the Search field and click on the admin user drop-down menu. The menu displays the following settings:
    1. Settings
    1. Users
    1. Logout

1. Click **Users**

   The Users screen appears allowing you to create new user accounts and groups. The Admin user is the default user of the ownCloud server and you can see the following details already configured for the admin user

    1. Username
    1. Full Name
    1. Password
    1. Groups – admin group
    1. Group Admin for – Name of the group created by admin
    1. Quota – Default

1. To Create a new user account,
    1. In the **Username** and **E-Mail** fields enter the respective details.
    1. Click the admin drop-down menu to select the required group for the newly created user.
    1. Click **Create**.

   The new user account is created and the details of the newly created user gets added to the Users table.

1. Click the **Passwor**d field to edit the password of the newly created user.
      Note – You cannot edit the **Username**.
# Accessing ownCloud Server through Desktop Client
   To connect to the ownCloud Server using a desktop client, you have to first download and install the ownCloud Desktop client packages.  
### Installing Desktop Client
   To download and install the ownCloud Desktop client packages,
1. From the [Download Desktop App](https://owncloud.com/desktop-app/) page, select your operating system and click **Download**.
   Based on your operating system, you are prompted to save the respective installer file to your system.
1. Click **Save**.  
   The Desktop Application files are downloaded to your system.
   For Example, if you have selected Windows as your operating system,
1. Double-click the **ownCloud-<latest_version>.<x32orx64>.msi** file to run the installer.  
   The Welcome screen of the ownCloud Setup Wizard appears:
1. Click **Next**
1. In the Custom Setup screen, the default features and the location where the setup files are placed are displayed. Click **Browse**, to change the default location.
1. Click **Next**
1. In the **Ready to install ownCloud** screen, click **Install**  The setup wizard installs the ownCloud Desktop client packages.
1. Once the setup is done, click **Finish**   
   The ownCloud Desktop client is successfully installed in your system. Restart your system for the changes to take effect.

### To connect to the ownCloud Server using you Desktop client
1. From the **Start** -> **Applications menu** –> select **ownCloud**

   The **ownCloud Connection Wizard** appears allowing you to configure the necessary connection details:
1. In the **Server Address** text box, provide the ip address of the ownCloud server.
1. Click **Next**.
1. Enter the login credentials of the ownCloud user in the **Username** and **Password** fields.
1. Click **Next**.
1. Select the necessary Server options. **Use virtual files instead of downloading content immediately** option is selected by default.
1. To change the location of the default folder, click the displayed folder location.
1. Click **Connect**

All the virtual files are successfully synchronized and a confirmation message is displayed.

You have successfully configured the desktop client for the Owncloud Server and connected to it to proceed with your further file sharing or synchronization operations.
