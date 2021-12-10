summary: Lab 2 - Modularize a Terraform Configuration and Auto-Scale on AWS
id: virt-pt-terraform-ec2-modules-asg
categories: terraform
tags: aws, MCCE, virt-pt, introduction
status: Published
authors: Thomas Schuetz

# VIRT Lab 2 - Modularize a Terraform Configuration and Auto-Scale on AWS
<!-- ------------------------ -->

## Prerequisites

* Follow the Lab https://fhb-codelabs.github.io/codelabs/virt-pt-terraform-ec2-cloudinit/index.html
* Undeploy the infrastructure if it is deployed: `terraform destroy`

## What you will learn

![Big Picture](./img/mcce-virt-terra-ec2-cloud-init-big-picture.png)

* Modularize a terraform configuration
* Enable Load Balancing and allow the application to scale

## Create Terraform Module
* Create a directory called `modules` in your terraform folder (where you created the initial configuration) and change to that directory
  * `mkdir modules`
  * `cd modules`

* Add a directory called `podtatohead` the the modules folder 
  * `mkdir podtatohead`
  
* Move your initial configuration to the `podtatohead directory`

* Remove the terraform.tfvars file from the modules/podatohead directory

<aside class="positive">
Now the root directory of your terraform directory should be empty and the whole configuration should be in the modules/podtatohead directory
</aside>


## Generalize configuration

As we need to ensure that objects can exist multiple times at AWS, we need to change the names of the resulting objects

Firstly, we will introduce a variable, which describes the name of the podtatohead-installation:

**vars.tf**
```terraform
resource "aws_security_group" "ingress-all-ssh" {
  #[...]
  variable "podtato_name" {
    type = string
  }
  #[...]
}
#
```
**modules/podtatohead/main.tf**
```terraform
resource "aws_security_group" "ingress-all-ssh" {
  #[...]
  name = "${var.podtato_name}-allow-all-ssh"
  #[...]
}

resource "aws_security_group" "ingress-all-http" {
  #[...]
  name = "${var.podtato_name}-allow-all-http"
  #[...]
}

resource "aws_security_group" "ingress-all-http" {
  #[...]
  name = "${var.podtato_name}-allow-all-http"
  #[...]
}

resource "aws_instance" "podtatohead-arms" {
  #[...]
  tags = {
    Name = "${var.podtato_name}-podtatohead-arms"
  }
  #[...]
}

resource "aws_instance" "podtatohead-legs" {
  #[...]
  tags = {
    Name = "${var.podtato_name}-podtatohead-legs"
  }
  #[...]
}

resource "aws_instance" "podtatohead-hats" {
  #[...]
  tags = {
    Name = "${var.podtato_name}-podtatohead-hats"
  }
  #[...]
}

resource "aws_instance" "podtatohead-main" {
  #[...]
  tags = {
    Name = "${var.podtato_name}-podtatohead-main"
  }
  #[...]
}
```

## Plan our first podtato-head instance
We need to create the module configuration in the root of your terraform directory. There, a file called main.tf should be created

**main.tf**
```terraform
module "podtatohead-1" {
  source = "./modules/podtatohead"
  podtato_name = "first"
  hats_version = "v3"
  left_arm_version = "v2"
  left_leg_version = "v1"
  podtato_version = "v0.1.0"
  right_arm_version = "v4"
  right_leg_version = "v1"
}
```

Furthermore, we need to create a provider.tf file with the following contents:
**provider.tf**
```terraform
provider "aws" {
  region = "us-east-1"
}
```

* Run terraform init and plan the execution 
  * `terraform init`
  * `terraform plan`

## Plan a second instance and execute
Now, that the configuration is modularized, it's an easy exercise to create a second instance. Just add a second module block to main.tf. Feel free to change the versions of the components

