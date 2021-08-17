# aws (Amazon Web Services)


## IAM Role / Policy
모든 iam role / policy 의 네이밍은 kebab-case 를 지키도록 합니다.

```
<environment-code>-<service-name>-<resource>-<resource(Optional)>-iam-role
```


```
<environment-code>-<service-name>-<resource>-<role-purpose>-<resource(Optional)>-iam-policy
```

### Environment Code
환경에 대한 코드입니다. 아래의 표를 참고하여 acronym 을 사용하시기 바랍니다.

| environment | description | acronym | 
| --- | --- | --- |
| develop | 개발 환경입니다. | `dev` | 
| staging |  QA를 위한 스테이지 환경입니다. | `staging` | 
| production | 프로덕션 환경입니다. | `prod` |
| sandbox | 샌드박스 사용경험을 위한 프로덕션 환경입니다. | `sandbox` | 

> `local` &rarr; `develop` &rarr; `staging` &rarr; `prod` or `sandbox` 의 순서로 인프라 프로비저닝 및 업데이트가 진행되어야 합니다.

### Service Name
서비스 이름입니다. 아래의 표를 참고하여 acronym 을 사용하시기 바랍니다. 새로운 서비스를 추가하신다면, PR 을 통해 문서를 업데이트 해주시기 바랍니다.

| service | description | acronym |
| --- | --- | --- |
| vreview | 브이리뷰 | `vreview` |
| vreviewx | 브이리뷰 X | `vreviewx` |  
| vreview 2.0 | 브이리뷰 2.0 | `vreview2` |
| openapi service | Open API 서비스 | `openapi` |
| viewcounter | View Counter 서비스 | `vvcs` | 
| ... | | |


### Resource
어떤 컴퓨팅 리소스를 위한 IAM Role / Policy 인지 안내합니다.

### Role Purpose
IAM Policy 의 목적을 설명합니다.

| role purpose | acronym |
| --- | --- |
| Deploy | `deploy`|
| Cloudwatch log | `cloudwatchlog` |
| EBS Snapshot | `ebssnapshot` |
| ... | | |


### 예시
```
dev-openapi-eks-service-iam-role
sandbox-vreview-rds-ebssnapshot-iam-policy
```

## resources
모든 리소스의 네이밍은 kebab-case 를 지키도록 합니다.
```
<environment-code>-<service-name>-<role>
```

### Role 
| role | description | acronym |
| --- | --- | --- |
| API server | 백엔드 API 서버 | `api` | 
| batch create api | 배치 API | `batchcreate` | 
| database dump | 데이터베이스를 덤프 | `dbdump` | 
| ... | | |

## Tag
[Tagging Best Practices](https://d1.awsstatic.com/whitepapers/aws-tagging-best-practices.pdf)를 참고하여 태깅, 인프라 관리해야 합니다.

## 테라폼 예시

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
