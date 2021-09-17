summary: Lab 1 - Enable a legacy application to use OIDC
id: ssoa-pt-oauth-lab
categories: SSOA-PT
tags: oauth, aws
status: Published 
authors: Thomas Schuetz

# SSOA Lab 1 - Enable a legacy application to use OIDC
<!-- ------------------------ -->
## Overview 

### What You’ll Learn 

 ![PodTatoHead-BigPicture](./img/podtatohead-bigpicture.png)

- Set Up an AWS Instance
- Install Docker
- Run a container on a specific port
- Getting Information from a metadata endpoint
- Issue a LetsEncrypt Certificate (Staging)
- Configure an oauth-application in GitHub
- Use oauth-proxy to authenticate via GitHub

## Set up an AWS Instance
- Open the AWS Console
- Search for EC2
- Click on Launch Instances
  * Step 1: Choose an Amazon Machine Image (AMI)
    * Amazon Linux 2 AMI (HVM), SSD Volume Type (x86)
  * Step 2: Choose an Instance Type
    * t2.micro
  * Step 3: Configure Instance Details
    * Keep Default
  * Step 4: Add Storage
    * Keep Default
  * Step 5: Add Tags
    * Name: podtatohead-oauth
  * Step 6: Configure Security Group
    * Create New Security Group
      * Security Group Name: podtatohead-http-in
      * Add Rules
        * Type: HTTP
        * Type: HTTPS
        * Rest Default
    * Review and Launch
  * Launch
  * Select an existing key-pair or create one
  * Launch instance

## Getting your Instance Name on the AWS CLI
- Prerequisites:
  * Installed awscli

- Open a Shell

- Ensure that aws-cli is configured
```
cat ~/.aws/credentials
```

- Describe all Instances
```
aws ec2 describe-instances 
```

- You should see all EC2 Instances (in our case one)

- Query the Instance Name
```
aws ec2 describe-instances --filters "Name=tag:Name,Values=podtatohead-oauth" --query "Reservations[].Instances[].PublicDnsName" --out text | xargs
[
    "<instance-name>.compute-1.amazonaws.com"
]
```

- Put the Instance Name in a Variable
```
  INSTANCE_HOSTNAME=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=podtatohead-oauth" --query "Reservations[].Instances[].PublicDnsName" --out text | xargs)
```
## Open a shell to your ec2-instance

- Get the instance hostname from the AWS console (Public IPv4 DNS or Public IPv4 Address)
- Open a shell
 ```
  ssh -i ~/.ssh/labsuser.pem ec2-user@${INSTANCE_HOSTNAME}

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

  https://aws.amazon.com/amazon-linux-2/ 
```

<aside class="positive">
:Now you're connected to your AWS Instance  
</aside>

## Install Docker

- Update Package List: `sudo yum update -y`
- Install Docker: `sudo yum install docker -y`
- Start Docker Service: `sudo service docker start`
- Add ec2-user to docker-group: `sudo usermod -a -G docker ec2-user`
- Log out and log in again
- Try if docker works
```
$> docker run hello-world

ec2-user@ip-172-31-85-66 ~]$ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
b8dfde127a29: Pull complete 
Digest: sha256:61bd3cb6014296e214ff4c6407a5a7e7092dfa8eefdbbec539e133e97f63e09f
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
 ```

<aside class="positive">
- Your machine is now able to run docker containers
</aside>

## Run your first container

- Run the container on the port 8080 daemonized

```
docker run -d -p 8080:9000 ghcr.io/podtato-head/podtatoserver:v0.1.2 
```

- Let's see if our app works

```
$> curl http://localhost:8080

<html>
  <head>
    <title>Hello server</title>
    <link rel="stylesheet" href="./static/styles.css"/>
    <link rel="stylesheet" href="./static/custom.css"/>
  </head>
  <body style="background-color: #849abd;color: #faebd7;">
  <main class="container">

  <div class="text-center">
    <h1>Hello from Podtato Head</h1>
    <div style="width:700px;height:800px;margin:auto;position:relative;">
      <img src="./static/images/body.svg" style="position:absolute;margin-top:80px;margin-left:200px;">
      <img src="./static/images/hats/hat-03.svg" style="position:absolute;margin-left:200px;margin-top:0px;">
      <img src="./static/images/left-arm/left-arm-03.svg" style="position:absolute;top:100px;left:-50px;">
      <img src="./static/images/right-arm/right-arm-03.svg" style="position:absolute;top:100px;left:450px;">
      <img src="./static/images/left-leg/left-leg-03.svg" style="position:absolute;top:480px;left: -0px;" >
      <img src="./static/images/right-leg/right-leg-03.svg" style="position:absolute;top:480px;left: 400px;">
    </div>
    <h2> Version 0.1.2 </h2>
  </div>

</main>  
</body>
</html>
````

<aside class="positive">
Seems like the podtatohead is running on our machine
</aside>


## Configure a GitHub oAuth Application

- Get your Public IP Address: 
```
export PUBLIC_IPV4_ADDRESS="$(curl http://169.254.169.254/latest/meta-data/public-ipv4)"
```

- Generate your needed parameters
```
cat << EOF