**main.tf**
```terraform
module "podtatohead-2" {
  source = "./modules/podtatohead"
  podtato_name = "second"
  hats_version = "v1"
  left_arm_version = "v3"
  left_leg_version = "v2"
  podtato_version = "v0.1.0"
  right_arm_version = "v2"
  right_leg_version = "v1"
}
```

### Define the outputs for this module
To get the correct urls for both instances, add the following lines to main.tf
```terraform
output "first-url" {
  value = module.podtatohead-1.podtato-url
}

output "second-url" {
  value = module.podtatohead-2.podtato-url
}
```

* Run terraform init and plan the execution
  * `terraform init`
  * `terraform plan`

* Execute the plan
  * `terraform apply`

* You should see a similar output as here:
```
Apply complete! Resources: 12 added, 0 changed, 0 destroyed.

Outputs:

first-url = "http://<ip>:8080"
second-url = "http://<ip>:8080"
```

* Open the AWS Console, and take a look on EC2 -> Instances
  * You should see some instances appearing
  * Note that they are prefixed with their `podtato_name`

* After some minutes, you are able to browse tot the two URLs specified in the output

<aside class="positive">
You templated your first cloud service :-)
</aside>

## Switch to AutoScaling Groups instead of basic instances
At some point in time, our application might get visited and used more often and we might want to add additional instances.

Therefore, we will create:
* Launch Configurations
* Auto Scaling Groups
* Elastic Load Balancers

Have fun in the following chapters ...

## Changing the main service
At first, we will change the main service to fulfill our new needs.

Therefore, we will create a launch configuration which will act as the template for our created instances. Let's create this in a new file:

**main-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-main" {
  image_id = data.aws_ami.amazon-2.image_id
  instance_type = "t3.micro"
  user_data = base64encode(templatefile("${path.module}/templates/init_main.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_instance.podtatohead-hats.private_ip, arms_host = aws_instance.podtatohead-arms.private_ip, legs_host = aws_instance.podtatohead-legs.private_ip, podtato_version=var.podtato_version } ))
  security_groups = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]
  name_prefix = "${var.podtato_name}-podtatohead-main-"

  lifecycle {
    create_before_destroy = true
  }
}
```
You might notice, that this is mainly the same as an ec2 instance. The only differences are the base64 encoded user_data and the security_group instead of the vpc_security_group_id.

* Remove the aws_instance "podtatohead_main" from your main.tf
* If we would apply this configuration now, the most of the things won't work as we only created an instance template now.
* To get the instances created afterwards, we need at least an auto-scaling group which takes control over the creation/deletion of the instances

**modules/podtatohead/main-svc.tf**
```terraform
resource "aws_autoscaling_group" "asg-podtatohead-main" {
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  desired_capacity   = var.desired_instances
  max_size           = var.max_instances
  min_size           = var.min_instances
  name = "${var.podtato_name}-main-asg"

  launch_configuration = aws_launch_configuration.podtatohead-main.name

  health_check_type    = "ELB"
  load_balancers = [
    aws_elb.main_elb.id
  ]

  instance_refresh {
    strategy = "Rolling"
    preferences {
      min_healthy_percentage = 50
      instance_warmup = "60"
    }
    triggers = ["tag"]
  }

  tag {
    key                 = "Name"
    value               = "${var.podtato_name}-podtatohead-main"
    propagate_at_launch = true
  }

}
```

* As you might have seen in the template, we introduced a few more variables in our configuration. The three variables `desired_instances`, `max_instances` and `min_instances` are controlling the sizing behaviour. Furthermore, we will define a region to specify where the instances should be created.

**modules/podtatohead/vars.tf**
```terraform
variable "region" {
  type = string
  default = "us-east-1"
}

variable "min_instances" {
  type = number
  default = 1
}

variable "max_instances" {
  type = number
  default = 1
}

variable "desired_instances" {
  type = number
  default = 1
}
```

* Another thing you might have noticed is, that we use some load balancing related things here. Therefore, we`ll also need a load balancer for the auto-scaling group to route the traffic there

