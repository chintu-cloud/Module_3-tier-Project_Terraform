resource "aws_launch_template" "frontend" {
  name                   = "${var.project_name}-frontend-lt"
  description            = "Frontend launch template"
  image_id               = var.frontend_ami
  instance_type          = var.instance_type
  vpc_security_group_ids = [var.frontend_sg_id]
  key_name               = var.key_name
  update_default_version = true

  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "${var.project_name}-frontend"
    }
  }
}

resource "aws_launch_template" "backend" {
  name                   = "${var.project_name}-backend-lt"
  description            = "Backend launch template"
  image_id               = var.backend_ami
  instance_type          = var.instance_type
  vpc_security_group_ids = [var.backend_sg_id]
  key_name               = var.key_name
  update_default_version = true

  tag_specifications {
    resource_type = "instance"
    tags = {
      Name = "${var.project_name}-backend"
    }
  }
}

