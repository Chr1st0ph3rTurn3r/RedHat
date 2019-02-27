# RedHat
Writing test specifically for ownCloud Quickstart document

#QuickStart 

#ownCloud Server Installation Using Docker

Welcome to ownCloud! This QuickStart is intended for administrators who are familiar with ownCloud and want to install ownCloud Server and configure user access quickly. 
##System Requirements

ownCloud server has the following System Requirements:

Operating System

*    Ubuntu 16.04 and 18.04
*    Debian 7, 8 and 9;
*    SUSE Linux Enterprise Server 12 with SP1, SP2 and SP3
*    Red Hat Enterprise Linux/Centos 6.9, 7.3, 7.4, and 7.5
*    Fedora 27, 28 and 29
*    openSUSE Leap 42.3 and 15

Database
	
*    MySQL or MariaDB 5.5+
*    Oracle 11g
*    PostgreSQL 9 (versions 10 and above are not yet supported)
*    SQLite

Web server

*    Apache 2.4 with prefork and mod_php

PHP Runtime

*    5.6, 7.0, 7.1, and 7.2

For a full list of supported platforms and additional information, please refer to the ownCloud System Requirements.

##Installation

There are several options to install ownCloud Server.
 
*    Using Docker 
*    Manually on Linux
*    Scripted or manually using the Command Line (Linux)
*    Using the Linux Package Manager (for single server installation, and not recommended for production environments).
*    Using the Installation Wizard (not a secure installation method) 

This guide focuses on installations using Docker. For information about other installation options, refer to the ownCloud Administrator Guide.

###Installation with Docker on a Local Machine

The quickest way to install and configure ownCloud is using Docker from the official ownCloud Docker image. This image is designed to work with a data volume in the host filesystem as well as with separate MariaDB and Redis containers. The installation and configuration will:

*    expose port 8080, allowing for HTTP connections.
*    mount the data and MySQL data directories on the host for persistent storage.

To install: 

1.	Create a new project directory on the local machine.
2.	Download docker-compose.yml from the ownCloud Docker GitHub repository into the directory.
3.	Create an .env configuration file with the following required configuration settings: 

<table style="border-collapse: separate;" width="50%" cellspacing="0" 
		 border="1">
	<col style="width: 35%;" />
	<col style="width: 30;" />
	<col style="width: 30%;" />
    <tr>
        <td>Setting Name</td>
        <td>Description</td>
        <td>Example</td>
    </tr>
    <tr>
        <td>OWNCLOUD_VERSION</td>
        <td>The ownCloud version</td>
        <td>latest</td>
    </tr>
    <tr>
        <td>OWNCLOUD_DOMAIN</td>
        <td>The ownCloud domain</td>
        <td>localhost</td>
    </tr>
    <tr>
        <td>ADMIN_USERNAME</td>
        <td>The admin username</td>
        <td>admin</td>
    </tr>
    <tr>
        <td>ADMIN_PASSWORD</td>
        <td>The admin password</td>
        <td>admin</td>
    </tr>
    <tr>
        <td>HTTP_PORT</td>
        <td>The HTTP port to bind to</td>
        <td>8080</td>
    </tr>
</table>

The admin username and password provide administrator access to the ownCloud UI, and allow the configuration of user accounts and groups. 

The Docker installation configures the IP address/domain and port based on the information entered into the environment configuration file. The URL can be changed by editing the config file. See Changing the ownCloud URL for more information. 

4.	Start the container, using a Docker command-line tool. The sample .env file below was created using Docker Compose and the information above. 

<pre><code>
    Create a new project directory
    mkdir owncloud-docker-server
    cd owncloud-docker-server
    Copy docker-compose.yml from the GitHub repository
    wget https://raw.githubusercontent.com/owncloud-docker/server/master/docker-compose.yml
    Create the environment configuration file
    cat << EOF > .env
    OWNCLOUD_VERSION=10.0
    OWNCLOUD_DOMAIN=localhost
    ADMIN_USERNAME=admin
    ADMIN_PASSWORD=admin
    HTTP_PORT=8080
    EOF
    Build and start the container
    docker-compose up -d
</pre></code>


5.	Run docker-compose ps to verify that all containers have started successfully, as indicated by the State column in the output below.  