**modules/podtatohead/main-svc.tf**
```terraform
resource "aws_elb" "main_elb" {
  name = "${var.podtato_name}-main-elb"
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  security_groups = [
    aws_security_group.elb_http.id
  ]

  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    interval = 30
    target = "HTTP:8080/"
  }

  listener {
    lb_port = 80
    lb_protocol = "http"
    instance_port = "8080"
    instance_protocol = "http"
  }
}
```

* This load-balancer also needs a security group to define the allowed traffic to it. Therefore, we'll create this security group in the main configuration file.

**modules/podtatohead/main.tf**
```terraform
resource "aws_security_group" "elb_http" {
  name        = "${var.podtato_name}-elb_http"
  description = "Allow HTTP traffic to instances through Elastic Load Balancer"

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "Allow HTTP through ELB Security Group"
  }
}
```

* Finally, we are able to switch the URL of our podtato-head to our new load balancer

**modules/podtatohead/outputs.tf**
```terraform
output "podtato-url" {
  value = "http://${aws_elb.main_elb.dns_name}"
}
```

* Now we could re-apply our terraform-configuration
  * `terraform plan`
  * `terraform apply`

* Afterwards, the two podtatohead-installations should be created, and you should see the URLs of the main-service load balancers.
* Now, you could inspect your work on the AWS Console (EC2-Load Balancers)
  * Take a look on the instances, and on their state
* It might take some time until the website is reachable

<aside class="positive">
You created your first load balancer and the endpoint for the main-service won't change anymore
</aside>


## Changing the hats service
Now, we will change the hats service, the methodology is almost the same, but we should be aware that we need to make changes in the main service too, as this will also use the load balancer for the hats service in the future.

### Launch Configuration
**modules/podtatohead/hats-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-hats" {
  image_id = data.aws_ami.amazon-2.image_id
  instance_type = "t3.micro"
  user_data = base64encode(templatefile("${path.module}/templates/init_hats.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-hats", podtato_version=var.podtato_version, version=var.hats_version } ))
  security_groups = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]
  name_prefix = "${var.podtato_name}-podtatohead-hats-"

  lifecycle {
    create_before_destroy = true
  }
}
```

* Remove the aws_instance "podtatohead_hats" from your main.tf

### Auto Scaling Group 
**modules/podtatohead/hats-svc.tf**
```terraform
resource "aws_autoscaling_group" "asg-podtatohead-hats" {
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  desired_capacity   = var.desired_instances
  max_size           = var.max_instances
  min_size           = var.min_instances
  name = "${var.podtato_name}-hats-asg"

  launch_configuration = aws_launch_configuration.podtatohead-hats.name


  health_check_type    = "ELB"
  load_balancers = [
    aws_elb.hats_elb.id
  ]
  instance_refresh {
    strategy = "Rolling"
    preferences {
      min_healthy_percentage = 50
    }
    triggers = ["tag"]
  }

  tag {
    key                 = "Name"
    value               = "${var.podtato_name}-podtatohead-hats"
    propagate_at_launch = true
  }

}
```

### Load Balancer
* Another thing you might have noticed is, that we use some load balancing related things here. Therefore, we`ll also need a load balancer for the auto-scaling group to route the traffic there

**modules/podtatohead/hats-svc.tf**
```terraform
resource "aws_elb" "hats_elb" {
  name = "${var.podtato_name}-hats-elb"
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  security_groups = [
    aws_security_group.elb_http_8080.id
  ]

  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    interval = 30
    target = "HTTP:8080/images/hat-01.svg"
  }

  listener {
    lb_port = 8080
    lb_protocol = "http"
    instance_port = "8080"
    instance_protocol = "http"
  }

}
```

* This load-balancer also needs a security group to define the allowed traffic to it. Therefore, we'll create this security group in the main configuration file. This is a different security group than we used for the main service.

