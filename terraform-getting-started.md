# Getting Started with Terraform

Terraform is the most popular langauge for defining and provisioning infrastructure as code (IaC). In this guide you'll learn to install Terraform and use it to deploy a Docker container.


## Prerequisites

- [Docker installed and running](https://docs.docker.com/desktop/install/linux-install/)


## Install Terraform

To install Terraform, visit [Terraform.io](https://www.terraform.io/downloads) and download the installer for your platform or follow the Package Manager installation instructions.

Once Terraform is installed, you're ready to create some infrastructure.


## Create a Directory

Most users find it easiest to create a new directory on their local machine for Terraform configuration code.

### CLI Command
```shell
[ec2-user@ip-172-31-84-22 ~]$ mkdir terraform-demo
[ec2-user@ip-172-31-84-22 ~]$ cd terraform-demo/
```


## Create a Terraform configuration file and add some code
Next, create a file for your Terraform configuration code.

### CLI Command
```shell
[ec2-user@ip-172-31-84-22 ~]$ touch main.tf
```

Paste the following lines into the file. These tell Terraform what you want to create.

```hcl
terraform {
  required_providers {
    docker = {
      source = "kreuzwerker/docker"
    }
  }
}
provider "docker" {
    host = "unix:///var/run/docker.sock"
}
resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "training"
  ports {
    internal = 80
    external = 80
  }
}
resource "docker_image" "nginx" {
  name = "nginx:latest"
}
```


## Initialize Terraform
Initialize Terraform with the `init` command. The Docker provider will be installed. 

### CLI Command
```shell
$ terraform init
```

### Sample Output
```shell
[ec2-user@ip-172-31-84-22 terraform-demo]$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding latest version of kreuzwerker/docker...
- Installing kreuzwerker/docker v2.20.2...
- Installed kreuzwerker/docker v2.20.2 (self-signed, key ID BD080C4571C6104C)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

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


## Apply the changes
You shoud check for any errors. If it ran successfully, provision the resource with the `apply` command.

### CLI Command
```shell
$ terraform apply
```

### Sample Output
The `apply` command will show you summary of everything to be created or modified and ask you to confirm your actions.

```shell
[ec2-user@ip-172-31-84-22 terraform-demo]$ terraform apply

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # docker_container.nginx will be created
  + resource "docker_container" "nginx" {
      + attach           = false
      + bridge           = (known after apply)
      + command          = (known after apply)
      + container_logs   = (known after apply)
      + entrypoint       = (known after apply)
      + env              = (known after apply)
      + exit_code        = (known after apply)
      + gateway          = (known after apply)
      + hostname         = (known after apply)
      + id               = (known after apply)
      + image            = (known after apply)
      + init             = (known after apply)
      + ip_address       = (known after apply)
      + ip_prefix_length = (known after apply)
      + ipc_mode         = (known after apply)
      + log_driver       = (known after apply)
      + logs             = false
      + must_run         = true
      + name             = "training"
      + network_data     = (known after apply)
      + read_only        = false
      + remove_volumes   = true
      + restart          = "no"
      + rm               = false
      + runtime          = (known after apply)
      + security_opts    = (known after apply)
      + shm_size         = (known after apply)
      + start            = true
      + stdin_open       = false
      + stop_signal      = (known after apply)
      + stop_timeout     = (known after apply)
      + tty              = false

      + healthcheck {
          + interval     = (known after apply)
          + retries      = (known after apply)
          + start_period = (known after apply)
          + test         = (known after apply)
          + timeout      = (known after apply)
        }

      + labels {
          + label = (known after apply)
          + value = (known after apply)
        }

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

  # docker_image.nginx will be created
  + resource "docker_image" "nginx" {
      + id          = (known after apply)
      + latest      = (known after apply)
      + name        = "nginx:latest"
      + output      = (known after apply)
      + repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.
╷
│ Warning: Deprecated attribute
│ 
│   on main.tf line 12, in resource "docker_container" "nginx":
│   12:   image = docker_image.nginx.latest
│ 
│ The attribute "latest" is deprecated. Refer to the provider documentation for
│ details.
│ 
│ (and one more similar warning elsewhere)
╵

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Creation complete after 5s [id=sha256:b692a91e4e1582db97076184dae0b2f4a7a86b68c4fe6f91affa50ae06369bf5nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=e5be6bec1a7b52d4f5301b313201331f4c3b0b83e969a1ec348908e1d63764f7]
╷
│ Warning: Deprecated attribute
│ 
│   on main.tf line 12, in resource "docker_container" "nginx":
│   12:   image = docker_image.nginx.latest
│ 
│ The attribute "latest" is deprecated. Refer to the provider documentation for
│ details.
│ 
│ (and one more similar warning elsewhere)
╵

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

The command will take up to a few minutes to run and will display a message indicating that the resource was created.


## Destroy what you created
Finally, destroy the infrastructure with the `destroy` command.

### CLI Command
```shell
$ terraform destroy
```
### Sample Output
As with the `apply` command, you'll see a list of what will be destroyed.

```shell
[ec2-user@ip-172-31-84-22 terraform-demo]$ terraform destroy
docker_image.nginx: Refreshing state... [id=sha256:b692a91e4e1582db97076184dae0b2f4a7a86b68c4fe6f91affa50ae06369bf5nginx:latest]
docker_container.nginx: Refreshing state... [id=e5be6bec1a7b52d4f5301b313201331f4c3b0b83e969a1ec348908e1d63764f7]

Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # docker_container.nginx will be destroyed
  - resource "docker_container" "nginx" {
      - attach            = false -> null
      - command           = [
          - "nginx",
          - "-g",
          - "daemon off;",
        ] -> null
      - cpu_shares        = 0 -> null
      - dns               = [] -> null
      - dns_opts          = [] -> null
      - dns_search        = [] -> null
      - entrypoint        = [
          - "/docker-entrypoint.sh",
        ] -> null
      - env               = [] -> null
      - gateway           = "172.17.0.1" -> null
      - group_add         = [] -> null
      - hostname          = "e5be6bec1a7b" -> null
      - id                = "e5be6bec1a7b52d4f5301b313201331f4c3b0b83e969a1ec348908e1d63764f7" -> null
      - image             = "sha256:b692a91e4e1582db97076184dae0b2f4a7a86b68c4fe6f91affa50ae06369bf5" -> null
      - init              = false -> null
      - ip_address        = "172.17.0.2" -> null
      - ip_prefix_length  = 16 -> null
      - ipc_mode          = "private" -> null
      - links             = [] -> null
      - log_driver        = "json-file" -> null
      - log_opts          = {} -> null
      - logs              = false -> null
      - max_retry_count   = 0 -> null
      - memory            = 0 -> null
      - memory_swap       = 0 -> null
      - must_run          = true -> null
      - name              = "training" -> null
      - network_data      = [
          - {
              - gateway                   = "172.17.0.1"
              - global_ipv6_address       = ""
              - global_ipv6_prefix_length = 0
              - ip_address                = "172.17.0.2"
              - ip_prefix_length          = 16
              - ipv6_gateway              = ""
              - network_name              = "bridge"
            },
        ] -> null
      - network_mode      = "default" -> null
      - privileged        = false -> null
      - publish_all_ports = false -> null
      - read_only         = false -> null
      - remove_volumes    = true -> null
      - restart           = "no" -> null
      - rm                = false -> null
      - runtime           = "runc" -> null
      - security_opts     = [] -> null
      - shm_size          = 64 -> null
      - start             = true -> null
      - stdin_open        = false -> null
      - stop_signal       = "SIGQUIT" -> null
      - stop_timeout      = 0 -> null
      - storage_opts      = {} -> null
      - sysctls           = {} -> null
      - tmpfs             = {} -> null
      - tty               = false -> null

      - ports {
          - external = 80 -> null
          - internal = 80 -> null
          - ip       = "0.0.0.0" -> null
          - protocol = "tcp" -> null
        }

      - ulimit {
          - hard = 65536 -> null
          - name = "nofile" -> null
          - soft = 32768 -> null
        }
    }

  # docker_image.nginx will be destroyed
  - resource "docker_image" "nginx" {
      - id          = "sha256:b692a91e4e1582db97076184dae0b2f4a7a86b68c4fe6f91affa50ae06369bf5nginx:latest" -> null
      - latest      = "sha256:b692a91e4e1582db97076184dae0b2f4a7a86b68c4fe6f91affa50ae06369bf5" -> null
      - name        = "nginx:latest" -> null
      - repo_digest = "nginx@sha256:790711e34858c9b0741edffef6ed3d8199d8faa33f2870dea5db70f16384df79" -> null
    }

Plan: 0 to add, 0 to change, 2 to destroy.
╷
│ Warning: Deprecated attribute
│ 
│   on main.tf line 12, in resource "docker_container" "nginx":
│   12:   image = docker_image.nginx.latest
│ 
│ The attribute "latest" is deprecated. Refer to the provider documentation for
│ details.
╵

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

docker_container.nginx: Destroying... [id=e5be6bec1a7b52d4f5301b313201331f4c3b0b83e969a1ec348908e1d63764f7]
docker_container.nginx: Destruction complete after 0s
docker_image.nginx: Destroying... [id=sha256:b692a91e4e1582db97076184dae0b2f4a7a86b68c4fe6f91affa50ae06369bf5nginx:latest]
docker_image.nginx: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.

```

Look for a message at the bottom of the output asking for confirmation. Type `yes` and hit ENTER. Terraform will destroy the resources it had created earlier.


## Next Steps

- [Learn to use Input Variables to make your code more portable](https://www.terraform.io/language/values/variables)
- [Use Outputs to expose data for other configurations](https://www.terraform.io/language/values/outputs)
- [Store the Terraform state remotely](https://www.terraform.io/language/state/remote)