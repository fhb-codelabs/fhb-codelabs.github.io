summary: Lab 2 - Installing a Microservice Application with Terraform
id: virt-pt-terraform-ec2-cloudinit
categories: terraform
tags: aws, MCCE, virt-pt, introduction, iac
status: Published
authors: Thomas Schuetz

# VIRT Lab 2 - Installing a Microservice Application with Terraform
<!-- ------------------------ -->

## What You’ll Learn

![Big Picture](./img/mcce-virt-terra-ec2-cloud-init-big-picture.png)

* Install and Configure Virtual Machines via Terraform
* Configure Security Groups via Terraform
* Connect Microservice Components (Object Links) in Terraform

## Install Terraform
To install terraform, refer to the respective documentation [here](https://learn.hashicorp.com/tutorials/terraform/install-cli)

## Get AWS Configuration (AWS Academy)
* In the AWS Academy
  * Click on AWS Details
  * Show AWS CLI Credentials and copy them

* Copy them to your machine ~/.aws/credentials
* Terraform is able to use them -> see https://registry.terraform.io/providers/hashicorp/aws/latest/docs

## Terraform Provider configuration
* Create a file called `provider.tf` in your directory
* Copy the following block in the file
```terraform
provider "aws" {
  region = "us-east-1"
}
```
* This should be enough to run our first terraform command
```
terraform init
```

* This leads to the following output
```
Initializing the backend...

Initializing provider plugins...
- Finding latest version of hashicorp/aws...
- Installing hashicorp/aws v3.61.0...
- Installed hashicorp/aws v3.61.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

<aside class="positive">
Using this step, terraform downloaded the provider from the terraform registry
</aside>

## Initial Setup of Cloud Instances
* We will create 4 instances
  - "main" is the entry point / frontend-service for this application and the body of the podtato-head
  -"legs" and "arms" are the services, which will represent the corresponding parts of the podtatohead
  -"hats" will represent the hat (obviously)

* Create a file called main.tf
* Create the configuration for these 4 instances
``` terraform

# Data Source for getting Amazon Linux AMI
data "aws_ami" "amazon-2" {
  most_recent = true

  filter {
    name = "name"
    values = ["amzn2-ami-hvm-*-x86_64-ebs"]
  }
  owners = ["amazon"]
}

# Resource for podtatohead-main
resource "aws_instance" "podtatohead-main" {
  ami = data.aws_ami.amazon-2.id
  instance_type = "t3.micro"

  tags = {
    Name = "podtatohead-main"
  }
}

# Resource for podtatohead-legs
resource "aws_instance" "podtatohead-legs" {
  ami = data.aws_ami.amazon-2.id
  instance_type = "t3.micro"

  tags = {
    Name = "podtatohead-legs"
  }
}

# Resource for podtatohead-arms
resource "aws_instance" "podtatohead-arms" {
  ami = data.aws_ami.amazon-2.id
  instance_type = "t3.micro"

  tags = {
    Name = "podtatohead-arms"
  }
}

# Resource for podtatohead-hats
resource "aws_instance" "podtatohead-hats" {
  ami = data.aws_ami.amazon-2.id
  instance_type = "t3.micro"

  tags = {
    Name = "podtatohead-hats"
  }
}
```

## Validate, plan and apply the configuration
* Switch to your shell and open the directory which contains your configuration
* To check if this configuration is syntactically correct and internally consistent, type `terraform validate`
* This should lead to following output
```
❯ terraform validate
Success! The configuration is valid.
```

* Afterwards, a dry-run of this using `terraform plan`
* The output should end like this:
```
Plan: 4 to add, 0 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't
guarantee to take exactly these actions if you run "terraform apply" now.
```
<aside class="negative">
Always inspect, what your configuration does and if this could have an impact on your application 
</aside>

* As there are only new objects created in this case, it's time to apply the configuration
* Type `terraform apply`
* You will get asked if you are sure that you want to perform these actions, type "yes". If you want to skip this question, you could start `terraform apply` with the parameter `--auto-approve` (but be sure what you're doing).
* Now, the instances get created and after that, you should see the following output:

```
[...]

Plan: 4 to add, 0 to change, 0 to destroy.
aws_instance.podtatohead-main: Creating...
aws_instance.podtatohead-legs: Creating...
aws_instance.podtatohead-arms: Creating...
aws_instance.podtatohead-hats: Creating...
aws_instance.podtatohead-legs: Still creating... [10s elapsed]
aws_instance.podtatohead-main: Still creating... [10s elapsed]
aws_instance.podtatohead-arms: Still creating... [10s elapsed]
aws_instance.podtatohead-hats: Still creating... [10s elapsed]
aws_instance.podtatohead-main: Creation complete after 19s [id=i-0024742a855b87fe8]
aws_instance.podtatohead-arms: Creation complete after 19s [id=i-026ffc94cd3117a72]
aws_instance.podtatohead-hats: Creation complete after 19s [id=i-04183576772e51162]
aws_instance.podtatohead-legs: Creation complete after 19s [id=i-095392d47383309ba]

Apply complete! Resources: 4 added, 0 changed, 0 destroyed.

```
<aside class="positive">
You created 4 instances using terraform, congratulations! 
</aside>

### Inspect our environment
* Open the AWS Console (Browser)
* Switch to EC2 -> Instances

* You should see the following instances:
  ![Big Picture](./img/mcce-virt-terra-ec2-cloud-init-instances.png)

* Currently, we should be aware of the following facts:
  * We provisioned 4, plain instances without any software installed
  * There is no security group configured, therefore nobody (even we!) will be able to access this instances

* Therefore, there's some more work to do

## Cloud Init
* Cloud Init can be used to run scripts after provisioning your Instances, we'll use this to install docker and run the podtatohead containers.

### Set up the Cloud Init Templates
* We will set up 3 templates (as the podtatohead needs different configurations), one for the arms and legs, one for the hats and one for main. 
* Create a directory called `templates` in your terraform directory
* Create a file `init_hats.tpl` in the templates directory and add the following:

```shell
#!/bin/bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user
sudo docker run -p 8080:8080 -e PORT=8080 -e VERSION=${version} -d ${container_image}:${podtato_version}
```

<aside class="negative">
Note, that we're using variables in the Docker run command, the values for these variables will be passed via terraform
</aside>

* Create an additional file `init.tpl` (for the legs and arms)

```shell
#!/bin/bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user
sudo docker run -p 8080:8080 -e PORT=8080 -e LEFT_VERSION=${left_version} -e RIGHT_VERSION=${right_version} -d ${container_image}:${podtato_version}
```

<aside class="negative">
The only thing that changed, are the variables, which are passed to the container, which define the version of the left and right body-parts
</aside>

* Finally, create a file `init_main.tpl` (for main)
```shell
#!/bin/bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user
sudo docker run -p 8080:8080 -e PORT=8080 -e HATS_HOST=${hats_host} -e HATS_PORT=8080 -e ARMS_HOST=${arms_host} -e ARMS_PORT=8080 -e LEGS_HOST=${legs_host} -e LEGS_PORT=8080 -d ${container_image}:${podtato_version}
```
<aside class="negative">
The main service needs to know, where to find the other services, therefore also different variables are used
</aside>

### Use the Cloud Init Templates
* To use these templates, we have to refer to them in our terraform configuration
* Therefore, open the `main.tf` file in your Terraform folder
* Add the following line to the `podtatohead-main` resource
```
  user_data = templatefile("${path.module}/templates/init_main.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_instance.podtatohead-hats.private_ip, arms_host = aws_instance.podtatohead-arms.private_ip, legs_host = aws_instance.podtatohead-legs.private_ip, podtato_version=var.podtato_version } )
```

<aside class="negative">
We refer to the template file, we've created previously and set some variables. Note, that we're using the ip addresses of the other services from the corresponding resource entries.
</aside>

* Add the following line to the `podtatohead-legs` resource
```
   user_data = templatefile("${path.module}/templates/init.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-legs", podtato_version=var.podtato_version, left_version=var.left_leg_version, right_version=var.right_leg_version} )
```

* As a reference, the podtatohead-main resource should look as follows now:
```terraform
resource "aws_instance" "podtatohead-main" {
  ami = data.aws_ami.amazon-2.id
  instance_type = "t3.micro"

  user_data = templatefile("${path.module}/templates/init_main.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_instance.podtatohead-hats.private_ip, arms_host = aws_instance.podtatohead-arms.private_ip, legs_host = aws_instance.podtatohead-legs.private_ip, podtato_version=var.podtato_version } )

  tags = {
    Name = "podtatohead-main"
  }
}
```

* Add the following line to the `podtatohead-arms` resource
```
  user_data = templatefile("${path.module}/templates/init.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-arms", podtato_version=var.podtato_version, left_version=var.left_arm_version, right_version=var.right_arm_version } )
```

* And finally this line to the `podtatohead-hats` resource
```
  user_data = templatefile("${path.module}/templates/init_hats.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-hats", podtato_version=var.podtato_version, version=var.hats_version } )