**modules/podtatohead/main.tf**
```terraform
resource "aws_security_group" "elb_http_8080" {
  name        = "${var.podtato_name}-elb_http_8080"
  description = "Allow HTTP traffic to instances through Elastic Load Balancer"

  ingress {
    from_port   = 8080
    to_port     = 8080
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port       = 0
    to_port         = 0
    protocol        = "-1"
    cidr_blocks     = ["0.0.0.0/0"]
  }

  tags = {
    Name = "Allow HTTP through ELB Security Group"
  }
}
```

* Finally, we are able to switch the hostname in the main service to the new load balancer

**modules/podtatohead/main-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-main" {
  #[...]

  user_data = base64encode(templatefile("${path.module}/templates/init_main.tpl", {
    container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_elb.hats_elb.dns_name,
    arms_host       = aws_instance.podtatohead-arms.private_ip, legs_host = aws_instance.podtatohead-legs.private_ip,
    podtato_version = var.podtato_version
  } ))
  #[...]
}
```

* Now we could re-apply our terraform-configuration
  * `terraform plan`
  * `terraform apply`

* Afterwards, the podtatohead-installations should get updated to remove the single instances of the hats-service, replace them with the auto scaling groups and create the load balancers. After some time, the installation should work again.

## Changing the legs service
Now, we will change the legs service, the methodology is almost the same, but we should be aware that we need to make changes in the main service too, as this will also use the load balancer for the hats service in the future.

### Launch Configuration
**modules/podtatohead/legs-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-legs" {
  image_id = data.aws_ami.amazon-2.image_id
  instance_type = "t3.micro"
  user_data = base64encode(templatefile("${path.module}/templates/init.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-legs", podtato_version=var.podtato_version, left_version=var.left_leg_version, right_version=var.right_leg_version} ))
  security_groups = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]
  name_prefix = "${var.podtato_name}-podtatohead-legs-"

  lifecycle {
    create_before_destroy = true
  }
}
```

* Remove the aws_instance "podtatohead_hats" from your main.tf

### Auto Scaling Group
**modules/podtatohead/legs-svc.tf**
```terraform
resource "aws_autoscaling_group" "asg-podtatohead-legs" {
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  desired_capacity   = var.desired_instances
  max_size           = var.max_instances
  min_size           = var.min_instances
  name = "${var.podtato_name}-legs-asg"

  launch_configuration = aws_launch_configuration.podtatohead-legs.name

  health_check_type    = "ELB"
  load_balancers = [
    aws_elb.legs_elb.id
  ]

  instance_refresh {
    strategy = "Rolling"
    preferences {
      min_healthy_percentage = 50
    }
    triggers = ["tag"]
  }

  tag {
    key                 = "Name"
    value               = "${var.podtato_name}-podtatohead-legs"
    propagate_at_launch = true
  }
}
```

### Load Balancer
* Another thing you might have noticed is, that we use some load balancing related things here. Therefore, we`ll also need a load balancer for the auto-scaling group to route the traffic there

**modules/podtatohead/legs-svc.tf**
```terraform
resource "aws_elb" "legs_elb" {
  name = "${var.podtato_name}-legs-elb"
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  security_groups = [
    aws_security_group.elb_http_8080.id
  ]

  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    interval = 30
    target = "HTTP:8080/images/left-leg-01.svg"
  }

  listener {
    lb_port = 8080
    lb_protocol = "http"
    instance_port = "8080"
    instance_protocol = "http"
  }

}
```
* Finally, we are able to switch the hostname in the main service to the new load balancer

**modules/podtatohead/main-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-main" {
  #[...]

  user_data = base64encode(templatefile("${path.module}/templates/init_main.tpl", {
    container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_elb.hats_elb.dns_name,
    arms_host       = aws_instance.podtatohead-arms.private_ip, legs_host = aws_elb.legs_elb.dns_name,
    podtato_version = var.podtato_version
  } ))
  #[...]
}
```

* Now we could re-apply our terraform-configuration
  * `terraform plan`
  * `terraform apply`

