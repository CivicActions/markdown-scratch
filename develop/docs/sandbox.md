# Platform Sandbox Installation

_This page needs review_

Table of Contents
=================

* [Requirements](#requirements)
  * [Step 1 (Linux). Getting Started with Docker](#step-1-linux-getting-started-with-docker)
  * [Step 1 (OSX). Getting Started with Docker](#step-1-osx-getting-started-with-docker)
    * [1.1. Remove boot2docker from OSX](#11-remove-boot2docker-from-osx)
    * [1.2. Install blinkreaction's boot2docker Vagrant VM](#12-install-blinkreactions-boot2docker-vagrant-vm)
  * [Step 2. Cloning PROJECT](#step-2-cloning-project)
    * [2.1. Provide Your Public SSH Key to CivicActions Infrastructure Support](#21-provide-your-public-ssh-key-to-civicactions-infrastructure-support)
    * [2.2. Configure CivicActions SSH Port](#22-configure-civicactions-ssh-port)
    * [2.2. Get the latest "develop" branch](#22-get-the-latest-integration-branch)
    * [2.4. Add a route to the docker sub-network](#24-add-a-route-to-the-docker-sub-network)
  * [Step 3. Activate Bowline](#step-3-activate-bowline)
    * [Bowline commands include:](#bowline-commands-include)
  * [Step 4. Pull Database and Support Files](#step-4-pull-database-and-support-files)
  * [Step 5. Build and Run PROJECT Container](#step-5-build-and-run-project-container)
    * [If you need the legacy database for migration development](#if-you-need-the-legacy-database-for-migration-development)
  * [Step 6. Run Tests Locally](#step-6-run-tests-locally)
    * [Using drush and fig](#using-drush-and-fig)
* [Updating Your Build](#updating-your-build)
* [Daily Usage](#daily-usage)
* [Troubleshooting](#troubleshooting)
  * [Issue 1: No web address in status report](#issue-1-no-web-address-in-status-report)
  * [Issue 2: Container doesn't appear to be working](#issue-2-container-doesnt-appear-to-be-working)
  * [Issue 3: Website working, but theme not displaying](#issue-3-website-working-but-theme-not-displaying)
  * [Issue 4: Website is very slow or hanging](#issue-4-website-is-very-slow-or-hanging)
    * [If that doesn't work, try this longer process:](#if-that-doesnt-work-try-this-longer-process)
  * [Issue 5: IP address conflict](#issue-5-ip-address-conflict)
    * [For Mac:](#for-mac)
    * [For Linux:](#for-linux)
  * [Issue 6: Site not loading in browser on OSX](#issue-6-site-not-loading-in-browser-on-osx)
    * [OSX: Rebuild Vagrant/Docker](#osx-rebuild-vagrantdocker)
  * [Issue 7: OSX / Vagrant Boot2docker stuck on "Mounting NFS shared folders" step](#issue-7-osx--vagrant-boot2docker-stuck-on-mounting-nfs-shared-folders-step)
* [Spinning up a second (or third, or fourth) container](#spinning-up-a-second-or-third-or-fourth-container)
* [Other Random Sandbox Operations](#other-random-sandbox-operations)
  * [Check Operational Cache status](#check-operational-cache-status)
  * [Run Apache Benchmark tests](#run-apache-benchmark-tests)
  * [View files in Docker Container](#view-files-in-docker-container)

The PROJECT developer sandbox runs on your workstation as a set of docker-based containers mimicking the operating environment of PROJECT servers.

Read these instructions carefully. These instructions describe how to download, install, and configure software and/or virtual machines on your workstation and run a set of scripts to build the containers and PROJECT code base.

* Linux users can install and run Docker and containers directly on  workstation
* OSX users are recommended to run Docker on a pre-configured Vagrant box described below

## Requirements

- A `workspace` directory in your home directory (e.g., `~/`)
- [Git 2.0+](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [Docker 1.3+](https://docs.docker.com/installation/)
  - Make sure you can successfully run docker commands without sudo. See [Ubuntu example](https://docs.docker.com/installation/ubuntulinux/#giving-non-root-access).
- [Docker Compose](http://docs.docker.com/compose/) (Linux users need to install Docker Compose; OSX users get Docker Composed installed automatically on the virtual machines)
- [Vagrant](https://www.vagrantup.com) (OSX users only)
- [VirtualBox - A GUI for your virtual machines](https://www.virtualbox.org/wiki/Downloads) (Optional, OSX users only)

### Step 1 (Linux). Getting Started with Docker

Install the following software:

- [Git 2.0+](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [Docker 1.3+](https://docs.docker.com/installation/)
  - Make sure you can successfully run docker commands without sudo. See [Ubuntu example](https://docs.docker.com/installation/ubuntulinux/#giving-non-root-access).
- [Docker Compose](http://docs.docker.com/compose/) 


### Step 1 (OSX). Getting Started with Docker

We've had problems reliabily running Docker on OSX. These instructions describe running `boot2docker` on [Blinkreaction's Vagrant VM](https://github.com/blinkreaction/boot2docker-vagrant). Some Linux users may also opt run Docker this way from a Vagrant vm.

#### 1.1. Remove boot2docker from OSX

Presumes you have an older version of boot2docker installed. If not, you should skip to the next section.

**REMOVE** boot2docker or any boot2docker variant as it will conflict with the boot2docker variant to be installed. If you have previously installed boot2docker follow [these instructions to remove](http://therealmarv.com/blog/how-to-fully-uninstall-the-offical-docker-os-x-installation/).

To make sure you removed boot2docker, run `vagrant global-status` and confirm that it does not mention boot2docker in the list. Also, running `boot2docker` in the command line should say that the command is not found.

When you run `vagrant global-status` it is possible to see stale results (machines say they're running but they're not). To prune the invalid entries, run `vagrant global-status --prune`.

#### 1.2. Install blinkreaction's boot2docker Vagrant VM

Run the following commands to install docker and other requirements:

``` bash
curl -s https://raw.githubusercontent.com/blinkreaction/boot2docker-vagrant/master/scripts/presetup-mac.sh
cd ~/workspace
curl -s https://raw.githubusercontent.com/blinkreaction/boot2docker-vagrant/master/scripts/setup.sh
```

If you receive an error, confirm `DOCKER_HOST=tcp://localhost:2375` environmental variable is set with the following command:

```bash
env | grep DOCKER
# should return DOCKER_HOST=tcp://localhost:2375
```

If the `DOCKER_HOST` does not exist, run the following code instead:

```bash
# destroy existing boot2docker vagrant box
vagrant destroy -f

# set DOCKER_HOST environmental variable
export DOCKER_HOST=tcp://localhost:2375

# create boot2docker vagrant box
vagrant up

# test docker install
docker version
docker-compose --version
```



### Step 2. Cloning PROJECT

Note: Many of these commands run from the `~/workspace/PROJECT` directory.

#### 2.1. Provide Your Public SSH Key to CivicActions Infrastructure Support

CivicActions needs your public SSH Key to grant you access to the PROJECT repository. Use Slack to contact the CivicActions Infrastructure Support team and provide them with your public SSHkey (e.g., `~/.ssh/id_rsa.pub`). Never share your private key.

#### 2.2. Configure CivicActions SSH Port 
All civicactions.net sites use a nonstandard port (940) for SSH traffic, so if the `git clone` command below fails with something like:

`port 22: Connection refused`

then add this to your `~/.ssh/config` file (creating it if necessary):

```bash
Host *.civicactions.net
Port 940
```

#### 2.2. Get the latest "integration" branch

You will be able to work with this code base normally, as you would in any non-dockerized sandbox.

```bash
cd ~/workspace
git clone git@git.civicactions.net:dsca/PROJECT.git PROJECT
cd PROJECT
```

#### 2.4. Add a route to the docker sub-network
```
route -n add 172.17.0.0/16 192.168.10.10
```



### Step 3. Activate Bowline

A number of scripts are made available when activating your shell session (based on the bowline project):

```
cd ~/workspace/PROJECT
. bin/activate
```
Once activated, to get a list of available commands and see info about your containers run this:

```
bowline
```

#### Bowline commands include:

- **activate**: Sets up bowline
- **backup**: Create a snapshot of your db called .git-branch.sql.gz
- **behat**: run behat command from inside the container
- **bowline**: show some info about your containers
- **build**: build the containers or start them if already built
- **composer**: php composer run inside the container
- **create-files**: dummy file generator for testing
- **hoist**: N/A - used for adding bowline things to the project such as behat
- **deactivate**: Turns off bowline
- **drush**: Runs drush for the installed sandbox instance
- **enter**: Starts a bash shell inside container
- **fix-perms**: Fixes permissions for Drupal directories
- **import**: Installs local copy of db (stored in .snapshot.sql.gz)
- **invoke_prox**y
- **make-contrib**: Runs an installer script for modules that includes relevant patches when a)adding new modules b) updating existing modules.
- **migrate**: Runs migration steps
- **phpcbf**: PHP code beautifier and fixer inside container
- **phpcs**: PHP code sniffer inside container
- **pull db**: Pulls a copy of the current db from devtest
- **pull full**: Pulls a copy of the sanitized db from v2.dev
- **pull files**: Pulls a copy of the files from v2.dev
- **quick-reload-config**: Quick Reload of Features and modules, does not rebuild search indexes. Used after git pull.
- **reload-config**: Reload Features and modules, rebuilds search indexes. Used after git pull.
- **run**: behat smoke tests
- **settings_init**: makes sure your settings.php file is configured for bowline



### Step 4. Pull Database and Support Files

Pull a recent, sanitized copy of the database in the base directory.

`pull db`     Download db snapshot

`pull files`  Download files



### Step 5. Build and Run PROJECT Container

Docker creates virtual instances of Linux on your local machine, and this next step installs those machines, then sets the `~/workspace/PROJECT` directory to work with the Linux environments installed here.

```bash
build
import
composer install
```

- Your first build can take some time as it downloads virtual Linux environments but subsequent builds will be fast.
- When the build is completed a status report will be displayed that includes a link to the new site. For example: `web address: http://172.17.0.5/` Click the link or copy it to your browser.
- If the web address in the status report is not populated, something has prevented your docker instance from being built successfully. Please see the **Troubleshooting** section below.
- If the address is displayed on the status report, but the website does not come up, you may need to manually include a subnet route to your docker instance. The command for adding the route is also at the end of the status report. It will look something like this: `sudo route -n add 172.0.0.0/8 192.168.59.103` (you can also try `sudo route -n add 172.17.0.0/16 192.168.10.10`)
- If you like, you can stop the container when done:

```
fig stop
```

- Restart with:

```
build
```
**NOTE: Each time you restart the container, the IP address of the site will change. See the bottom of the status message for the new IP address for your site.**



#### If you need the legacy database for migration development

Run these commands to sync the legacy (D6) database (one-time, or very infrequently):

Download and import the legacy db. These two operations can take at least 10 minutes, and possibly much more:

``` bash
pull legacy-db
import legacy
```
The above commands only need to be run once.

### Step 6. Run Tests Locally

``` bash
# Assumes cd ~/workspace/PROJECT; . bin/activate
run
```

Or run all tests:
```
# Assumes cd ~/workspace/PROJECT; . bin/activate
run full
```

#### Using drush and fig

If you would like to use `drush` on the newly built containers, activate your bash session:
(Assumes cd ~/workspace/PROJECT; . bin/activate)

```
. bin/activate
```

Note the "dot space" is intentional.

- This will:
  - Alias fig to use the project name for this project.
  - Add a special drush script which overrides your installed drush
- To undo the above, simply exit your terminal or run `deactivate`




## Updating Your Build

To update your code (but keep your database) and restart your container:

```bash
git pull
build
```

If you want to start with a fresh database, run:

```bash
git pull
pull db
import
```



## Daily Usage

You will need to restart a few functions each time rebuild your container or restart your computer.

**Activate**

```
. bin/activate
```



**OSX users: Restart docker and activate**

```basic
vagrant up
sudo route -n add 172.17.0.0/16 192.168.10.10
. bin/activate
build
```

Logging into PROJECT via `drush` one time login

```bash
drush uli
```
Then, paste generated link into browser



## Troubleshooting

The script `~/workspace/PROJECT/bin/check` runs a series of test to check your installation and configuration. Run this script before and during your troubleshooting for valuable information.

```
./bin/check
```



### Issue 1: No web address in status report

If you aren't seeing a web address after you build your container, it could be cause by not having docker correctly install and or configured. If you suspect this is the case, try rebuilding your docker installation.

**OSX USERS:** Try rebuilding your boot2docker installation.

```bash
boot2docker delete
boot2docker init
boot2docker start
$(boot2docker shellinit)
```

Then, rerun the following command:

```
build; import
```



### Issue 2: Container doesn't appear to be working

If your container isn't working it may be from an outdated docker image. Try removing containers and images and start over:

```bash
./scripts/build destroy  # Read the prompts carefully.
build; import   # Start over with a fresh build.
```



### Issue 3: Website working, but theme not displaying

If your website comes up at the expected address, but it's not displaying a theme, it could be a permissions issue in your ~/workspace/PROJECT directory. To fix:

```bash
fix-perms
```



### Issue 4: Website is very slow or hanging

If your website is unusually slow, try:

`fig stop` followed by `build`

#### If that doesn't work, try this longer process:

1. Backup if you want to save your sandbox config: `./scripts/backup`
   - Completely harmless, saves a file and tells you where it is
2. Destroy existing project containers: `./scripts/build destroy`
3. Check that container is gone: `docker images|grep PROJECT`
   - If that lists an image redo the destroy in #2.
4. if you made a backup and you want to restore it, copy it to the snapshot location: `cp .rd1234.sql.gz .snapshot.sql.gz`  (use the filename that the backup script told you. You can check with `ls -a .*gz`)
5. Rebuild and import.



### Issue 5: IP address conflict

If you have an IP conflict with the message "The specified host network collides with a non-hostonly network!" then you can alter the boot2docker IP as follows - 192.168.99.10 is the example in this case, change as needed...

First, delete your current routing profile:

```bash
sudo route -n delete 172.0.0.0/8 192.168.10.10
```
or

```bash
sudo route -n delete 172.17.0.0/16 192.168.10.10
```

Then, define your new routing profile (do this outside of your PROJECT directory):

```bash
export VAGRANT_IP_OVERRIDE=192.168.99.10
vagrant reload
sudo route -n add 172.17.0.0/16 192.168.99.10
```

To review your current routing table, issue the following command:

#### For Mac:

```bash
netstat -nr
```

#### For Linux:

```bash
route
```



### Issue 6: Site not loading in browser on OSX

Make sure your network stack is routing you to the Docker container:

```
sudo route -n add 172.0.0.0/8 192.168.10.10
netstat -nr
```

(you can also try `route -n add 172.17.0.0/16 192.168.10.10` which is more specific)

#### OSX: Rebuild Vagrant/Docker

If worse comes to worse, rebuild your Vagrant/Docker environment

```bash
vagrant destroy
vagrant up
. bin/activate
build
pull db
import
reload-config
drush fl
drush uli
```



### Issue 7: OSX / Vagrant Boot2docker stuck on "Mounting NFS shared folders" step

(via [https://github.com/blinkreaction/boot2docker-vagrant/issues/9](http://))

If OSX firewall is turned on and "Block all incoming connections" setting is checked, folders annot be mounted and the script is stuck on the "Mounting NFS shared folders" step. Disabling this setting resolves the issue.

Also, disabling "Stealth mode" substantially speeds up the NFS mount process.



## Spinning up a second (or third, or fourth) container

Let's say you are working on a ticket then have an idea or need to look into something
completely different with different code and/or configuration. But you don't want to
lose what you're currently working on. Maybe want to try out something features module,
or configuration module, ...or "foo bar" whatever that is.

```
cd ~/workspace
cp -a PROJECT PROJECT-foo_bar   # complete copy of code base
cd PROJECT-foo_bar
. bin/activate
build; import  # We're in a new dir so need new containers
```

The `fig ps` command will display what is currently running

## Other Random Sandbox Operations
### Check Operational Cache status

OpCache:

```bash
wget https://raw.github.com/rlerdorf/opcache-status/master/opcache.php
mv opcache.php docroot
```

 and visit `http://[yourdockeripaddress]/opcache.php`



### Run Apache Benchmark tests

In your docker container:

```bash
enter ab -n100 -c1 http://localhost/PROJECT
```

Copy the results and share



### View files in Docker Container

```
enter
```
To leave the docker container files:

```
exit
```

