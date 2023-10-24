summary: Lab 1 - Container Intro (Exoscale Edition)
id: iac-container-intro
categories: containers
tags: containers, introduction, exoscale
status: Published
authors: Thomas Schuetz

# Container Technologies - Lab 1 - Introduction / Setup

<!-- ------------------------ -->

## What You’ll Learn

In this lab, you will:

- Spin up a Virtual machine in Exoscale using the UI
- Install a container runtime on this machine
- Start a container on this machine

### Prerequisites

- SSH Client installed (OpenSSH, PuTTY)

## Creating a Virtual Machine in Exoscale

### Goal:

- You should be able to provision a Virtual Machine using the Exoscale UI
- Furthermore, you should get familiar a bit with Virtual Instances and things you can configure

## Provisioning a Virtual Machine and access it via SSH

We will deploy the Virtual Machine via the Exoscale UI. Therefore, open the [Exoscale Console](https://portal.exoscale.com/login)
and Log In with your credentials.

### Create an SSH Key

You might want to be able to access your Virtual Machine after it is provisioned. Therefore, we need to preccreate an SSH Key which can be used to access this machine.

This SSH Key has to be created on your machine. On Linux, Mac or WSL machines, this should work as follows:

- Open a shell (bash, zsh)
- Run `ssh-keygen -t ed25519 -f exoscale` (and remember where you ran this!)
  - Use a passphrase
- Open the public part of the key (exoscale.pub) and copy it.

The public part of the SSH Keypair has to be stored in Exoscale to make it usable in Virtual Machines. Therefore, select "Compute" -> "SSH-Keys" -> "Add",
assign a name you remember to the key and paste the public part.

### Provision the Virtual Machine

With this, we should have everything we need to provision a virtual machine.

- Select "Compute" -> "Instances" and click on "Add"
  - Assign a meaningful Hostname to your Instance
  - Use "Linux Debian 64-bit" as Template
  - Select the zone of your choice
  - Choose the Instance Type "small"
  - 10GB Storage will fit our needs
  - Select the Keypair you created before
  - Leave the IPv6 Options unchecked and the User Data Field Empty
  - Check your Configuration (as below and click on "Create")

![img/virt-exo-terraform-intro-instance.png](img/virt-exo-terraform-intro-instance.png)

- You will see a new screen, where your virtual instance is shown. After some times, it will get into a "running" state
- Make your self familiar with the information and options on this screen

### Adding a Security Group

To add the security group, select "Security Groups" in the "Compute" menu:

- Add a new security group ("Add"):
  - Name: `Public Access` and "Create Group"
  - On the Security Groups Overview Page, click on the three dots on the right side of the instance row and "Details"
  - Add a new Rule
    - Select "SSH"
  - Now you should see your new rule there
  - **In a real world scenario you would make sure that only your IP address could access the SSH port**
  - To assign the security group, open the instances screen, and select your instance
    - Click on "Security Groups"
    - Attach your Security Group
  - Retry accessing your machine via SSH now
  - This should work now

<aside class="positive">Congrats! You provisioned your first Virtual Machine and are able to access it! </aside>

### Access the Virtual Machine via SSH

In our lab, we want to install software and therefore, it is beneficial to have shell access. Now it's time to remember
where you stored the private ssh key. When you found it, open up your shell and execute:

- `ssh -i {{path-to-your-private-key}} debian@{{ip-address-of-your-instance}}` (you can find the ip address on the instance screen)

You will find out that you are not able to connect to the machine. This is due to a missing security group.

<aside class="negative">You can imagine security groups as host-firewalls on cloud-based virtual instances. To access a
virtual instance from the public internet, you need a security-group rule to access it</aside>

## Installing Docker Engine

Run the following commands to install containerd on the virtual machine:

```
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get -yy install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Add debian user to docker-group: `sudo usermod -a -G docker debian`
- Log out and log in again
- Try if docker works

## Running a Container

Now, we want to run a container on our virtual machine. Therefore, we will use the `docker run` command. This command
will pull the container image from the docker hub and run it on our machine.

```
docker run hello-world
```

## Create a GitHub Token and log in with docker (if you want)

- Create a GitHub Profile if you don't have one yet
- Navigate to your personal account settings https://github.com/settings/profile
- Settings -> Developer Settings

  - Personal Access Token
  - Click "Generate new token"
  - Choose a name: `ghcr-token`
  - Choose Permissions:
    - `write:packages`
  - Generate Token
  - Copy the Token to your clipboard

- Log in to the GitHub Container Registry
  - `docker login ghcr.io -u {{your-github-username}} -p {{your-github-token}}`

<aside class="positive">
Your Exoscale Instance is now ready for your experiments!
</aside>