=======
Application name: 
-- podtatohead-on-aws

Homepage URL:     
- https://$PUBLIC_IPV4_ADDRESS.nip.io

Authorization callback URL: 
- https://$PUBLIC_IPV4_ADDRESS.nip.io/oauth2/callback
=======


EOF

```
- Open https://github.com/settings/developers
- Click on "New OAuth App"
  * Add the parameters according to the output above

- Click on the application:
  - Note client-id
  - Create client-secret and keep it open

## Install and configure LetsEncrypt
- As we only have ephemeral dns names, we will use LetsEncrypt Staging

- Enable EPEL Repositories
```
sudo amazon-linux-extras install epel -y
sudo yum-config-manager --enable epel
````

- Install CertBot
```
sudo yum install certbot -y
```

- Get your Hostname from Metadata Endpoint
```
export PUBLIC_IPV4_ADDRESS="$(curl http://169.254.169.254/latest/meta-data/public-ipv4)"
export PUBLIC_INSTANCE_NAME="$(curl http://169.254.169.254/latest/meta-data/public-hostname)"
```
- Do cert-bot dry-run
```
sudo certbot certonly --standalone --preferred-challenges http -d $PUBLIC_IPV4_ADDRESS.nip.io --dry-run
```

- If this is successful, run cert-bot staging
```
sudo certbot certonly --standalone --preferred-challenges http -d $PUBLIC_IPV4_ADDRESS.nip.io --staging
```

<aside class="negative">
- In the real world, you would now do the same thing without staging ...
</aside>

## Run and configure oauth2-proxy

- Download oauth2-proxy
```
mkdir -p /tmp/oauth2-proxy
sudo mkdir -p /opt/oauth2-proxy

cd /tmp/oauth2-proxy
curl -sfL https://github.com/oauth2-proxy/oauth2-proxy/releases/download/v7.1.3/oauth2-proxy-v7.1.3.linux-amd64.tar.gz | tar -xzvf -

sudo mv oauth2-proxy-v7.1.3.linux-amd64/oauth2-proxy /opt/oauth2-proxy/
```


- Create cookie secret
  * Generate cookie-secret: `export COOKIE_SECRET=$(python -c 'import os,base64; print(base64.urlsafe_b64encode(os.urandom(16)).decode())')`

- Set some variables (could also be a script) 
```
export GITHUB_USER=<GITHUB_USER>
export GITHUB_CLIENT_ID=<GITHUB_CLIENT_ID>
export GITHUB_CLIENT_SECRET=<GITHUB_CLIENT_SECRET>
export PUBLIC_URL=$(curl http://169.254.169.254/latest/meta-data/public-ipv4).nip.io
```
- Run oauth2-proxy
```
sudo /opt/oauth2-proxy/oauth2-proxy --github-user="${GITHUB_USER}"  --cookie-secret="${COOKIE_SECRET}" --client-id="${GITHUB_CLIENT_ID}" --client-secret="${GITHUB_CLIENT_SECRET}" --email-domain="*" --upstream=http://127.0.0.1:8080 --provider github --cookie-secure false --redirect-url=https://${PUBLIC_URL}/oauth2/callback --https-address=":443" --force-https --tls-cert-file=/etc/letsencrypt/live/$PUBLIC_URL/fullchain.pem --tls-key-file=/etc/letsencrypt/live/$PUBLIC_URL/privkey.pem
```

## Open the PodTatoHead

- Open a Browser

- Browse to "https://${PUBLIC_URL}"
- Inspect the certificate and ignore the warning (only here!)
- Click on "Log In"

- Now you should see the following:
  
  ![PodTatoHead](./img/podtatohead.png)

<aside class="positive">
If you see the PodTatoHead, Congratulations!
</aside>

## Clean Up

- Open the AWS Console
- Navigate to EC2 / Shell
- Delete the Instance
  * on the shell
  ```
  INSTANCE_ID=$(aws ec2 describe-instances --filters "Name=tag:Name,Values=podtatohead-oauth" --query "Reservations[].Instances[].InstanceId" --out text)

  aws ec2 terminate-instances --instance-ids $INSTANCE_ID
  ```
- Clean up the applicaton on GitHub (https://github.com/settings/developers)

## Advanced Version
- Try to run the oauth-proxy with environment variables (as described here: https://oauth2-proxy.github.io/oauth2-proxy/docs/configuration/overview#environment-variables)