* Afterwards, the podtatohead-installations should get updated to remove the single instances of the legs-service, replace them with the auto scaling groups and create the load balancers. After some time, the installation should work again.

## Changing the arms service
Finally, we will change the arms service, the methodology is almost the same, but we should be aware that we need to make changes in the main service too, as this will also use the load balancer for the hats service in the future.

### Launch Configuration
**modules/podtatohead/arms-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-arms" {
  image_id = data.aws_ami.amazon-2.image_id
  instance_type = "t3.micro"
  user_data = base64encode(templatefile("${path.module}/templates/init.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-arms", podtato_version=var.podtato_version, left_version=var.left_arm_version, right_version=var.right_arm_version} ))
  security_groups = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]
  name_prefix = "${var.podtato_name}-podtatohead-arms-"

  lifecycle {
    create_before_destroy = true
  }

}
```

* Remove the aws_instance "podtatohead_hats" from your main.tf

### Auto Scaling Group
**modules/podtatohead/arms-svc.tf**
```terraform
resource "aws_autoscaling_group" "asg-podtatohead-arms" {
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  desired_capacity   = var.desired_instances
  max_size           = var.max_instances
  min_size           = var.min_instances
  name = "${var.podtato_name}-arms-asg"

  launch_configuration = aws_launch_configuration.podtatohead-arms.name


  health_check_type    = "ELB"
  load_balancers = [
    aws_elb.arms_elb.id
  ]

  instance_refresh {
    strategy = "Rolling"
    preferences {
      min_healthy_percentage = 50
    }
    triggers = ["tag"]
  }
  tag {
    key                 = "Name"
    value               = "${var.podtato_name}-podtatohead-arms"
    propagate_at_launch = true
  }
}
```

### Load Balancer
* Another thing you might have noticed is, that we use some load balancing related things here. Therefore, we`ll also need a load balancer for the auto-scaling group to route the traffic there

**modules/podtatohead/arms-svc.tf**
```terraform
resource "aws_elb" "arms_elb" {
  name = "${var.podtato_name}-arms-elb"
  availability_zones = ["${var.region}a", "${var.region}b", "${var.region}c"]
  security_groups = [
    aws_security_group.elb_http_8080.id
  ]

  health_check {
    healthy_threshold = 2
    unhealthy_threshold = 2
    timeout = 3
    interval = 30
    target = "HTTP:8080/images/left-arm-01.svg"
  }

  listener {
    lb_port = 8080
    lb_protocol = "http"
    instance_port = "8080"
    instance_protocol = "http"
  }

}
```
* Finally, we are able to switch the hostname in the main service to the new load balancer

**modules/podtatohead/main-svc.tf**
```terraform
resource "aws_launch_configuration" "podtatohead-main" {
  image_id = data.aws_ami.amazon-2.image_id
  instance_type = "t3.micro"
  user_data = base64encode(templatefile("${path.module}/templates/init_main.tpl", { container_image = "ghcr.io/fhb-codelabs/podtato-small-main", hats_host = aws_elb.hats_elb.dns_name, arms_host = aws_elb.arms_elb.dns_name, legs_host = aws_elb.legs_elb.dns_name, podtato_version=var.podtato_version } ))
  security_groups = [aws_security_group.ingress-all-ssh.id, aws_security_group.ingress-all-http.id]
  name_prefix = "${var.podtato_name}-podtatohead-main-"

  lifecycle {
    create_before_destroy = true
  }
}
```

* Now we could re-apply our terraform-configuration
  * `terraform plan`
  * `terraform apply`

* Afterwards, the podtatohead-installations should get updated to remove the single instances of the legs-service, replace them with the auto scaling groups and create the load balancers. After some time, the installation should work again.

## Finally

* Now you can play around, change versions of the components and scale them up and down
* If you want you can make the components scale individually 
* Note, that you will face service outages when you have less than one instance running

