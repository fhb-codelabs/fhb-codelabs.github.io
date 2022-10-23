summary: BITI IPM Lab - Icinga Installation
id: biti-ipm-icinga-installation-lab
categories: icinga
tags: ipm, icinga, BITI, introduction
status: Draft
authors: Roland Pellegrini

# BITI IPM Lab - Icinga Installation
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn

In this codelab you will learn

* how to install Icinga2 on Debian 11 
* how to configure Icinga2 as a single node

###  Where You Can Look Up

The best source of documentation is the homepage of Icinga2. The latest documentation can be found [here](https://icinga.com/docs/icinga-2/latest/doc/01-about/).

### What You'll need

#### Guest operation system (Guest OS)

This is the OS of the virtual machine. This will be Debian 11 (Bullseye).

#### Administators privileges

By default, administrator privileges are required on the Host OS to install additional software. Make sure that you have the required permissions.

For the Guest OS, you will create and manage your own users. These users will therefore be different from the Host's user administration. 

### Root privileges via sudo

In this codelab you have to work with root privileges. Therefore, a few words of caution: double check whatever you type and make backups whenever necessary.

Working with root privileges is quite easy. Open a terminal (a shell) and enter the following commmand:
```
sudo -s
```
Enter the password of the icinga user and voila:
```
root@server:/home/icinga#
```

Once you are root via sudo, it is no longer necessary to prepend the sudo command. Instead of `sudo ls -lisa /root/` you can also type `ls -lisa /root/` because you have root privileges already. However, all commands in this codelab will always start with `sudo` to remind you that you are working with root privileges.

<!-- ------------------------ -->

## System update

### Description

Run the following commands to update your system package repositories.

```
sudo apt update
sudo apt upgrade
```
<aside class="positive">
When using the sudo command, you will be prompted for your password.
</aside>

Depending on the package upgrades, it is useful to restart the system here.

<!-- ------------------------ -->

## Icinga2 Application

### Core App
Now that Icinga 2 repos are in place, you can install it by running the command below:

```
sudo apt install icinga2
```

Durning the installation, the apt command will output the following information.
```
--- more ---
enabling default icinga2 features
Enabling feature checker. Make sure to restart Icinga 2 for these changes to take effect.
Enabling feature notification. Make sure to restart Icinga 2 for these changes to take effect.
Enabling feature mainlog. Make sure to restart Icinga 2 for these changes to take effect.
--- more ---
```

<aside class="positive">
Please do not restart any Icinga2 service here. We will do this later. 
</aside>

On Debian, Icinga2 is started and enabled upon installation. You can check this by running the command;
```
systemctl status icinga2
```

The output of the service status should look like the following:
``` 
  icinga2.service - Icinga host/service/network monitoring system
     Loaded: loaded (/lib/systemd/system/icinga2.service; enabled; vendor preset: enabled)
     Active: active (running) since Wed 2021-10-16 22:05:17 CET; 35min ago
       Docs: https://icinga.com/docs/icinga2/latest/
    Process: 9826 ExecStartPre=/usr/lib/icinga2/prepare-dirs /usr/lib/icinga2/icinga2 (code=exited, status=0/SUCCESS)
   Main PID: 9831 (icinga2)
      Tasks: 18 (limit: 2324)
     Memory: 12.5M
        CPU: 3.207s
     CGroup: /system.slice/icinga2.service
             ├─9831 /usr/lib/x86_64-linux-gnu/icinga2/sbin/icinga2 --no-stack-rlimit daemon -e
             ├─9851 /usr/lib/x86_64-linux-gnu/icinga2/sbin/icinga2 --no-stack-rlimit daemon -e
             └─9856 /usr/lib/x86_64-linux-gnu/icinga2/sbin/icinga2 --no-stack-rlimit daemon -e

Oct 16 22:30:47 server icinga2[9851]: [2021-10-16 22:30:47 +0100] information/IdoMysqlConnection: Pending queries: 5 (Input: 3/s; Output: 3/s)
Oct 16 22:35:17 server icinga2[9851]: [2021-10-16 22:35:17 +0100] information/ConfigObject: Dumping program state to file '/var/lib/icinga2/icinga2.state'
Oct 16 22:35:37 server icinga2[9851]: [2021-10-16 22:35:37 +0100] information/WorkQueue: #6 (ApiListener, RelayQueue) items: 0, rate:  0/s (0/min 0/5min 0/15min);
Oct 16 22:35:37 server icinga2[9851]: [2021-10-16 22:35:37 +0100] information/WorkQueue: #7 (ApiListener, SyncQueue) items: 0, rate:  0/s (0/min 0/5min 0/15min);
Oct 16 22:35:47 server icinga2[9851]: [2021-10-16 22:35:47 +0100] information/IdoMysqlConnection: Pending queries: 0 (Input: 3/s; Output: 3/s)
Oct 16 22:39:07 server icinga2[9851]: [2021-10-16 22:39:07 +0100] information/IdoMysqlConnection: Pending queries: 11 (Input: 3/s; Output: 2/s)
Oct 16 22:40:17 server icinga2[9851]: [2021-10-16 22:40:17 +0100] information/ConfigObject: Dumping program state to file '/var/lib/icinga2/icinga2.state'
Oct 16 22:40:37 server icinga2[9851]: [2021-10-16 22:40:37 +0100] information/WorkQueue: #6 (ApiListener, RelayQueue) items: 0, rate:  0/s (0/min 0/5min 0/15min);
Oct 16 22:40:37 server icinga2[9851]: [2021-10-16 22:40:37 +0100] information/WorkQueue: #7 (ApiListener, SyncQueue) items: 0, rate:  0/s (0/min 0/5min 0/15min);
Oct 16 22:40:47 server icinga2[9851]: [2021-10-16 22:40:47 +0100] information/IdoMysqlConnection: Pending queries: 0 (Input: 3/s; Output: 4/s)
```

Check if the status Active is in **running** mode which indicates that the service is up and running.

### Monitoring Plugins

To  check external services, Icinga 2 requires additional monitoring plugins.  Run the command below to install the plugins.

```
sudo apt install monitoring-plugins
```

<aside class="positive">
The command above will install a lot of Nagios-plugins. 
</aside>

## Icinga2 Backend

### Database installation

At the time, Debian repository contains MariaDB 10.5, which causes problems during installation.

To fix this issue, follow the next steps to install MariaDB 10.6 on your latest Debian Servers.

```
sudo apt install software-properties-common dirmngr
```

Run the below commands respectively to import MariaDB signing key and add MariaDB APT repository.

```
sudo apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
sudo add-apt-repository 'deb [arch=amd64,arm64,ppc64el] https://mariadb.mirror.liquidtelecom.com/repo/10.6/debian bullseye main'
```

Next, update packages and install MariaDB server.

```
sudo apt update
sudo apt install mariadb-server mariadb-client
```

Proceed to install MariaDB packages and all its dependencies.

Finally, start and enable MariaDB.
```
sudo systemctl start mariadb
sudo systemctl enable mariadb
```


You can check the status of the MariaDB service with the following command:

```
sudo systemctl status mariadb

```

The output should look like this:
```
root@server:~# systemctl status mariadb
● mariadb.service - MariaDB 10.6.4 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/mariadb.service.d
             └─migrated-from-my.cnf-settings.conf
     Active: active (running) since Sat 2021-10-16 21:43:23 CEST; 34s ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
    Process: 25828 ExecStartPre=/usr/bin/install -m 755 -o mysql -g root -d /var/run/mysqld (code=>
    Process: 25829 ExecStartPre=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (code>
    Process: 25832 ExecStartPre=/bin/sh -c [ ! -e /usr/bin/galera_recovery ] && VAR= ||   VAR=`cd >
    Process: 25895 ExecStartPost=/bin/sh -c systemctl unset-environment _WSREP_START_POSITION (cod>
    Process: 25897 ExecStartPost=/etc/mysql/debian-start (code=exited, status=0/SUCCESS)
   Main PID: 25881 (mariadbd)
     Status: "Taking your SQL requests now..."
      Tasks: 12 (limit: 4658)
     Memory: 72.0M
        CPU: 1.103s
     CGroup: /system.slice/mariadb.service
             └─25881 /usr/sbin/mariadbd

Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: Phase 6/7: Checking and upgrading tables
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: Processing databases
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: information_schema
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: performance_schema
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: sys
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: sys.sys_config                             >
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: Phase 7/7: Running 'FLUSH PRIVILEGES'
Oct 16 22:43:29 server /etc/mysql/debian-start[25902]: OK
Oct 16 22:43:29 server /etc/mysql/debian-start[26501]: Checking for insecure root accounts.
```

### IDO modules for MariaDB/MySQL 

The package `icinga2-ido-mysql` provides IDO modules for MySQL and can be installed by running the command below:

```
sudo apt install icinga2-ido-mysql
```
During installation, you are prompted to specify whether Icinga 2 should use MySQL. Select `Yes` to enable this feature. 
![Windows Registry](./img/biti-ipm-icinga-installation-1.png)

Then you are prompted to specify whether database should be configured with dbconfig-common. Select `Yes` to enable dbconfig-common.
![Windows Registry](./img/biti-ipm-icinga-installation-2.png)

<aside class="positive">
Dbconfig-common is not completely uncircumvented. This framework presents a policy and implementation for managing various databases used by applications included in Debian packages.
<br>
<br>
Some database administrators simply don't like the dbconfig-common framework. 
<br>
<br>
However, we choose this option because we don't want to administrate a database. Instead, we just want to use it for demonstration purpose only.
</aside>

Next,  you will be prompt to provide MySQL application password for icinga2-ido-mysql as shown in the following screen. To keep things easy, use `icinga2` as password.

<aside class="negative">
Warning: 
<br>
Under no circumstances do not use this password in other systems or environments, especially not in a production system. We use this password to ensure a smooth installation process during the lecture and to eliminate potential sources of error.
</aside>

![Windows Registry](./img/biti-ipm-icinga-installation-3.png)


You have to repeat the password. Type `icinga2` and hit **ENTER**.

![Windows Registry](./img/biti-ipm-icinga-installation-4.png)


Finally, you have check if `ido-mysql` feature is enabled. For this, run the command:

```
sudo icinga2 feature list
```

The command above may show up the following output:
```
Disabled features: api command compatlog debuglog elasticsearch gelf graphite icingadb ido-mysql influxdb livestatus opentsdb perfdata statusdata syslog

Enabled features: checker mainlog notification
```

As shown in the output, only the features `checker mainlog notification` are enabled whereas the feature `ido-mysql` is disabled.

Enable it with the following command:

```
sudo icinga2 feature enable ido-mysql
```

Check, if `ido-mysql` feature is enabled now. Run the command:
```
sudo icinga2 feature list
```

The command should show up the following output:
```
Disabled features: api command compatlog debuglog elasticsearch gelf graphite icingadb influxdb livestatus opentsdb perfdata statusdata syslog

Enabled features: checker ido-mysql mainlog notification
```

Great, you are fine here. Now restart Icinga 2.
```
sudo systemctl restart icinga2
```

<!-- ------------------------ -->

## IDO Configfile

Next, open Icinga 2 MySQL IDO configuration file and set the Icinga2 database connection details.
```
sudo nano /etc/icinga2/features-available/ido-mysql.conf
```

The configuration **must** look like this:
```
/**
 * The db_ido_mysql library implements IDO functionality
 * for MySQL.
 */

library "db_ido_mysql"

object IdoMysqlConnection "ido-mysql" {
  user = "icinga2",
  password = "icinga2",
  host = "localhost",
  database = "icinga2"
}
```
If your configfile looks different, set the Icinga2 database connection details manually.

Quit the configuration file with `CTRL-X` (and confirm with `y` if you changed something in the configfile). 

## Icinga2 Restart

### One-liner

If you have modified `/etc/icinga2/features-available/ido-mysql.conf` then restart the Icinga2 service with the following command:

```
sudo systemctl restart icinga2
```

<aside class="positive">
Please take into consideration that you will restart icinga2 more than once. Icinga2 uses configuration files which are only read during a restart. 
</aside>

## Icinga Web2 Application

### Installation
Icinga Web 2 requires Icinga 2 with IDO configured plus some additional requirements include a web server, PHP and some extensions. Run the command below to install these requirements before you can proceed.

```
sudo apt install icingaweb2
```

### Authentication Token

Icinga web setup requires authentication using tokens. To generate the authentication token, run the command below:

```
sudo icingacli setup token create 
```

This will generate such a token as follows:
```                                    
The newly generated setup token is: **7fb3fb0cbae252b3**
```

You can always display the taken using the command:
```
sudo icingacli setup token show
```

<aside class="positive">
You will need this token later for the frontend wizard.
</aside>

Next, ensure that the icingaweb2 system group exists and that the web server user, www-data, is a member of the group.

```                                    
sudo id www-data
```

This will dispaly a message as follows:

```                                    
uid=33(www-data) gid=33(www-data) groups=33(www-data),117(icingaweb2)
```
 
### Restart Apache Web Server

Make sure that the web server accepts all changes.
```
sudo systemctl restart apache2
```
## Icinga Web2 Backend

### Database installation

Sorry but true: you have to create a database and a database user for Icinga web 2 manually. 

First, log in to MariaDB shell with the following command:

```
sudo mysql -u root -p
```

Provide your root password (or simple press enter) and create a database and user for Icinga web 2 with the following command:

```
create database icingaweb2;
```


MariaDB/MySQL answers with:
```
Query OK, 1 row affected (0.001 sec)
```

<aside class="positive">
Some people may ask why the name of the database does not end with db (e.g. icingaweb2db). I have been trying to find an answer as to why I should name a database with the extension db, just to indicate that it really is a database. With no success.
</aside>

Create Icinga 2 database user. Remember, we want to keep things easy.
```
grant all on icingaweb2.* to icingaweb2@localhost identified by 'icingaweb2';
```

MariaDB/MySQL answers with:
```
Query OK, o row affected (0.001 sec)
```


<aside class="negative">
Warning: 
<br>
Under no circumstances do not use this password in other systems or environments, especially not in a production system. We use this password to ensure a smooth installation process during the lecture and to eliminate potential sources of error.
</aside>

Reload privileges tables with the following commands:
```
flush privileges;
quit
```


## Icinga Web2 Frontend

### Setup Wizard

<aside class="positive">
The Icingaweb2 page can be opened either inside the GuestOS or outside the HostOS with the network bridge enabled. It is recommended to open the Icingaweb2 page from the HostOS.
</aside>

To access the setup wizard, use the address, http://icinga-server-ip-address/icingaweb2/setup:

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-1.png)

Enter your authentication token and click `Next` to proceed. If you do not know the authentication token, run the following command again:

```
sudo icingacli setup token show
```

On the next page, select Icinga modules to enable.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-2.png)

Ensure that Monitoring module is activated only.

The next page verifies if the required PHP extensions are met. If there are any missing PHP extensions, install them and proceed with setup.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-3.png)

Only PHP extensions for PostgreSQL are missing. That is okay because we use MySQL/MariaDB. All the otherPHP modules are available. Good !

Next, configure Icinga Web 2 authentication method. This codelab uses local authentication hence, selecting Database as the type of authentication.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-4.png)

Configure database authentication details. Click `Validate` to test connection to DB.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-5.png)

Where

* Database Name = icingaweb2
* Usermame = icingaweb2
* Password = icingaweb2

You should see a positive feedback, saying that the configuration bas been successfully validated.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-6.png)

If you do not get this message but an error message, check if you have mistyped or are using different users/passwords than specified.

Set the Icinga web 2 authentication backend name and click `Next`.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-7.png)

Where
* Backend Name = icingaweb2

Setup Icinga Web 2 administrative user. Note, these are the authentication details for Icinga Web interface.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-8.png)

Where
* Username = icinga2
* Password = icinga2
* RepeatPw = icinga2

<aside class="negative">
Warning: 
<br>
Under no circumstances do not use this password in other systems or environments, especially not in a production system. We use this password to ensure a smooth installation process during the lecture and to eliminate potential sources of error.
</aside>

Configure application and logging related options and click `Next`.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-9.png)

Where
* Show Stacktraces = yes
* Show Application State Messages = yes
* User Preference Storage Type = Database
* Logging Type = Syslog
* Logging Level = Error
* Application Prefix = icingaweb2
* Facility = user

Icinga Web 2 configuration summary.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-10.png)

On the next screen, click `Next` to configure Icinga Web 2 monitoring (IDO) backend.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-11.png)

Configure Backend details for the IDO database set in the previous section.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-12.png)

Where,
* Backend Name = icinga2
* Backend Type = IDO

<aside class="positive">
If you get the error, “There is currently no icinga instance writing to the IDO. Make sure that a icinga instance is configured and able to write to the IDO“, it means that the ido-mysql is not enabled. Enable it and restart Icinga 2 and proceed.
</aside>

Configure authentication details for the IDO database set in the previous section.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-13.png)

Where,
* Database Name = icinga2
* Username = icinga2
* Password = icinga2

You should see a positive feedback, saying that the configuration bas been successfully validated.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-14.png)

If you do not get this message but an error message, check if you have mistyped or are using different users/passwords than specified.

Configure Icinga Transport commands. In this codelab, we are setting up `Local Command File` transport type.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-15.png)

Where,

* Transport name = icinga2
* Transport type = LOcal Command File
* Command File = /var/run/icinga2/cmd/icinga2.cmd


Next, define your custom variables to protect.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-16.png)

Where,
* Protected Custom Variables = *pw*,*pass*,community

Review configuration summary and click `Finish` to complete the installation.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-17.png)

Congratulations, you have installed and set up Icinga2 successfully. Click on `Login to Icinga Web 2`

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-18.png)

## Icinga Web2 Login

Enter the username and password from the previous section.

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-19.png)

If URL is unknown, try this: 

```
http://<icinga-server-IP>/icingaweb2/
```

After logging in, Icinga2 will greet you with a critical message (at least in this screenshot)

![Icinga Web 2 Wizard](./img/biti-ipm-icinga-installation-wizard-20.png)


Congratulations! You have successfully installed Icinga2 and Icingaweb2 on your GuestOS. You can now browse through the sidebar menu to discover the features of Icinga2. Familiarize yourself with the menu navigation.