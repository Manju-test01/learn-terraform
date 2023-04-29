# learn-terraform


data "aws_ami" "centos" {
  owners = ["973714476881"]
  most_recent = true
  name_regex = "Centos-8-DevOps-Practice"
}
data "aws_security_group" "allow-all" {
 name = "allow-all"
}

variable "instance_type" {
  default = "t3.small"
}
variable "components" {
  default = [ "frontend-dev", "catalogue-dev", "mongodb-dev" ]
}
variable "dns_name" {
  default = [ "frontend.manju-devops.online", "catalogue-devl.manju-devops.online", "mongodb-dev.manju-devops.online" ]
}
resource "aws_instance" "frontend" {
  count                  = length(var.components)
  ami                    = data.aws_ami.centos.image_id
  instance_type          = var.instance_type
  vpc_security_group_ids = [data.aws_security_group.allow-all.id]

  tags = {
    Name = var.components[count.index]
  }
}
resource "aws_route53_record" "frontend" {
  zone_id = Z06653061K00JAPLVF5JM
  name    = var.dns_name[count.index]
  type    = "A"
  ttl     = 30
  records = [aws_instance.frontend.private_ip]
}