```

### Validate the configuration
* To validate this configuration, type `terraform validate`
* After that, we should see something like this:
```
Error: Reference to undeclared input variable
│
│   on main.tf line 30, in resource "aws_instance" "podtatohead-legs":
│   30:   user_data = templatefile("${path.module}/templates/init.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-legs", podtato_version=var.podtato_version, left_version=var.left_leg_version, right_version=var.right_leg_version} )
│
│ An input variable with the name "podtato_version" has not been declared. This
│ variable can be declared with a variable "podtato_version" {} block.
```
<aside class="negative">
It seems like we're missing some variables. Let's create them now!
</aside>

### Defining Variables
* To define the variables, create a new file `vars.tf` in your terraform folder
* Add the following to that file
```terraform
variable "podtato_version" {
  type = string
}

variable "hats_version" {
  type = string
}

variable "left_arm_version" {
  type = string
}

variable "right_arm_version" {
  type = string
}

variable "right_leg_version" {
  type = string
}

variable "left_leg_version" {
  type = string
}
```
<aside class="negative">
These variables define, which version of the podtatohead body-parts will be used
</aside>

### Assigning Variables
* These variables can be used in different ways (environment variables starting with TF_VAR or via a .tfvars file). In this example, we will use a tfvars file, which is automatically used when present.
* Create a file `terraform.tfvars` in your terraform directory and add the following content
```
podtato_version="v0.1.0"
left_arm_version = "v1"
right_arm_version = "v2"
left_leg_version = "v3"
right_leg_version = "v3"
hats_version = "v4"
```

<aside class="negative">
Always ensure, that such vars files do not contain secrets, when checking them in in git.
</aside>

* Now, we created our variables and should be able to apply our configuration

### Plan and apply the configuration
* `terraform validate` should pass now
* run `terraform plan` and inspect the output:

```
[...] 
Plan: 4 to add, 0 to change, 4 to destroy.

