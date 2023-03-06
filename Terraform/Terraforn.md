# Terraform

## IaC

![img_8.png](Images/img_8.png)

## How to install Terraform

https://developer.hashicorp.com/terraform/downloads?product_intent=terraform

- First using the link above you want to downland the version of terraform that's compatible with your computer and extract the .exe file of terraform
- The next stage is to make a directory in you C drive and copy the terraform.exe file into this new folder called terraform
- Once this is done you want to press on the Windows key and search on your local computer for `env` and this should bring up `Edit the system environment variables`

![img.png](Images/img.png)

- this should open up a `System Properties` window and from here you can edit your environment variables.

![img_1.png](Images/img_1.png)

- In the `User variables` you want to your `AWS_ACCESS_KEY_ID` and also add your `AWS_SECRET_ACCESS_KEY`
- In the section beneath we want to edit `Path` in our system variables
- We want to create a new path and just path the path from C drive to terraform 

![img_2.png](Images/img_2.png)

- Now we want to run GitBash as an administrator and check to see if we have terraform with `terraform --version`

![img_3.png](Images/img_3.png)

https://www.youtube.com/watch?v=SkcRSJWNRS8

- This video can help with the installing terraform step

Now that we have terraform installed we can use it to provision an ec2 instance using terraform.

- Start by opening us Visual studio code as an ADMINISTRATOR
- you want to create a new project and name it something like `IaC_terraform`
- In this directory we want to create a `main.tf` file and use this file to input our code.
- 
```
# launch ec2
# which cloud proviider - aws

provider "aws" {
    region = "eu-west-1"

}

resource "aws_instance" "app_instance" {
    ami = "ami-0488c2ca4ffe453c3"
    instance_type = "t2.micro"
    associate_public_ip_address = true
    tags = { 
        Name = "abubakar-tech201-terraform-app"

    }
  
}
```

- Tip: you may also want to get some extensions on your vs code to help terraform:

![img_4.png](Images/img_4.png)

![img_5.png](Images/img_5.png)

![img_6.png](Images/img_6.png)

Now we want to open up a bash terminal in our VSCode and run the command `terraform init` and this will give us all the dependencies we need for terraform. (if you don't get `Terraform has been successfully initialized` there may be an issue with your aws keys)

- Before we run our code we want to check our code using `terraform plan` this will let us know if there are any errors in our code
- If there are not any errors we can now create an ec2 instance by running `terraform apply` (you may need to type yes to authorize)
- If we want to then terminate our instance we can run `terraform destroy` (you may need to type yes to authorize)
- **Note** You may also want to create a `.ignore` file to ignore the dependencies when pushing your code to git hub
```
.terraform
.terraform.lock.hcl
terraforrm.tfstate
terraform.tfstate.backup
variable.tf
```

![img_7.png](Images/img_7.png)

## Terraform commands

`terraform init`: Prepare your working directory for other commands

`terraform validate`: Check whether the configuration is valid

`terraform plan`: Show changes required by the current configuration

`terraform apply`: Create or update infrastructure

`terraform destroy`: Destroy previously-created infrastructure

```
# launch ec2
# which cloud proviider - aws

provider "aws" {
    region = "eu-west-1"

}

resource "aws_vpc" "abubakar-vpc-terraform" {
  cidr_block       = "10.0.0.0/16"
  instance_tenancy = "default"

  tags = {
    Name = "abubakar-tech201-vpc"
  }
}

resource "aws_internet_gateway" "gw" {
  vpc_id = var.vpc_id

  tags = {
    Name = "abubakar-tech201-IG"
  }
}

resource "aws_subnet" "abubakar-tech201-pub" {
  vpc_id     = var.vpc_id
  cidr_block = var.pub_sn_ip
  map_public_ip_on_launch = true

  tags = {
    Name = "abubakar-tech201-pub-sn-app"
  }
}

resource "aws_subnet" "abubakar-tech201-priv-subnet" {
  vpc_id     = var.vpc_id
  cidr_block = var.priv_sn_ip

  tags = {
    Name = "abubakar-tech201-priv-sn-app"
  }
}

resource "aws_route_table" "abubakar-tech201-public-rt" {
    vpc_id = var.vpc_id
    route {
        cidr_block = "0.0.0.0/0"
        gateway_id = var.IG_id
        }
        tags = {
            Name = "abubakar-tech201-public-rt"
            }
        }
resource "aws_route_table_association" "abubakar-tech201-public-rt-association" {
    subnet_id = var.public_sn_id
    route_table_id = var.rt_id
    }
# add rout to internet gateway 
resource "aws_security_group" "abubakar-tech201-app-sg-terraform" {
  name        = "allow ports 22,80,3000"
  description = "abubakar-tech201-app-sg-terraformc"
  vpc_id      = var.vpc_id

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 3000
    to_port     = 3000
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    description      = "Not secure"
    from_port        = 0
    to_port          = 0
    protocol         = "-1"
    cidr_blocks      = ["0.0.0.0/0"]
  }
  egress {
    from_port       = 0
    to_port         = 0
    protocol        = "-1"
    cidr_blocks     = ["0.0.0.0/0"]
  }

  tags = {
    Name = "abubakar-tech201-app-sg-terraform"
  }
  }


resource "aws_instance" "app_instance" {
    ami = var.webapp_ami-id
    instance_type = "t2.micro"
    key_name = var.MyKeyPair

    subnet_id = var.public_sn_id
    vpc_security_group_ids = [var.app-sg]
    associate_public_ip_address = true
    tags = { 
        Name = "abubakar-tech201-terraform-app"

    }
  
}


```






