<table style="border-collapse: separate;" width="70%" cellspacing="0" 
		 border="1">
	<col style="width: 25%;" />
	<col style="width: 35%;" />
	<col style="width: 15%;" />
        <col style="width: 30%;" />
    <tr>
        <td>Name</td>
        <td>Command</td>
        <td>State</td>
        <td>Ports</td>
    </tr>
    <tr>
        <td>server_db_1</td>
        <td>/usr/bin/entrypoint/bin/s ...</td>
        <td>Up</td>
        <td>3306/tcp</td>
    </tr>
    <tr>
        <td>server_owncloud_1</td>
        <td>/usr/local/bin/entrypoint ...</td>
        <td>Up</td>
        <td>0.0.0.0:8080->8080/tcp</td>
    </tr>
    <tr>
        <td>server_redis_1</td>
        <td>/bin/s6-svscan /etc/s6</td>
        <td>Up</td>
        <td>6379/tcp</td>
    </tr>
</table>
When the database, ownCloud, and Redis containers are running, ownCloud is accessible via port 8080 on the host machine.

Note that it takes a few minutes for ownCloud to be fully functional. Run docker-compose logs --follow owncloud to view the logging to the console. If there is a significant amount of activity, wait until it slows down to attempt to access the web UI.

6.	Log in to the ownCloud UI using http://localhost:8080 in a browser.
7.	The ownCloud login screen appears.
8.	Enter the Admin username and password defined in the .env configuration file earlier. 

##Changing the ownCloud URL

To change the ownCloud URL on Debian/Ubuntu Linux, edit these files:

    /etc/apache2/sites-enabled/owncloud.conf
    /var/www/owncloud/config/config.php

1.	Edit the Alias directive in /etc/apache2/sites-enabled/owncloud.conf to alias the ownCloud directory to the Web server root:

    Alias / "/var/www/owncloud/"

2.	Edit the overwrite.cli.url parameter in /var/www/owncloud/config/config.php:

    'overwrite.cli.url' => 'http://localhost/',

3.	Save the file and restart Apache. ownCloud can accessed via the URL specified. 

Because ownCloud is aliased to the web root, other virtual hosts will not run. To change this on CentOS/Fedora/Red Hat edit the following files and restart Apache.

    /etc/httpd/conf.d/owncloud.conf
    /var/www/html/owncloud/config/config.php

##Creating a New User

Create new users from the User Management page of the ownCloud Web UI. Login names may contain letters (a-z, A-Z), numbers (0-9), dashes (-), underscores (_), periods (.) and at signs (@). After the user has been created, their Full Name can be added if it is different than the login name, or leave it for the user to complete.

1.	In the default view, enter a new user Login Name. 
2.	Enter an initial Password (cannot be "0").
3.	Optionally, assign Groups memberships.
4.	Click the Create button.

To send an email to a new user, select *Send email to new user* in the control panel on the lower left sidebar. Enter the new user’s email address, and ownCloud will automatically send them a notification with their new login information. The email content can be edited using the email template editor on the Admin page (see Email Configuration).

User accounts have the following properties:

**Login Name (Username)**

The unique ID of an ownCloud user. This cannot be changed.

**Full Name**

The user’s display name that appears on file shares, the ownCloud Web interface, and emails. Admins and users may change the Full Name anytime. If the Full Name is not set, it defaults to the login name.

**Password**

The admin sets the new user’s first password. Both the user and the admin can change the user’s password at anytime.

**Groups**

Create groups and assign group memberships to users. By default new users are not assigned to any groups.

**Group Admin**

Group admins are granted administrative privileges on specific groups, and can add and remove users from their groups.

**Quota**

The maximum disk space assigned to each user. Any user that exceeds the quota cannot upload or sync data. There is an option to include external storage in user quotas.

For additional information regarding user management, refer to the User Configuration section of the ownCloud Administrator documentation. 

##Users Connecting to ownCloud

The ownCloud Administrator configures usernames and intial passwords, and an email is sent to each user containing the ownCloud URL. 

1.	Open a browser.
2.	Enter http: //localhost.owncloud.com in the address bar.
3.	When the ownCloud login screen appears, enter a user name and password. This information was provided in a welcome email from the adminstrator. 
4.	Click the Login arrow, or press Enter on the keyboard. 
5.	The ownCloud User Interface appears. 

###Connecting via a Mobile Device

To connect to your ownCloud server with the ownCloud mobile apps, use the base URL and folder only:

    example.com/owncloud

1.	When the ownCloud login screen appears, enter a user name and password. This information was provided in a welcome email from the adminstrator. 
2.	Click or tap Login. 
3.	The ownCloud User Interface appears.

For detailed information about using ownCloud, refer to the ownCloud User Manual. 