───────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't
guarantee to take exactly these actions if you run "terraform apply" now.
```

<aside class="negative">
You will delete 4 instance and create 4 new ones. Note, that there are changes (like changes in cloud-init scripts) which enforce a recreation of the instance (immutable infrastructure). In our case this will be ok, in the real world you would take mechanisms in place which avoid the downtime (load balances, multiple instances).
</aside>

* run `terraform apply` 

<aside class="positive">
If your configuration has been applied successfully, you provisioned your virtual instances and the cloud init scripts should work now
</aside>

### Open a shell to your instances
* Open the AWS Console
* Navigate to EC2 -> Instances
* Right click on one of the instances
  * Use EC2 Instance Connect
  * Click Connect
  * Something like this should be shown

![Big Picture](./img/mcce-virt-terra-cloud-init-ssh-error.png)
<aside class="negative">
When you take a closer look on the security groups of the instance, you might notice that there are no inbound rules. Therefore, SSH is also not working.
</aside>

## Create Security Groups
* To assign a security group in terraform, we have to create a `security_group` resource and assign it to an instance. 

<aside class="positive">
Our application needs two security groups, one for the ssh access and the second one for the connections between the services.
</aside>

* To create the SSH Security Group, add the following resource block to your Terraform `main.tf` file:

```
resource "aws_security_group" "ingress-all-ssh" {
  name = "allow-all-ssh"
  ingress {
    cidr_blocks = [
      "0.0.0.0/0"
    ]
    from_port = 22
    to_port = 22
    protocol = "tcp"
  }
  // Terraform removes the default rule
  egress {
    from_port = 0
    to_port = 0
    protocol = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

* And do the same for the http Security Group 
```
resource "aws_security_group" "ingress-all-http" {
  name = "allow-all-http"
  ingress {
    cidr_blocks = [
      "0.0.0.0/0"
    ]
    from_port = 8080
    to_port = 8080
    protocol = "tcp"
  }
  // Terraform removes the default rule
  egress {
    from_port = 0
    to_port = 0
    protocol = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

<aside class="negative">
The security groups are very open in this example, in the real world this should be more specific
</aside>

### Assign the Security Groups
* These Security Groups can now be used in your instances, when you reference them as follows in your resource configuration:

```
 vpc_security_group_ids = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]
```

* A sample configuration should look like this now:
```
resource "aws_instance" "podtatohead-main" {
  ami = data.aws_ami.amazon-2.id
  instance_type = "t3.micro"

  user_data = templatefile("${path.module}/templates/init_main.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_instance.podtatohead-hats.private_ip, arms_host = aws_instance.podtatohead-arms.private_ip, legs_host = aws_instance.podtatohead-legs.private_ip, podtato_version=var.podtato_version } )

  vpc_security_group_ids = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]

  tags = {
    Name = "podtatohead-main"
  }

  lifecycle {
    create_before_destroy = true
  }
}
```

### Apply the configuration
* Now you can validate, plan and apply your configuration again:
  * `terraform validate`
  * `terraform plan`
  * `terraform apply`

<aside class="negative">
Note, that 2 additional resources (security groups) are created and the instances are only changed
</aside>

  * After you applied the configuration, you should be able to access the SSH console as described before
  * Furthermore, you should be able to access the podtatohead-application after some minutes

<aside class="negative">
You can lookup the public ip address of the podtato-head in the AWS Console, but there's a more convenient way to achieve this.
</aside>

## Outputs
* You can specify things which should declared as outputs in terraform, in our case we want to get the url for our podtatohead
* Create a file `outputs.tf` in your Terraform directory with the following contents

```
output "podtato-url" {
  value = "http://${aws_instance.podtatohead-main.public_ip}:8080"
}
```

* Afterwards, do a `terraform refresh` to update the state
* In the outputs section, you can see the `podtato-url` which can be put in a browser, simply try to do this

## Congratulations
<aside class="positive">
If you see the PodTatoHead, you have successfully finished this lesson. If you want to change the behaviour of the podtatohead, simply change the version of the corresponding part (v1-v4) in your terraform.tfvars file. In this example, everytime you change a version, you'll get a new IP address. In one of our next lessons we'll take a look on load balancers</aside>

![Big Picture](./img/mcce-virt-terra-ec2-cloud-init-congrats.png)

### Clean up
* After you have finished this lesson, simply remove everything using `terraform destroy`