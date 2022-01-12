# Terraform

Terraform example
```bash
# EC2 instance
resource "aws_instance" "api" {
  ami           = "${data.aws_ami.ubuntu.id}"
  instance_type = "t2.micro"

  tags = {
    Name            = "dev-vreview-api"
    Service         = "vreview"
    ApplicationType = "django"
    Role            = "api"
    Environment     = "dev"
    CreatedBy       = "infra@indentcorp.com"
    Terraform       = true
  }
}

# RDS instance
resource "aws_db_instance" "master" {
  name                 = "prod-vreview-maindb"
  identifier           = "prod-vreview-maindb"
  allocated_storage    = 20
  storage_type         = "gp2"
  engine               = "mysql"
  engine_version       = "5.7"
  instance_class       = "db.t2.micro"
  username             = "foo"
  password             = "foobarbaz"
  parameter_group_name = "default.mysql5.7"

  tags = {
    Service         = "vreview"
    ApplicationType = "mysql"
    Role            = "maindb"
    Environment     = "prod"
    CreatedBy       = "infra@indentcorp.com"
    Terraform       = true
  }
}

# Security group
resource "aws_security_group" "public_access" {
  name = "prod-vreview-sg-pub"
  description = "HTTP and HTTPS public access"
  vpc_id = "${data.terraform_remote_state.env_vpc.vpc}"

  tags = {
    Service         = "vreview"
    ApplicationType = "django"
    Role            = "public"
    Environment     = "prod"
    CreatedBy       = "infra@indentcorp.com"
    Terraform       = true
  }
}

# IAM role
resource "aws_iam_role" "vreview_ec2" {
  name               = "prod-vreview-eks-service-iam-role"

  assume_role_policy = <<EOF
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": "sts:AssumeRole",
            "Principal": {
               "Service": "ec2.amazonaws.com"
            },
            "Effect": "Allow",
            "Sid": ""
        }
    ]
}
EOF
}

# IAM policy
resource "aws_iam_policy" "vreview_s3_access" {
  name        = "prod-vreview-eks-service-downloads3-iam-policy"
  description = "Downloads s3 access"
  role = "${aws_iam_role.vreview_s3_access.id}"

  policy = <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject"
      ],
      "Resource": [
         "arn:aws:s3:::downloads-s3-bucket/*"
      ]
    }
  ]
}
EOF
}
```

Terraform variable example
```bash
variable "service" {
  default = "vreview"
}

variable "app" {
  default = "django"
}

variable "env" {
  default = "dev"
}

variable "createdby" {
  default = "infra@indentcorp.com"
}

resource "aws_instance" "web" {
  ami           = "${data.aws_ami.ubuntu.id}"
  instance_type = "t2.micro"

  tags = {
    Name = "${var.env}-${var.service}-api"
    Service = "${var.service}"
    ApplicationType = "${var.app}"
    Role = "api"
    Environment = "${var.env}"
    CreatedBy = "${var.createdby}"
    Terraform = true
  }
}
```
