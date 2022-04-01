summary: BITI IPM Lab - Icinga Custom Check
id: biti-ipm-icinga-custom-check-lab
categories: icinga
tags: ipm, icinga, BITI, introduction
status: Draft
authors: Roland Pellegrini

# BITI IPM Lab - Icinga Custom Check
<!-- ------------------------ -->
## Before You Begin 

### What You’ll Learn


Icinga2 comes with a bunch of pre-installed check_commands. However, there are situations where Icinga2 Administrators need to implement custom check_commands.

In this codelab you will learn

* how to create and implement a custom check_command to monitor the memory usage of our GuestOS.

###  Where You Can Look Up

The best source of documentation is the homepage of Icinga2. The latest documentation can be found [here](https://icinga.com/docs/icinga-2/latest/doc/01-about/)).

### What You'll need

#### Icinga2 instance

You need a working Icinga2 instance that you can access via IcingaWeb2 GUI. If you do not have a running Icinga2 instance, please consult the Codelab **BITI IPM Lab - Icinga Installation** and **BITI IPM Lab - Icinga Agentless Monitoring**. 

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


## Get the plugin

Indeed, there are many, many different check_mem plugins for Icinga2. However, we want to install and use a revised version of check_mem.pl which splits the memory detection into cache memory and application memory.

First, we need to change to the plugin directory:

```
sudo cd /usr/lib/nagios/plugins
```
In this directory, you will find a set of check plugins, just run `sudo ls -al`. Unfortunately, you will not find a check_mem plugins here. Luckely, we can download a plugins named `check_mem.pl` by running the following command:

```
sudo wget https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl
```

The command `wget` is a command line utility from the GNU project for downloading files from the Internet. You should see the following output:
```
--2021-12-03 22:45:11--  https://raw.githubusercontent.com/justintime/nagios-plugins/master/check_mem/check_mem.pl
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.110.133, 185.199.108.133, 185.199.111.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.110.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 15019 (15K) [text/plain]
Saving to: ‘check_mem.pl.2’

check_mem.pl.2                        100%[======================================================================>]  14.67K  --.-KB/s    in 0s      

2021-12-03 22:45:12 (85.4 MB/s) - ‘check_mem.pl.2’ saved [15019/15019]
```

Make sure that the plugins is executable:
```
sudo chmod +x /usr/lib/nagios/plugins/check_mem.pl
```

You can test if the plugins works.
```
sudo /usr/lib/nagios/plugins/check_mem.pl -f -w 20 -c 10
```
Where,
* -f - tell the plugin to check for free memory
* -w - specify the warning threshold as number interpreted as percent
* -c - specify the critical threshold as number interpreted as percent

The output of this plugin can look like this:
```
WARNING - 14.1% (286656 kB) free!|TOTAL=2030328KB;;;; USED=1743672KB;1624262;1827295;; FREE=286656KB;;;; CACHES=817248KB;;;;
```

## Create the check

In order for this new custom plugin to be widely used in Icinga 2, add a new command definition in Icinga 2 command’s configuration file, as shown in the below example.

```
sudo cd /etc/icinga2/conf.d/
sudo nano /etc/icinga2/conf.d/commands.conf
```

Now, go to the bottom of this file and add the command definition as follows:

```
object CheckCommand "my_mem" {
  import "plugin-check-command"
 
  command = [ PluginDir + "/check_mem.pl" ]
 
  arguments = {
    "-f" = {
      description               = "Check FREE memory"
      required                  = false
      set_if                    = "$mem_free$"
    }
    "-u" = {
      description               = "Check USED memory"
      required                  = false
      set_if                    = "$mem_used$"
    }
    "-C" = {
      description               = "Count OS caches as FREE memory"
      required                  = false
      set_if                    = "$mem_cache$"
    }
    "-w" = {
      value                     = "$mem_warning$"
      description               = "Percent free/used when to warn"
      required                  = true
    }
    "-c" = {
      value                     = "$mem_critical$"
      description               = "Percent free/used when critical"
      required                  = true
    }
  }
  vars.mem_warning           = "30"
  vars.mem_critical          = "15"
}
```

This CheckCommand is pretty well self-explanatory. 
* First, each CheckCommand needs a name. We will simply call it `my_mem`. 
* Next, the CheckCommand imports the `plugin-check-command` which simply tells Icinga2 how to execute commands. 
* Next, the `command` tells Icinga2 where to find the `check_mem.pl` plugin (the PluginDir points to the **/usr/lib/nagios/plugins** directory, by the way). 
* Next, the argument section describes all parameters that are accepted by the plugin. In addition, the plugin accepts a number of input variables like **$mem_used$** or **$mem_critical$**. We will use them soon. 
* Finally, the defaults for mem_warning and mem_critical are set and defined.

Do not forget to quit the editor with `CTRL-X`. Confirm with `y` to save all changes. 

<aside class="positive">
Hint: Check on open brackets.
</aside>

## Implement the check

In order to use our new CheckCommand, we need to implement it for monitoring our GuestOS, as shown in the below example. 

```
sudo nano /etc/icinga2/conf.d/hosts/localhost.conf
```

Now, go to the bottom of this file and add the service definition as follows:
```
object Service "my_mem" {
    host_name = "localhost"
    check_command = "my_mem"
    vars.mem_warning = "20"
    vars.mem_critical = "10"
    vars.mem_used = "false"
    vars.mem_free = "true"
}
```
As you can see, we overwrite the thresholds for warning and critical. 

Finally, do not forget to quit the editor with `CTRL-X`. Confirm with `y` to save all changes. 

## Test and restart 
Okay, time to test the configuration. Run the command:
```
sudo icinga2 daemon -C
```

Do you see errors? If yes, try to fix them. Otherwise, if not errors occur, restart and check the Icinga2 service with the following commands:

```
sudo systemctl start icinga2
sudo systemctl status icinga2
```
Any errors? Try to fix them.

## In action

If the restart of the icinga2 service was successful, we will see our new CheckCommand named `my_mem`. Depending on the setup of your GuestOS, you may see different status messages. The screenshot below shows a warning message:

![Icinga Web 2](./img/biti-ipm-icinga-agentless-mem-1.png)

By clicking on the warning box, you can see more details of the warning.

![Icinga Web 2](./img/biti-ipm-icinga-agentless-mem-2.png)

## Cleanup

Congratulations !

You have successfully set up your first custom CheckCommand.